---
title: Web3教程：编写您的第一个“Hello, World”智能合约（1）
description: null
author: 李留白
weight: 0
date: 2022-10-27T10:26:52.043Z
lastmod: 2022-10-27T11:04:20.136Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210271827013.png
---

如果您是 web3 开发的新手，那么这是开始的地方！

智能合约构成了构建在区块链上的所有 dApp 的基础，它们通常使用自定义语言编写，例如 Solidity（用于 Ethereum）或 Cadence（用于 Flow）。

它们往往比传统程序简单得多，但风险要高得多 - 单个智能合约错误可能导致数百万美元在几分钟内耗尽！

您将在本课程中培养哪些技能？我们将引导您完成编写和部署您的第一个智能合约所需的工具，包括： 

- [**Metamask：** ](https://metamask.io/)用于与以太坊区块链交互的软件加密货币钱包，允许您支付 gas 费用来发布您的交易。
- [**Solidity：** ](https://docs.soliditylang.org/)一种面向对象的编程语言，用于在以太坊上实现智能合约。
- [**Hardhat：** ](https://hardhat.org/)一个以太坊开发环境，允许您编译 Solidity 合约、在开发网络上测试合约并查看 Solidity 堆栈跟踪。
- [**Etherscan**： ](https://etherscan.io/)一个在线区块链浏览器，允许您查看公共区块链上的交易、区块和钱包状态。
- [**Alchemy：** ](https://alchemy.com/)一个 web3 开发者平台，允许您通过免费节点与以太坊网络的其余部分连接。

如果你有兴趣，让我们开始吧！

## 目录

1、什么是智能合约
2、部署您的第一个智能合约
3、什么是气体
4、与您的智能合约交互
5、智能合约的结构
6、将您的合同提交给 Etherscan
7、智能合约安全挑战
8、将合约集成到前端
9、其他资源


## 1、什么是智能合约？

### 背景

虽然这个概述主要是关于以太坊的，但实际上所有的概念都适用于支持智能合约的区块链！

智能合约是区块链开发领域存在的主要原因。

以太坊在 2013 年的核心创新是允许开发人员编写称为智能合约的小代码块，这些代码可以部署到以太坊网络，独立于其创建者运行。在以太坊中，智能合约是用 Solidity 编写的，这是一种设计用于在[以太坊虚拟机上运行的高级编程语言。](https://cypherpunks-core.github.io/ethereumbook/13evm.html)

智能合约是定义一组规则或“合约”的程序，当用户在区块链上调用时，它会自动执行编码规则。特别是，一旦部署了智能合约，它的功能总是相同的——它不能被不法分子修改或控制。

因此，智能合约是运行金融应用程序的理想选择——它们可以作为独立参与者以有保证的、可重复的方式接收或发送加密货币。

### 真实世界的例子

好的，但是有哪些现实世界的工具可以被智能合约取代的例子呢？

- **金融工具**：通常，当您在线交易股票或货币时，您需要第三方在交易发生时将这两种资产托管在托管中 - 例如股票经纪人。智能合约可以充当第三方，允许在没有中间人的情况下进行交易。
- **财产所有权：** 与法律文件相比，智能合约几乎是即时执行的，可以公开访问，并且可以允许分散所有权。
- **构建额外的代币：** 令人难以置信的是，你实际上可以在以太坊上启动新的代币系统，例如 Tether 或 Chainlink。同样，您可以使用智能合约启动 NFT 项目，例如 Bored Apes 或 CryptoPunks。

还有很多很多。事实上，最终可以使用区块链重新设计哪些系统，我们可能仍然只是接触到了皮毛。

### 智能合约的特征

哪些特征使智能合约能够彻底改变或取代现有的技术模型？与传统编程语言不同，智能合约具有以下属性：

- **透明度**：智能合约发布到区块链，任何有权访问区块链的人都可以读写。
- **简单性**：由于智能合约部署到区块链上的成本很高，并且包含指示金融交易流程的敏感逻辑，因此它们往往比大多数代码库更小更简单。
- **不变性**：一旦部署了智能合约，它（通常）就不能被修改，并且无论何时调用都保证其功能相同！这使得智能合约可以作为可靠、受信任的第三方运行——因为没有个人控制智能合约，它可以充当金融中介、值得信赖的自动化做市商，或者通过保证公正性而发挥更多作用。

部署后，这些智能合约表现为完全透明但可以包含复杂逻辑的独立参与者。因此，在以太坊上拥有账户的不仅是人类用户，还有两种类型的账户：

- 由人类用户管理的**外部拥有的帐户(EOA)。**
- 由其底层智能合约代码管理的**合约账户。**

> #### EOA 和合约账户有什么区别？
>
> 令人印象深刻的是，这两种类型的帐户可以做基本相同的事情！这两种账户类型都可以：
>
> - 接收或发送可替代代币（以太币）到任何账户
> - 接收或发送不可替代的代币（加密猫）到任何账户
> - 触发另一个合约账户——允许一个智能合约运行其他智能合约。
> - 生成新的智能合约——让智能合约充当合约工厂！
>
> 但是，合约账户有一些限制：
>
> - 合约账户不能自行实例化操作——它们只能响应他们收到的交易（通常来自 EOA）。
> - 合约账户完全由其代码控制，而 EOA 则由其关联的私钥控制。

## 2、部署您的第一个智能合约

如果您是区块链开发的新手并且不知道从哪里开始，或者您只是想了解如何部署智能合约并与之交互，那么本指南适合您。我们将使用虚拟钱包( [Metamask](https://metamask.io/) )、[Solidity](https://docs.soliditylang.org/en/v0.8.0/)、[Hardhat](https://hardhat.org/)和[Alchemy](https://dashboard.alchemyapi.io/signup?referral=affiliate:df70d561-e6cf-4922-b101-e2bb27c00a15)在 Goerli 测试网络上创建和部署一个简单的智能合约（如果你还不明白这意味着什么，请不要担心，我们会解释一下！）。

### 创建和部署您的智能合约

#### 第 1 步：连接到以太坊网络

有很多方法可以向以太坊链发出请求。为简单起见，我们将在 Alchemy 上使用免费帐户，这是一个区块链开发平台和 API，允许我们与以太坊链进行通信，而无需运行我们自己的节点。该平台还具有用于监视和分析的开发人员工具，我们将在本教程中利用这些工具来了解我们的智能合约部署中的幕后情况。

[如果您还没有 Alchemy 帐户，请在此处免费注册。](https://alchemy.com/)

#### 第 2 步：创建您的应用（和 API 密钥）

创建 Alchemy 帐户后，您可以通过创建应用程序来生成 API 密钥。这将允许我们向 Goerli 测试网络发出请求。如果您不熟悉测试网，请查看[本指南](https://docs.alchemyapi.io/guides/choosing-a-network)。

将鼠标悬停在导航栏中的“应用程序”上并单击“创建应用程序”，导航到 Alchemy 仪表板中的“创建应用程序”页面。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210271842540.png)

将您的应用命名为“Hello World”，提供简短描述，为环境选择“Staging”（用于您的应用记账），然后为您的网络选择“Goerli”。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210271842227.png)

仔细检查您是否选择了 Goerli 测试网！

点击“创建应用程序”，就是这样！您的应用程序应显示在下表中。

#### 第3步：创建以太坊账户（地址）

我们需要一个以太坊账户来发送和接收交易。在本教程中，我们将使用 Metamask，这是浏览器中的一个虚拟钱包，用于管理您的以太坊账户地址。如果你想了解更多关于以太坊交易如何运作的信息，请查看以太坊基金会的[这个页面](https://ethereum.org/en/developers/docs/transactions/)。

[您可以在此处](https://metamask.io/download.html)免费下载和创建 Metamask 帐户。当您正在创建一个帐户时，或者如果您已经有一个帐户，请确保切换到右上角的“Goerli 测试网络”（这样我们就不会处理真钱）。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210271842896.png)

#### 第 4 步：从水龙头中添加以太

为了将我们的智能合约部署到测试网络，我们需要一些虚假的 Eth。要获取 Eth，您可以前往[Goerli 水龙头](https://goerlifaucet.com/)并输入您的 Goerli 帐户地址，然后单击“Send Me Eth”。由于网络流量，可能需要一些时间才能收到您的虚假 Eth。（在撰写本文时，大约需要 30 分钟。）不久之后，您应该会在您的 Metamask 帐户中看到 Eth！

#### 第 5 步：检查您的余额

为了仔细检查我们的余额，让我们使用[Alchemy 的 composer tool发出一个](https://composer.alchemyapi.io/?composer_state={"network"%3A0%2C"methodName"%3A"eth_getBalance"%2C"paramValues"%3A[""%2C"latest"]})[eth_getBalance](https://docs.alchemyapi.io/alchemy/documentation/alchemy-api-reference/json-rpc#eth_getbalance)请求。这将返回我们钱包中的 Eth 数量。查看[此视频](https://youtu.be/r6sjRxBZJuU)以获取有关如何使用作曲家工具的说明！

输入您的 Metamask 帐户地址并单击“发送请求”后，您应该会看到如下所示的响应：

```js
{"jsonrpc": "2.0", "id": 0, "result": "0x2B5E3AF16B1880000"}
```

> 注意：这个结果是 wei 而不是 eth。魏被用作以太的最小面额。wei到eth的换算为：1 eth = 10^18 wei。因此，如果我们将 0x2B5E3AF16B1880000 转换为十进制，我们会得到 5*10^18，它等于 5 eth。呸！我们的假钱就在那里🤑。

#### 第6步：初始化我们的项目

```js
mkdir hello-world
cd hello-world
```

首先，我们需要为我们的项目创建一个文件夹。导航到您的[命令行](https://www.computerhope.com/jargon/c/commandi.htm)并键入：

现在我们在项目文件夹中，我们将使用 npm init 来初始化项目。如果您还没有安装 npm，请按照[这些说明进行操作](https://docs.alchemyapi.io/alchemy/guides/alchemy-for-macs#1-install-nodejs-and-npm)（我们还需要 Node.js，所以也下载它！）。

```js
npm init # (or npm init --yes)
```

您如何回答安装问题并不重要，以下是我们的做法以供参考：

```js
package name: (hello-world)
version: (1.0.0)
description: hello world smart contract
entry point: (index.js)
test command:
git repository:
keywords:
author:
license: (ISC)

About to write to /Users/.../.../.../hello-world/package.json:

{
   "name": "hello-world",
   "version": "1.0.0",
   "description": "hello world smart contract",
   "main": "index.js",
   "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1"
   },
   "author": "",
   "license": "ISC"
}
```

批准 package.json，我们就可以开始了！

#### 第7步：下载[hardhat](https://hardhat.org/getting-started/#overview)

Hardhat 是一个用于编译、部署、测试和调试以太坊软件的开发环境。在部署到实时链之前，它可以帮助开发人员在本地构建智能合约和 dApp。

在我们的`hello-world`项目内部运行：

```js
npm install --save-dev hardhat
```

查看此页面以获取有关[安装说明](https://hardhat.org/getting-started/#overview)的更多详细信息。

### 第8步：创建hardhat项目

在我们的`hello-world`项目文件夹中，运行：

```js
npx hardhat
```

然后，您应该会看到一条欢迎消息和用于选择您想要执行的操作的选项。选择“创建一个空的 hardhat.config.js”：

```js
888    888                      888 888               888
888    888                      888 888               888
888    888                      888 888               888
8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
888    888 .d888888 888    888  888 888  888 .d888888 888
888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888

👷 Welcome to Hardhat v2.0.11 👷‍

What do you want to do? …
Create a sample project
❯ Create an empty hardhat.config.js
Quit
```

这将为我们生成一个`hardhat.config.js`文件，我们将在其中指定项目的所有设置（在第 13 步中）。

### 第9步：添加项目文件夹

为了让我们的项目井井有条，我们将创建两个新文件夹。在命令行中导航到 hello-world 项目的根目录并键入

```js
mkdir contracts
mkdir scripts
```

`contracts/`是我们保存 hello world 智能合约代码文件的地方

`scripts/`是我们将保留脚本以部署和与我们的合约交互的地方

### 第10步：编写我们的合同

您可能会问自己，我们到底什么时候要编写代码？好吧，我们到了，第 10 步😄

在您喜欢的编辑器中打开 hello-world 项目（我们喜欢[VSCode](https://code.visualstudio.com/)）。智能合约是用一种称为 Solidity 的语言编写的，我们将使用它来编写我们的 HelloWorld.sol 智能合约。‌

1. 导航到“contracts”文件夹并创建一个名为 HelloWorld.sol 的新文件

2. 下面是我们将在本教程中使用的来自[以太坊基金会](https://ethereum.org/en/)的 Hello World 智能合约示例。将以下内容复制并粘贴到您的 HelloWorld.sol 文件中，并确保阅读注释以了解此合约的作用：

```js
// Specifies the version of Solidity, using semantic versioning.
// Learn more: https://solidity.readthedocs.io/en/v0.5.10/layout-of-source-files.html#pragma
pragma solidity >=0.7.3;

// Defines a contract named `HelloWorld`.
// A contract is a collection of functions and data (its state). Once deployed, a contract resides at a specific address on the Ethereum blockchain. Learn more: https://solidity.readthedocs.io/en/v0.5.10/structure-of-a-contract.html
contract HelloWorld {

   //Emitted when update function is called
   //Smart contract events are a way for your contract to communicate that something happened on the blockchain to your app front-end, which can be 'listening' for certain events and take action when they happen.
   event UpdatedMessages(string oldStr, string newStr);

   // Declares a state variable `message` of type `string`.
   // State variables are variables whose values are permanently stored in contract storage. The keyword `public` makes variables accessible from outside a contract and creates a function that other contracts or clients can call to access the value.
   string public message;

   // Similar to many class-based object-oriented languages, a constructor is a special function that is only executed upon contract creation.
   // Constructors are used to initialize the contract's data. Learn more:https://solidity.readthedocs.io/en/v0.5.10/contracts.html#constructors
   constructor(string memory initMessage) {

      // Accepts a string argument `initMessage` and sets the value into the contract's `message` storage variable).
      message = initMessage;
   }

   // A public function that accepts a string argument and updates the `message` storage variable.
   function update(string memory newMessage) public {
      string memory oldMsg = message;
      message = newMessage;
      emit UpdatedMessages(oldMsg, newMessage);
   }
}
```

这是一个超级简单的智能合约，它在创建时存储一条消息，并且可以通过调用`update`函数来更新。

### 第11步：将 Metamask 和 Alchemy 连接到您的项目

我们已经创建了一个 Metamask 钱包、Alchemy 账户，并编写了我们的智能合约，现在是时候连接这三者了。

从您的虚拟钱包发送的每笔交易都需要使用您唯一的私钥进行签名。为了向我们的程序提供此权限，我们可以将我们的私钥（和 Alchemy API 密钥）安全地存储在环境文件中。

> 要了解有关发送交易的更多信息，请查看有关使用 web3 发送交易的[教程。](https://docs.alchemyapi.io/alchemy/tutorials/sending-transactions-using-web3-and-alchemy)

首先，在你的项目目录中安装 dotenv 包：

```js
npm install dotenv --save
```

这是一个超级简单的智能合约，它在创建时存储一条消息，并且可以通过调用`update`函数来更新。

> 您的环境文件必须命名为 .env 否则将不会被识别为环境文件。请勿将其命名为 process.env 或 .env-custom 或其他任何名称。

- 按照[这些说明](https://metamask.zendesk.com/hc/en-us/articles/360015289632-How-to-Export-an-Account-Private-Key)导出您的私钥
- 请参阅下文以获取 HTTP Alchemy API URL

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210271845606.png)

你的`.env`应该是这样的：

```js
API_URL = "https://eth-goerli.alchemyapi.io/v2/your-api-key"
PRIVATE_KEY = "your-metamask-private-key"
```

为了将这些实际连接到我们的代码，我们将在第 13 步的`hardhat.config.js`文件中引用这些变量。

#### 第12步：安装 Ethers.js

Ethers.js 是一个库，它通过使用更用户友好的方法包装[标准 JSON-RPC 方法，使交互和向以太坊发出请求变得更加容易。](https://docs.alchemyapi.io/alchemy/documentation/alchemy-api-reference/json-rpc)

Hardhat 使集成[插件](https://hardhat.org/plugins/)变得非常容易，以获得额外的工具和扩展功能。我们将利用[Ethers 插件](https://hardhat.org/plugins/nomiclabs-hardhat-ethers.html)进行合约部署（[Ethers.js](https://github.com/ethers-io/ethers.js/)有一些超级干净的合约部署方法）。

在您的项目目录类型中：

```js
npm install --save-dev @nomiclabs/hardhat-ethers "ethers@^5.0.0"
```

在下一步中，我们还将在我们的hardhat.config.js中请求以太坊。

#### 第13步：更新 hardhat.config.js

到目前为止，我们已经添加了几个依赖项和插件，现在我们需要更新 hardhat.config.js 以便我们的项目了解所有这些。

更新你的hardhat.config.js，使其看起来像这样。：

```js
/**
* @type import('hardhat/config').HardhatUserConfig
*/

require('dotenv').config();
require("@nomiclabs/hardhat-ethers");

const { API_URL, PRIVATE_KEY } = process.env;

module.exports = {
   solidity: "0.7.3",
   defaultNetwork: "goerli",
   networks: {
      hardhat: {},
      goerli: {
         url: API_URL,
         accounts: [`0x${PRIVATE_KEY}`]
      }
   },
}
```

#### 第14步：编译我们的合约

为了确保到目前为止一切正常，让我们编译我们的合约。编译任务是内置hardhat任务之一。

从命令行运行：

```js
npx hardhat compile
```

您可能会收到关于`SPDX license identifier not provided in source file` 的警告，但无需担心——希望其他一切看起来都不错！

如果没有，您可以随时在[Alchemy discord](https://discord.gg/u72VCg3)中留言。

#### 第15步：编写我们的部署脚本

现在我们的合约已经写好并且我们的配置文件已经准备好了，是时候编写我们的合约部署脚本了。

导航到`/scripts`文件夹并创建一个名为`deploy.js`的新文件 ，向其中添加以下内容：

```js
async function main() {
   const HelloWorld = await ethers.getContractFactory("HelloWorld");

   // Start deployment, returning a promise that resolves to a contract object
   const hello_world = await HelloWorld.deploy("Hello World!");
   console.log("Contract deployed to address:", hello_world.address);
}

main()
  .then(() => process.exit(0))
  .catch(error => {
    console.error(error);
    process.exit(1);
  });
```

Hardhat 在他们的[合同教程](https://hardhat.org/tutorial/testing-contracts.html#writing-tests)中解释了每行代码的作用，我们在这里采用了他们的解释。

```js
const HelloWorld = await ethers.getContractFactory("HelloWorld");
```

在ethers.js中，ContractFactory是一个用于部署新的智能合约的抽象概念，所以这里的HelloWorld是我们hello world合约实例的工厂。当使用hardhat-ethers插件ContractFactory和Contract时，实例被默认连接到第一个签名者（所有者）。

```js
const hello_world = await HelloWorld.deploy();
```

调用a将启动部署，并返回解析为 Contract 对象的 Promise。这是为我们的每个智能合约功能提供方法的对象。`deploy()``ContractFactory`

#### 第16步：部署我们的合约

我们终于准备好部署我们的智能合约了！导航到命令行并运行：

```js
npx hardhat run scripts/deploy.js --network goerli
```

然后，您应该会看到如下内容：

```js
Contract deployed to address: 0xCAFBf889bef0617d9209Cf96f18c850e901A6D61
```

**请复制并粘贴此地址以将其保存在某处**，因为我们将在以后的教程中使用此地址，因此您不想丢失它。

如果我们去[Goerli etherscan](https://goerli.etherscan.io/)并搜索我们的合约地址，我们应该能够看到它已经成功部署。交易将如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210271846090.png)

发件人地址应与您的 Metamask 帐户地址匹配，收件人地址将显示“合同创建”，但如果我们点击交易，我们将在收件人字段中看到我们的合同地址：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210271846741.png)

恭喜！你刚刚在以太坊链上部署了一个智能合约🎉

要了解幕后发生的事情，让我们导航到[Alchemy 仪表板](https://dashboard.alchemyapi.io/explorer)中的 Explorer 选项卡。如果您有多个 Alchemy 应用程序，请确保按应用程序过滤并选择“Hello World”。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210271847842.png)

在这里，你会看到Hardhat/Ethers在我们调用deploy()函数时在引擎盖下进行的一些JSON-RPC调用。这里有两个重要的调用，一个是eth_sendRawTransaction，这是实际将我们的合同写到Ropsten链上的请求，另一个是eth_getTransactionByHash，这是一个读取关于我们交易的哈希值的请求（发送交易时的一个典型模式）。

要了解有关发送交易的更多信息，请查看有关[使用 Web3 发送交易](https://docs.alchemy.com/alchemy/tutorials/sending-txs)的教程。

> 译者注：下一篇明天到来，保持关注。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)