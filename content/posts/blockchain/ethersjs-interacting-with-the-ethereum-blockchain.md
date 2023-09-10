---
title: Ethers.js：与以太坊区块链交互
description: null
author: 李留白
weight: 0
date: 2023-09-10T16:14:39.242Z
lastmod: 2023-09-10T16:21:09.870Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202309110014057.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202309110014057.png)

## 介绍

**Ethers.js**是一个强大而紧凑的库，用于实现与以太坊区块链的交互。它可以帮助将**DApp**与**区块链**网络连接起来，使用 ethers.js 的开发人员还可以访问以太坊区块链的完整功能。

## Ethers.js 的特点

Ethers.js 是一个 JavaScript 库，提供广泛的功能。它有助于确保客户端中**私钥的安全存储**，以及导入和导出 **JSON 钱包** 的灵活性。

**ethers.js 的其他重要功能是通过 JSON-RPC 或任何节点服务提供商连接到以太坊节点**的灵活性。它可以帮助促进与以太坊区块链相关的不同需求的综合功能。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202309110017734.png)

## Ethers.js 的重要实用工具

应用程序二进制接口、地址、编码实用程序、常量和字节操作是 ethers.js 中一些最重要的实用程序。除了 Web 实用程序之外，其他重要实用程序还包括 HD 钱包、哈希方法、字符串、日志记录、签名密钥、交易、显示逻辑和输入。在本文的这一部分中，我们将快速总结 ethers.js 中的每个实用程序。

### 应用程序二进制接口

应用程序二进制接口（ABI）作为不同片段的集合，提供与不同合约组件交互的规范。ether.js 库中的 ABI 利用接口根据类型组织片段，同时提供编码、解码和使用各个组件的灵活性。

### Address

ethers.js 中的地址指的是 20 字节的字符串，其中还包括混合情况的选项，从而产生校验和地址。ethers.js 地址最流行的格式之一是 ICAP 地址格式。它通过利用银行业的 IBAN 格式，提供了在以太坊地址中包含校验和的可能性。

### 字节操作

另一个重要的实用程序有助于理解“如何在 JavaScript 中使用 Ether？”的答案。是字节操作。您可以找到多个可以与以太坊交互的高级 API，如合约和提供商 API 的示例所示。然而，大多数低级访问条件需要涉及字节操作的操作。大多数字节操作操作仅限于内部使用。另一方面，它们还可以帮助标准化不同方法和函数生成的输出的二进制数据表示。

### 显示逻辑和输入

开发人员还应该了解 ethers.js 库中的显示逻辑和输入实用程序以创建应用程序。确保将用户友好的字符串（通常显示以太币）转换为合约可以理解的机器可读值非常重要。

### 哈希算法

Ethers JS 教程中的哈希算法对于 DApp 和智能合约的操作至关重要。整个区块链生态系统实现了多种哈希算法，复杂的应用程序需要实用程序来确保常见任务。例如，加密哈希函数用作一组专用的哈希函数。除此之外，您还可以找到其他哈希算法，例如基于 HMAC 的算法、哈希助手、Solidity 哈希算法和类型化数据编码器。

### HD钱包

Ethers.js 还包含对 HD 钱包或分层确定性钱包的引用。**它是作为比特币**的标准而创建的，尽管它已经扩展到使用 secp256k1 私钥的广泛区块链。

HD钱包的解释分为BIP-32、BIP-39和BIP-44三个方面。BIP-32 提供分层确定性描述，而 BIP-39 提供从助记词短语导出 BIP-32 种子所需的方法。BIP-44 是BIP-32 与任何类型的新区块链更轻松**互操作**的关键标准。

### 日志

ethers.js 库中的实用程序列表进一步引用了日志记录实用程序。想要学习 ethers.js 的考生必须认识到日志实用程序在简化 ethers.js 可用的错误管理功能方面的重要性。您应该了解**Logger**库，它具有零依赖且轻量级的特点，从而确保更容易包含在不同的库中。另一个重要的日志记录实用程序是审查功能。

### 字符串

