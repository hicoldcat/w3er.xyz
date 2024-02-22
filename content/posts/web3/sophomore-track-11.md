---
title: Web3系列教程之入门篇---11. DAO
description: null
author: 李留白
weight: 0
date: 2022-07-16T00:58:34.440Z
lastmod: 2022-10-12T02:11:22.605Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703215340.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220716080334.png)

## 什么是 DAO？

DAO代表去中心化的自治组织。您可以将 DAO 视为类似于现实世界中的公司。从本质上讲，DAO 允许成员创建治理决策并对其进行投票。

在传统公司中，当需要做出决定时，公司的董事会或高管负责做出该决定。然而，在 DAO 中，这个过程是民主化的，任何成员都可以创建提案，所有其他成员都可以对其进行投票。创建的每个提案都有一个投票截止日期，在截止日期之后做出有利于投票结果的决定（是或否）。

DAO 的成员资格通常受到 ERC20 Token 所有权或 NFT 所有权的限制。成员资格和投票权与您拥有的Token数量成正比的 DAO 示例包括Uniswap和ENS。基于 NFT 的 DAO 示例包括Meebits DAO。

## 构建我们的 DAO

假如你想为你的`CryptoDevs`NFT 持有者启动一个 DAO。从通过 ICO 获得的 ETH 中，你建立了一个 DAO 库。DAO 现在有很多 ETH，但目前什么也没做。

您希望允许您的 NFT 持有者创建并投票使用该 ETH 从 NFT 市场购买其他 NFT 的提案，并推测价格。也许将来当你卖回 NFT 时，你会将利润分配给 DAO 的所有成员。

## 要求

- 任何拥有`CryptoDevs`NFT 的人都可以创建从 NFT 市场购买不同 NFT 的提案
- 每个拥有`CryptoDevs`NFT 的人都可以投票支持或反对活跃的提案
- 每个 NFT 计为每个提案的一票
- 投票者不能对具有相同 NFT 的同一个提案多次投票
- 如果在截止日期前多数选民投票支持该提案，NFT 购买将自动执行

## 我们将做什么

- 为了能够在提案通过时自动购买 NFT，您需要一个可以调用`purchase()`函数的链上 NFT 市场。那里有很多 NFT 市场，但为了避免事情过于复杂，我们将为本教程创- 建一个简化的假 NFT 市场，因为重点是 DAO。
- 我们还将使用 Hardhat 制作实际的 DAO 智能合约。
- 我们将使用 Next.js 制作网站，以允许用户创建和对提案进行投票

## 先决条件
- 你已完成[NFT-Collection ](https://w3er.xyz/posts/web3/sophomore-track-9/)教程
- 你必须有一些 ETH 给 DAO 财政部

## 构建

### 智能合约开发

我们将从创建智能合约开始。我们将制作两个智能合约：

- `FakeNFTMarketplace.sol`
- `CryptoDevsDAO.sol`

为此，我们将使用我们在过去几个教程中一直使用的Hardhat开发框架。

- 为此项目创建一个名为 的文件夹`DAO-Tutorial`，并在该文件夹中打开一个终端窗口。

- 通过在终端中运行以下命令来设置新的安全帽项目：

```
mkdir hardhat-tutorial
cd hardhat-tutorial
npm init --yes
npm install --save-dev hardhat
```
现在您已经安装了 Hardhat，我们可以设置一个项目。在终端中执行以下命令。

- 在安装 Hardhat 的同一目录中运行：

```
npx hardhat
```

- 选择Create a Javascript project

- 按回车键已指定Hardhat Project root

- 如果您想添加一个问题，请按 Enter 键.gitignore

- 按回车键Do you want to install this sample project's dependencies with npm (@nomicfoundation/hardhat-toolbox)?

现在你有一个安全帽项目准备好了！

如果您在 Windows 上，请执行此额外步骤并安装这些库：)

```
npm install --save-dev @nomicfoundation/hardhat-toolbox
```

并按下Enter所有问题（选择Create a basic sample project）选项。

- 现在，让我们从 NPM 安装包`@openzeppelin/contracts`开始，因为我们将使用OpenZeppelin 的 Ownable Contract作为 DAO 合约。

```
npm install @openzeppelin/contracts
```

