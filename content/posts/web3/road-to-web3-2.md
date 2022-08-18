---
title: Web3之路——如何开发“Buy Me a Coffee” DeFi dapp
description: null
author: 李留白
weight: 0
date: 2022-08-18T13:47:30.470Z
lastmod: 2022-08-18T13:52:22.327Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814204307.png
---

区块链技术是惊人的，因为它使我们有能力使用代码和软件对金钱进行编程。只需几行代码，就可以建立各种应用和协议，为世界各地的人们创造新的机会。

**Buy Me A Coffee** 是一个流行的网站，创作者、教育家、艺人和各种人都用它来创建一个登陆页面，任何人都可以发送一定数量的钱作为对他们服务的感谢。然而，为了使用它，你必须有一个银行账户和一张信用卡。不是每个人都有这个条件!

建立在区块链之上的去中心化应用程序的一个好处是，世界各地的任何人都可以只用一个以太坊钱包来访问该应用程序，任何人都可以在1分钟内免费设置。让我们看看如何利用这一点来发挥我们的优势

在本教程中，你将学习如何使用Alchemy、Hardhat、Ethers.js和Ethereum Goerli开发和部署一个去中心化的 "Buy Me a Coffee "智能合约，让游客给你发送（假的）ETH作为小费并留下好的信息。

在本教程结束时，你将学会如何做以下事情：

- 使用Hardhat开发环境来构建、测试和部署我们的智能合约。
- 使用Alchemy RPC端点将你的MetaMask钱包连接到Goerli测试网络。
- 从goerlifaucet.com获得免费的Goerli ETH。
- 使用Ethers.js与你部署的智能合约互动。
- 用Replit为你的去中心化应用建立一个前端网站。

## 先决条件

为了准备本教程的其余部分，你需要有：

