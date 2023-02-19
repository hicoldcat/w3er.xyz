---
title: 全栈以太坊和 EVM 开发的完整指南
description: null
author: 李留白
weight: 0
date: 2023-02-19T12:49:21.947Z
lastmod: 2023-02-19T13:14:29.107Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230219204902.png
---

> 使用 React、Ethers.js、Solidity 和 Hardhat 构建全栈 dApp

在本教程中，您将学习一个 web3 技术栈，它允许您利用以太坊虚拟机 (EVM) 在包括以太坊、Polygon、Avalanche、Celo 等在内的数十个区块链网络上构建全栈应用程序。

> 该项目的代码位于[此处](https://github.com/dabit3/full-stack-ethereum)。本教程的视频课程位于[此处](https://www.youtube.com/watch?v=a0osIaAOFSE)。另请查看[定义 web3 堆栈](https://edgeandnode.com/blog/defining-the-web3-stack)

自从我开始在区块链领域工作以来，我一直在深入研究 Solidity 和 EVM 的智能合约开发。我已经确定了我认为是开始使用 Solidity 构建全栈 dApp 的技术栈：

▶︎ 客户端框架 - **React**
▶︎ 以太坊开发环境 - [**Hardhat**](https://hardhat.org/)
▶︎ 以太坊 Web 客户端库 - [**Ethers.js**](https://docs.ethers.io/v5/)
▶︎ API 层 - [The Graph Protocol](https://thegraph.com/)

我在学习这个的过程中遇到的问题是，虽然对于这些东西中的每一个都有相当好的文档，但实际上没有关于如何将所有这些东西放在一起并理解它们如何相互工作的东西. 有一些非常好的样板，例如[scaffold-eth](https://github.com/austintgriffith/scaffold-eth)（其中还包括 Ethers、Hardhat 和 The Graph），但对于刚入门的人来说可能太多了。

我想要一个端到端的指南来向我展示如何使用最新的资源、库和工具构建完整的以太坊应用程序。

我感兴趣的是：

1. 如何创建、部署和测试以太坊智能合约到本地、测试和主网
2. 如何在本地、测试和生产环境/网络之间切换
3. 如何使用 React、Vue、Svelte 或 Angular 等前端的各种环境连接到合约并与之交互

在花了一些时间弄清楚所有这些并开始使用我感到非常满意的堆栈之后，我认为写出如何使用这个堆栈构建和测试一个完整的以太坊应用程序会很好，而不仅仅是为了其他人还有谁可能对这个栈感兴趣，也供自己以后参考。这就是那个参考。

## 碎片

让我们回顾一下我们将使用的主要部分以及它们如何适应堆栈。

### 一、以太坊开发环境

在构建智能合约时，您需要一种无需处理实时环境即可部署合约、运行测试和调试 Solidity 代码的方法。

您还需要一种方法将您的 Solidity 代码编译成可以在客户端应用程序中运行的代码——在我们的例子中，是一个 React 应用程序。稍后我们将详细了解其工作原理。

Hardhat 是专为全栈开发而设计的以太坊开发环境和框架，也是我将在本教程中使用的框架。

生态系统中的其他类似工具是[Ganache](https://www.trufflesuite.com/ganache)、[Truffle](https://www.trufflesuite.com/)和[Foundry](https://www.paradigm.xyz/2021/12/introducing-the-foundry-ethereum-development-toolbox/)。

### 2. 以太坊 Web 客户端库

在我们的 React 应用程序中，我们需要一种方法来与已部署的智能合约进行交互。我们将需要一种方法来读取数据以及发送新交易。

[ethers.js](https://docs.ethers.io/v5/)旨在成为一个完整而紧凑的库，用于从 React、Vue、Angular 或 Svelte 等客户端 JavaScript 应用程序与以太坊区块链及其生态系统进行交互。这是我们将要使用的库。

生态系统中另一个流行的选择是[web3.js](https://web3js.readthedocs.io/en/v1.3.4/)

### 3.MetaMask

[Metamask](https://metamask.io/download.html)有助于处理帐户管理并将当前用户连接到区块链。MetaMask 使用户能够以几种不同的方式管理他们的帐户和密钥，同时将他们与站点上下文隔离开来。

一旦用户连接了他们的 MetaMask 钱包，您作为开发人员就可以与全球可用的以太坊 API ( `window.ethereum`) 交互，该 API 可以识别 web3 兼容浏览器的用户（例如 MetaMask 用户），并且每当您请求交易签名时，MetaMask 都会提示以尽可能易于理解的方式向用户展示。

### 4.React

React 是一个前端 JavaScript 库，用于构建 Web 应用程序、用户界面和 UI 组件。它由 Facebook 和许多个人开发人员和公司维护。

React 及其庞大的元框架生态系统（如[Next.js](https://nextjs.org/)、[Gatsby](https://www.gatsbyjs.com/)、[Redwood](https://redwoodjs.com/)、[Blitz.js](https://blitzjs.com/)等）支持所有类型的部署目标，包括传统 SPA、静态站点生成器、服务器端渲染以及这三者的组合。React 似乎继续在前端领域占据主导地位，我认为至少在不久的将来会继续这样做。

### 5.Graph

对于大多数构建在以太坊等区块链上的应用程序，直接从链上读取数据既困难又费时，因此您过去经常看到人们和公司构建自己的集中式索引服务器并为来自这些服务器的 API 请求提供服务。这需要大量的工程和硬件资源，并破坏了去中心化所需的安全属性。

Graph 是一种用于查询区块链数据的索引协议，它支持创建完全去中心化的应用程序并解决了这个问题，暴露了应用程序可以使用的丰富的 GraphQL 查询层。在本指南中，我们不会为我们的应用程序构建子图，但会在以后的教程中这样做。

要了解如何使用 The Graph 构建区块链 API，请查看[在以太坊上构建 GraphQL API](https://dev.to/dabit3/building-graphql-apis-on-ethereum-4poa)。

## 我们将建造什么

在本教程中，我们将构建、部署和连接到几个基本的智能合约：

1. 在以太坊区块链上创建和更新消息的合约
2. 铸造代币的合同，然后允许合同的所有者将代币发送给其他人并读取代币余额，并且新代币的所有者也可以将它们发送给其他人。

我们还将构建一个 React 前端，允许用户：

1. 从部署到区块链的合约中读取问候语
2. 更新问候语
3. 将新铸造的代币从他们的地址发送到另一个地址
4. 一旦有人收到代币，允许他们也将代币发送给其他人
5. 从部署到区块链的合约中读取代币余额

### 先决条件

1. 本地机器上安装的 Node.js
2. [MetaMask](https://metamask.io/) Chrome 扩展安装在您的浏览器中

您不需要为本指南拥有任何以太坊，因为我们将在整个教程的测试网络上使用假/测试以太币。

## 入门

首先，我们将创建一个新的 React 应用程序：

```
npx create-react-app react-dapp
```

接下来，切换到新目录并使用**NPM**或**Yarn**[`ethers.js`](https://docs.ethers.io/v5/)安装和[`hardhat`](https://github.com/nomiclabs/hardhat)使用：

```
npm install ethers hardhat @nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers
```

### 安装和配置以太坊开发环境

接下来，使用 Hardhat 初始化一个新的以太坊开发环境：

```
npx hardhat

? What do you want to do? Create a JavaScript project
? Hardhat project root: <Choose default path>
```

> 如果您遇到有关 README.md 文件的错误，请删除 README.md 文件并重新运行该命令。

现在您应该会在根目录中看到为您创建的以下工件：

**hardhat.config.js** - 您的整个 Hardhat 设置（即您的配置、插件和自定义任务）都包含在此文件中。
**scripts** - 一个包含名为**sample-script.js**的脚本的文件夹，该脚本将在执行时部署你的智能合约
**test** - 一个包含示例测试脚本的
**文件夹 contracts** - 一个包含示例 Solidity 智能合约的文件夹

由于[MetaMask 配置问题](https://hardhat.org/metamask-issue.html)，我们需要将 HardHat 配置上的链 ID 更新为**1337**。[我们还需要更新已编译合约的工件](https://hardhat.org/guides/compile-contracts.html#artifacts)位置，使其位于React 应用程序的**src目录中。**

要进行这些更新，请打开**hardhat.config.js**并将`module.exports`更新为如下所示：

```js
module.exports = {
  solidity: "0.8.9",
  paths: {
    artifacts: './src/artifacts',
  },
  networks: {
    hardhat: {
      chainId: 1337
    }
  }
};
```

## 我们的智能合约

接下来，让我们看一下在**contracts/Greeter.sol**中提供给我们的示例合约：

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "hardhat/console.sol";


contract Greeter {
  string greeting;

  constructor(string memory _greeting) {
    console.log("Deploying a Greeter with greeting:", _greeting);
    greeting = _greeting;
  }

  function greet() public view returns (string memory) {
    return greeting;
  }

  function setGreeting(string memory _greeting) public {
    console.log("Changing greeting from '%s' to '%s'", greeting, _greeting);
    greeting = _greeting;
  }
}
```

这是一个非常基本的智能合约。部署时，它会设置一个 Greeting 变量并公开一个可以调用以返回问候语的函数 (`greet`)。

它还公开了一个允许用户更新问候语 (`setGreeting`) 的函数。当部署到以太坊区块链时，这些方法将可供用户进行交互。

### 读写以太坊区块链

与智能合约交互的方式有两种，读取或写入/交易。在我们的合约中，`greet`可以认为是读，而`setGreeting`可以认为是写/交易。

在写入或初始化交易时，您必须为要写入区块链的交易付费。为了使这项工作成功，您需要支付[gas](https://www.investopedia.com/terms/g/gas-ethereum.asp#:~:text=What Is Gas (Ethereum)%3F,on the Ethereum blockchain platform)，这是在以太坊区块链上成功进行交易和执行合同所需的费用或价格。

只要您只是从区块链中读取数据而不更改或更新任何内容，您就不需要进行交易，也不会产生任何 gas 或成本。然后，您调用的函数将仅由您连接的节点执行，因此您无需支付任何气体，并且读取是免费的。

`ethers.js`在我们的 React 应用程序中，我们与智能合约交互的方式是使用库、合约地址和Hardhat 从合约创建的[ABI](https://docs.soliditylang.org/en/v0.5.3/abi-spec.html)的组合。

什么是 ABI？ABI 代表应用程序二进制接口。您可以将其视为您的客户端应用程序与部署您将与之交互的智能合约的以太坊区块链之间的接口。

ABI 通常由 HardHat 等开发框架从 Solidity 智能合约编译而成。[您还可以经常在Etherscan](https://etherscan.io/)上找到智能合约的 ABI

### 编译 ABI

现在我们已经了解了基本的智能合约并了解了 ABI 是什么，让我们为我们的项目编译一个 ABI。

为此，请转到命令行并运行以下命令：

```
npx hardhat compile
```



> 如果您有任何依赖性错误问题`hardhat-toolbox`，请查看[此处的](https://hardhat.org/hardhat-runner/plugins/nomicfoundation-hardhat-toolbox)安装说明。

现在，您应该会在**src目录中看到一个名为artifacts的新文件夹**。**artifacts/contracts/Greeter.json**文件包含 ABI 作为属性之一。当我们需要使用 ABI 时，我们可以从我们的 JavaScript 文件中导入它：

```js
import Greeter from './artifacts/contracts/Greeter.sol/Greeter.json'
```

然后我们可以像这样引用 ABI：

```js
console.log("Greeter ABI: ", Greeter.abi)
```

> 请注意，Ethers.js 还支持[人类可读的 ABI](https://blog.ricmoo.com/human-readable-contract-abis-in-ethers-js-141902f4d917)，但在本教程中不会涉及到这一点。

### 部署和使用本地网络/区块链

接下来，让我们将智能合约部署到本地区块链，以便我们对其进行测试。

要部署到本地网络，首先需要启动本地测试节点。为此，请打开 CLI 并运行以下命令：

```
npx hardhat node
```

当我们运行此命令时，您应该会看到一个地址和私钥列表。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230219205342.png)

这些是为我们创建的 20 个测试帐户和地址，我们可以使用它们来部署和测试我们的智能合约。每个账户还装有 10,000 个假以太币。稍后，我们将学习如何将测试帐户导入 MetaMask，以便我们使用它。

接下来，使用以下代码更新**scripts/deploy.js**以部署`Greeter`合约：

```js
const hre = require("hardhat");

async function main() {  
  const Greeter = await hre.ethers.getContractFactory("Greeter");
  const greeter = await Greeter.deploy("Hello World");
  await greeter.deployed();

  console.log(
    `contract successfully deployed to ${greeter.address}`
  );
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

现在我们可以运行部署脚本并为我们想要部署到本地网络的 CLI 提供一个标志：

```
npx hardhat run scripts/deploy.js --network localhost
```

执行此脚本后，智能合约应部署到本地测试网络，然后我们应该能够开始与其交互。

> 部署合约时，它使用了我们启动本地网络时创建的第一个帐户。

如果您查看 CLI 的输出，您应该能够看到如下内容：

```
Greeter deployed to: 0x9fE46736679d2D9a65F0992F2272dE9f3c7fa6e0
```

这个地址是我们将在我们的客户端应用程序中用来与智能合约对话的地址。保持此地址可用，因为我们在从客户端应用程序连接到它时需要使用它。

要将交易发送到智能合约，我们需要使用运行`npx hardhat node`时创建的帐户之一连接我们的 MetaMask 钱包。在 CLI 注销的合约列表中，您应该会看到**帐号**和**私钥**：

```
➜  react-dapp git:(main) npx hardhat node
Started HTTP and WebSocket JSON-RPC server at http://127.0.0.1:8545/

Accounts
========
Account #0: 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266 (10000 ETH)
Private Key: 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

...
```

我们可以将此帐户导入 MetaMask，以便开始使用那里可用的一些假 Eth。

为此，首先打开 MetaMask 并启用**测试网络**：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230219205405.png)

接下来，将网络更新为 Localhost 8545：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230219205430.png)

接下来，在 MetaMask 中点击账户菜单中的**导入账户：**

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230219205448.png)

复制然后粘贴由 CLI 注销的**私钥**之一，然后单击**导入**。导入帐户后，您应该会在帐户中看到 Eth：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230219205502.png)

现在我们已经部署了智能合约并准备好使用帐户，我们可以开始从 React 应用程序与其交互。

### 连接 React 客户端

在本教程中，我们不会担心使用 CSS 构建漂亮的 UI 以及所有这些，我们 100% 专注于核心功能，以帮助您启动和运行。从那里，你可以把它拿走，如果你愿意的话，让它看起来不错。

话虽如此，让我们回顾一下我们希望从 React 应用程序中实现的两个目标：

1. 从智能合约中获取`greeting`当前值
2. 允许用户更新`greeting`的值

了解了这些事情后，我们如何做到这一点？以下是我们需要做的事情来实现这一点：

1. 创建一个输入字段和一些本地状态来管理输入的值（更新`greeting`）
2. 允许应用程序连接到用户的 MetaMask 帐户以签署交易
3. 创建用于读取和写入智能合约的函数

为此，使用以下代码打开`src/App.js`并更新它，将`greeterAddress`的值设置为您的智能合约的地址：

```react
import './App.css';
import { useState } from 'react';
import { ethers } from 'ethers'
import Greeter from './artifacts/contracts/Greeter.sol/Greeter.json'

// Update with the contract address logged out to the CLI when it was deployed 
const greeterAddress = "your-contract-address"

function App() {
  // store greeting in local state
  const [greeting, setGreetingValue] = useState()

  // request access to the user's MetaMask account
  async function requestAccount() {
    await window.ethereum.request({ method: 'eth_requestAccounts' });
  }

  // call the smart contract, read the current greeting value
  async function fetchGreeting() {
    if (typeof window.ethereum !== 'undefined') {
      const provider = new ethers.providers.Web3Provider(window.ethereum)
      const contract = new ethers.Contract(greeterAddress, Greeter.abi, provider)
      try {
        const data = await contract.greet()
        console.log('data: ', data)
      } catch (err) {
        console.log("Error: ", err)
      }
    }    
  }

  // call the smart contract, send an update
  async function setGreeting() {
    if (!greeting) return
    if (typeof window.ethereum !== 'undefined') {
      await requestAccount()
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      const signer = provider.getSigner()
      const contract = new ethers.Contract(greeterAddress, Greeter.abi, signer)
      const transaction = await contract.setGreeting(greeting)
      await transaction.wait()
      fetchGreeting()
    }
  }

  return (
    <div className="App">
      <header className="App-header">
        <button onClick={fetchGreeting}>Fetch Greeting</button>
        <button onClick={setGreeting}>Set Greeting</button>
        <input onChange={e => setGreetingValue(e.target.value)} placeholder="Set greeting" />
      </header>
    </div>
  );
}

export default App;
```

要测试它，请启动 React 服务器：

```
npm start
```

当应用程序加载时，您应该能够获取当前问候语并将其注销到控制台。您还应该能够通过使用您的 MetaMask 钱包签署合同并花费假 Ether 来更新问候语。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230219205521.png)

### 部署和使用实时测试网络

我们还可以部署几个以太坊测试网络，如 Ropsten、Rinkeby 或 Kovan，以便在无需将其部署到主网的情况下获得可公开访问的合约版本。在本教程中，我们将部署到**Ropsten**测试网络。

首先，请先更新您的 MetaMask 钱包以连接到 Ropsten 网络。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230219205538.png)

[接下来，通过访问这个](https://faucet.egorfine.com/)或另一个测试水龙头，给自己发送一些测试以太币，以便在本教程的其余部分使用。

[我们可以通过注册Infura](https://infura.io/dashboard/ethereum/cbdf7c5eee8b4e2b91e76b77ffd34533/settings)或[Alchemy](https://www.alchemyapi.io/)（我在本教程中使用 Infura）等服务来访问 Ropsten（或任何其他测试网络） 。

在 Infura 或 Alchemy 中创建应用程序后，您将获得一个如下所示的端点：

```
https://ropsten.infura.io/v3/your-project-id
```

请务必在 Infura 或 Alchemy 应用程序配置中设置**ALLOWLIST ETHEREUM ADDRESSES ，以包含您将从中进行部署的帐户的钱包地址。**

要部署到测试网络，我们需要使用一些额外的网络信息更新我们的 hardhat 配置。我们需要设置的一件事是我们将从中部署的钱包的私钥。

要获取私钥，您可以从 MetaMask 导出它。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230219205552.png)

> 我建议不要在您的应用程序中对这个值进行硬编码，而是将其设置为环境变量。

接下来，添加具有以下配置的`networks`属性：

```js
module.exports = {
  defaultNetwork: "hardhat",
  paths: {
    artifacts: './src/artifacts',
  },
  networks: {
    hardhat: {},
    ropsten: {
      url: "https://ropsten.infura.io/v3/your-project-id",
      accounts: [`0x${your-private-key}`]
    }
  },
  solidity: "0.8.9",
};
```

要部署，请运行以下脚本：

```
npx hardhat run scripts/deploy.js --network ropsten
```

部署合约后，您应该能够开始与其交互。[您现在应该能够在Etherscan Ropsten Testnet Explorer](https://ropsten.etherscan.io/)上查看实时合约。

## 铸造代币

智能合约最常见的用例之一是创建代币，让我们看看我们如何做到这一点。由于我们对所有这些工作原理了解得更多一些，所以我们会走得更快一些。

在主**contracts**目录中创建一个名为**Token.sol**的新文件。

接下来，使用以下智能合约更新**Token.sol ：**

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "hardhat/console.sol";

contract Token {
  string public name = "Nader Dabit Token";
  string public symbol = "NDT";
  uint public totalSupply = 1000000;
  mapping(address => uint) balances;

  constructor() {
    balances[msg.sender] = totalSupply;
  }

  function transfer(address to, uint amount) external {
    require(balances[msg.sender] >= amount, "Not enough tokens");
    balances[msg.sender] -= amount;
    balances[to] += amount;
  }

  function balanceOf(address account) external view returns (uint) {
    return balances[account];
  }
}
```

> 请注意，此代币合约仅用于演示目的，不符合[ERC20](https://eips.ethereum.org/EIPS/eip-20)标准。[我们将在这里](https://dev.to/dabit3/the-complete-guide-to-full-stack-ethereum-development-3j13#erc20-token)介绍 ERC20 代币

该合约将创建一个名为“Nader Dabit Token”的新代币，并将供应量设置为 1000000。

接下来，编译这个合约：

```
npx hardhat compile
```

现在，更新**scripts/deploy.js**中的部署脚本以包含这个新的 Token 合约：

```js
const hre = require("hardhat");

async function main() {
  const [deployer] = await hre.ethers.getSigners();

  console.log(
    "Deploying contracts with the account:",
    deployer.address
  );

  const Greeter = await hre.ethers.getContractFactory("Greeter");
  const greeter = await Greeter.deploy("Hello, World!");

  const Token = await hre.ethers.getContractFactory("Token");
  const token = await Token.deploy();

  await greeter.deployed();
  await token.deployed();

  console.log("Greeter deployed to:", greeter.address);
  console.log("Token deployed to:", token.address);
}

main()
  .then(() => process.exit(0))
  .catch(error => {
    console.error(error);
    process.exit(1);
  });
```

现在，我们可以将这个新合约部署到本地或 Ropsten 网络：

```
npx hardhat run scripts/deploy.js --network localhost
```

部署合约后，您可以开始将这些代币发送到其他地址。

为此，让我们更新完成这项工作所需的客户端代码：

```react
import './App.css';
import { useState } from 'react';
import { ethers } from 'ethers'
import Greeter from './artifacts/contracts/Greeter.sol/Greeter.json'
import Token from './artifacts/contracts/Token.sol/Token.json'

const greeterAddress = "your-contract-address"
const tokenAddress = "your-contract-address"

function App() {
  const [greeting, setGreetingValue] = useState()
  const [userAccount, setUserAccount] = useState()
  const [amount, setAmount] = useState()

  async function requestAccount() {
    await window.ethereum.request({ method: 'eth_requestAccounts' });
  }

  async function fetchGreeting() {
    if (typeof window.ethereum !== 'undefined') {
      const provider = new ethers.providers.Web3Provider(window.ethereum)
      console.log({ provider })
      const contract = new ethers.Contract(greeterAddress, Greeter.abi, provider)
      try {
        const data = await contract.greet()
        console.log('data: ', data)
      } catch (err) {
        console.log("Error: ", err)
      }
    }    
  }

  async function getBalance() {
    if (typeof window.ethereum !== 'undefined') {
      const [account] = await window.ethereum.request({ method: 'eth_requestAccounts' })
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      const contract = new ethers.Contract(tokenAddress, Token.abi, provider)
      const balance = await contract.balanceOf(account);
      console.log("Balance: ", balance.toString());
    }
  }

  async function setGreeting() {
    if (!greeting) return
    if (typeof window.ethereum !== 'undefined') {
      await requestAccount()
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      console.log({ provider })
      const signer = provider.getSigner()
      const contract = new ethers.Contract(greeterAddress, Greeter.abi, signer)
      const transaction = await contract.setGreeting(greeting)
      await transaction.wait()
      fetchGreeting()
    }
  }

  async function sendCoins() {
    if (typeof window.ethereum !== 'undefined') {
      await requestAccount()
      const provider = new ethers.providers.Web3Provider(window.ethereum);
      const signer = provider.getSigner();
      const contract = new ethers.Contract(tokenAddress, Token.abi, signer);
      const transation = await contract.transfer(userAccount, amount);
      await transation.wait();
      console.log(`${amount} Coins successfully sent to ${userAccount}`);
    }
  }

  return (
    <div className="App">
      <header className="App-header">
        <button onClick={fetchGreeting}>Fetch Greeting</button>
        <button onClick={setGreeting}>Set Greeting</button>
        <input onChange={e => setGreetingValue(e.target.value)} placeholder="Set greeting" />

        <br />
        <button onClick={getBalance}>Get Balance</button>
        <button onClick={sendCoins}>Send Coins</button>
        <input onChange={e => setUserAccount(e.target.value)} placeholder="Account ID" />
        <input onChange={e => setAmount(e.target.value)} placeholder="Amount" />
      </header>
    </div>
  );
}

export default App;
```

接下来，运行应用程序：

```
npm start
```

我们应该能够点击**Get Balance**并看到我们登录到控制台的帐户中有 1,000,000 个代币。

您还应该能够通过单击**导入令牌**在 MetaMask 中查看它们：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230219205632.png)

接下来单击**Custom Token**并输入令牌合约地址，然后单击**Add Custom Token**。（如果询问代币小数，请选择 0）现在代币应该在您的钱包中可用：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230219205648.png)

接下来，让我们尝试将这些硬币发送到另一个地址。

为此，请复制另一个帐户的地址，并使用更新后的 React UI 将它们发送到该地址。当您检查令牌数量时，它应该等于原始数量减去您发送到该地址的数量。

## ERC20代币

ERC20[代币标准](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/)定义了一套适用于所有 ERC20 代币的规则，使它们能够轻松地相互交互。ERC20 使得人们可以很容易地铸造自己的代币，这些代币将与以太坊区块链上的其他代币具有互操作性。

让我们看看如何使用 ERC20 标准构建我们自己的代币。

首先，安装[OpenZepplin](https://github.com/OpenZeppelin/openzeppelin-contracts)智能合约库，我们将在其中导入基础`ERC20`令牌：

```
npm install @openzeppelin/contracts
```



接下来，我们将通过扩展（或继承）合约来创建我们的代币`ERC20`：

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract NDToken is ERC20 {
    constructor(string memory name, string memory symbol) ERC20(name, symbol) {
        _mint(msg.sender, 100000 * (10 ** 18));
    }
}
```

构造函数允许您设置代币名称和符号，`_mint`函数允许您铸造代币并设置数量。

默认情况下，ERC20 将小数位数设置为 18，因此在我们的`_mint`函数中，我们将 100,000 乘以 10 的 18 次方来铸造总共 100,000 个代币，每个代币有 18 个小数位（类似于 1 Eth 由 10 到18[wei](https://www.investopedia.com/terms/w/wei.asp)。

要部署，我们需要传入构造函数值（`name`和`symbol`），因此我们可能会在部署脚本中执行如下操作：

```
const NDToken = await hre.ethers.getContractFactory("NDToken");
const ndToken = await NDToken.deploy("Nader Dabit Token", "NDT");
```

通过扩展原始的 ERC20 代币，您的代币将继承以下所有功能和功能：

```solidity
function name() public view returns (string)
function symbol() public view returns (string)
function decimals() public view returns (uint8)
function totalSupply() public view returns (uint256)
function balanceOf(address _owner) public view returns (uint256 balance)
function transfer(address _to, uint256 _value) public returns (bool success)
function transferFrom(address _from, address _to, uint256 _value) public returns (bool success)
function approve(address _spender, uint256 _value) public returns (bool success)
function allowance(address _owner, address _spender) public view returns (uint256 remaining)
```

部署后，您可以使用这些功能中的任何一个与新的智能合约进行交互。有关 ERC20 代币的另一个示例，请查看 [Solidity by example](https://solidity-by-example.org/app/erc20/)

## 结论

好吧，我们在这里涵盖了很多，但对我来说，这是开始使用这个堆栈的面包和黄油/核心，也是我想要拥有的东西，不仅是作为一个正在学习所有这些东西的人，而且也是未来如果我需要参考我将来可能需要的任何东西。我希望你学到了很多。

如果除了 MetaMask 之外你还想支持多个钱包，请查看[Web3Modal](https://github.com/Web3Modal/web3modal)，它可以通过相当简单和可自定义的配置轻松地在你的应用程序中实现对多个提供商的支持。

在我未来的教程和指南中，我将深入研究更复杂的智能合约开发，以及如何将它们部署为[subgraphs](https://thegraph.com/docs/define-a-subgraph) 以在它们之上公开 GraphQL API 并实现分页和全文搜索等功能。

我还将探讨如何使用 IPFS 和 Web3 数据库等技术以分散的方式存储数据。

原文：https://web3.career/learn-web3/web3-interview-questions

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)