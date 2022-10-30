---
title: Web3教程：编写您的第一个“Hello, World”智能合约（4）
description: null
author: 李留白
weight: 0
date: 2022-10-30T03:48:39.211Z
lastmod: 2022-10-30T03:48:44.723Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210271827013.png
---

上一篇传送门：[Web3教程：编写您的第一个“Hello, World”智能合约（3）](https://mp.weixin.qq.com/s/_tSlbHkeG70IJgT9L9fm7Q)

## 目录

1. 什么是智能合约
2. 部署您的第一个智能合约
3. 什么是气体
4. 与您的智能合约交互
5. 智能合约的结构
6. 将您的合同提交给 Etherscan
7. 智能合约安全挑战
8. 将合约集成到前端
9. 其他资源

## 8、将您的智能合约与前端集成

🎉您终于到了要完成了本教程系列的最后一部分：通过将您的 Hello World 智能合约连接到前端项目并与之交互来创建一个全栈去中心化应用程序 (dApp)。

在本教程结束时，您将知道如何：

- 将 Metamask 钱包连接到您的 dApp 项目
- [使用Alchemy Web3](https://docs.alchemy.com/alchemy/documentation/alchemy-web3) API从您的智能合约中读取数据
- 使用 Metamask 签署以太坊交易

对于这个 dApp，我们将使用[React](https://reactjs.org/)作为我们的前端框架；但是，重要的是要注意，我们不会花太多时间分解它的基础，因为我们将主要专注于将 Web3 功能引入我们的项目。

> 作为先决条件，你应该对 React 有初级的了解——知道组件、props、useState/useEffect 和基本的函数调用是如何工作的。如果你以前从未听说过这些术语，我们建议你查看这个[React 简介教程](https://reactjs.org/tutorial/tutorial.html)。对于更多的视觉学习者，我们强烈推荐这个很棒的 Net Ninja [Full Modern React 教程](https://www.youtube.com/playlist?list=PL4cUxeGkcC9gZD-Tvwfod2gaISzfRiP9d)视频系列。

我们来这里是为了什么？让我们开始吧！😎

### 第 1 步：克隆启动文件

首先，转到[hello-world-part-four github 存储库](https://github.com/alchemyplatform/hello-world-part-four-tutorial)以获取此项目的启动文件。将此存储库克隆到您的本地环境中。

> 不知道如何克隆存储库？从 Github查看[本指南。](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/cloning-a-repository)

当您打开这个克隆的`hello-world-part-four`存储库时，您会注意到它包含两个文件夹：`starter-files`和`completed`。

`starter files`包含该项目的启动文件（本质上是 React UI）。在本教程中，我们将在此目录中工作，因为您将学习如何通过将其连接到您的以太坊钱包和您在 Etherscan 上发布的 Hello World 智能合约来使此 UI 栩栩如生第 3 部分。

`completed`包含完整的完整教程，如果您遇到困难，可以作为参考。

接下来，用您最喜欢的代码编辑器打开`starter-files`的拷贝（在 Alchemy，我们是VScode），然后导航到您的`src`文件夹：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210301007909.png)

我们将编写的所有代码都将存在于该`src`文件夹下。我们将编辑`HelloWorld.js`组件和`util/interact.js`文件来添加我们的项目 Web3 功能。

### 第 2 步：检查启动文件

在我们开始编码之前，弄清楚启动文件中已经为我们提供了什么是非常重要的。

#### 让你React项目运行

让我们从在浏览器中运行 React 项目开始。React 的美妙之处在于，一旦我们的项目在浏览器中运行，我们保存的任何更改都会在浏览器中实时更新。

要让项目运行，请导航到文件夹的根目录`starter files`，然后`npm install`在终端中运行以安装项目的依赖项：

```js
cd starter-files
npm install
```

完成安装后，在终端中运行`npm start`：

```js
npm start
```

这样会在您的浏览器中打开http://localhost:3000/，您将在其中看到我们项目的前端。它应该由一个字段（一个更新存储在智能合约中的消息的地方）、一个“连接钱包”按钮和一个“更新”按钮组成。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210301033056.png)

如果您尝试单击“连接钱包”或“更新”按钮，您会发现它们不起作用——那是因为我们仍然需要对它们的功能进行编程！:)

#### HelloWorld.js 组件

> **注意：**确保您在 starter-files 文件夹中，而不是在completed的文件夹中！

让我们回到编辑器中的`src`文件夹并打开`HelloWorld.js`文件。理解这个文件中的所有内容非常重要，因为它是我们将要处理的主要 React 组件。

在这个文件的顶部，你会注意到我们有几个 import 语句是让我们的项目运行所必需的，包括 React 库、useEffect 和 useState 钩子，一些其他项`./util/interact.js`（我们很快会更详细地描述它们！ )，还有Alchemy的logo。🧙

```js
import React from "react";
import { useEffect, useState } from "react";
import {
  helloWorldContract,
  connectWallet,
  updateMessage,
  loadCurrentMessage,
  getCurrentWalletConnected,
} from "./util/interact.js";

import alchemylogo from "./alchemylogo.svg";
```

接下来，我们将在特定事件后更新状态变量。

```js
//State variables
const [walletAddress, setWallet] = useState("");
const [status, setStatus] = useState("");
const [message, setMessage] = useState("No connection to the network.");
const [newMessage, setNewMessage] = useState("");
```

> 从未听说过 React 状态变量或状态钩子？查看[这些](https://reactjs.org/docs/hooks-state.html)文档。

#### 以下是每个变量所代表的内容：

`walletAddress`- 存储用户钱包地址的字符串

`status`- 存储有用消息的字符串，指导用户如何与 dApp 交互

`message`- 在智能合约中存储当前消息的字符串

`new message`- 存储将写入智能合约的新消息的字符串

在状态变量之后，您将看到五个未实现的函数：`useEffect`、`addSmartContractListener`、`connectWalletPressed`和`onUpdatePressed`。我们将在下面解释它们的作用：

```js
//called only once
useEffect(async () => { //TODO: implement

}, []);

function addSmartContractListener() { //TODO: implement

}

function addWalletListener() { //TODO: implement

}

const connectWalletPressed = async () => { //TODO: implement

};

const onUpdatePressed = async () => { //TODO: implement

};
```

`useEffect`- 这是一个 React 钩子，在渲染组件后调用。因为它有一个空数组 [] 传递给它（见第 4 行），它只会在组件的第一次渲染时被调用。在这里，我们将加载存储在我们的智能合约中的当前消息，调用我们的智能合约和钱包监听器，并更新我们的 UI 以反映钱包是否已经连接。

`addSmartContractListener`- 这个函数设置了一个监听器，它将监听我们的 HelloWorld 合约的`UpdatedMessages`事件，并在我们的智能合约中的消息发生更改时更新我们的 UI。

`addWalletListener`- 此函数设置一个监听器，检测用户 Metamask 钱包状态的变化，例如用户断开钱包或切换地址时。

`connectWalletPressed`- 将调用此函数将用户的 Metamask 钱包连接到我们的 dApp。

`onUpdatePressed`- 当用户想要更新存储在智能合约中的消息时，将调用此函数。

在这个文件的末尾，我们有我们组件的 UI。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210301041443.png)

**如果您仔细浏览此代码，您会注意到我们在 UI 中使用各种状态变量的位置**

在第6-12行，如果用户的钱包被连接（即`walletAddress.length>0`），我们在ID为 "walletButton "的按钮中显示用户`walletAddress`的截断版本；否则，它只是说 "Connect Wallet."。

在第 17 行，我们显示存储在智能合约中的当前消息，该消息被存储在` message`字符串中。

在第 23-26 行，我们使用当文本字段中的输入发生变化时，控制组件更新我们的`newMessage`状态变量。

除了我们的状态变量，你还会看到，当ID为publishButton和walletButton的按钮被点击时，connectWalletPressed和onUpdatePressed函数被调用。

最后让我们解决这个`HelloWorld.js`组件在哪里添加。

如果你去看App.js，它是React的主要组件，作为所有其他组件的容器，你会看到我们的HelloWorld.js组件在第7行被注入。

最后但并非最不重要的是，让我们看看为你提供的另一个文件，interactive.js文件。

#### interact.js文件

因为我们要遵守M-V-C范式，所以我们希望有一个单独的文件，包含我们所有的函数来管理我们的DApp的逻辑、数据和规则，然后能够将这些函数输出到我们的前端（我们的HelloWorld.js组件）。

👆🏽这就是我们的interactive.js文件的确切目的!

导航到src目录下的util文件夹，你会注意到我们已经包含了一个名为`interactive.js`的文件，它将包含我们所有的智能合约交互和钱包函数和变量。

```js
//export const helloWorldContract;

export const loadCurrentMessage = async () => {

};

export const connectWallet = async () => {

};

const getCurrentWalletConnected = async () => {

};


export const updateMessage = async (message) => {

};
```

你会注意到在文件的顶部，我们已经注释了`helloWorldContract`对象。在本教程的后面，我们将取消对这个对象的注释，并在这个变量中实例化我们的智能合约，然后我们将把它导出到我们的`HelloWorld.js`组件。

在我们的helloWorldContract对象之后的四个未实现的函数,做了以下事情:

loadCurrentMessage--该函数处理加载存储在智能合约中的当前消息的逻辑。它将使用Alchemy Web3 API对Hello World智能合约进行读取调用。

connectWallet- 此函数将连接用户的Metamask到我们的DApp。

getCurrentWalletConnected- 此函数将在页面加载时检查Ethereum账户是否已经连接到我们的dApp，并相应地更新我们的UI。

updateMessage 这个函数将更新存储在智能合约中的消息。它将对Hello World智能合约进行写入调用，因此用户的Metamask钱包将不得不签署一个以太坊交易来更新消息。

现在，我们明白了我们的工作内容，让我们来弄清楚如何从我们的智能合约中读取信息吧

### 第3步：从你的智能合约中读取信息

要从你的智能合约中读取，你需要成功设置。

- 一个与以太坊链的API连接
- 一个加载的智能合约实例
- 一个调用你的智能合约功能的函数
- 一个监听器，当你从智能合约中读取的数据发生变化时，监听器会注意更新。

这听起来可能有很多步骤，但不要担心！我们将指导你如何完成这些步骤。我们会一步一步地指导你如何做每一个步骤! :)

#### 建立一个与以太坊链的API连接

还记得在本教程的第二部分，我们是如何[使用Alchemy Web3密钥来读取我们的智能合约](https://docs.alchemy.com/alchemy/tutorials/hello-world-smart-contract/interacting-with-a-smart-contract#step-1-install-web3-library)的吗？你也需要一个Alchemy Web3密钥在你的DApp中从链上读取数据。

如果您还没有安装Alchemy Web3，请首先安装它。方法是浏览您的启动文件的根目录，并在您的终端上运行以下内容启动文件并在您的终端上运行以下内容

```js
npm install @alch/alchemy-web3
```

> [Alchemy Web3](https://github.com/alchemyplatform/alchemy-web3)是一个围绕[Web3.js](https://web3js.readthedocs.io/en/v1.2.9/)的封装器，提供了增强的API方法和其他重要的好处，使你作为一个Web3开发者的生活更容易。它的设计需要最小的配置，所以你可以立即开始在你的应用程序中使用它

然后，在你的项目目录中安装[dotenv](https://www.npmjs.com/package/dotenv)包，这样我们就有一个安全的地方来存储我们的API密钥，在我们获取它之后。

```js
npm install dotenv --save
```

对于我们的DApp，我们将使用我们的Websockets API密钥，而不是我们的HTTP API密钥，因为它将允许我们设置一个监听器，以检测存储在智能合约中的消息何时发生变化。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210301052448.gif)

一旦你有了你的API密钥，在你的根目录下创建一个`.env`文件，并将你的Alchemy Websockets网址添加到其中。之后，你的.env文件应该看起来像这样。

```js
REACT_APP_ALCHEMY_KEY = wss://eth-goerli.ws.alchemyapi.io/v2/
```

现在，我们已经准备好在我们的dApp中设置我们的Alchemy Web3端点了! 让我们回到嵌套在`util`文件夹中的`interactive.js`，并在文件的顶部添加以下代码。

```js
require('dotenv').config();
const alchemyKey = process.env.REACT_APP_ALCHEMY_KEY;
const { createAlchemyWeb3 } = require("@alch/alchemy-web3");
const web3 = createAlchemyWeb3(alchemyKey);

//export const helloWorldContract;
```

上面，我们首先从我们的.env文件中导入了Alchemy ket，然后将我们的alchemyKey传递给createAlchemyWeb3以建立我们的Alchemy Web3端点。

准备好这个端点后，就可以加载我们的智能合约了。

#### 加载你的Hello World智能合约

要加载您的Hello World智能合约，您需要它的合约地址和ABI，如果您完成了本教程的第三部分，就可以在Etherscan上找到这两个信息。

一旦你有了你的API密钥，在你的根目录下创建一个.env文件，并将你的Alchemy Websockets网址添加到其中。之后，你的.env文件应该看起来像这样。

```js
REACT_APP_ALCHEMY_KEY = wss://eth-goerli.ws.alchemyapi.io/v2/
```

现在，我们已经准备好在我们的dApp中设置我们的Alchemy Web3端点了! 让我们回到嵌套在util文件夹中的interactive.js，并在文件的顶部添加以下代码。

```js
require('dotenv').config();
const alchemyKey = process.env.REACT_APP_ALCHEMY_KEY;
const { createAlchemyWeb3 } = require("@alch/alchemy-web3");
const web3 = createAlchemyWeb3(alchemyKey);

//export const helloWorldContract;
```

上面，我们首先从我们的.env文件中导入了Alchemy ket，然后将我们的alchemyKey传递给createAlchemyWeb3以建立我们的Alchemy Web3端点。

准备好这个端点后，就可以加载我们的智能合约了。

#### 加载你的Hello World智能合约

要加载您的Hello World智能合约，您需要它的合约地址和ABI，如果您完成了本教程的第三部分，就可以在Etherscan上找到这两个信息。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210301053016.gif)

> 如果你跳过了本教程的第三部分，你可以使用地址为[0x6f3f635A9762B47954229Ea479b4541eAF402A6A](https://ropsten.etherscan.io/address/0x6f3f635a9762b47954229ea479b4541eaf402a6a#code)的HelloWorld合约。[它的ABI可以在这里找到](https://ropsten.etherscan.io/address/0x6f3f635a9762b47954229ea479b4541eaf402a6a#code)。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210301053175.png)

有了我们的合约地址、ABI和Alchemy Web3端点，我们可以使用合约方法来加载我们的智能合约实例。将你的合约ABI导入interactive.js文件，并添加你的合约地址。

```js
const contractABI = require("../contract-abi.json");
const contractAddress = "0x6f3f635A9762B47954229Ea479b4541eAF402A6A";
```

现在我们终于可以取消对helloWorldContract变量的注释，并使用我们的AlchemyWeb3端点加载智能合约。

```js
export const helloWorldContract = new web3.eth.Contract(
  contractABI,
  contractAddress
);
```

回顾一下，你的interactive.js的前12行现在应该是这样的。

```js
require('dotenv').config();
const alchemyKey = process.env.REACT_APP_ALCHEMY_KEY;
const { createAlchemyWeb3 } = require("@alch/alchemy-web3");
const web3 = createAlchemyWeb3(alchemyKey);

const contractABI = require('../contract-abi.json')
const contractAddress = "0x6f3f635A9762B47954229Ea479b4541eAF402A6A";

export const helloWorldContract = new web3.eth.Contract(
  contractABI,
  contractAddress
);
```

现在，我们已经加载了我们的合同，我们可以实现我们的loadCurrentMessage函数了。

#### 在你的interactive.js文件中实现loadCurrentMessage

这个函数超级简单。就像我们在本教程系列的第二部分所做的那样，在这里我们要做一个简单的异步web3调用来读取我们的合同。我们的函数将返回存储在智能合约中的消息。

将你的interactive.js文件中的loadCurrentMessage更新为以下内容。

```js
export const loadCurrentMessage = async () => {
    const message = await helloWorldContract.methods.message().call();
    return message;
};
```

由于我们想在我们的用户界面中显示这个智能合约，让我们把HelloWorld.js组件中的useEffect函数更新为以下内容。

```js
//called only once
useEffect(async () => {
    const message = await loadCurrentMessage();
    setMessage(message);
}, []);
```

注意，我们希望我们的loadCurrentMessage在组件的第一次渲染时被调用一次。我们很快就会实现addSmartContractListener，以便在智能合约中的消息改变后自动更新用户界面。

在我们深入研究我们的监听器之前，让我们看看我们目前所拥有的东西吧 保存你的HelloWorld.js和interactive.js文件，然后转到http://localhost:3000/

你会注意到，当前的消息不再说 "没有连接到网络"。相反，它反映了存储在智能合约中的信息。有病啊!

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210301054927.png)

现在说说那个监听器...

#### 实现addSmartContractListener

如果你回想一下我们在本教程系列第一部分中写的`HelloWorld.sol`文件，你会记得有一个名为UpdateMessages的智能合约事件，在我们的智能合约的更新函数被调用后被发射出来（见第9和27行）。

```js
// Specifies the version of Solidity, using semantic versioning.
// Learn more: https://solidity.readthedocs.io/en/v0.5.10/layout-of-source-files.html#pragma
pragma solidity ^0.7.3;

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

> 智能合约事件是你的合约向你的前端应用程序传达区块链上发生的事情（即有一个事件）的一种方式，它可以 "监听 "特定的事件并在它们发生时采取行动。

addSmartContractListener函数将专门监听我们的Hello World智能合约的UpdateMessages事件，并更新我们的用户界面以显示新消息。

将addSmartContractListener修改为以下内容。

```js
function addSmartContractListener() {
  helloWorldContract.events.UpdatedMessages({}, (error, data) => {
    if (error) {
      setStatus("😥 " + error.message);
    } else {
      setMessage(data.returnValues[1]);
      setNewMessage("");
      setStatus("🎉 Your message has been updated!");
    }
  });
}
```

#### 让我们来分析一下当监听器检测到一个事件时会发生什么。

如果事件发出时发生了错误，它将通过我们的状态变量反映在用户界面中。

否则，我们将使用返回的数据对象。data.returnValues是一个索引为0的数组，数组中的第一个元素存储了之前的消息，第二个元素存储了更新后的消息。总之，在一个成功的事件中，我们将把我们的消息字符串设置为更新的消息，清除newMessage字符串，并更新我们的状态变量以反映新的消息已经在我们的智能合约上发布。

最后，让我们在useEffect函数中调用我们的监听器，以便它在HelloWorld.js组件第一次渲染时被初始化。总的来说，你的useEffect函数应该是这样的。

```js
useEffect(async () => {
    const message = await loadCurrentMessage();
    setMessage(message);
    addSmartContractListener();
}, []);
```

现在我们已经能够从我们的智能合约中读取信息了，如果能弄清楚如何向它写入信息，那就更好了！然而，要向我们的DApp写入信息，我们必须首先有一个连接到Ethereum钱包。然而，要写到我们的DApp，我们必须首先有一个以太坊钱包连接到它。

因此，接下来我们将解决设置我们的Ethereum钱包（Metamask），然后将其连接到我们的DApp上!

### 第4步：设置你的以太坊钱包

要向以太坊链写入任何东西，用户必须使用他们的虚拟钱包的私钥签署交易。在本教程中，我们将使用[Metamask](https://metamask.io/)，一个用于管理你的以太坊账户地址的浏览器中的虚拟钱包，因为它使这种交易签署对终端用户来说超级简单。

> 如果你想了解更多关于以太坊交易的运作方式，请查看[以太坊基金会](https://ethereum.org/en/developers/docs/transactions/)的这个页面。

#### 下载Metamask

你可以在这里[免费下载并创建一个Metamask账户](https://metamask.io/download.html)。当你在创建账户时，或者如果你已经有一个账户，确保切换到右上方的 "Ropsten测试网络"（这样我们就不会和真实的钱打交道）。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210301055802.png)

#### 从水龙头中添加ether

为了在以太坊区块链上签署交易，我们需要一些假的Eth。要获得Eth，你可以去[Ropsten faucet](https://faucet.ropsten.be/)，输入你的Ropsten账户地址，然后点击 "发送Ropsten Eth"。你应该很快在你的Metamask账户中看到Eth!

#### 检查您的余额

为了仔细检查我们的余额，让我们使用[Alchemy’s composer tool](https://composer.alchemyapi.io/?composer_state={"network"%3A0%2C"methodName"%3A"eth_getBalance"%2C"paramValues"%3A[""%2C"latest"]})做一个https://docs.alchemyapi.io/alchemy/documentation/alchemy-api-reference/json-rpc#eth_getbalance请求。这将返回我们钱包中的Eth数量。在你输入你的Metamask账户地址并点击 "发送请求 "后，你应该看到这样的响应。

```js
{"jsonrpc": "2.0", "id": 0, "result": "0xde0b6b3a7640000"}
```

> 注意：这个结果是以wei而不是eth为单位的。Wei被用作最小的以太坊面额。从wei到eth的转换是：1 eth = 10¹⁸ wei。因此，如果我们将0xde0b6b3a7640000转换为十进制，我们得到1*10¹⁸，等于1 eth。

唷! 我们的假钱都在这里了! 🤑

### 第5步：将Metamask连接到你的用户界面

现在我们的Metamask钱包已经设置好了，让我们把我们的DApp连接到它!

#### connectWallet函数

在我们的interactive.js文件中，让我们实现connectWallet函数，然后我们可以在HelloWorld.js组件中调用它。

让我们把connectWallet修改为以下内容。

```js
export const connectWallet = async () => {
  if (window.ethereum) {
    try {
      const addressArray = await window.ethereum.request({
        method: "eth_requestAccounts",
      });
      const obj = {
        status: "👆🏽 Write a message in the text-field above.",
        address: addressArray[0],
      };
      return obj;
    } catch (err) {
      return {
        address: "",
        status: "😥 " + err.message,
      };
    }
  } else {
    return {
      address: "",
      status: (
        <span>
          <p>
            {" "}
            🦊{" "}
            <a target="_blank" href={`https://metamask.io/download.html`}>
              You must install Metamask, a virtual Ethereum wallet, in your
              browser.
            </a>
          </p>
        </span>
      ),
    };
  }
};
```

那么，这个巨大的代码块到底是做什么的？

嗯，首先，它检查`window.ethereum`是否在你的浏览器中启用。

> window.ethereum是由Metamask和其他钱包提供商注入的全球API，允许网站请求用户的以太坊账户。如果被批准，它可以从用户连接的区块链中读取数据，并建议用户签署信息和交易. 请查看[Metamask文档](https://docs.metamask.io/guide/ethereum-provider.html#table-of-contents)以了解更多信息!

如果`window.ethereum`不存在，那么就意味着Metamask没有安装。这将导致一个JSON对象被返回，其中返回的地址是一个空字符串，而状态JWX对象转达了用户必须安装Metamask。

现在，如果window.ethereum存在，那么事情就变得有趣了。

使用try/catch循环，我们将尝试通过调用`window.ethereum.request({ method: "eth_requestAccounts" })`连接到Metamask；调用此函数将在浏览器中打开Metamask，用户将被提示将他们的钱包连接到你的dApp。

如果用户选择了连接，`method: "eth_requestAccounts"`将返回一个数组，其中包含所有连接到dApp的用户的账户地址。总的来说，我们的connectWallet函数将返回一个JSON对象，其中包含这个数组中的第一个地址（见第9行）和一个统计信息，提示用户写一个信息给智能合约。

如果用户拒绝连接，那么JSON对象将包含一个返回地址的空字符串和一个反映用户拒绝连接的状态消息。

现在我们已经写好了这个connectWallet函数，下一步是将其调用到我们的HelloWorld.js组件。

#### 将connectWallet函数添加到你的HelloWorld.js UI组件中

导航到HelloWorld.js中的connectWalletPressed函数，并将其更新为以下内容。

```js
const connectWalletPressed = async () => {
  const walletResponse = await connectWallet();
  setStatus(walletResponse.status);
  setWallet(walletResponse.address);
};
```

注意到我们的大部分功能是如何从interactive.js文件中抽象出来给我们的HelloWorld.js组件的吗？这是为了让我们符合M-V-C的范式!

在connectWalletPressed中，我们简单地对导入的connectWallet函数进行等待调用，并使用其响应，通过其状态钩子更新我们的状态和walletAddress变量。

现在，让我们保存这两个文件（HelloWorld.js和interactive.js）并测试一下我们到目前为止的用户界面。

在http://localhost:3000/ 页面上打开你的浏览器，然后按下页面右上方的 "连接钱包 "按钮。

如果你安装了Metamask，你应该被提示将你的钱包连接到你的dApp。接受连接的邀请。

您应该看到钱包按钮现在反映出您的地址已被连接! Yasssss 🔥

接下来，尝试刷新页面...这很奇怪。我们的钱包按钮正在提示我们连接Metamask，尽管它已经连接了...

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210301055541.gif)

重新加载时的问题页面

然而，不用担心! 我们可以通过实现getCurrentWalletConnected来解决这个问题（明白吗？ 😅），它将检查一个地址是否已经连接到我们的DApp，并相应地更新我们的用户界面

#### getCurrentWalletConnected函数

将 interact.js 文件中的 getCurrentWalletConnected 函数更新为以下内容。

```js
export const getCurrentWalletConnected = async () => {
  if (window.ethereum) {
    try {
      const addressArray = await window.ethereum.request({
        method: "eth_accounts",
      });
      if (addressArray.length > 0) {
        return {
          address: addressArray[0],
          status: "👆🏽 Write a message in the text-field above.",
        };
      } else {
        return {
          address: "",
          status: "🦊 Connect to Metamask using the top right button.",
        };
      }
    } catch (err) {
      return {
        address: "",
        status: "😥 " + err.message,
      };
    }
  } else {
    return {
      address: "",
      status: (
        <span>
          <p>
            {" "}
            🦊{" "}
            <a target="_blank" href={`https://metamask.io/download.html`}>
              You must install Metamask, a virtual Ethereum wallet, in your
              browser.
            </a>
          </p>
        </span>
      ),
    };
  }
};
```



这段代码与我们在上一步刚刚写好的connectWallet函数非常相似。

主要的区别是，我们没有调用方法eth_requestAccounts（打开Metamask让用户连接他们的钱包），而是在这里调用方法eth_accounts，它只是返回一个包含当前连接到我们dApp的Metamask地址的数组。

主要的区别是，我们没有调用方法eth_requestAccounts，打开Metamask让用户连接他们的钱包，而是在这里调用方法eth_accounts，它只是返回一个包含当前连接到我们dApp的Metamask地址的数组。



```js
useEffect(async () => {
    const message = await loadCurrentMessage();
    setMessage(message);
    addSmartContractListener();

    const {address, status} = await getCurrentWalletConnected();
    setWallet(address);
    setStatus(status);

}, []);
```

注意，我们使用调用getCurrentWalletConnected的响应来更新我们的walletAddress和状态变量。

现在你已经添加了这些代码，让我们试着刷新我们的浏览器窗口。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210301056131.gif)

很好! 这个按钮应该说你已经连接上了，并显示你所连接的钱包的地址预览--即使在你刷新之后也是如此

#### 实现addWalletListener

dApp钱包设置的最后一步是实现钱包监听器，这样当我们的钱包状态改变时，我们的用户界面就会更新，例如当用户断开连接或切换账户时。

在你的HelloWorld.js文件中，修改你的addWalletListener函数如下。

我打赌你甚至不需要我们的帮助来理解这里发生了什么😉，但为了彻底了解，让我们快速分解它。

首先，我们的函数检查window.ethereum是否被启用（即Metamask被安装）。

如果没有，我们只需将我们的状态变量设置为一个JSX字符串，提示用户安装Metamask。

如果它被启用，我们在第3行设置监听器window.ethereum.on("accountsChanged")，监听Metamask钱包的状态变化，其中包括当用户连接一个额外的帐户到DApp，切换帐户，或断开一个帐户。如果至少有一个账户被连接，walletAddress状态变量将被更新为监听器返回的账户阵列中的第一个账户。否则，walletAddress被设置为一个空字符串。

最后但同样重要的是，我们必须在我们的useEffect函数中调用它。

```js
useEffect(async () => {
    const message = await loadCurrentMessage();
    setMessage(message);
    addSmartContractListener();

    const {address, status} = await getCurrentWalletConnected();
    setWallet(address)
    setStatus(status);

    addWalletListener();
}, []);
```



就这样了! 我们已经成功地完成了所有的钱包功能的编程! 现在进入我们的最后一项任务：更新存储在我们的智能合约中的消息

### 第6步：实现updateMessage函数

好了，家人，我们已经到了最后的关头了 在你的interactive.js文件的updateMessage中，我们要做以下工作。

确保我们希望在我们的智能联系人中发布的信息是有效的

使用Metamask签署我们的交易

从我们的HelloWorld.js前台组件中调用这个函数

#### 输入错误处理

自然地，在函数的开始有某种输入错误处理是有意义的。

如果没有安装Metamask扩展，没有连接钱包（即传入的地址是一个空字符串），或者消息是一个空字符串，我们希望我们的函数提前返回。让我们为updateMessage添加以下错误处理。

```js
export const updateMessage = async (address, message) => {
  if (!window.ethereum || address === null) {
    return {
      status:
        "💡 Connect your Metamask wallet to update the message on the blockchain.",
    };
  }

  if (message.trim() === "") {
    return {
      status: "❌ Your message cannot be an empty string.",
    };
  }
};
```

现在，它有适当的输入错误处理，是时候通过Metamask签署交易了

#### 签署我们的交易

如果你对传统的web3以太坊交易已经很熟悉了，那么我们接下来写的代码将非常熟悉。在你的输入错误处理代码下面，给updateMessage添加以下内容。

```js
//set up transaction parameters
 const transactionParameters = {
    to: contractAddress, // Required except during contract publications.
    from: address, // must match user's active address.
    data: helloWorldContract.methods.update(message).encodeABI(),
  };

//sign the transaction
  try {
    const txHash = await window.ethereum.request({
      method: "eth_sendTransaction",
      params: [transactionParameters],
    });
    return {
      status: (

          ✅{" "}

            View the status of your transaction on Etherscan!


          ℹ️ Once the transaction is verified by the network, the message will
          be updated automatically.

      ),
    };
  } catch (error) {
    return {
      status: "😥 " + error.message,
    };
  }
```

让我们来分析一下发生了什么。首先，我们设置了我们的交易参数，其中。

to 指定收件人地址（我们的智能合约）。

from 指定交易的签署者，即我们传入函数的地址变量

data包含对我们的Hello World智能合约的更新方法的调用，接收我们的messagestring变量作为输入。

然后，我们进行一个等待调用，window.ethereum.request，在这里我们要求Metamask签署交易。注意，在第11行和第12行，我们指定了我们的eth方法，eth_sendTransaction，并传入我们的transactionParameters。

在这一点上，Metamask将在浏览器中打开，并提示用户签署或拒绝交易。

- 如果交易成功，该函数将返回一个JSON对象，其中的status JSX字符串提示用户查看Etherscan，以获得更多关于他们交易的信息。
- 如果交易失败，该函数将返回一个JSON对象，其中的status字符串会转达错误信息。

总的来说，我们的 updateMessage 函数应该是这样的。

```js
export const updateMessage = async (address, message) => {

  //input error handling
  if (!window.ethereum || address === null) {
    return {
      status:
        "💡 Connect your Metamask wallet to update the message on the blockchain.",
    };
  }

  if (message.trim() === "") {
    return {
      status: "❌ Your message cannot be an empty string.",
    };
  }

  //set up transaction parameters
  const transactionParameters = {
    to: contractAddress, // Required except during contract publications.
    from: address, // must match user's active address.
    data: helloWorldContract.methods.update(message).encodeABI(),
  };

  //sign the transaction
  try {
    const txHash = await window.ethereum.request({
      method: "eth_sendTransaction",
      params: [transactionParameters],
    });
    return {
      status: (

          ✅{" "}

            View the status of your transaction on Etherscan!


          ℹ️ Once the transaction is verified by the network, the message will
          be updated automatically.

      ),
    };
  } catch (error) {
    return {
      status: "😥 " + error.message,
    };
  }
};
```

最后但并非最不重要的是，我们需要将updateMessage函数连接到我们的HelloWorld.js组件。

#### 将updateMessage连接到HelloWorld.js的前台

我们的onUpdatePressed函数应该对导入的updateMessage函数进行等待调用，并修改状态变量以反映我们的交易是成功还是失败。

```js
const onUpdatePressed = async () => {
    const { status } = await updateMessage(walletAddress, newMessage);
    setStatus(status);
};
```

这是超级干净和简单的。😌 你猜怎么着......你的DAPP已经完成了！！。

让我们测试一下 "更新 "按钮!

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210301057159.gif)

### 第7步：制作您自己的自定义DApp 🚀

Wooooo，你已经走到了本教程的最后! 回顾一下，你学会了如何。

- 将Metamask钱包连接到你的dApp项目上
- 使用Alchemy Web3 API从您的智能合约中读取数据
- 使用Metamask签署Ethereum交易

现在，您已经完全有能力应用本教程中的技能来构建您自己的自定义dApp项目了 如果您有任何问题，请不要犹豫，在Alchemy Discord中向我们寻求帮助。🧙♂️

## 9、其他资源

您觉得我们的智能合约介绍如何？

还有很多东西要学。特别是，扎实掌握 Solidity 的基础知识并使用以太坊中一些最常见的框架进行智能合约开发会非常有用。

我们建议检查以下工具： 

- [Solidity 示例：](https://solidity-by-example.org/)Solidity主要功能的快节奏编码演练：数据类型、结构、修饰符和简单的应用程序。
- [OpenZeppelin Docs：](https://docs.openzeppelin.com/) 用于构建智能合约的各种教学教程和工具，特别是提供可重用的模块化智能合约库，您可以将其放入项目中。
- [Scaffold-Eth：](https://github.com/scaffold-eth/scaffold-eth) 围绕尽快实施小型编码项目的各种以太坊编码挑战。

> 译者注：
> 文章代码较多，如果疑问，请查看github源码：https://github.com/alchemyplatform/hello-world-part-four-tutorial。
> 文章原文链接：https://www.web3.university/tracks/create-a-smart-contract/integrate-your-smart-contract-with-a-frontend

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)