- `npm` (`npx`) version 8.5.5
- `node` version 16.13.1
- Alchemy 账户 ([sign up here for free](https://alchemy.com)!)

以下内容不是必须的，但非常有用：

- 对[命令行](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Understanding_client-side_tools/Command_line)有一定的熟悉程度
- 对[JavaScript](https://www.codecademy.com/learn/introduction-to-javascript)有一定的熟悉程度

现在，让我们开始构建我们的智能合约吧!

## 开发BuyMeACoffee.sol智能合约

Github参考： https://github.com/alchemyplatform/RTW3-Week2-BuyMeACoffee-Contracts

如果你以前使用过OpenZeppelin Wizard和Remix等工具，那么你就已经准备好使用Hardhat了。

Hardhat是类似的，因为它是一个开发环境和编码工具，但它的可定制性更强一些，并从你自己的计算机的命令行界面而不是浏览器应用程序中运行。

我们将使用Hardhat来。

- 生成项目模板
- 测试我们的智能合约代码
- 部署到Goerli测试网络

开始吧!

打开你的终端，创建一个新的目录。

```
mkdir BuyMeACoffee-contracts
cd BuyMeACoffee-contracts
```

在这个目录中，我们要启动一个新的npm项目（默认设置就可以了）。

```
npm init -y
```

这应该为你创建一个package.json文件，看起来像这样。

```
thatguyintech@albert BuyMeACoffee-contracts % npm init -y
Wrote to /Users/thatguyintech/Documents/co/videos/week2/BuyMeACoffee-contracts/package.json:

{
  "name": "buymeacoffee-contracts",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

现在我们创建一个示例项目。

```
npx hardhat
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220818212857.png)

按回车键接受所有默认设置并安装样本项目的依赖项（hardhat、@nomiclabs/hardhat-waffle、ethereum-waffle、chai、@nomiclabs/hardhat-ethers、ethers）。

如果依赖关系的安装出了问题，可以尝试通过运行这个命令重新安装。

```
npm install --save-dev hardhat@^2.9.3 @nomiclabs/hardhat-waffle@^2.0.0 ethereum-waffle@^3.0.0 chai@^4.2.0 @nomiclabs/hardhat-ethers@^2.0.0 ethers@^5.0.0
```

你的项目目录现在应该是这样的（我用树状图来显示）。

```
thatguyintech@albert BuyMeACoffee-contracts % tree -C -L 1
.
├── README.md
├── contracts
├── hardhat.config.js
├── node_modules
├── package-lock.json
├── package.json
├── scripts
└── test
```

重要的文件夹和文件是:

- contracts - 你的智能合约所在的文件夹
  - 在这个项目中，我们将只创建一个，以组织我们的BuyMeACoffee逻辑。
- scripts - 文件夹中存放着你的hardhatjavscript脚本
  - 我们将编写部署逻辑
  - 购买咖啡脚本的例子
  - 和一个提取脚本来兑现我们的提示
- hardhat.config.js - 配置文件，包括solidity版本和部署的设置。

现在，使用任何代码编辑器来打开项目文件夹! 我喜欢使用VSCode。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220818212950.png)

你会注意到，有一些文件已经通过Hardhat样本项目工具自动生成了。我们将替换所有这些文件，从Greeter.sol合约开始。

1. 将合约文件重命名为BuyMeACoffee.sol
2. 用以下内容替换合约代码。

```solidity
//SPDX-License-Identifier: Unlicense

// contracts/BuyMeACoffee.sol
pragma solidity ^0.8.0;

// Switch this to your own contract address once deployed, for bookkeeping!
// Example Contract Address on Goerli: 0xDBa03676a2fBb6711CB652beF5B7416A53c1421D

contract BuyMeACoffee {
    // Event to emit when a Memo is created.
    event NewMemo(
        address indexed from,
        uint256 timestamp,
        string name,
        string message
    );
    
    // Memo struct.
    struct Memo {
        address from;
        uint256 timestamp;
        string name;
        string message;
    }
    
    // Address of contract deployer. Marked payable so that
    // we can withdraw to this address later.
    address payable owner;

    // List of all memos received from coffee purchases.
    Memo[] memos;

    constructor() {
        // Store the address of the deployer as a payable address.
        // When we withdraw funds, we'll withdraw here.
        owner = payable(msg.sender);
    }

    /**
     * @dev fetches all stored memos
     */
    function getMemos() public view returns (Memo[] memory) {
        return memos;
    }

    /**
     * @dev buy a coffee for owner (sends an ETH tip and leaves a memo)
     * @param _name name of the coffee purchaser
     * @param _message a nice message from the purchaser
     */
    function buyCoffee(string memory _name, string memory _message) public payable {
        // Must accept more than 0 ETH for a coffee.
        require(msg.value > 0, "can't buy coffee for free!");

        // Add the memo to storage!
        memos.push(Memo(
            msg.sender,
            block.timestamp,
            _name,
            _message
        ));

        // Emit a NewMemo event with details about the memo.
        emit NewMemo(
            msg.sender,
            block.timestamp,
            _name,
            _message
        );
    }

    /**
     * @dev send the entire balance stored in this contract to the owner
     */
    function withdrawTips() public {
        require(owner.send(address(this).balance));
    }
}
```

花点时间阅读一下合约的评论，看看你是否能收集到发生了什么事！我在这里列出了重点。

我将在这里列出重点。

- 当我们部署合约时，构造函数将负责部署的钱包的地址保存在所有者变量内，作为一个应付地址。这对以后我们想提取合约收集的任何小费很有用。
- buyCoffee函数是合约上最重要的函数。它接受两个字符串，一个是_name，一个是_message，而且由于paying修改器的存在，它还接受了ether。它使用_name和_message输入来创建一个Memo结构，存储在区块链上。
  - 当访问者调用buyCoffee函数时，由于require(msg.value > 0)语句，他们必须提交一些ether 。然后，该以太坊被保留在合约余额上，直到它被提取。
- 备忘录数组持有所有从咖啡购买中产生的备忘录结构。
- 每次购买咖啡时都会发出NewMemo日志事件。这使我们能够从我们的前端网站监听新的咖啡购买。
- withdrawTips是一个任何人都可以调用的函数，但只向合约的原始部署者发送钱。
  - address(this).balance获取存储在合约上的ether 。
  - owner.send(...)是创建一个以太坊发送交易的语法。
  - require(...)语句包装了所有东西，以确保如果有任何问题，交易会被恢复，没有任何损失。
  - 这就是我们如何得到 require(owner.send(address(this).balance))

有了这个智能合约代码，我们现在可以写一个脚本来测试我们的逻辑了

## 创建一个buy-coffee.js脚本来测试你的合约

在scripts文件夹下，应该有一个已经填充好的样本脚本 sample-script.js。让我们将该文件重命名为buy-coffee.js，并粘贴以下代码。

```js
const hre = require("hardhat");

// Returns the Ether balance of a given address.
async function getBalance(address) {
  const balanceBigInt = await hre.waffle.provider.getBalance(address);
  return hre.ethers.utils.formatEther(balanceBigInt);
}

// Logs the Ether balances for a list of addresses.
async function printBalances(addresses) {
  let idx = 0;
  for (const address of addresses) {
    console.log(`Address ${idx} balance: `, await getBalance(address));
    idx ++;
  }
}

// Logs the memos stored on-chain from coffee purchases.
async function printMemos(memos) {
  for (const memo of memos) {
    const timestamp = memo.timestamp;
    const tipper = memo.name;
    const tipperAddress = memo.from;
    const message = memo.message;
    console.log(`At ${timestamp}, ${tipper} (${tipperAddress}) said: "${message}"`);
  }
}

async function main() {
  // Get the example accounts we'll be working with.
  const [owner, tipper, tipper2, tipper3] = await hre.ethers.getSigners();

  // We get the contract to deploy.
  const BuyMeACoffee = await hre.ethers.getContractFactory("BuyMeACoffee");
  const buyMeACoffee = await BuyMeACoffee.deploy();

  // Deploy the contract.
  await buyMeACoffee.deployed();
  console.log("BuyMeACoffee deployed to:", buyMeACoffee.address);

  // Check balances before the coffee purchase.
  const addresses = [owner.address, tipper.address, buyMeACoffee.address];
  console.log("== start ==");
  await printBalances(addresses);

  // Buy the owner a few coffees.
  const tip = {value: hre.ethers.utils.parseEther("1")};
  await buyMeACoffee.connect(tipper).buyCoffee("Carolina", "You're the best!", tip);
  await buyMeACoffee.connect(tipper2).buyCoffee("Vitto", "Amazing teacher", tip);
  await buyMeACoffee.connect(tipper3).buyCoffee("Kay", "I love my Proof of Knowledge", tip);

  // Check balances after the coffee purchase.
  console.log("== bought coffee ==");
  await printBalances(addresses);

  // Withdraw.
  await buyMeACoffee.connect(owner).withdrawTips();

  // Check balances after withdrawal.
  console.log("== withdrawTips ==");
  await printBalances(addresses);

  // Check out the memos.
  console.log("== memos ==");
  const memos = await buyMeACoffee.getMemos();
  printMemos(memos);
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

在这一点上，你的项目目录应该是这样的:

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220818213150.png)

*将scripts/sample-script.js重命名为scripts/buy-coffee.js，并粘贴上示例代码*

请随意花点时间阅读一下脚本代码。为方便起见，在顶部定义了一些实用函数，以做一些诸如获取钱包余额并打印出来的事情。

脚本的主要逻辑在main()函数中。注释的代码显示了该脚本的流程。

1. 获取我们将要使用的示例账户。
2. 我们得到要部署的合约。
3. 部署合约。
4. 在购买咖啡前检查余额。
5. 给业主买几杯咖啡。
6. 购买咖啡后检查余额。
7. 提款。
8. 提款后检查余额。
9. 查看备忘录。

这个脚本测试了我们在智能合约中实现的所有功能! 这真是太棒了。

你可能还注意到，我们正在进行有趣的调用，比如。

- `hre.waffle.provider.getBalance`
- `hre.ethers.getContractFactory`
- `hre.ethers.utils.parseEther`
- 等。

这些代码行是我们利用Hardhat（hre）开发环境以及Ethers和Waffle SDK插件的优势，以访问允许我们读取区块链钱包账户余额、部署合约和格式化以太币加密货币值的功能。

在本教程中，我们不会对这些代码进行太深入的研究，但你可以通过查阅Hardhat和Ethers.js文档来了解它们。

说得够多了。现在，为了好玩，让我们来运行脚本。

```
npx hardhat run scripts/buy-coffee.js
```

你应该在你的终端看到这样的输出。

```
thatguyintech@albert BuyMeACoffee-contracts % npx hardhat run scripts/buy-coffee.js
Compiled 1 Solidity file successfully
BuyMeACoffee deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3
== start ==
Address 0 balance:  9999.99877086625
Address 1 balance:  10000.0
Address 2 balance:  0.0
== bought coffee ==
Address 0 balance:  9999.99877086625
Address 1 balance:  9998.999752902808629985
Address 2 balance:  3.0
== withdrawTips ==
Address 0 balance:  10002.998724967892122376
Address 1 balance:  9998.999752902808629985
Address 2 balance:  0.0
== memos ==
At 1652033688, Carolina (0x70997970C51812dc3A010C7d01b50e0d17dc79C8) said: "You're the best!"
At 1652033689, Vitto (0x3C44CdDdB6a900fa2b585dd299e03d12FA4293BC) said: "Amazing teacher"
At 1652033690, Kay (0x90F79bf6EB2c4f870365E785982E1f101E93b906) said: "I love my Proof of Knowledge"
```

在脚本的开始（就在合约部署之后），注意0号地址有9999.99877086625 ETH。这是因为它作为预先填充的hardhat地址之一，开始时有10k ETH，但它必须花费很小的金额来部署到本地区块链上。

在第二步==买了咖啡==，地址1购买了一杯咖啡。另外两个没有显示的钱包也购买了咖啡。总共购买了3杯咖啡，总小费金额为3.0ETH。你可以看到地址2（代表合约地址），正持有3.0ETH。

在== withdrawTips==中调用withdrawTips()函数后，合约回落到0ETH，原来的部署者，也就是地址0，现在已经赚了一些钱，坐拥10002.998724967892122376 ETH。

我们玩得开心吗！？你能想象你即将赚到的小费吗？我可以。

现在，让我们实现一个孤立的部署脚本，以保持真正的部署简单，也准备好部署到Goerli测试网络上!

## 使用Alchemy和MetaMask将你的BuyMeACoffe.sol智能合约部署到Ethereum Goerli测试网中

让我们创建一个新的文件scripts/deploy.js，它将是超级简单的，只是为了将我们的合约部署到我们以后选择的任何网络（如果你没有注意到，我们以后会选择Goerli）。

deploy.js文件看起来应该是这样的。

```
// scripts/deploy.js

const hre = require("hardhat");

async function main() {
  // We get the contract to deploy.
  const BuyMeACoffee = await hre.ethers.getContractFactory("BuyMeACoffee");
  const buyMeACoffee = await BuyMeACoffee.deploy();

  await buyMeACoffee.deployed();

  console.log("BuyMeACoffee deployed to:", buyMeACoffee.address);
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

回顾一下项目结构，我们现在有一个智能合约和两个hardhat脚本。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220818213330.png)

现在有了这个deploy.js脚本的编码和保存，如果你运行以下命令。

```
npx hardhat run scripts/deploy.js
```

你会看到打印出来的一个单行。

```
BuyMeACoffee deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3
```

有趣的是，如果你反复运行它，你每次都会看到相同的部署地址。

```
thatguyintech@albert BuyMeACoffee-contracts % npx hardhat run scripts/deploy.js
BuyMeACoffee deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3
thatguyintech@albert BuyMeACoffee-contracts % npx hardhat run scripts/deploy.js
BuyMeACoffee deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3
thatguyintech@albert BuyMeACoffee-contracts % npx hardhat run scripts/deploy.js
BuyMeACoffee deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3
```

这是为什么呢？这是因为当你运行脚本时，Hardhat工具使用的默认网络是一个本地开发网络，就在你的计算机上。它是快速和确定的，对于一些快速的理智检查是很好的。

然而，为了实际部署到一个在互联网上运行、节点遍布世界各地的测试网络，我们需要改变我们的Hardhat配置文件，给自己一个选择。

这就是hardhat.config.json文件的作用。

在我们深入研究之前，有一点需要注意。

> ### 配置是很难的! 保持你的秘钥的安全!
>
> 有各种各样的小细节可能会出错，而且事情一直在变化。最危险的是秘钥值，例如，你的MetaMask私钥和你的Alchemy URL。
>
> 如果有什么东西不适合你，请检查Ethereum StackExchange、Alchemy Discord，或者谷歌你的错误。
>
> 永远不要分享你的秘钥! 你的钥匙，你的钱币!

当你打开你的hardhat.config.js文件时，你会看到一些部署代码的样本。删除它并粘贴这个版本。

```js
// hardhat.config.js

require("@nomiclabs/hardhat-ethers");
require("@nomiclabs/hardhat-waffle");
require("dotenv").config()

// You need to export an object to set up your config
// Go to https://hardhat.org/config/ to learn more

const GOERLI_URL = process.env.GOERLI_URL;
const PRIVATE_KEY = process.env.PRIVATE_KEY;

/**
 * @type import('hardhat/config').HardhatUserConfig
 */
module.exports = {
  solidity: "0.8.4",
  networks: {
    goerli: {
      url: GOERLI_URL,
      accounts: [PRIVATE_KEY]
    }
  }
};
```

这里有几件事要做。

- 通过在配置文件的顶部导入hardhat-ethers、hardhat-waffle和dotenv，我们的整个Hardhat项目都可以访问这些依赖项。
- 我知道我们还没有介绍dotenv，那是一个重要的工具，我们稍后会讲到。
- process.env.GOERLI_URL和process.env.PRIVATE_KEY是我们访问环境变量的方式，可以在我们的配置文件中使用，同时不暴露秘钥值。
- 在modules.exports里面，我们使用的是solidity编译器0.8.4版本。不同的编译器版本支持不同的功能和语法集，所以将这个版本与我们的BuyMeACoffee.sol智能合约顶部的pragma声明相匹配很重要。
  - 如果你回到那个文件，你可以交叉检查pragma solidity ^0.8.0;的声明。在这种情况下，即使数字不完全匹配，也没关系，因为karat ^符号意味着任何大于或等于0.8.0的版本都可以工作。
- 同样在modules.exports中，我们定义了一个networks设置，其中包含goerli的一个测试网络配置。
  现在，在我们进行部署之前，我们需要确保我们安装了最后一个工具，即dotenv模块。顾名思义，dotenv帮助我们将.env文件与我们项目的其他部分连接起来。让我们把它设置好。

安装dotenv:

```
npm install dotenv
```

创建一个.env文件

```
touch .env
```

用我们需要的变量填充.env文件

```
GOERLI_URL=https://eth-goerli.alchemyapi.io/v2/<your api key>
GOERLI_API_KEY=<your api key>
PRIVATE_KEY=<your metamask api key>
```

你会注意到我没有泄露任何我自己的秘钥。是的。安全第一。不过你完全可以把这个文件放进去，只要你还有一个.gitignore，确保你不会意外地把文件推送到版本控制。确保.env被列在你的.gitignore中

```
node_modules
.env
coverage
coverage.json
typechain

#Hardhat files
cache
artifacts
```

另外，为了获得你所需要的环境变量，你可以使用以下资源。

- GOERLI_URL - 在Alchemy上注册一个账户，创建一个Ethereum -> Goerli应用程序，并使用HTTP URL
- GOERLI_API_KEY - 从你的同一个Alchemy Ethereum Goerli应用程序中，你可以得到URL的最后一部分，这将是你的API KEY
- PRIVATE_KEY - 按照MetaMask的指示，导出你的私钥。

现在，随着dotenv的安装和你的.env文件的填充，我们几乎已经准备好部署到Goerli测试网了!

我们需要做的最后一件事是，确保你有一些Goerli ETH。这是假的以太坊，允许你在Goerli测试网络上练习做事，这有点像建立以太坊应用程序的练习区。这样你就不用在以太坊主网上花真钱了。

去https://www.goerlifaucet.com，用你的alchemy账户登录，就可以得到一些免费的测试用ether。

现在我们可以部署了!

运行部署脚本，这次添加一个特殊的标志来使用Goerli网络。

```
npx hardhat run scripts/deploy.js --network goerli
```

如果你在这里遇到任何错误，例如错误HH8，那么我强烈建议你在谷歌和Stack Overflow或Ethereum Stackexchange上搜索解决方案。当你的hardhat.config.js、.env或你的dotenv模块中的某些内容没有正确设置时，遇到这些问题很常见。

如果一切顺利，你应该能够在几秒钟后看到你的合约地址被记录到控制台。

```
BuyMeACoffee deployed to: 0xDBa03676a2fBb6711CB652beF5B7416A53c1421D
```

🎉 🎉 🎉

你现在有一个合约部署到Goerli测试网。你可以在Goerli etherscan区块链探索器上查看它，在这里粘贴你的地址：https://goerli.etherscan.io/

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220818213719.png)

