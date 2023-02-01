---
title: 库对比：web3js 与 ethersjs
description: null
author: 李留白
weight: 0
date: 2023-01-04T03:05:34.600Z
lastmod: 2023-01-09T07:08:58.057Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202301091450680.png
draft: true
---

每一个可编程的区块链都有一套SDK或库，帮助连接并与特定的区块链通信。今天的大多数区块链都依赖于以太坊网络，使其与EVM兼容。如果创建了ETH库，就有可能在任何与EVM兼容的区块链上使用它。然而，通信是可以使用JSON-RPC协议的。

在这篇文章中，我们将对两个流行的ETH库进行比较。你可以了解到他们之间的差异，他们的优点和缺点。这篇文章也可以帮助你决定在未来的项目中使用什么库。

# 用于区块链的 JavaScript

自 1995 年 Brendan Eich 发明 JavaScript 以来，它已发展成为互联网上最常用的编程语言，用于构建当今使用的许多工具。据[Statista](https://www.statista.com/statistics/793628/worldwide-developer-survey-most-used-languages/)称，软件开发人员中最常用的语言是 JavaScript。

想象一下你需要解决一个特定的问题；你知道的唯一语言是 JavaScript。应该可以使用 JavaScript 来解决这个问题，而不是学习一门新的高级语言来解决问题。许多开发人员都面临着这个障碍，并找到了通过构建可以通过节点提供商连接到区块链的 SDK/库来使用 JavaScript 构建 dApp 的方法。

然而，这种变通降低了在区块链空间中构建的入门级别。您无需学习 Rust 和 Solidity 等高级语言即可为[Solana](https://solana.com/)或以太坊链构建[dApp](https://ethereum.org/en/)。

此外，降低区块链开发的入门水平是JavaScript的众多好处之一。

# 什么是库

在编写 dApp 时，您可能会发现需要为区块链上的特定交易重复的特定代码。此代码/程序可能很长，您必须为新项目继续编写此代码。无需重复此程序，您可以使用代码片段自动执行该过程。您可以在需要时随时将此库插入到您的项目中。这种方法可以加快开发速度并消除重复。

# JavaScript 模块

Javascript 模块是包含 JavaScript 函数或代码簇的典型文件。您可以将它们提取到不同的文件中，而不是让函数遍布您的代码。创建模块使更改变得非常容易，因为您不必开始查看大型代码库来查找特定函数来更新变量或模式。

由于Javascript本身不支持类，所以JavaScript模块模仿了类。类帮助开发人员在单个对象中将方法和字段设为公共和私有。通过这种模式，我们可以使用 export 和 import 关键字在我们的 JavaScript 项目中创建和使用函数。请参见下面的示例：

```typescript
export function myFunction(value1, value2) {
  return value1 * value2;
}
```

从上面的代码中，我们已经将这个函数公开。为了使用这个函数，我们可以使用如下所示的 import 关键字：

```typescript
import myFunction from "./value.js"
```

现在我们了解了 JavaScript 模块。让我们在下一节中讨论 ethers.js 库。

# Ethers.js

自 Ethers.js 诞生以来，它经历了稳定的增长，甚至成为以太坊开发人员中最常用的基于 JavaScript 的 web3 库之一。

该工具可帮助 Javascript 开发人员与以太坊链和任何以太坊虚拟机 (EVM) 兼容的区块链进行交互。

一位名叫 Richard Moore 的加拿大软件工程师在 2015 年创建了 ethers.js 作为 web3.js 的替代品。

Ethers.js 以轻量级着称。它的不同之处在于它管理密钥和与区块链交互的方式。在 ethers.js 中，有两个实体处理上述问题：

1. 使用私钥签署交易的钱包。

2. 使用 JSON-RPC URL 连接到区块链的提供程序。Ethers.js 检查状态并发送交易。

## Ethers.js 的优势

- **库大小 -** Ethers.js 压缩后为 88KB，未压缩时为 284KB。
- **ENS** - ENS 就像区块链中的 DNS。无论在哪里使用以太坊地址，也可以在那里使用 .eth 地址。您无需样板代码即可轻松设置域名。
- **测试 -**ethers.js经过广泛测试。其中许多测试用例都是由 Richard Moore 亲自编写的。有超过 10,000 个测试用例。
- **许可**——由于 ethers.js 是在 MIT 许可下发布的，任何人都可以免费使用和修改它。尽管如此，只要修改的源代码在 LGPL-3.0 许可下可用，就可以更改库。

```
*  **Performance** - Considering the small size, it will be a good idea to use it on the front end as it will drastically improve performance.


## Drawbacks of Ether.js

* Ethers.js is relatively new. Having bugs in your code is unavoidable, so finding answers in places like StackOverflow might not be enough.

* Many foundational companies actively use web3.js for their core projects, and if you need to work for such companies, you might need to spend some more time learning web3.js.
```

## Ethers.js 模块

在最近的部分中，在我们讨论 Ethers.js 之前，我们查看了 JavaScript 模块，ethers.js 有四 (4) 个模块，它们是 ethers.js API 的核心。

**Ethers.Provider**

要了解`Ethers.Provider`模块的作用，必须了解什么是节点。

要访问区块链网络，您需要在您的机器上运行一个节点来访问它。该节点连接到其他节点以检查人与人之间的交易是否有效，同时它存储有关区块链状态的信息。

区块链完全由节点构建或组成。这些节点由世界各地的个人运行。这意味着没有中央服务器或单一的事实来源。这就是它去中心化的原因。将节点想象成汽车的汽油，如果它是空的，它就不会启动。

节点的建立可能是一个痛苦的过程，它可能需要一个星期或更长时间。幸运的是，存在 Alchemy、infura 和 Quicknode 等提供商。他们为您设置了节点。他们所需要的只是让您建立连接，并且您可以访问区块链。

**Ethers.Contract**

该模块用于部署智能合约并与之交互。它提供了监听智能合约事件的能力（打包函数）。但是，您可以使用该模块来调用有关智能合约的信息以及智能合约提供的功能。

**Ethers.utils**

该模块可以帮助您格式化钱包余额等数据并处理其他用户输入。

```typescript
const accountChangedHandler = async (newAccount) => {
        const address = await newAccount.getAddress();
        setDefaultAccount(address);
        const balance = await newAccount.getBalance()
        setUserBalance(ethers.utils.formatEther(balance));
        await getuserBalance(address)
    }
```

从上面的代码中，在从钱包中获取地址后，我们检索钱包的余额，但由于返回值以 Wei 格式输出，我们使用 .formatEther 使其更易于阅读。

**Ethers.Wallet**

该模块允许您连接到现有地址。但是，您可以无缝地签署和创建新钱包。

# Web3.js

Web3.js 也是一种流行的基于 JavaScript 的库，可将客户端连接到区块链。该库使用 HTTP、IPC 或 Websockets 运行本地或远程以太坊节点并与之交互。

Web3.js 于 2015 年发布，它是一个由以太坊基金会自行创建的开源库。这意味着 web3.js 拥有最大的社区，这与一年后发布的 Ethers.js 不同。web3.js 库是比任何其他库都多的项目和包的一部分。

然而，Web3.js 不像 Ethers.js 那样管理密钥。钱包和提供商没有分开，而是假设本地节点连接到处理密钥存储、交易签名和检查区块链状态的应用程序。

## web3.js 的优势

**支持Ethereum基金会**

Web3.js 由以太坊基金会开发。这意味着它有许多开发人员为它做出贡献，而且当问题出现时你肯定比它的对应物 Ethers.js 更快地找到解决方案是最理想的

**受欢迎的**

我们可以用 GitHub star 的数量来衡量它在区块链开发者中的受欢迎程度。根据 Github，web3.js 拥有超过一万七千 (17,000) 颗星，而 ethers.js 拥有七千 (7000) 颗星。它的流行很大程度上是因为先行者的影响，这使它拥有更大的社区。

## Web3.js 的缺点

**大小**

Web3.js 比 Ethers.js 相对更大，这使得在前端使用它是不可取的，因为它会降低 dApps 的性能。

**灵活性**

由于 ethers.js 通过关注点分离处理密钥的方式，允许开发人员以不同方式处理密钥的存储和管理，因此 web3.js 在这方面存在不足。

**许可证**

Web3.js 在更严格的许可下工作，如果不公开发布则不允许修改。

## Web3.js 模块

**Web3.eth：-**该模块用于连接以太坊区块链和智能合约。它还允许您订阅区块链上的事件。

**Web3.net** **:-**此模块允许您与网络节点属性进行交互。

**Web3.shh：-**该模块允许与 Whisper（一种 dApp 用于相互通信的通信技术）协议进行通信。

**Web3.utils：-**该模块为以太坊 dApp 和其他 web3 包提供实用功能。

**Web3.bzz：-**此模块允许您与[Swarm 网络进行交互](https://swarm-guide.readthedocs.io/en/latest/)

# Web3.js 和 Ethers.js 的比较

在本次会议中，我们将使用以下标准比较这两个库：

- 开发者体验
- 人气
- 文档
- 易用性
- 性能
- 维护

## 开发者体验

这两个库都可以用几行代码创建真正强大的区块链应用程序，但 ethers.js 被认为更干净，因为提供者和钱包之间的关注点分离。

然而，许多基金会项目使用 web3.js。这种情况使得大多数可用的教程都是用 web3.js 编写的，这为新开发人员提供了一个非常容易学习的曲线。与 Ethers.js 相比，有更多的资源和解决各种问题的方法。

## 人气

### GitHub

GitHub 上的一个热门项目，如你所想是真实的。

Web3.js 作为最流行的以太坊 JavaScript 库位居榜首，在 GitHub 上有超过 16000 个启动，但在我看来，GitHub stars 只能证明先发优势。

另一方面，ethers.js 在 GitHub 上拥有超过 6k 星。

### NPM 下载量

NPM 下载显示开发人员为他们的项目每周下载库的次数。

Web3.js 每周下载量超过 265000 次，而 ethers.js 每周下载量超过 610 次。

此下载显示从 web3.js 到 ethers.js 发生了变化或转变。

## 文档

这两个库都有相当详尽的文档。无论哪种方式，ethers.js 都比 web3.js 有优势，这要归功于它的入门和游戏区域。

## 使用的便利性

Web3 将钱包和供应商的处理变成一个单一的对象，但它的以太坊使读取区块链、修改其状态和管理密钥成为所有单独的操作。

## 性能表现

在性能方面，Ethers.js 是一个更好的库。由于其紧凑的尺寸，它适合在前端应用程序上使用。以太是 248 kB 未压缩和 77 kB 压缩。

## 维护

Web3.js 由以太坊基金会维护，但在 2020 年秋季[获得了 ChainSafe 的资助](https://blog.chainsafe.io/chainsafe-receives-grant-from-ef-for-web3js-9e4376f5f36a)，用于使用现代 Typescript 重写 web3.js。

随着这一发展，Chainsafe 致力于带来改变，以缓解使用 Web3.js 的痛点。

根据 Chainsafe 的说法，他们计划修复的领域是：

- 高效地过渡到 Typescript。
- 完全拥有 Typescript 中的整个库。
- 创建灵活统一的库版本。
- 支持 eth2 的各种功能。

总之，Chainsafe 似乎致力于做好维护 web3.js 的工作。

然而，ethers.js 由一位加拿大软件工程师 Richard Moore 维护。在 GitHub 上，只列出了 15 名贡献者。

# 下一步是什么

使用这些库时要自己判断哪一个最适合您的要求。互联网上有很多教程可以帮助您尽快入门。

# 结论

Web3.js 和 Ethers.js 都有强大的生态系统，可用于创建极快的 dApp。

令人惊奇的是，在我对这篇文章的研究过程中，这些库已经扩展了多少并且仍在使用。我希望这能让您有所了解，并让您更清楚地选择适合工作的工具。

# 资源

[ethers.js](https://docs.ethers.org/v5/)

[Web3.js](https://web3js.readthedocs.io/en/v1.8.1/)

> 原文：[Godwin Alexander Ekainu] https://flutterpage.hashnode.dev/comparison-of-libraries-web3js-vs-ethersjs

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)