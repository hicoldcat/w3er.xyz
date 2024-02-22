---
title: Web3系列教程之入门篇---9. NFT集合
description: null
author: 李留白
weight: 0
date: 2022-07-10T06:37:50.100Z
lastmod: 2022-10-12T02:11:22.614Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703215340.png
---

现在是你推出你自己的NFT系列的时候了 - `Crypto Devs`。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220710151326.png)

## 要求

- 应该只存在 20 个 Crypto Dev NFT，并且每个都应该是唯一的。
- 用户应该能够通过一笔交易仅铸造 1 个 NFT。
- 白名单用户应在实际销售前有 5 分钟的预售期，保证每笔交易获得 1 NFT。
- 你的 NFT 收藏应该有一个网站。

让我们开始建造🚀

## 先决条件
你应该已经完成​​了[白名单 dApp 教程](https://w3er.xyz/posts/web3/sophomore-track-8/)

## 理论

- 什么是不可替代代币？可替代意味着相同或可互换，例如 Eth 是可替代的。考虑到这一点，NFT 是独一无二的。每一个都是不同的。每个令牌都有独特的特征和价值。它们都可以相互区分并且不可互换，例如独特的艺术
- 什么是 ERC-721？ERC-721 是一个开放标准，描述了如何在 EVM（以太坊虚拟机）兼容的区块链上构建不可替代的代币；它是不可替代代币的标准接口；它有一套规则，可以很容易地使用 NFT。在继续之前，先看看[ERC721支持的所有功能](https://docs.openzeppelin.com/contracts/3.x/api/token/erc721)

## 智能合约

- 我们还将使用 Openzeppelin 的 `Ownable.sol`，它可以帮助您管理合约的所有权
  - 默认情况下，Ownable 合约的所有者是部署它的帐户，这通常正是您想要的。
  - Ownable 还可以让您：
    - 将所有权从所有者帐户转移到新帐户
    - renounceOwnership 让所有者放弃此管理特权，这是集中管理初始阶段结束后的常见模式。
- 我们还将使用 ERC721 的扩展，称为[ERC721 Enumerable](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/extensions/ERC721Enumerable.sol)
  - ERC721 Enumerable 可帮助您跟踪合约中的所有 tokenIds 以及给定合约的地址持有的 tokensIds。
  - 在继续之前，请先看看它实现的[功能](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721#ERC721Enumerable)

为了构建智能合约，我们将使用[Hardhat](https://hardhat.org/)。Hardhat 是一个以太坊开发环境和框架，专为 Solidity 中的全栈开发而设计。简单来说，您可以编写智能合约、部署它们、运行测试和调试代码。

- 要设置hardhat项目，请打开终端并执行以下命令

```
mkdir NFT-Collection
cd NFT-Collection
mkdir hardhat-tutorial
cd hardhat-tutorial
npm init --yes
npm install --save-dev hardhat
```

- 在安装 Hardhat 的同一目录中运行：

```
npx hardhat
```

- 选择`Create a basic sample project`
- 按回车键指定`Hardhat Project root`
- 如果您想添加一个问题，请按 Enter 键.gitignore
- 按回车键`Do you want to install this sample project's dependencies with npm (@nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers)?`

现在你有一个Hardhat项目准备好了！

如果您不在 Mac 上，请执行此额外步骤并安装这些库：)

```
npm install --save-dev @nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers
```

并按下enter所有问题。

- 现在在同一个终端中安装`@openzeppelin/contracts`，因为我们将在我们的合约中导入[Openzeppelin 的 ERC721Enumerable 合约](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/extensions/ERC721Enumerable.sol)`CryptoDevs`。

```
npm install @openzeppelin/contracts
```

- 我们将需要调用`Whitelist Contract`您为之前级别部署的地址，以检查列入白名单的地址并授予他们预售访问权限。因为我们只需要调用`mapping(address => bool) public whitelistedAddresses;`我们可以只为这个映射创建一个`Whitelist contract`带有函数的接口，这样我们就可以保存`gas`，因为我们不需要继承和部署整个`Whitelist Contract`，而只需要它的一部分。

- 在目录中创建一个新文件`contracts`并调用它`IWhitelist.sol`

```
 // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.4;

    interface IWhitelist {
        function whitelistedAddresses(address) external view returns (bool);
    }
```

现在让我们在目录中创建一个新文件`contracts`并调用它`CryptoDevs.sol`

```
 // SPDX-License-Identifier: MIT
  pragma solidity ^0.8.4;

  import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
  import "@openzeppelin/contracts/access/Ownable.sol";
  import "./IWhitelist.sol";

  contract CryptoDevs is ERC721Enumerable, Ownable {
      /**
       * @dev _baseTokenURI for computing {tokenURI}. If set, the resulting URI for each
       * token will be the concatenation of the `baseURI` and the `tokenId`.
       */
      string _baseTokenURI;

      //  _price is the price of one Crypto Dev NFT
      uint256 public _price = 0.01 ether;

      // _paused is used to pause the contract in case of an emergency
      bool public _paused;

      // max number of CryptoDevs
      uint256 public maxTokenIds = 20;

      // total number of tokenIds minted
      uint256 public tokenIds;

      // Whitelist contract instance
      IWhitelist whitelist;

      // boolean to keep track of whether presale started or not
      bool public presaleStarted;

      // timestamp for when presale would end
      uint256 public presaleEnded;

      modifier onlyWhenNotPaused {
          require(!_paused, "Contract currently paused");
          _;
      }

      /**
       * @dev ERC721 constructor takes in a `name` and a `symbol` to the token collection.
       * name in our case is `Crypto Devs` and symbol is `CD`.
       * Constructor for Crypto Devs takes in the baseURI to set _baseTokenURI for the collection.
       * It also initializes an instance of whitelist interface.
       */
      constructor (string memory baseURI, address whitelistContract) ERC721("Crypto Devs", "CD") {
          _baseTokenURI = baseURI;
          whitelist = IWhitelist(whitelistContract);
      }

      /**
      * @dev startPresale starts a presale for the whitelisted addresses
       */
      function startPresale() public onlyOwner {
          presaleStarted = true;
          // Set presaleEnded time as current timestamp + 5 minutes
          // Solidity has cool syntax for timestamps (seconds, minutes, hours, days, years)
          presaleEnded = block.timestamp + 5 minutes;
      }

      /**
       * @dev presaleMint allows a user to mint one NFT per transaction during the presale.
       */
      function presaleMint() public payable onlyWhenNotPaused {
          require(presaleStarted && block.timestamp < presaleEnded, "Presale is not running");
          require(whitelist.whitelistedAddresses(msg.sender), "You are not whitelisted");
          require(tokenIds < maxTokenIds, "Exceeded maximum Crypto Devs supply");
          require(msg.value >= _price, "Ether sent is not correct");
          tokenIds += 1;
          //_safeMint is a safer version of the _mint function as it ensures that
          // if the address being minted to is a contract, then it knows how to deal with ERC721 tokens
          // If the address being minted to is not a contract, it works the same way as _mint
          _safeMint(msg.sender, tokenIds);
      }

      /**
      * @dev mint allows a user to mint 1 NFT per transaction after the presale has ended.
      */
      function mint() public payable onlyWhenNotPaused {
          require(presaleStarted && block.timestamp >=  presaleEnded, "Presale has not ended yet");
          require(tokenIds < maxTokenIds, "Exceed maximum Crypto Devs supply");
          require(msg.value >= _price, "Ether sent is not correct");
          tokenIds += 1;
          _safeMint(msg.sender, tokenIds);
      }

      /**
      * @dev _baseURI overides the Openzeppelin's ERC721 implementation which by default
      * returned an empty string for the baseURI
      */
      function _baseURI() internal view virtual override returns (string memory) {
          return _baseTokenURI;
      }

      /**
      * @dev setPaused makes the contract paused or unpaused
       */
      function setPaused(bool val) public onlyOwner {
          _paused = val;
      }

      /**
      * @dev withdraw sends all the ether in the contract
      * to the owner of the contract
       */
      function withdraw() public onlyOwner  {
          address _owner = owner();
          uint256 amount = address(this).balance;
          (bool sent, ) =  _owner.call{value: amount}("");
          require(sent, "Failed to send Ether");
      }

       // Function to receive Ether. msg.data must be empty
      receive() external payable {}

      // Fallback function is called when msg.data is not empty
      fallback() external payable {}
  }
```

- 现在我们将安装`dotenv`包以便能够导入 env 文件并在我们的配置中使用它。打开指向`hardhat-tutorial`目录的终端并执行此命令

```
npm install dotenv
```

- 现在在`hardhat-tutorial`文件夹中创建一个`.env`文件，并添加以下几行，使用注释中的说明来获取你的`Alchemy API Key URL`和`RINKEBY私钥`。确保你获得Rinkeby私钥的账户有Rinkeby的以太币资金。

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

- 让我们把合同部署到`rinkeby`网络。在`scripts`文件夹下创建一个名为`deploy.js`的新文件
- 现在我们要写一些代码，在`deploy.js`文件中部署合同。

```
const { ethers } = require("hardhat");
require("dotenv").config({ path: ".env" });
const { WHITELIST_CONTRACT_ADDRESS, METADATA_URL } = require("../constants");

async function main() {
  // Address of the whitelist contract that you deployed in the previous module
  const whitelistContract = WHITELIST_CONTRACT_ADDRESS;
  // URL from where we can extract the metadata for a Crypto Dev NFT
  const metadataURL = METADATA_URL;
  /*
  A ContractFactory in ethers.js is an abstraction used to deploy new smart contracts,
  so cryptoDevsContract here is a factory for instances of our CryptoDevs contract.
  */
  const cryptoDevsContract = await ethers.getContractFactory("CryptoDevs");

  // deploy the contract
  const deployedCryptoDevsContract = await cryptoDevsContract.deploy(
    metadataURL,
    whitelistContract
  );

  // print the address of the deployed contract
  console.log(
    "Crypto Devs Contract Address:",
    deployedCryptoDevsContract.address
  );
}

// Call the main function and catch if there is any error
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

- 正如你所看到的，`deploy.js`需要一些常量。让我们在`hardhat-tutorial`文件夹下创建一个名为`constants`的文件夹。
- 现在在`constants`文件夹内添加一个`index.js`文件，并在该文件中添加以下几行。将 "address-of-the-whitelist-contract "替换为你在之前的教程中部署的白名单合同的地址。对于Metadata_URL，复制已经提供的样本即可。我们将在本教程中进一步替换它。

```
// Address of the Whitelist Contract that you deployed
const WHITELIST_CONTRACT_ADDRESS = "address-of-the-whitelist-contract";
// URL to extract Metadata for a Crypto Dev NFT
const METADATA_URL = "https://nft-collection-sneh1999.vercel.app/api/";

module.exports = { WHITELIST_CONTRACT_ADDRESS, METADATA_URL };
```

- 现在打开`hardhat.config.js`文件，我们将在这里添加`rinkeby`网络，这样我们就可以把我们的合同部署到rinkeby。将`hardhart.config.js`文件中的所有行替换成下面的行

```
require("@nomiclabs/hardhat-waffle");
require("dotenv").config({ path: ".env" });

const ALCHEMY_API_KEY_URL = process.env.ALCHEMY_API_KEY_URL;

const RINKEBY_PRIVATE_KEY = process.env.RINKEBY_PRIVATE_KEY;

module.exports = {
  solidity: "0.8.4",
  networks: {
    rinkeby: {
      url: ALCHEMY_API_KEY_URL,
      accounts: [RINKEBY_PRIVATE_KEY],
    },
  },
};
```

- 编译合同，打开终端，指向`thardhat-tutorial`目录，执行以下命令

```
  npx hardhat compile
```

- 要部署，打开终端指向`thardhat-tutorial`目录并执行以下命令

```
  npx hardhat run scripts/deploy.js --network rinkeby
```

- 将打印在终端上的Crypto Devs合同地址保存在你的记事本中，你会在接下来的教程中需要它。

## 网站

- 为了开发该网站，我们将使用React和Next Js。React是一个用于制作网站的javascript框架，而Next Js是建立在React之上的。
- 首先，你将需要创建一个新的`next `应用程序。你的文件夹结构应该是这样的

```
   - NFT-Collection
       - hardhat-tutorial
       - my-app
```

- 要创建`my-app`程序，在终端指向NFT-Collection文件夹并输入

```
  npx create-next-app@latest
```
并对所有问题按回车键

- 现在要运行该应用程序，在终端执行这些命令

```
cd my-app
npm run dev
```

- 现在去`http://localhost:3000`，你的应用程序应该正在运行🤘。
- 现在让我们安装Web3Modal库（https://github.com/Web3Modal/web3modal）。Web3Modal是一个易于使用的库，帮助开发者通过简单的可定制配置在他们的应用程序中添加对多个供应商的支持。默认情况下，Web3Modal库支持注入的提供者，如（Metamask、Dapper、Gnosis Safe、Frame、Web3 Browsers等），你也可以轻松配置该库以支持Portis、Fortmatic、Squarelink、Torus、Authereum、D'CENT Wallet和Arkane。打开终端，指向`my-app`目录，执行以下命令

```
  npm install web3modal
```

- 在同一终端中也安装`ethers.js`

```
npm install ethers
```

- 在你的公共文件夹中，下载这个文件夹和其中的所有图片（[下载链接](https://download-directory.github.io/?url=https%3A%2F%2Fgithub.com%2FLearnWeb3DAO%2FNFT-Collection%2Ftree%2Fmain%2Fmy-app%2Fpublic%2Fcryptodevs)）。确保下载的文件夹的名称是`cryptodevs`

- 现在去style文件夹，用以下代码替换`Home.modules.css`文件的所有内容，这将给你的dapp添加一些样式。

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
  padding: 20px;
  width: 200px;
  cursor: pointer;
  margin-bottom: 2%;
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

- 打开page文件夹下的index.js文件，粘贴以下代码，代码的解释可以在评论中找到。

```
import { Contract, providers, utils } from "ethers";
import Head from "next/head";
import React, { useEffect, useRef, useState } from "react";
import Web3Modal from "web3modal";
import { abi, NFT_CONTRACT_ADDRESS } from "../constants";
import styles from "../styles/Home.module.css";

export default function Home() {
  // walletConnected keep track of whether the user's wallet is connected or not
  const [walletConnected, setWalletConnected] = useState(false);
  // presaleStarted keeps track of whether the presale has started or not
  const [presaleStarted, setPresaleStarted] = useState(false);
  // presaleEnded keeps track of whether the presale ended
  const [presaleEnded, setPresaleEnded] = useState(false);
  // loading is set to true when we are waiting for a transaction to get mined
  const [loading, setLoading] = useState(false);
  // checks if the currently connected MetaMask wallet is the owner of the contract
  const [isOwner, setIsOwner] = useState(false);
  // tokenIdsMinted keeps track of the number of tokenIds that have been minted
  const [tokenIdsMinted, setTokenIdsMinted] = useState("0");
  // Create a reference to the Web3 Modal (used for connecting to Metamask) which persists as long as the page is open
  const web3ModalRef = useRef();

  /**
   * presaleMint: Mint an NFT during the presale
   */
  const presaleMint = async () => {
    try {
      // We need a Signer here since this is a 'write' transaction.
      const signer = await getProviderOrSigner(true);
      // Create a new instance of the Contract with a Signer, which allows
      // update methods
      const whitelistContract = new Contract(
        NFT_CONTRACT_ADDRESS,
        abi,
        signer
      );
      // call the presaleMint from the contract, only whitelisted addresses would be able to mint
      const tx = await whitelistContract.presaleMint({
        // value signifies the cost of one crypto dev which is "0.01" eth.
        // We are parsing `0.01` string to ether using the utils library from ethers.js
        value: utils.parseEther("0.01"),
      });
      setLoading(true);
      // wait for the transaction to get mined
      await tx.wait();
      setLoading(false);
      window.alert("You successfully minted a Crypto Dev!");
    } catch (err) {
      console.error(err);
    }
  };

  /**
   * publicMint: Mint an NFT after the presale
   */
  const publicMint = async () => {
    try {
      // We need a Signer here since this is a 'write' transaction.
      const signer = await getProviderOrSigner(true);
      // Create a new instance of the Contract with a Signer, which allows
      // update methods
      const whitelistContract = new Contract(
        NFT_CONTRACT_ADDRESS,
        abi,
        signer
      );
      // call the mint from the contract to mint the Crypto Dev
      const tx = await whitelistContract.mint({
        // value signifies the cost of one crypto dev which is "0.01" eth.
        // We are parsing `0.01` string to ether using the utils library from ethers.js
        value: utils.parseEther("0.01"),
      });
      setLoading(true);
      // wait for the transaction to get mined
      await tx.wait();
      setLoading(false);
      window.alert("You successfully minted a Crypto Dev!");
    } catch (err) {
      console.error(err);
    }
  };

  /*
      connectWallet: Connects the MetaMask wallet
    */
  const connectWallet = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // When used for the first time, it prompts the user to connect their wallet
      await getProviderOrSigner();
      setWalletConnected(true);
    } catch (err) {
      console.error(err);
    }
  };

  /**
   * startPresale: starts the presale for the NFT Collection
   */
  const startPresale = async () => {
    try {
      // We need a Signer here since this is a 'write' transaction.
      const signer = await getProviderOrSigner(true);
      // Create a new instance of the Contract with a Signer, which allows
      // update methods
      const whitelistContract = new Contract(
        NFT_CONTRACT_ADDRESS,
        abi,
        signer
      );
      // call the startPresale from the contract
      const tx = await whitelistContract.startPresale();
      setLoading(true);
      // wait for the transaction to get mined
      await tx.wait();
      setLoading(false);
      // set the presale started to true
      await checkIfPresaleStarted();
    } catch (err) {
      console.error(err);
    }
  };

  /**
   * checkIfPresaleStarted: checks if the presale has started by quering the `presaleStarted`
   * variable in the contract
   */
  const checkIfPresaleStarted = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // No need for the Signer here, as we are only reading state from the blockchain
      const provider = await getProviderOrSigner();
      // We connect to the Contract using a Provider, so we will only
      // have read-only access to the Contract
      const nftContract = new Contract(NFT_CONTRACT_ADDRESS, abi, provider);
      // call the presaleStarted from the contract
      const _presaleStarted = await nftContract.presaleStarted();
      if (!_presaleStarted) {
        await getOwner();
      }
      setPresaleStarted(_presaleStarted);
      return _presaleStarted;
    } catch (err) {
      console.error(err);
      return false;
    }
  };

  /**
   * checkIfPresaleEnded: checks if the presale has ended by quering the `presaleEnded`
   * variable in the contract
   */
  const checkIfPresaleEnded = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // No need for the Signer here, as we are only reading state from the blockchain
      const provider = await getProviderOrSigner();
      // We connect to the Contract using a Provider, so we will only
      // have read-only access to the Contract
      const nftContract = new Contract(NFT_CONTRACT_ADDRESS, abi, provider);
      // call the presaleEnded from the contract
      const _presaleEnded = await nftContract.presaleEnded();
      // _presaleEnded is a Big Number, so we are using the lt(less than function) instead of `<`
      // Date.now()/1000 returns the current time in seconds
      // We compare if the _presaleEnded timestamp is less than the current time
      // which means presale has ended
      const hasEnded = _presaleEnded.lt(Math.floor(Date.now() / 1000));
      if (hasEnded) {
        setPresaleEnded(true);
      } else {
        setPresaleEnded(false);
      }
      return hasEnded;
    } catch (err) {
      console.error(err);
      return false;
    }
  };

  /**
   * getOwner: calls the contract to retrieve the owner
   */
  const getOwner = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // No need for the Signer here, as we are only reading state from the blockchain
      const provider = await getProviderOrSigner();
      // We connect to the Contract using a Provider, so we will only
      // have read-only access to the Contract
      const nftContract = new Contract(NFT_CONTRACT_ADDRESS, abi, provider);
      // call the owner function from the contract
      const _owner = await nftContract.owner();
      // We will get the signer now to extract the address of the currently connected MetaMask account
      const signer = await getProviderOrSigner(true);
      // Get the address associated to the signer which is connected to  MetaMask
      const address = await signer.getAddress();
      if (address.toLowerCase() === _owner.toLowerCase()) {
        setIsOwner(true);
      }
    } catch (err) {
      console.error(err.message);
    }
  };

  /**
   * getTokenIdsMinted: gets the number of tokenIds that have been minted
   */
  const getTokenIdsMinted = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // No need for the Signer here, as we are only reading state from the blockchain
      const provider = await getProviderOrSigner();
      // We connect to the Contract using a Provider, so we will only
      // have read-only access to the Contract
      const nftContract = new Contract(NFT_CONTRACT_ADDRESS, abi, provider);
      // call the tokenIds from the contract
      const _tokenIds = await nftContract.tokenIds();
      //_tokenIds is a `Big Number`. We need to convert the Big Number to a string
      setTokenIdsMinted(_tokenIds.toString());
    } catch (err) {
      console.error(err);
    }
  };

  /**
   * Returns a Provider or Signer object representing the Ethereum RPC with or without the
   * signing capabilities of metamask attached
   *
   * A `Provider` is needed to interact with the blockchain - reading transactions, reading balances, reading state, etc.
   *
   * A `Signer` is a special type of Provider used in case a `write` transaction needs to be made to the blockchain, which involves the connected account
   * needing to make a digital signature to authorize the transaction being sent. Metamask exposes a Signer API to allow your website to
   * request signatures from the user using Signer functions.
   *
   * @param {*} needSigner - True if you need the signer, default false otherwise
   */
  const getProviderOrSigner = async (needSigner = false) => {
    // Connect to Metamask
    // Since we store `web3Modal` as a reference, we need to access the `current` value to get access to the underlying object
    const provider = await web3ModalRef.current.connect();
    const web3Provider = new providers.Web3Provider(provider);

    // If user is not connected to the Rinkeby network, let them know and throw an error
    const { chainId } = await web3Provider.getNetwork();
    if (chainId !== 4) {
      window.alert("Change the network to Rinkeby");
      throw new Error("Change network to Rinkeby");
    }

    if (needSigner) {
      const signer = web3Provider.getSigner();
      return signer;
    }
    return web3Provider;
  };

  // useEffects are used to react to changes in state of the website
  // The array at the end of function call represents what state changes will trigger this effect
  // In this case, whenever the value of `walletConnected` changes - this effect will be called
  useEffect(() => {
    // if wallet is not connected, create a new instance of Web3Modal and connect the MetaMask wallet
    if (!walletConnected) {
      // Assign the Web3Modal class to the reference object by setting it's `current` value
      // The `current` value is persisted throughout as long as this page is open
      web3ModalRef.current = new Web3Modal({
        network: "rinkeby",
        providerOptions: {},
        disableInjectedProvider: false,
      });
      connectWallet();

      // Check if presale has started and ended
      const _presaleStarted = checkIfPresaleStarted();
      if (_presaleStarted) {
        checkIfPresaleEnded();
      }

      getTokenIdsMinted();

      // Set an interval which gets called every 5 seconds to check presale has ended
      const presaleEndedInterval = setInterval(async function () {
        const _presaleStarted = await checkIfPresaleStarted();
        if (_presaleStarted) {
          const _presaleEnded = await checkIfPresaleEnded();
          if (_presaleEnded) {
            clearInterval(presaleEndedInterval);
          }
        }
      }, 5 * 1000);

      // set an interval to get the number of token Ids minted every 5 seconds
      setInterval(async function () {
        await getTokenIdsMinted();
      }, 5 * 1000);
    }
  }, [walletConnected]);

  /*
      renderButton: Returns a button based on the state of the dapp
    */
  const renderButton = () => {
    // If wallet is not connected, return a button which allows them to connect their wllet
    if (!walletConnected) {
      return (
        <button onClick={connectWallet} className={styles.button}>
          Connect your wallet
        </button>
      );
    }

    // If we are currently waiting for something, return a loading button
    if (loading) {
      return <button className={styles.button}>Loading...</button>;
    }

    // If connected user is the owner, and presale hasnt started yet, allow them to start the presale
    if (isOwner && !presaleStarted) {
      return (
        <button className={styles.button} onClick={startPresale}>
          Start Presale!
        </button>
      );
    }

    // If connected user is not the owner but presale hasn't started yet, tell them that
    if (!presaleStarted) {
      return (
        <div>
          <div className={styles.description}>Presale hasnt started!</div>
        </div>
      );
    }

    // If presale started, but hasn't ended yet, allow for minting during the presale period
    if (presaleStarted && !presaleEnded) {
      return (
        <div>
          <div className={styles.description}>
            Presale has started!!! If your address is whitelisted, Mint a
            Crypto Dev 🥳
          </div>
          <button className={styles.button} onClick={presaleMint}>
            Presale Mint 🚀
          </button>
        </div>
      );
    }

    // If presale started and has ended, its time for public minting
    if (presaleStarted && presaleEnded) {
      return (
        <button className={styles.button} onClick={publicMint}>
          Public Mint 🚀
        </button>
      );
    }
  };

  return (
    <div>
      <Head>
        <title>Crypto Devs</title>
        <meta name="description" content="Whitelist-Dapp" />
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <div className={styles.main}>
        <div>
          <h1 className={styles.title}>Welcome to Crypto Devs!</h1>
          <div className={styles.description}>
            Its an NFT collection for developers in Crypto.
          </div>
          <div className={styles.description}>
            {tokenIdsMinted}/20 have been minted
          </div>
          {renderButton()}
        </div>
        <div>
          <img className={styles.image} src="./cryptodevs/0.svg" />
        </div>
      </div>

      <footer className={styles.footer}>
        Made with &#10084; by Crypto Devs
      </footer>
    </div>
  );
}
```

- 现在在my-app文件夹下创建一个新的文件夹，并将其命名为`constants`。

- 在`constants `文件夹中创建一个文件，`index.js`并粘贴以下代码。

  - 将 `"addres of your NFT contract"` 改为你部署并保存在记事本中的CryptoDevs合同的地址。

  - 用你的CryptoDevs合同的ABI替换`---your abi---`。要获得你的合同的abi，请到你的`hardhat-tutorial/artifacts/contracts/CryptoDevs.sol`文件夹，从你的CryptoDevs.json文件中获得 `"abi "`键下的数组标记。

```
export const abi =---your abi---
export const NFT_CONTRACT_ADDRESS = "address of your NFT contract"
```

- 现在，在你的终端，也就是指向my-app文件夹，执行

```
npm run dev
```

你的Crypto Devs NFT dapp现在应该可以正常工作而不会出现错误🚀。

## 推送到github

在继续之前，请确保你已经将所有的代码[推送到github ](https://medium.com/hackernoon/a-gentle-introduction-to-git-and-github-the-eli5-way-43f0aa64f2e4):)

## 部署你的dApp

现在我们将部署你的DApp，这样每个人都可以看到你的网站，你可以与所有LearnWeb3 DAO的朋友分享它。

- 进入https://vercel.com/，用你的GitHub登录
- 然后点击新项目按钮，然后选择你的NFT-Collection repo
- 在配置你的新项目时，Vercel将允许你定制你的根目录
- 点击根目录旁边的编辑，并将其设置为我的应用程序
- 选择框架为Next.js
- 单击 "部署"。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220710154526.png)

- 现在，你可以通过进入你的仪表板，选择你的项目，并从那里复制域名来查看你部署的网站!把域名保存在记事本上，你以后会需要它。


## 在Opensea上查看您的收藏

现在让您的收藏在 Opensea 上可用

为了使该集合在 Opensea 上可用，我们需要创建一个元数据端点。该端点将返回给定 NFT 的元数据`tokenId`。

- 打开你的my-app文件夹和underpages/api文件夹，创建一个名为[tokenId].js的新文件（确保该名称也有括号）。添加括号有助于在[next.js](https://nextjs.org/docs/routing/dynamic-routes)中创建动态路由
- 在`[tokenId].js`文件中添加以下几行。[在此阅读关于在Next js中添加API路由的信息](https://nextjs.org/docs/api-routes/introduction)

```
export default function handler(req, res) {
  // get the tokenId from the query params
  const tokenId = req.query.tokenId;
  // As all the images are uploaded on github, we can extract the images from github directly.
  const image_url =
    "https://raw.githubusercontent.com/LearnWeb3DAO/NFT-Collection/main/my-app/public/cryptodevs/";
  // The api is sending back metadata for a Crypto Dev
  // To make our collection compatible with Opensea, we need to follow some Metadata standards
  // when sending back the response from the api
  // More info can be found here: https://docs.opensea.io/docs/metadata-standards
  res.status(200).json({
    name: "Crypto Dev #" + tokenId,
    description: "Crypto Dev is a collection of developers in crypto",
    image: image_url + tokenId + ".svg",
  });
}
```
- 现在你有一个api路由，Opensea可以调用它来检索NFT的元数据。
- 让我们部署一个新的`Crypto Devs`合同，把这个新的api路由作为你的`METADATA_URL`。
- 打开`hardhat-tutorial/constants`文件夹，在`index.js`文件中，用你保存在记事本中的域名替换 `"https://nft-collection-sneh1999.vercel.app/api/"`，并在其末尾添加`"/api/"。`
- 保存文件并打开一个新的终端，指向`hardhat-tutorial`文件夹并部署一个新的合同

```
  npx hardhat run scripts/deploy.js --network rinkeby
```

- 将新的NFT合同地址保存在一个记事本上。
- 打开 `"my-app/constants "`文件夹，在`index.js`文件中用新的地址替换旧的NFT合同地址。
- 将所有代码推送到github，并等待vercel部署新代码。
- 在vercel部署了你的代码后，打开你的网站，并铸成一个NFT
- 在您的交易成功后，在您的浏览器中打开这个链接，将您的NFT合同地址替换为您的NFT合同地址（https://testnets.opensea.io/assets/your-nft-contract-address/1）。
- 您的NFT现在可以在Opensea上使用  🚀 🥳
- 与大家分享你的Opensea链接

> 原文：[https://www.learnweb3.io/tracks/sophomore/nft-collection](https://www.learnweb3.io/tracks/sophomore/nft-collection)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