在我们进入教程的前端网站（dapp）部分之前，让我们再准备一个以后要用到的脚本，即withdraw.js脚本。

## 实现一个提款脚本

以后当我们发布网站时，我们需要一个方法来收集我们的朋友和粉丝给我们留下的所有很棒的提示。我们可以再写一个hardhat脚本来做这件事!

在scripts/withdraw.js创建一个文件

```js
// scripts/withdraw.js

const hre = require("hardhat");
const abi = require("../artifacts/contracts/BuyMeACoffee.sol/BuyMeACoffee.json");

async function getBalance(provider, address) {
  const balanceBigInt = await provider.getBalance(address);
  return hre.ethers.utils.formatEther(balanceBigInt);
}

async function main() {
  // Get the contract that has been deployed to Goerli.
  const contractAddress="0xDBa03676a2fBb6711CB652beF5B7416A53c1421D";
  const contractABI = abi.abi;

  // Get the node connection and wallet connection.
  const provider = new hre.ethers.providers.AlchemyProvider("goerli", process.env.GOERLI_API_KEY);

  // Ensure that signer is the SAME address as the original contract deployer,
  // or else this script will fail with an error.
  const signer = new hre.ethers.Wallet(process.env.PRIVATE_KEY, provider);

  // Instantiate connected contract.
  const buyMeACoffee = new hre.ethers.Contract(contractAddress, contractABI, signer);

  // Check starting balances.
  console.log("current balance of owner: ", await getBalance(provider, signer.address), "ETH");
  const contractBalance = await getBalance(provider, buyMeACoffee.address);
  console.log("current balance of contract: ", await getBalance(provider, buyMeACoffee.address), "ETH");

  // Withdraw funds if there are funds to withdraw.
  if (contractBalance !== "0.0") {
    console.log("withdrawing funds..")
    const withdrawTxn = await buyMeACoffee.withdrawTips();
    await withdrawTxn.wait();
  } else {
    console.log("no funds to withdraw!");
  }

  // Check ending balance.
  console.log("current balance of owner: ", await getBalance(provider, signer.address), "ETH");
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

你的项目结构应该是这样的。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220818213757.png)

*现在我们有1个合约和3个*Hardhat的脚本。*

这个脚本最重要的部分是我们调用withdrawTips()函数，从我们的合约余额中抽出钱来，并把它送到主人的钱包里。

```
// Withdraw funds if there are funds to withdraw.
  if (contractBalance !== "0.0") {
    console.log("withdrawing funds..")
    const withdrawTxn = await buyMeACoffee.withdrawTips();
    await withdrawTxn.wait();
  }