- 首先，让我们做一个简单的 `Fake NFT Marketplace`。在`hardhat-tutorial` `contracts`的目录下创建一个名为`FakeNFTMarketplace.sol`的文件，并添加以下代码

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract FakeNFTMarketplace {
    /// @dev Maintain a mapping of Fake TokenID to Owner addresses
    mapping(uint256 => address) public tokens;
    /// @dev Set the purchase price for each Fake NFT
    uint256 NFTPrice = 0.1 ether;

    /// @dev purchase() accepts ETH and marks the owner of the given tokenId as the caller address
    /// @param _tokenId - the fake NFT token Id to purchase
    function purchase(uint256 _tokenId) external payable {
        require(msg.value == NFTPrice, "This NFT costs 0.1 ether");
        tokens[_tokenId] = msg.sender;
    }

    /// @dev getPrice() returns the price of one NFT
    function getPrice() external view returns (uint256) {
        return NFTPrice;
    }

    /// @dev available() checks whether the given tokenId has already been sold or not
    /// @param _tokenId - the tokenId to check for
    function available(uint256 _tokenId) external view returns (bool) {
        // address(0) = 0x0000000000000000000000000000000000000000
        // This is the default value for addresses in Solidity
        if (tokens[_tokenId] == address(0)) {
            return true;
        }
        return false;
    }
}
```

- `FakeNFTMarketplace`如果提案获得通过，我们将从 DAO 合约中使用这些基本功能来购买 NFT 。真正的 NFT 市场会更加复杂——因为并非所有 NFT 的价格都相同。

- 在开始编写 DAO 合约之前，让我们确保一切都编译好。`hardhat-tutorial`从终端在文件夹中运行以下命令。

```
npx hardhat compile
```
并确保没有编译错误。

- 现在，我们将开始编写`CryptoDevsDAO`合约。由于这主要是一个完全自定义的合约，并且比我们目前所做的相对复杂，所以我们将一点一点地解释这一点。

- 首先，让我们为合约编写样板代码。`CryptoDevsDAO.sol`在`contracts`目录下创建一个名为 in 的新文件`hardhat-tutorial`，并将以下代码添加到其中。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/access/Ownable.sol";

// We will add the Interfaces here

contract CryptoDevsDAO is Ownable {
    // We will write contract code here
}
```

- 现在，我们将需要调用`FakeNFTMarketplace`合约上的函数，以及您之前部署的`CryptoDevs NFT`合约。回想一下`Advanced Solidity Topics`教程，我们需要为这些合约提供一个接口，所以这个合约知道哪些函数可以调用，它们作为参数接受什么，返回什么。

- 通过添加以下代码将以下两个接口添加到您的代码中

```
/**
 * Interface for the FakeNFTMarketplace
 */
interface IFakeNFTMarketplace {
    /// @dev getPrice() returns the price of an NFT from the FakeNFTMarketplace
    /// @return Returns the price in Wei for an NFT
    function getPrice() external view returns (uint256);

    /// @dev available() returns whether or not the given _tokenId has already been purchased
    /// @return Returns a boolean value - true if available, false if not
    function available(uint256 _tokenId) external view returns (bool);

    /// @dev purchase() purchases an NFT from the FakeNFTMarketplace
    /// @param _tokenId - the fake NFT tokenID to purchase
    function purchase(uint256 _tokenId) external payable;
}

/**
 * Minimal interface for CryptoDevsNFT containing only two functions
 * that we are interested in
 */
interface ICryptoDevsNFT {
    /// @dev balanceOf returns the number of NFTs owned by the given address
    /// @param owner - address to fetch number of NFTs for
    /// @return Returns the number of NFTs owned
    function balanceOf(address owner) external view returns (uint256);

    /// @dev tokenOfOwnerByIndex returns a tokenID at given index for owner
    /// @param owner - address to fetch the NFT TokenID for
    /// @param index - index of NFT in owned tokens array to fetch
    /// @return Returns the TokenID of the NFT
    function tokenOfOwnerByIndex(address owner, uint256 index)
        external
        view
        returns (uint256);
}
```

- 现在，让我们考虑一下我们在 DAO 合约中需要哪些功能。

- 以合约状态存储已创建的提案
- 允许 CryptoDevs NFT 的持有者创建新提案
- 允许 CryptoDevs NFT 的持有者对提案进行投票，因为他们尚未投票，并且提案尚未通过其截止日期
- 允许 CryptoDevs NFT 的持有者在超过截止日期后执行提案，以在提案通过时触发 NFT 购买

- 让我们首先创建一个代表Proposal的结构。在你的合约中，添加以下代码。

```
// Create a struct named Proposal containing all relevant information
struct Proposal {
    // NFTTokenId - the tokenID of the NFT to purchase from FakeNFTMarketplace if the proposal passes
    uint256 NFTTokenId;
    // deadline - the UNIX timestamp until which this proposal is active. Proposal can be executed after the deadline has been exceeded.
    uint256 deadline;
    // yayVotes - number of yay votes for this proposal
    uint256 yayVotes;
    // nayVotes - number of nay votes for this proposal
    uint256 nayVotes;
    // executed - whether or not this proposal has been executed yet. Cannot be executed before the deadline has been exceeded.
    bool executed;
    // voters - a mapping of CryptoDevsNFT tokenIDs to booleans indicating whether that NFT has already been used to cast a vote or not
    mapping(uint256 => bool) voters;
}
```

- 让我们也创建一个从提案ID到提案的映射，以保存所有创建的提案，以及一个计数器来计算存在的提案数量。

