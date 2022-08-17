---
title: 完整的 Web3 开发者路线图 - 2022
date: 2022-08-17T13:47:20.574Z
author: 李留白
weight: 0
categories:
  - WEB3.0
  - 资讯
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220817214709.png
lastmod: 2022-08-17T14:27:15.996Z
description: ""
---

## 简介

我们将有关在 web3 中构建的所有最有用的信息汇集到一个文章中。您将找到指导您完成开发过程的每一步的教程 — 部署智能合约、Dapps 等等。从完全没有编码经验的人到高级 Web 开发人员，本指南旨在帮助所有背景水平的个人。

Web3 开发有多种途径可供选择。你可以是前端开发者、全栈开发者、智能合约开发者、rust/solana 开发者、solidity 开发者、架构、区块链、协议开发者等等。

## 先决条件 - 计算机科学 101

在深入研究 Web3 开发之前，我们建议您具备计算机科学基础知识。有很多可用的在线资源，包括：

- [哈佛的 CS50](https://learning.edx.org/course/course-v1:HarvardX+CS50+X/home?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co) - 这个高度评价的课程将教您了解计算机和数据处理基础知识所需的一切！
- [Introduction to Computer Science and Programming Using Python](https://www.edx.org/course/introduction-to-computer-science-and-programming-7?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co) - 本课程将向你介绍计算机科学的世界。已经接触过编程的学生，无论是从上面的课程还是从其他地方的学习，都应该学习这门课程以了解未来的材料。如果你在学完这门课程后还想了解更多，那么计算机科学很可能适合你。

## 区块链基础知识

作为一名Web3开发者，你需要了解这一趋势的核心技术--区块链。

区块链是一种共享数据库，其存储信息的方式与传统数据库不同。区块链将信息存储在 "区块 "中，然后通过密码学连接在一起。

不同类型的信息可以存储在区块链上，但到目前为止，最常见的用途是作为交易的分类账。

在加密货币的情况下，区块链是以去中心化的方式使用的，因此没有任何一个人或团体拥有控制权--相反，所有用户集体保留控制权。

感到困惑吗？幸运的是，网上有很多很好的课程可以学习更多关于区块链的知识。

- [区块链理论 101](https://www.udemy.com/course/blockchain-theory-101/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co) - 本课程将教您区块链技术的基础知识和潜在应用。

- [区块链：基础和用例](https://www.coursera.org/learn/blockchain-foundations-and-use-cases?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)- 非常适合想要彻底了解区块链基础知识的非开发人员和开发人员。本课程不限于区块链技术方面；它还将介绍权力下放的哲学概念。

- [加州大学伯克利分校的区块链基础](https://www.edx.org/professional-certificate/uc-berkeleyx-blockchain-fundamentals?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)- 本课程将让您深入了解并让您构建自己的区块链

### 什么是 Dapp？

Dapp 代表 Decentralized Application，指的是建立在区块链之上的应用程序。Dapps一般由两部分组成：
- 使用传统工具构建的标准前端，例如 HTML 和 JavaScript（包括 React、Vue、Svelte 等框架/库）。
- Solidity/Solana/Rust 后端，是将应用程序区分为 Dapp 的部分。这个后端是与你正在构建的区块链交互的东西。

许多流行的生态系统，如 Ethereum、Gemini 和 Solana，都有 Dapps 的教程和介绍：

- [以太坊基金会介绍 Dapps](https://ethereum.org/en/developers/docs/dapps/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)
- [什么是去中心化应用程序？来自Gemini](https://www.gemini.com/cryptopedia/decentralized-applications-defi-dapps?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)
- [什么是去中心化应用程序？来自 Coindesk](https://www.coindesk.com/learn/what-is-a-decentralized-application/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)

### 前端开发入门

如前所述，Dapps 的前端通常是相当传统的 Web 开发。幸运的是，互联网上有无数的前端开发资源。 

虽然谷歌是一个寻找前端开发资源的好地方，但问题是很难分辨好坏。我们在互联网上为您找到了一些最好的资源：

- [freecodecamp.com 提供的 300 多个小时的免费响应式网页设计课程，是启动前端开发人员职业生涯的最佳场所](https://www.freecodecamp.org/learn/responsive-web-design/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)
- [Freecodecamp 提供免费的 300 小时以上的认证 JavaScript 课程，该课程还包含算法和数据结构的基础知识](https://www.freecodecamp.org/learn/javascript-algorithms-and-data-structures/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)
- [W3C 的 JavaScript 介绍](https://www.edx.org/course/javascript-introduction?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)

尽管您可以使用 vanilla JavaScript 来构建功能齐全的前端，但它的可扩展性较差，并且不会为您提供那些可以使用框架和库的工具，例如：

- Vue.js -[这里有一些课程](https://medium.com/javarevisited/10-free-vue-js-nuxt-js-online-courses-for-beginners-in-2021-a347ea2ad144?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)
- React -[这里有一些课程](https://medium.com/javarevisited/5-best-react-js-books-for-beginners-and-experienced-web-developers-e7b90b1ab9d2?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)
-  Svelte -[这里有一些课程](https://medium.com/javarevisited/5-best-svelet-js-courses-for-beginners-in-2021-fadf0c4e2bab?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)

选择一个 JavaScript 前端框架也会极大地增加你的工作机会。

## 成为 Web3 开发人员需要后端开发吗？

如果您想使用智能合约，则必须了解并熟悉后端开发。在后端，你选择一个区块链，坚持使用一种语言，一旦你弄清楚架构的转变，你就可以开始更深入地思考效率，优化存储——所有这些让你成为一个非常聪明的人合约开发商。

但是，如果您只是想做前端开发，也不是 100% 必要的。如果您想使用区块链并帮助创建构成 Web3 的基础，那么就不能跳过掌握后端开发。如果你是一个完全的初学者，这个领域很难入门，因为后端代码中的错误已经让一些公司损失了数百万美元。 

同样重要的是要注意去中心化应用程序后端与“普通”Web 后端有很大不同。这源于区块链被用作分散数据存储的主要来源，而常规后端通常使用 Postgresql 等数据库。大部分技术原理还是一样的，所以强烈建议在进入 Web3 后端开发之前先掌握标准的后端开发。 

这里有一些很好的资源和路线图可供入门：

- [后端开发者路线图](http://distributedstack.dev/backend-developer-skills-roadmap-and-resources/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)
- [掌握 Github 和版本控制](https://www.udemy.com/course/github-ultimate/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)
- [后端开发人员的 12 大资源 ](https://www.waveapps.com/freelancing/web-development/back-end-development-resources?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)
- [2021 年终极后端开发者路线图](https://vitto.cc/2021-backend-developer-roadmap/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)

### 什么是以太坊？

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220817220517.png)

以太坊是具有智能合约功能的开源、去中心化区块链。该平台的原生货币是以太币。它由程序员 Vitalik Buterin 于 2013 年开发。它目前是世界上最大的智能合约和去中心化应用程序中心，其最大的竞争对手是 Solana。

### 智能合约如何运作？

智能合约是在满足预定标准时在区块链上运行的自动执行程序。它们通常用于自动执行交易，以便每个人都可以立即知道结果，而无需任何第三方参与或时间损失。

交易、投资、借贷等金融应用只是智能合约的一小部分用途。它们可用于游戏、医疗保健和房地产，甚至可用于构建整个公司结构。

### 什么是Solidity

[Solidity](https://soliditylang.org/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)是由以太坊团队创建的一种编译的面向对象的编程语言，其语法与 Javascript 相似。然而，与 Javascript 不同的是，Solidity 使用继承并且是强类型的。 
该语言的主要目的是开发合同并将其部署到区块链上，并且正在成为目前最有价值和最需要学习的语言之一。 

## Web3 开发者课程

### 通过构建项目学习 Solidity – [Buildspace](https://buildspace.so/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220817221101.png)

Buildspace 只是“一个简短的周末项目，您将在其中学习一些 Solidity，编写智能合约并将其部署到 ETH 区块链，并构建一个 Web3 客户端应用程序以与您的合约进行交互。” 

Buildspace 还有一个令人惊叹的 Discord 社区，他们渴望帮助初学者进入 Web 3.0。老实说，这是目前学习 Solidity 最有趣和最有效的方法之一。 

### 通过构建项目来学习 Solidity – [CryptoZombies](https://cryptozombies.io/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220817221234.png)

这是我最喜欢的一个❤️。CryptoZombies 的目标是教你如何使用 Solidity 在以太坊上创建游戏。该课程面向新手，从基本要素开始。如果您以前从未使用过 Solidity，那么它是一种完美的入门方式。你可能还喜欢：

### 通过构建项目学习 Web3 – [Remote3](https://remote3.co/?utm_source=r3blog&utm_medium=blog&utm_campaign=seo)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220817221416.png)

从指导教程中免费学习构建很酷的 web3 项目，以展示您的投资组合并获得聘用。Remote3 是一个用于发现远程 web3 工作的工作板。您还可以在 remote3 上找到 web3 教程和博客来学习 web3。

### 学习 Web3.js 

Web3JS 使您能够开发与以太坊区块链交互的客户端。它是一组库，可让您执行各种操作，例如将以太币从一个帐户发送到另一个帐户、从智能合约读取和写入数据、创建智能合约等等。 

这里有一些学习 Web3.js 的最佳资源：

- [Dapp 大学介绍 Web3.js](https://www.dappuniversity.com/articles/web3-js-intro?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)

- [从零开始学习 Web3.js 视频课程](https://www.udemy.com/course/learn-ethereum-web3js-from-scratch/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)

- [Web3.js 官方文档](https://web3js.readthedocs.io/en/v1.2.1/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)

### 学习 Ethers.js

 ethers.js 库被开发成一个完整的轻量级框架，用于访问以太坊区块链及其生态系统。与 Web3.js 非常相似，但最大的区别在于它们如何处理密钥管理以及与以太坊区块链的交互，我们不会在本文中深入探讨。 

以下是一些免费的入门资源：

- [Ether.js 简介](https://www.youtube.com/watch?v=cqdAQK7WOlE?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co) （视频）
- [使用 Ether.js 构建以太坊 Dapp](https://www.zastrin.com/tutorials/build-an-ethereum-dapp-using-ethersjs?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)（文章）
- [官方 Ether.js 文档](https://docs.ethers.io/v5/getting-started/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)

### 学习 Alchemy

 Alchemy Web3 是 web3.js 的另一种可能替代品，它的构建和配置可与 Alchemy 无缝协作，并提供多种优势，例如自动重启和强大的 WebSocket 支持。 

目前没有很多好的资源可以使用这个库，除了你可以在[这里](https://docs.alchemy.com/alchemy/documentation/alchemy-web3?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)找到的官方文档。谢天谢地，它写得很好。 

### 学习 Remix IDE

 Remix IDE 是一个有用的开源工具，它允许您直接从浏览器创建 Solidity 合约。它是用 JavaScript 编写的，可以在浏览器中使用，但它也有一个桌面版，可以在本地和远程运行。Remix IDE 具有用于智能合约测试、调试和部署的模块。 

最好的开始方式：

- [Remix 和 Solidity 简介](https://www.youtube.com/watch?v=JWJWT9cwFbo?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)（视频） 
- [在 Remix 上部署你的第一个 Solidity 合约](https://www.youtube.com/watch?v=bZKVfXmzRDw?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co) （视频） 
- [官方remix文档](https://remix-ide.readthedocs.io/en/latest/?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)

### 学习 HardHat

为了编译、部署、测试和调试您的以太坊软件，您需要一个像 Hardhat 这样的开发环境。它有助于管理和自动化创建智能合约和 dApp 所涉及的日常活动，并围绕此过程添加额外的功能。

Hardhat 还允许您使用 Truffle 来测试您的智能合约。这主要意味着与 @truffle/contract 包的兼容性以与您的智能合约进行交互。

入门资源：

- [如何使用 Hardhat 创建和部署智能合约](https://www.quicknode.com/guides/web3-sdks/how-to-create-and-deploy-a-smart-contract-with-hardhat?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co) （文章）
- [带Hardhat的 Hello World 智能合约](https://www.youtube.com/watch?v=m8ZBH46R_yQ?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co) （视频）
- [使用 Web3.js 和 Truffle 进行测试 | Hardhat](https://hardhat.org/guides/truffle-testing.html?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co) （文章）

### 学习 Truffle

 Truffle Suite 是用于在以太坊网络上构建去中心化应用程序的三个工具的集合：Truffle、Ganache 和 Drizzle。Truffle 是用于编写智能合约的开发环境、资产管道和测试框架。 

入门资源：

- [Truffle 快速入门指南](https://trufflesuite.com/docs/truffle/quickstart.html?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co) （书籍）
- [开始使用 Truffle Suite](https://www.youtube.com/watch?v=AgoSF8mih1M?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co) （视频）
- [你的第一个 Truffle Dapp](https://medium.com/@tapdev/your-first-truffle-dapp-an-attempt-at-a-beginners-guide-to-the-truffle-framework-c7edf0493b7?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co) （文章）

### 智能合约测试

编写智能合约测试对于任何区块链应用程序的安全性和效率都极为重要。 

我们可以使用集成和单元测试来测试智能合约的内部逻辑。我们可以编写单元测试来验证函数返回值和状态变量值以及检查合约之间交互的集成测试。这些检查确保继承和依赖注入过程正常工作。

学习测试的资源：

- [如何测试以太坊智能合约](https://betterprogramming.pub/how-to-test-ethereum-smart-contracts-35abc8fa199d?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)（文章）
- [如何编写智能合约测试](https://www.youtube.com/watch?v=nqaiSvsXIEk?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co) （视频） 

### Moralis - 用于 Dapps 的 Firebase

Moralis为区块链项目提供可管理的后端，是在Ethereum、BSC、Polygon、Solana和Elrond（更多即将到来）上构建和部署dApps的最快方式。所有Moralis dApps默认是跨链的。在Moralis上构建确保你的dApp是面向未来的。即使新的区块链被发明出来，你的DApp也会立即在任何链上工作。

无论你是在建立你的第一个区块链项目，还是已经是一个经验丰富的开发者 - Moralis将使你的项目更容易建立、维护和改进。

查看[Moralis 文档](https://docs.moralis.io/introduction/readme?utm_source=remote3.co&utm_medium=remote3.co&utm_campaign=remote3.co)以开始使用！

## 总结

谈到智能合约可以做什么，想象空间是无限的。随着我们继续发展这项技术，我们将看到越来越多令人惊奇的创新在Web3世界中得到实现；疯狂的是，任何人，包括你，都可以对这个行业产生巨大的影响，从而对未来产生影响。永远不要忘记，旅程就是回报，我们生活在一个非常激动人心的时代，这在人类历史上是前所未有的。我祝愿你在你的Web3开发者旅程中好运，现在去那里建设吧。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
