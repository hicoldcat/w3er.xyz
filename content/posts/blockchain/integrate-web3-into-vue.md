---
title: 将 Web3 集成到 Vue 应用程序中
description: null
author: 李留白
weight: 0
date: 2022-11-13T12:03:38.202Z
lastmod: 2022-11-13T12:36:25.289Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211132008850.png
---

借助 Web3，可以在区块链网络上构建去中心化应用程序，让用户完全拥有自己的数据。区块链网络使用智能合约来处理去中心化应用程序的所有后端逻辑。

作为最受欢迎的[可编程区块链网络](https://blog.logrocket.com/deploy-ethereum-smart-contracts-cosmos-ecosystem/)，以太坊允许开发人员构建和操作去中心化应用程序 (DApp)、去中心化金融 (DeFi)、智能合约和不可替代代币 (NFT)。

在本文中，我们将介绍在 Vue 应用程序中使用 Web3 的基础知识，包括安装过程和与智能合约的交互。要继续阅读本文，您需要以下内容：

- 对 Vue CLI 的理解
- Web3的基本[理解](https://blog.logrocket.com/solidity-javascript-web3-blockchain-applications/#what-web3-programming)
- 熟悉 Solidity 智能合约以及如何部署它们的知识

## 什么是 Web3？

Web3是基于区块链技术的新一代互联网，提倡去中心化和基于代币的经济。调用智能合约和发送交易构成了区块链网络上的大部分互动。

## 什么是智能合约？

智能合约是一种存在于区块链网络上的自动执行计算机软件。区块链在部署后立即执行智能合约。当合约被执行时，它会创建 DApp 用来扩展其功能的接口。

考虑以下简单的 Solidity 智能合约：

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.8.0;

contract Contract {
    string private text;

    function speak() public view returns (string memory) {
        return text;
    }

    function changeText(string memory newText) public {
        text = newText;
    }
}
```

当您将上面的合约部署到区块链网络时，它会创建两个接口方法，`speak`返回文本字符串，以及`changeText`更改`speak`返回的字符串。

## 项目设置

如果您已经准备好集成的 Vue 项目，则可以跳过本节。如果不这样做，请使用以下命令为 npx 包管理器创建项目：

```
npx vue create web3-project
```

如果您使用 Yarn 包管理器，请改用以下命令：

```bash
yarn vue create web3-project
```

在菜单提示中，选择**Vue3**或**Vue2**。选择哪一个并不重要，因为您可以以相同的方式将 Web3 集成到它们中。创建项目后，在您的`App.vue`文件中编写以下代码：

```javascript
<template>
  <div>
    <!-- connect-wallet button is visible if the wallet is not connected -->
  <button v-if="!connected">Connect wallet</button>
    <!-- call-contract button is visible if the wallet is connected -->
    <button v-if="connected">Call contract</button>
    {{ contractResult }}
  </div>
</template>

<script>

export default {
  name: 'App',

  data() {
    return {
      connected: false,
      contractResult: '',
    }
  },
}
</script>
```

在`App.vue`中，我们有两个按钮；第一个用于连接钱包，第二个用于调用智能合约方法。

在用户将他们的钱包连接到 Web 应用程序之前，`call contract`按钮是隐藏的。我们的应用程序使用`connected`状态变量来保存钱包连接状态。

## 设置 Web3

在您的项目目录中，运行以下命令以使用 npm 安装 Web3 包：

```bash
npm install web3
```

如果您使用的是 Yarn，请运行以下命令：

```bash
yarn add web3
```

安装完包后，我们将Web3导入到`App.vue`文件中，运行项目来测试一下它的兼容性：

```javascript
...
<script>
import Web3 from 'web3'
export default {
  name: 'App',
…
```

如果您没有收到除`Web3 is imported but never used` 之外的任何错误消息，则 Web3 与您的框架兼容。但是，如果框架产生其他错误，您必须切换到旧版本的 Vue。Vue 3 使用的 Webpack 5 模块会出现Web3[兼容性问题。](https://stackoverflow.com/questions/71245845/error-webpack-5-after-installing-web3-and-implementing-into-react-native-app)

## 连接我们的钱包

MetaMask 是一个[允许用户存储钱包并将其连接](https://blog.logrocket.com/understanding-resolving-metamask-error-codes/)到 Web3 应用程序的应用程序。Metamask 可以安装在所有主流浏览器中。通过将钱包连接到网站，用户可以执行以下操作：

- 在网站上买卖代币
- 管理区块链网络上的数字资产
- 对网站上的帐户进行交易
- 与区块链网络上的智能合约交互

当您在浏览器中安装 MetaMask 扩展时，该扩展会创建一个`window.ethereum`对象。Web3 应用程序使用`window.ethereum`对象访问用户的钱包并连接到以太坊网络。

`window.ethereum.request()`方法提示用户连接他们的 MetaMask 钱包：

```javascript
ethereum.request({ method: 'eth_requestAccounts' })
```

我们可以使用此提示将钱包连接事件注册到我们的`connect wallet`按钮。将以下代码添加到您的`App.vue`文件中：

```javascript
<template>
  <div>
    <!-- "connect" click event is registered -->
    <button v-if="!connected" @click="connect">Connect wallet</button>
    <button v-if="connected">Call contract</button>
    {{ contractResult }}
  </div>
</template>

<script>
export default {
  name: 'App',

  data() {
    return {
      connected: false,
      contractResult: '',
    }
  },

  methods: {

    connect: function () {
        // this connects to the wallet
      
      if (window.ethereum) { // first we check if metamask is installed
        window.ethereum.request({ method: 'eth_requestAccounts' })
          .then(() => {
            this.connected = true; // If users successfully connected their wallet
          });
      }
    }
  }
}
</script>
```

当用户点击连接钱包按钮时，会出现一个MetaMask提示，允许用户选择他们想要连接的账户。在用户连接他们的钱包后，`App.vue`文件中的`connected`状态变量变为`true`。

## 与已部署的智能合约交互

部署的智能合约创建了 Web3 应用程序与之交互的接口。要访问这些接口，我们需要向 Web3 提供 ABI、智能合约接口的描述以及合约地址，即合约在以太坊网络上的位置。

[在写这篇文章之前，我在Rinkeby 测试网络](https://www.rinkeby.io/#stats)上部署了一个智能合约。我将在以下示例中使用它的 ABI 和地址，但如果您已经部署了智能合约，则可以使用该合约的地址和 ABI，而不是此处包含的那些。

要与已部署的合约交互，请使用`window.ethereum`对象创建一个 Web3 实例：

```javascript
let web3 = new Web3(window.ethereum);
```

然后，使用其 ABI 和地址创建对已部署合约的引用：

```javascript
let contract = new web3.eth.Contract(abi, contractAddress)
```

初始化合约后，您可以与之交互。如果合约有一个名为 `greet`的接口方法，我们使用下面的代码调用它：

```javascript
contract.methods.greet().call()
```

现在，我们将修改`call contract`按钮以使用`greet`您部署的合约的方法调用合约：

```vue
<!-- vue -->
<button v-if="connected" @click="callContract">Call contract</button>
```

接下来，在Vue对象中创建一个`call contract`按钮的callContract方法。

```javascript
callContract: function () {
  // method for calling the contract method
  let web3 = new Web3(window.ethereum);
  let contractAddress = '0xC0B2D76aB95B7E31E241ce713ea1C72d0a50588e';

  let abi = JSON.parse(`[{"inputs": [],"stateMutability": "nonpayable","type": "constructor"},{"inputs": [],"name": "greet","outputs": [{"internalType": "string","name": "","type": "string"}],"stateMutability": "view","type": "function"}]`);


  let contract = new web3.eth.Contract(abi, contractAddress);

  contract.methods.greet().call()
    .then(result => this.contractResult = result);
}
```

该`callContract`方法将被注册为按钮的点击事件，并在部署的智能合约中调用`greet`方法：

```vue
<template>
  <div>
    <button v-if="!connected" @click="connect">Connect wallet</button>

    <!-- "callContract" event handler is added -->
>    <button v-if="connected" @click="callContract">Call contract</button>

    <!-- displays the result of the contract -->
    {{ contractResult }}
  </div>
</template>

<script>
import Web3 from 'web3'

export default {
  name: 'App',

  data() {
    return {
      connected: false,
      contractResult: '',
    }
  },

  methods: {

    connect: function () {
      let ethereum = window.ethereum;
      if (ethereum) {
      ethereum.request({ method: 'eth_requestAccounts' })
       .then(() => {
            this.connected = true;
          });
      }
    },

   callContract: function () {
       // method for calling the contract method
      let web3 = new Web3(window.ethereum);
      let contractAddress = '0xC0B2D76aB95B7E31E241ce713ea1C72d0a50588e';

      let abi = JSON.parse(`[{"inputs": [],"stateMutability": "nonpayable","type": "constructor"},{"inputs": [],"name": "greet","outputs": [{"internalType": "string","name": "","type": "string"}],"stateMutability": "view","type": "function"}]`);


      let contract = new web3.eth.Contract(abi, contractAddress);

      contract.methods.greet().call()
        .then(result => this.contractResult = result);
    }

  }
}
</script>
```

用户连接钱包并调用合约后，应用程序应如下图所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211132022730.png)

## 结论

在本文中，我们学习了如何使用 Vue 创建一个使用以太坊区块链网络的 Web3 应用程序，连接一个 MetaMask 钱包，并与智能合约进行交互。如您所见，只要您熟悉 Solidity，将 Web3 集成到 Vue 应用程序中是相当简单的。Web3 为隐私和数据所有权带来了重大好处，我们探索了构成这一过程的一些工具和概念，例如智能合约。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