```

如果合约中没有资金，我们就避免尝试提款，以免不必要地花费汽油费。

当你运行这个脚本时，你会看到这样的输出。

```
thatguyintech@albert BuyMeACoffee-contracts % npx hardhat run scripts/withdraw.js
current balance of owner:  0.039608085986833815 ETH
current balance of contract:  0.001 ETH
withdrawing funds..
current balance of owner:  0.040562731986622163 ETH
```

注意，这次我们没有添加--网络goerli标志，这是因为我们的脚本直接在逻辑中硬编码了网络配置。

```
const provider = new hre.ethers.providers.AlchemyProvider(
    "goerli",
    process.env.GOERLI_API_KEY
);
```

很好，现在我们有办法拯救合约中的小费了

让我们进入这个项目的dapp部分，这样我们就可以和所有的朋友分享我们的小费页面了 :)

## 用Replit和Ethers.js构建前端的Buy Me A Coffee网站dapp

对于这个网站部分，为了保持简单和干净，我们将使用一个惊人的工具来快速启动演示项目，称为Replit IDE。

请在这里访问我的示例项目，并fork它以创建你自己的副本进行修改：https://replit.com/@thatguyintech/BuyMeACoffee-Solidity-DeFi-Tipping-app

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220818213958.png)

*点击右上方的 "Fork repl "来制作你自己的副本，并跟着做!*

你也可以在这里查看完整的网站代码：https://github.com/alchemyplatform/RTW3-Week2-BuyMeACoffee-Website

分叉复制后，你应该会被带到一个IDE页面，在那里你可以。

- 查看Next.js网站应用程序的代码
- 访问控制台、终端外壳和README.md文件的预览
- 查看你的dapp的热重载版本

它应该看起来像这样。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220818214016.png)

本教程的这一部分将是快速而有趣的 -- 我们将更新几个变量，以便它与我们在项目前几部分部署的智能合约相连接，并使它在网站上显示你自己的名字！这将是一个很好的教程。

让我们先把所有的东西都连接起来，让它工作起来，然后我会向你解释每一部分的情况。

下面是我们需要做的改动。

1. 更新pages/index.js中的contractAddress
2. 在pages/index.js中更新名字字符串为你自己的名字。
3. 确保合约ABI与你在utils/BuyMeACoffee.json中的合约相符。

### 在pages/index.js中更新contractAddress。

你可以看到，contractAddress变量已经被填入了一个地址。这是我部署的一个合约例子，欢迎你使用，但如果你这样做......所有发送到你的网站的提示都会转到我的地址:)

你可以通过粘贴我们之前部署BuyMeACoffee.sol智能合约时的地址来解决这个问题。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220818214045.png)

### 在pages/index.js中更新名字字符串为你自己的名字。

现在这个网站上到处都是我的名字。找到所有使用Albert的地方，用你的名字/匿名资料/ENS域名，或任何你想让人们称呼你的东西来替换它。

你可以用cmd+F或ctrl+F来寻找所有Albert的实例来替换。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220818214136.png)

### 确保合约ABI在utils/BuyMeACoffee.json中匹配。

这也是一个需要检查的关键事项，特别是当你以后对你的智能合约进行修改时（本教程之后）。

ABI是应用二进制接口，它只是告诉我们的前端代码在智能合约上可以调用什么样的功能的一种花哨方式。ABI是在编译智能合约时在一个json文件中生成的。你可以在智能合约文件夹中找到它，路径是artifacts/contracts/BuyMeACoffee.sol/BuyMeACoffee.json。

每当你改变你的智能合约代码并重新部署时，你的ABI也会改变。把它复制过来并粘贴到Replit文件中：utils/BuyMeACoffee.json

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220818214336.png)

现在，如果应用程序还没有运行，你可以到shell中使用npm run dev来启动一个本地服务器来测试你的改动。网站应该在几秒钟内加载。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220818214350.png)

Replit的厉害之处在于，一旦你建立了网站，你可以回到你的个人资料，找到Replit项目的链接，并将其发送给朋友，让他们访问你的小费页面。

现在让我们来参观一下网站和代码。你已经可以从上面的截图中看到，当你第一次访问dapp时，它会检查你是否安装了MetaMask，以及你的钱包是否连接到该网站。你第一次访问时，你将不会被连接，所以会出现一个按钮，要求你连接你的钱包。

点击连接你的钱包后，会弹出一个MetaMask窗口，询问你是否要通过签署信息来确认连接。这个信息签署不需要任何加油费或费用。

一旦签名完成，网站将承认你的连接，你将能够看到咖啡的形式，以及其他访客留下的任何先前的备忘录。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220818214404.png)

这就是整个项目。花点时间拍拍自己的背，反思一下你所走过的旅程。

来回顾一下。

- 我们使用Hardhat和Ethers.js来编码、测试和部署一个自定义的solidity智能合约。
- 我们使用Alchemy和MetaMask将智能合约部署到Goerli测试网络。
- 我们实现了一个提款脚本，使我们能够接受我们的劳动成果。
- 我们通过使用Ethers.js加载合约ABI，将一个用Next.js、React和Replit构建的前端网站连接到智能合约。

这是一个很大的问题。

## 挑战

好了，现在到了最精彩的部分。我将给你留下一些挑战，让你自己去尝试，看看你是否完全理解了你在这里学到的东西 

1. 允许你的智能合约更新提款地址。
2. 允许你的智能合约以0.003ETH购买LargeCoffee，并在前端网站上创建一个显示 "以0.003ETH购买大咖啡 "的按钮。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