```
// Create a mapping of ID to Proposal
mapping(uint256 => Proposal) public proposals;
// Number of proposals that have been created
uint256 public numProposals;
```

- 现在，由于我们将在`FakeNFTMarketplace`和`CryptoDevsNFT`合约上调用函数，让我们为这些合约初始化变量。

```
IFakeNFTMarketplace NFTMarketplace;
ICryptoDevsNFT cryptoDevsNFT;
```

- 创建一个构造函数，初始化这些合约变量，同时接受部署者的ETH存款以填充DAO的ETH库。(在后台，由于我们导入了`Ownable`合约，这也将设置合约部署者为该合约的所有者）。

```
// Create a payable constructor which initializes the contract
// instances for FakeNFTMarketplace and CryptoDevsNFT
// The payable allows this constructor to accept an ETH deposit when it is being deployed
constructor(address _NFTMarketplace, address _cryptoDevsNFT) payable {
    NFTMarketplace = IFakeNFTMarketplace(_NFTMarketplace);
    cryptoDevsNFT = ICryptoDevsNFT(_cryptoDevsNFT);
}
```

- 现在，由于我们希望几乎所有的其他函数只被那些拥有`CryptoDevs NFT`合约的人调用，我们将创建一个`modifier`以避免重复代码。

```
// Create a modifier which only allows a function to be
// called by someone who owns at least 1 CryptoDevsNFT
modifier NFTHolderOnly() {
    require(cryptoDevsNFT.balanceOf(msg.sender) > 0, "NOT_A_DAO_MEMBER");
    _;
}
```

- 我们现在有足够的能力来编写我们的`createProposal`函数，它将允许成员创建新的提案。

```
/// @dev createProposal allows a CryptoDevsNFT holder to create a new proposal in the DAO
/// @param _NFTTokenId - the tokenID of the NFT to be purchased from FakeNFTMarketplace if this proposal passes
/// @return Returns the proposal index for the newly created proposal
function createProposal(uint256 _NFTTokenId)
    external
    NFTHolderOnly
    returns (uint256)
{
    require(NFTMarketplace.available(_NFTTokenId), "NFT_NOT_FOR_SALE");
    Proposal storage proposal = proposals[numProposals];
    proposal.NFTTokenId = _NFTTokenId;
    // Set the proposal's voting deadline to be (current time + 5 minutes)
    proposal.deadline = block.timestamp + 5 minutes;

    numProposals++;

    return numProposals - 1;
}

```

- 现在，要对一个提案进行投票，我们要增加一个额外的限制，即被投票的提案必须没有超过其最后期限。为了做到这一点，我们将创建第二个修改器。

```
// Create a modifier which only allows a function to be
// called if the given proposal's deadline has not been exceeded yet
modifier activeProposalOnly(uint256 proposalIndex) {
    require(
        proposals[proposalIndex].deadline > block.timestamp,
        "DEADLINE_EXCEEDED"
    );
    _;
}

```

注意这个修改器是如何接受一个参数的!

- 此外，由于投票只能是两个值中的一个（YAY或NAY）--我们可以创建一个代表可能选项的枚举。

```
// Create an enum named Vote containing possible options for a vote
enum Vote {
    YAY, // YAY = 0
    NAY // NAY = 1
}
```

- 让我们来写写`voteOnProposal`的函数

```
/// @dev voteOnProposal allows a CryptoDevsNFT holder to cast their vote on an active proposal
/// @param proposalIndex - the index of the proposal to vote on in the proposals array
/// @param vote - the type of vote they want to cast
function voteOnProposal(uint256 proposalIndex, Vote vote)
    external
    NFTHolderOnly
    activeProposalOnly(proposalIndex)
{
    Proposal storage proposal = proposals[proposalIndex];

    uint256 voterNFTBalance = cryptoDevsNFT.balanceOf(msg.sender);
    uint256 numVotes = 0;

    // Calculate how many NFTs are owned by the voter
    // that haven't already been used for voting on this proposal
    for (uint256 i = 0; i < voterNFTBalance; i++) {
        uint256 tokenId = cryptoDevsNFT.tokenOfOwnerByIndex(msg.sender, i);
        if (proposal.voters[tokenId] == false) {
            numVotes++;
            proposal.voters[tokenId] = true;
        }
    }
    require(numVotes > 0, "ALREADY_VOTED");

    if (vote == Vote.YAY) {
        proposal.yayVotes += numVotes;
    } else {
        proposal.nayVotes += numVotes;
    }
}
```

- 我们快完成了!为了执行一个已经超过截止日期的提案，我们将创建最后的修改器。

```
// Create a modifier which only allows a function to be
// called if the given proposals' deadline HAS been exceeded
// and if the proposal has not yet been executed
modifier inactiveProposalOnly(uint256 proposalIndex) {
    require(
        proposals[proposalIndex].deadline <= block.timestamp,
        "DEADLINE_NOT_EXCEEDED"
    );
    require(
        proposals[proposalIndex].executed == false,
        "PROPOSAL_ALREADY_EXECUTED"
    );
    _;
}
```
注意这个修改器也需要一个参数!

- 我们来写一下`executeProposal`的代码

```
/// @dev executeProposal allows any CryptoDevsNFT holder to execute a proposal after it's deadline has been exceeded
/// @param proposalIndex - the index of the proposal to execute in the proposals array
function executeProposal(uint256 proposalIndex)
    external
    NFTHolderOnly
    inactiveProposalOnly(proposalIndex)
{
    Proposal storage proposal = proposals[proposalIndex];

    // If the proposal has more YAY votes than NAY votes
    // purchase the NFT from the FakeNFTMarketplace
    if (proposal.yayVotes > proposal.nayVotes) {
        uint256 NFTPrice = NFTMarketplace.getPrice();
        require(address(this).balance >= NFTPrice, "NOT_ENOUGH_FUNDS");
        NFTMarketplace.purchase{value: NFTPrice}(proposal.NFTTokenId);
    }
    proposal.executed = true;
}
```

- 在这一点上，我们已经实现了所有的核心功能。然而，有几个额外的功能我们可以而且应该实现。

  - 如果需要，允许合约所有人从DAO提取ETH
  - 允许合约进一步接受ETH存款

- 我们继承的`Ownable`合约包含一个修改器`onlyOwner`，它限制一个函数只能被合约所有者调用。让我们使用这个修饰符来实现 `withdrawEther`。

```
/// @dev withdrawEther allows the contract owner (deployer) to withdraw the ETH from the contract
function withdrawEther() external onlyOwner {
    payable(owner()).transfer(address(this).balance);
}
```

这将把合约的全部ETH余额转移到业主地址

- 最后，为了允许向DAO金库增加更多的ETH存款，我们需要增加一些特殊的功能。通常情况下，合约地址不能接受发给他们的ETH，除非是通过`payable`函数。但是我们不希望用户只是为了存钱而调用函数，他们应该能够直接从他们的钱包里转出ETH。为此，让我们添加这两个函数。

```
// The following two functions allow the contract to accept ETH deposits
// directly from a wallet without calling a function
receive() external payable {}

fallback() external payable {}
```

### 智能合约的部署

现在我们已经写好了合约，让我们把它们部署到[Rinkeby Testnet](https://rinkeby.etherscan.com/)上。确保你在Rinkeby Testnet上有一些ETH。

- 安装NPM的`dotenv`包，以便能够使用`hardhat.config.js`中.env文件中指定的环境变量。在`hardhat-tutorial`目录下的终端中执行以下命令。

```
npm install dotenv
```

- 现在在`hardhat-tutorial`目录下创建一个`.env`文件，设置以下两个环境变量。按照说明来获取它们的值。确保你使用的Rinkeby私钥在Rinkeby Testnet上有ETH。

```
// Go to https://www.alchemyapi.io, sign up, create
// a new App in its dashboard and select the network as Rinkeby, and replace "add-the-alchemy-key-url-here" with its key url
ALCHEMY_API_KEY_URL="add-the-alchemy-key-url-here"

// Replace this private key with your RINKEBY account private key
// To export your private key from Metamask, open Metamask and
// go to Account Details > Export Private Key
// Be aware of NEVER putting real Ether into testing accounts
RINKEBY_PRIVATE_KEY="add-the-rinkeby-private-key-here"
```

- 现在，让我们写一个部署脚本，为我们自动部署我们的两个合约。在`hardhat-tutorial/scripts`下创建一个新文件，或替换现有的默认文件，命名为`deploy.js`，并添加以下代码。

```
const { ethers } = require("hardhat");
const { CRYPTODEVS_NFT_CONTRACT_ADDRESS } = require("../constants");

async function main() {
  // Deploy the FakeNFTMarketplace contract first
  const FakeNFTMarketplace = await ethers.getContractFactory(
    "FakeNFTMarketplace"
  );
  const fakeNFTMarketplace = await FakeNFTMarketplace.deploy();
  await fakeNFTMarketplace.deployed();

  console.log("FakeNFTMarketplace deployed to: ", fakeNFTMarketplace.address);

  // Now deploy the CryptoDevsDAO contract
  const CryptoDevsDAO = await ethers.getContractFactory("CryptoDevsDAO");
  const cryptoDevsDAO = await CryptoDevsDAO.deploy(
    fakeNFTMarketplace.address,
    CRYPTODEVS_NFT_CONTRACT_ADDRESS,
    {
      // This assumes your account has at least 1 ETH in it's account
      // Change this value as you want
      value: ethers.utils.parseEther("1"),
    }
  );
  await cryptoDevsDAO.deployed();

  console.log("CryptoDevsDAO deployed to: ", cryptoDevsDAO.address);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });

```

- 你可能已经注意到，`deploy.js`从一个名为`constants`的文件中导入了一个名为`CRYPTODEVS_NFT_CONTRACT_ADDRESS`的变量。让我们来做这个。在`hardhat-tutorial`目录下创建一个名为`constants.js`的新文件。

```
// Replace the value with your NFT contract address
const CRYPTODEVS_NFT_CONTRACT_ADDRESS =
  "YOUR_CRYPTODEVS_NFT_CONTRACT_ADDRESS_HERE";

module.exports = { CRYPTODEVS_NFT_CONTRACT_ADDRESS };
```

- 现在，让我们把Rinkeby网络添加到你的Hardhat配置中，以便我们可以部署到Rinkeby。打开你的`hardhat.config.js`文件，用以下内容替换它。

```
require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config({ path: ".env" });

const ALCHEMY_API_KEY_URL = process.env.ALCHEMY_API_KEY_URL;

const RINKEBY_PRIVATE_KEY = process.env.RINKEBY_PRIVATE_KEY;

module.exports = {
  solidity: "0.8.9",
  networks: {
    rinkeby: {
      url: ALCHEMY_API_KEY_URL,
      accounts: [RINKEBY_PRIVATE_KEY],
    },
  },
};

```

- 让我们在继续进行之前确保一切都能编译。在`hardhat-tutorial`文件夹中从终端执行以下命令。

```
npx hardhat compile
```

并确保没有编译错误。如果你确实遇到了编译错误，试着将你的代码与[这里的最终版本](https://github.com/LearnWeb3DAO/Building-a-DAO/blob/main/hardhat-tutorial/contracts/CryptoDevsDAO.sol)进行比较

- 让我们开始部署吧!在终端中从`hardhat-tutorial`目录中执行以下命令

```
npx hardhat run scripts/deploy.js --network rinkeby
```

- 保存`FakeNFTMarketplace`和`CryptoDevsDAO`的合约地址，它们被打印在你的终端上。你以后会需要这些。

### 前端开发

哇！已经开发了这么多！

我们已经成功开发并部署了我们的合约到 Rinkeby 测试网。现在，是时候构建前端界面了，这样用户就可以从网站上创建提案并对其进行投票。

为了开发网站，我们将使用目前为止的[Next.js](https://nextjs.org/)，这是一个建立在React之上的元框架。

- 让我们从创建一个新`next`应用开始。设置`next`应用程序后，您的文件夹结构应如下所示：

```
- DAO-Tutorial
    - hardhat-tutorial
    - my-app
```

- 要创建，请在目录`my-app`中的终端中执行以下命令`DAO-Tutorial`

```
npx create-next-app@latest
```

并按下Enter所有问题提示。这应该创建my-app文件夹并设置一个基本的 Next.js 项目。

- 让我们看看是否一切正常。在终端中运行以下命令

```
cd my-app
npm run dev
```

- 您的网站应该在`http://localhost:3000`. 但是，这是一个基本的初学者 Next.js 项目，我们需要为其添加代码以执行我们想要的操作。

- 让我们安装`web3modal`和`ethers`库。Web3Modal 将允许我们支持在浏览器中连接到钱包，而以太币将用于与区块链交互。从目录`my-app`中在终端中运行它。

```
npm install web3modal ethers

```

- 在`my-app/public/cryptodevs`下载并保存以下文件为`0.svg`。我们将在网页上显示这个图片。注意：你需要在public里面创建cryptodevs文件夹。[下载图片](https://github.com/LearnWeb3DAO/Building-a-DAO/blob/main/my-app/public/cryptodevs/0.svg)


- 在`my-app/styles/Home.modules.css`中添加以下CSS样式

```
.main {
  min-height: 90vh;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  font-family: "Courier New", Courier, monospace;
}

.footer {
  display: flex;
  padding: 2rem 0;
  border-top: 1px solid #eaeaea;
  justify-content: center;
  align-items: center;
}

.image {
  width: 70%;
  height: 50%;
  margin-left: 20%;
}

.title {
  font-size: 2rem;
  margin: 2rem 0;
}

.description {
  line-height: 1;
  margin: 2rem 0;
  font-size: 1.2rem;
}

.button {
  border-radius: 4px;
  background-color: blue;
  border: none;
  color: #ffffff;
  font-size: 15px;
  padding: 10px;
  width: 200px;
  cursor: pointer;
  margin-right: 2%;
}

.button2 {
  border-radius: 4px;
  background-color: indigo;
  border: none;
  color: #ffffff;
  font-size: 15px;
  padding: 10px;
  cursor: pointer;
  margin-right: 2%;
  margin-top: 1rem;
}

.proposalCard {
  width: fit-content;
  margin-top: 0.25rem;
  border: black 2px solid;
  flex: 1;
  flex-direction: column;
}

.container {
  margin-top: 2rem;
}

.flex {
  flex: 1;
  justify-content: space-between;
}

@media (max-width: 1000px) {
  .main {
    width: 100%;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
}
```

- 该网站还需要从两个智能合约--`CryptoDevsDAO`和`CryptoDevsNFT`中读/写数据。让我们把它们的合约地址和ABI存储在一个常量文件中。在`my-app`目录下创建一个`constants.js`文件。

```
export const CRYPTODEVS_DAO_CONTRACT_ADDRESS = "";
export const CRYPTODEVS_NFT_CONTRACT_ADDRESS = "";

export const CRYPTODEVS_DAO_ABI = [];
export const CRYPTODEVS_NFT_ABI = [];
```

- 用你的相关合约地址和ABI值替换合约地址和ABI值。

- 现在来看看实际的酷网站代码。打开`my-app/pages/index.js`，写下以下代码。代码的解释可以在注释中找到。

```
import { Contract, providers } from "ethers";
import { formatEther } from "ethers/lib/utils";
import Head from "next/head";
import { useEffect, useRef, useState } from "react";
import Web3Modal from "web3modal";
import {
  CRYPTODEVS_DAO_ABI,
  CRYPTODEVS_DAO_CONTRACT_ADDRESS,
  CRYPTODEVS_NFT_ABI,
  CRYPTODEVS_NFT_CONTRACT_ADDRESS,
} from "../constants";
import styles from "../styles/Home.module.css";

export default function Home() {
  // ETH Balance of the DAO contract
  const [treasuryBalance, setTreasuryBalance] = useState("0");
  // Number of proposals created in the DAO
  const [numProposals, setNumProposals] = useState("0");
  // Array of all proposals created in the DAO
  const [proposals, setProposals] = useState([]);
  // User's balance of CryptoDevs NFTs
  const [NFTBalance, setNFTBalance] = useState(0);
  // Fake NFT Token ID to purchase. Used when creating a proposal.
  const [fakeNFTTokenId, setFakeNFTTokenId] = useState("");
  // One of "Create Proposal" or "View Proposals"
  const [selectedTab, setSelectedTab] = useState("");
  // True if waiting for a transaction to be mined, false otherwise.
  const [loading, setLoading] = useState(false);
  // True if user has connected their wallet, false otherwise
  const [walletConnected, setWalletConnected] = useState(false);
  const web3ModalRef = useRef();

  // Helper function to connect wallet
  const connectWallet = async () => {
    try {
      await getProviderOrSigner();
      setWalletConnected(true);
    } catch (error) {
      console.error(error);
    }
  };

  // Reads the ETH balance of the DAO contract and sets the `treasuryBalance` state variable
  const getDAOTreasuryBalance = async () => {
    try {
      const provider = await getProviderOrSigner();
      const balance = await provider.getBalance(
        CRYPTODEVS_DAO_CONTRACT_ADDRESS
      );
      setTreasuryBalance(balance.toString());
    } catch (error) {
      console.error(error);
    }
  };

  // Reads the number of proposals in the DAO contract and sets the `numProposals` state variable
  const getNumProposalsInDAO = async () => {
    try {
      const provider = await getProviderOrSigner();
      const contract = getDaoContractInstance(provider);
      const daoNumProposals = await contract.numProposals();
      setNumProposals(daoNumProposals.toString());
    } catch (error) {
      console.error(error);
    }
  };

  // Reads the balance of the user's CryptoDevs NFTs and sets the `NFTBalance` state variable
  const getUserNFTBalance = async () => {
    try {
      const signer = await getProviderOrSigner(true);
      const NFTContract = getCryptodevsNFTContractInstance(signer);
      const balance = await NFTContract.balanceOf(signer.getAddress());
      setNFTBalance(parseInt(balance.toString()));
    } catch (error) {
      console.error(error);
    }
  };

  // Calls the `createProposal` function in the contract, using the tokenId from `fakeNFTTokenId`
  const createProposal = async () => {
    try {
      const signer = await getProviderOrSigner(true);
      const daoContract = getDaoContractInstance(signer);
      const txn = await daoContract.createProposal(fakeNFTTokenId);
      setLoading(true);
      await txn.wait();
      await getNumProposalsInDAO();
      setLoading(false);
    } catch (error) {
      console.error(error);
      window.alert(error.data.message);
    }
  };

  // Helper function to fetch and parse one proposal from the DAO contract
  // Given the Proposal ID
  // and converts the returned data into a Javascript object with values we can use
  const fetchProposalById = async (id) => {
    try {
      const provider = await getProviderOrSigner();
      const daoContract = getDaoContractInstance(provider);
      const proposal = await daoContract.proposals(id);
      const parsedProposal = {
        proposalId: id,
        NFTTokenId: proposal.NFTTokenId.toString(),
        deadline: new Date(parseInt(proposal.deadline.toString()) * 1000),
        yayVotes: proposal.yayVotes.toString(),
        nayVotes: proposal.nayVotes.toString(),
        executed: proposal.executed,
      };
      return parsedProposal;
    } catch (error) {
      console.error(error);
    }
  };

  // Runs a loop `numProposals` times to fetch all proposals in the DAO
  // and sets the `proposals` state variable
  const fetchAllProposals = async () => {
    try {
      const proposals = [];
      for (let i = 0; i < numProposals; i++) {
        const proposal = await fetchProposalById(i);
        proposals.push(proposal);
      }
      setProposals(proposals);
      return proposals;
    } catch (error) {
      console.error(error);
    }
  };

  // Calls the `voteOnProposal` function in the contract, using the passed
  // proposal ID and Vote
  const voteOnProposal = async (proposalId, _vote) => {
    try {
      const signer = await getProviderOrSigner(true);
      const daoContract = getDaoContractInstance(signer);

      let vote = _vote === "YAY" ? 0 : 1;
      const txn = await daoContract.voteOnProposal(proposalId, vote);
      setLoading(true);
      await txn.wait();
      setLoading(false);
      await fetchAllProposals();
    } catch (error) {
      console.error(error);
      window.alert(error.data.message);
    }
  };

  // Calls the `executeProposal` function in the contract, using
  // the passed proposal ID
  const executeProposal = async (proposalId) => {
    try {
      const signer = await getProviderOrSigner(true);
      const daoContract = getDaoContractInstance(signer);
      const txn = await daoContract.executeProposal(proposalId);
      setLoading(true);
      await txn.wait();
      setLoading(false);
      await fetchAllProposals();
    } catch (error) {
      console.error(error);
      window.alert(error.data.message);
    }
  };

  // Helper function to fetch a Provider/Signer instance from Metamask
  const getProviderOrSigner = async (needSigner = false) => {
    const provider = await web3ModalRef.current.connect();
    const web3Provider = new providers.Web3Provider(provider);

    const { chainId } = await web3Provider.getNetwork();
    if (chainId !== 4) {
      window.alert("Please switch to the Rinkeby network!");
      throw new Error("Please switch to the Rinkeby network");
    }

    if (needSigner) {
      const signer = web3Provider.getSigner();
      return signer;
    }
    return web3Provider;
  };

  // Helper function to return a DAO Contract instance
  // given a Provider/Signer
  const getDaoContractInstance = (providerOrSigner) => {
    return new Contract(
      CRYPTODEVS_DAO_CONTRACT_ADDRESS,
      CRYPTODEVS_DAO_ABI,
      providerOrSigner
    );
  };

  // Helper function to return a CryptoDevs NFT Contract instance
  // given a Provider/Signer
  const getCryptodevsNFTContractInstance = (providerOrSigner) => {
    return new Contract(
      CRYPTODEVS_NFT_CONTRACT_ADDRESS,
      CRYPTODEVS_NFT_ABI,
      providerOrSigner
    );
  };

  // piece of code that runs everytime the value of `walletConnected` changes
  // so when a wallet connects or disconnects
  // Prompts user to connect wallet if not connected
  // and then calls helper functions to fetch the
  // DAO Treasury Balance, User NFT Balance, and Number of Proposals in the DAO
  useEffect(() => {
    if (!walletConnected) {
      web3ModalRef.current = new Web3Modal({
        network: "rinkeby",
        providerOptions: {},
        disableInjectedProvider: false,
      });

      connectWallet().then(() => {
        getDAOTreasuryBalance();
        getUserNFTBalance();
        getNumProposalsInDAO();
      });
    }
  }, [walletConnected]);

  // Piece of code that runs everytime the value of `selectedTab` changes
  // Used to re-fetch all proposals in the DAO when user switches
  // to the 'View Proposals' tab
  useEffect(() => {
    if (selectedTab === "View Proposals") {
      fetchAllProposals();
    }
  }, [selectedTab]);

  // Render the contents of the appropriate tab based on `selectedTab`
  function renderTabs() {
    if (selectedTab === "Create Proposal") {
      return renderCreateProposalTab();
    } else if (selectedTab === "View Proposals") {
      return renderViewProposalsTab();
    }
    return null;
  }

  // Renders the 'Create Proposal' tab content
  function renderCreateProposalTab() {
    if (loading) {
      return (
        <div className={styles.description}>
          Loading... Waiting for transaction...
        </div>
      );
    } else if (NFTBalance === 0) {
      return (
        <div className={styles.description}>
          You do not own any CryptoDevs NFTs. <br />
          <b>You cannot create or vote on proposals</b>
        </div>
      );
    } else {
      return (
        <div className={styles.container}>
          <label>Fake NFT Token ID to Purchase: </label>
          <input
            placeholder="0"
            type="number"
            onChange={(e) => setFakeNFTTokenId(e.target.value)}
          />
          <button className={styles.button2} onClick={createProposal}>
            Create
          </button>
        </div>
      );
    }
  }

  // Renders the 'View Proposals' tab content
  function renderViewProposalsTab() {
    if (loading) {
      return (
        <div className={styles.description}>
          Loading... Waiting for transaction...
        </div>
      );
    } else if (proposals.length === 0) {
      return (
        <div className={styles.description}>
          No proposals have been created
        </div>
      );
    } else {
      return (
        <div>
          {proposals.map((p, index) => (
            <div key={index} className={styles.proposalCard}>
              <p>Proposal ID: {p.proposalId}</p>
              <p>Fake NFT to Purchase: {p.NFTTokenId}</p>
              <p>Deadline: {p.deadline.toLocaleString()}</p>
              <p>Yay Votes: {p.yayVotes}</p>
              <p>Nay Votes: {p.nayVotes}</p>
              <p>Executed?: {p.executed.toString()}</p>
              {p.deadline.getTime() > Date.now() && !p.executed ? (
                <div className={styles.flex}>
                  <button
                    className={styles.button2}
                    onClick={() => voteOnProposal(p.proposalId, "YAY")}
                  >
                    Vote YAY
                  </button>
                  <button
                    className={styles.button2}
                    onClick={() => voteOnProposal(p.proposalId, "NAY")}
                  >
                    Vote NAY
                  </button>
                </div>
              ) : p.deadline.getTime() < Date.now() && !p.executed ? (
                <div className={styles.flex}>
                  <button
                    className={styles.button2}
                    onClick={() => executeProposal(p.proposalId)}
                  >
                    Execute Proposal{" "}
                    {p.yayVotes > p.nayVotes ? "(YAY)" : "(NAY)"}
                  </button>
                </div>
              ) : (
                <div className={styles.description}>Proposal Executed</div>
              )}
            </div>
          ))}
        </div>
      );
    }
  }

  return (
    <div>
      <Head>
        <title>CryptoDevs DAO</title>
        <meta name="description" content="CryptoDevs DAO" />
        <link rel="icon" href="/favicon.ico" />
      </Head>

      <div className={styles.main}>
        <div>
          <h1 className={styles.title}>Welcome to Crypto Devs!</h1>
          <div className={styles.description}>Welcome to the DAO!</div>
          <div className={styles.description}>
            Your CryptoDevs NFT Balance: {NFTBalance}
            <br />
            Treasury Balance: {formatEther(treasuryBalance)} ETH
            <br />
            Total Number of Proposals: {numProposals}
          </div>
          <div className={styles.flex}>
            <button
              className={styles.button}
              onClick={() => setSelectedTab("Create Proposal")}
            >
              Create Proposal
            </button>
            <button
              className={styles.button}
              onClick={() => setSelectedTab("View Proposals")}
            >
              View Proposals
            </button>
          </div>
          {renderTabs()}
        </div>
        <div>
          <img className={styles.image} src="/cryptodevs/0.svg" />
        </div>
      </div>

      <footer className={styles.footer}>
        Made with &#10084; by Crypto Devs
      </footer>
    </div>
  );
}
```

- 让我们来运行它!在你的终端，从`my-app`目录中，执行。

```
npm run dev
```

来看看你的网站的运行情况。它应该看起来像本教程开头的截图。

祝贺您！您的CryptoDevs DAO网站现在应该可以运行了。你的CryptoDevs DAO网站现在应该工作了。

### 测试

- 创建几个提案
- YAY尝试对一个和NAY另一个投票
- 等待 5 分钟让他们的最后期限过去
- 执行这两个。
- 观察 DAO 财政部的余额`0.1 ETH`由于在执行时购买 NFT 时通过的提案而下降。

### 推送到 Github

在继续下一步之前，请确保将所有这些代码推送到 Github。

### 网站部署

如果您无法与他人共享网站，那么它有什么用？让我们努力将您的 dApp 部署到世界各地，以便您可以与所有 LearnWeb3DAO 朋友分享它。

- 转到[Vercel 仪表板](https://vercel.com/)并使用您的 GitHub 帐户登录。
- 单击New Project按钮并选择您的DAO-Tutorial存储库。
- 在配置您的新项目时，Vercel 将允许您自定义您的Root Directory
- 由于我们的 Next.js 应用程序位于 repo 的子文件夹中，因此我们需要对其进行修改。
- 单击Edit旁边Root Directory并将其设置为my-app。
- 选择框架为Next.js
- 点击Deploy

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220716085744.png)

- 现在，您可以通过转到 Vercel 仪表板、选择您的项目并从那里复制域来查看您部署的网站！

恭喜！你们都完成了！

> 原文：[https://www.learnweb3.io/tracks/sophomore/decentralized-autonomous-organizations](https://www.learnweb3.io/tracks/sophomore/decentralized-autonomous-organizations)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)