ethers JS typescript兼容性也引起了人们对字符串的关注，字符串是 ethers.js 的另一个重要实用工具。字符串作为人类可读的输入或输出的表示。然而，对于**区块链应用**来说，它们的意义更大。字符串有助于正确管理人类可读的数据，以避免资金损失、权限错误和资产所有权问题。

## Ethers.js 命令入门

### 安装中

在安装 Ether.js 之前，您需要在计算机上安装**NodeJS 。**在计算机中设置后，在终端中键入此命令。

```javascript
// this will install ethers library
npm install --save ethers
```

### 导入

我们将看到如何在将 ether.js 安装到我们的文件夹中后导入它。有javascript和react两种方式。

```javascript
// node.js
const { ethers } = require("ethers");

// ES6
import { ethers } from "ethers";
```

### 网络和链ID

ethers.js使用方法的描述还指出了获取网络和链ID的方法。链ID可以根据网络类型而变化，主网的链ID为1。

```javascript
const chainId = network.chainId;

const network = await provider.getNetwork();
```

### 账户

您可以使用以下命令获取帐户列表。

```javascript
const accounts = await provider.listAccounts();
```

### 余额

Balance命令是帮助获得特定地址Balance的重要要求。以下命令可帮助您获取特定地址的余额。

```javascript
const balance = await provider.getBalance(‘address’);
```

### 连接钱包

connect 命令可以帮助通过钱包连接到以太坊。以下是使用 Metamask 连接以太坊的命令。

```javascript
const provider = new ethers.provider.Web3Provider(window.ethereum);
```

### 连接远程过程调用

使用 RPC 连接以太坊的命令如下。**命令中的 'url' 指的是节点服务提供商**平台的 URL ，例如**Alchemy**或**Infura**。

```javascript
const provider = new ethers.provider.JsonRpcProvider('url');
```

### 发送交易

要使用 JS 发送以太币，需要使用这些命令。

```javascript
const tx = signer.sendTransaction({
    to: "mrinnnmoy.nft",
    value: ethers.utils.parseEther("1.0")
});
```

### 签署消息

要在 ethers.js 库中签署消息，请使用这些命令。

```javascript
signature = await signer.signMessage("Hello World !!!");
```

### 查询合约

Ethers JS 教程中最重要的亮点是用于查询合约的命令集。以下是用于查询合约不同详细信息的一些重要命令。

```javascript
// Obtain the ERC-20 token name
const tokenName = await daiCOntract.name("Contract-Address");

// Obtain the ERC-20 token symbol
const tokenSymbol = await daiContract.symbol("Contract-Address");

// Obtain the balance of a specific address
balance = await daiContract.balanceOf("mrinnnmoy.nft");
```

### 创建合约实例

创建合约实例是强制性细节之一。确保用户必须拥有钱包并且始终保持连接非常重要。开发者可以使用以下命令创建合约实例。

```javascript
Const contract = new ethers.Contract (‘address’, ‘abi’. ‘signer’);
```

## 合约事件监听器

学习 Ethers.js 过程中另一个值得注意的方面体现在合约事件监听器功能上。您还可以使用该命令启用实用程序来侦听合约中生成的事件。

```javascript
contract.on(`event`, `listener`);
```

# 结论

这篇 ethers.js 文章大纲全面概述了该语言的基础。最重要的是，它强调了ethers.js 库在区块链开发中的优势的关键特征。通用的开源库除了具有适应性优势之外，还确保了安全性和透明度。由于其模块化结构，ether.js 库可以通过合并更多功能来扩展。

以下是我用来学习**区块链开发的**资源链接。

- [**Code Eater**](https://www.youtube.com/playlist?list=PLgPmWS2dQHW_CuryjGPkyH0PNXJ2B0hAk)
- [**LearnWeb3 DAO**](https://learnweb3.io/)
- [**Alchemy University**](https://university.alchemy.com/)
- [**Metaschool**](https://metaschool.so/)

>原文：https://mrinnnmoy.hashnode.dev/ethersjs-interacting-with-the-ethereum-blockchain