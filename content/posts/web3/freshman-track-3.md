---
title: Web3系列教程之新手篇---Level 3：什么是ETH？
description: null
author: 李留白
weight: 0
date: 2022-06-19T16:27:47.998Z
lastmod: 2022-07-01T16:47:00.739Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/freshman.png
---

> 原文：[https://www.learnweb3.io/tracks/freshman](https://www.learnweb3.io/tracks/freshman)<br/>
> 翻译：李留白

本文是[learnweb3](https://www.learnweb3.io/)的新手篇，包括：
- [Level 0：前置基础编程知识](https://hicoldcat.com/posts/web3/freshman-track-0)
- [Level 1：什么是区块链？](https://hicoldcat.com/posts/web3/freshman-track-1)
- [Level 2：什么是Web3？](https://hicoldcat.com/posts/web3/freshman-track-2)
- [Level 3：什么是ETH？](https://hicoldcat.com/posts/web3/freshman-track-3)
- [Level 4：加密钱包？](https://hicoldcat.com/posts/web3/freshman-track-4)
- [Level 5：Remix IDE介绍](https://hicoldcat.com/posts/web3/freshman-track-5)
- [Level 6：Solidity介绍](https://hicoldcat.com/posts/web3/freshman-track-6)
- [Level 7：dApp介绍](https://hicoldcat.com/posts/web3/freshman-track-7)
- [Level 8：加密货币(ERC20)简介](https://hicoldcat.com/posts/web3/freshman-track-8)
- [Level 9：NFT介绍](https://hicoldcat.com/posts/web3/freshman-track-9)


## 什么是以太坊？

以太坊是一个支持智能合约的去中心化区块链。与比特币只支持比特币代币在网络上的转移不同，以太坊更通用。

开发人员可以构建 dApp 或去中心化应用程序，这些应用程序可以在以太坊虚拟机 (EVM) 上的以太坊网络上执行。因此，以太坊的全球状态不仅包括每个账户的余额，还包括每个 dApp 的状态。

dApp 使用以太坊的编程语言 Solidity 构建在以太坊上。您可以使用 Solidity 编写智能合约并将智能合约部署到以太坊网络。

它通过工作证明 (PoW) 在其网络中的所有计算机之间保持共识。在不久的将来，随着 Serenity 补丁，以太坊将转向权益证明 (PoS) 机制。

## 以太坊基础

### 历史

以太坊最初是由年轻的程序员和比特币杂志的联合创始人[Vitalik Buterin](https://en.wikipedia.org/wiki/Vitalik_Buterin)提出的。他是一个好奇的学生，即使在年轻的时候也是一个天才的程序员。他曾经在 2007-2010 年玩过魔兽世界，然而，当暴雪决定从他最喜欢的术士的虹吸生命技能中移除伤害组件时，Buterin 非常沮丧并彻底退出了游戏。

2011 年，他遇到了比特币，并对这个想法非常着迷。他想正式投稿，所以他开始写关于它的文章，以换取每篇文章的几个比特币。2011 年底，他与人共同创办了比特币杂志，并全职投身于加密领域。他环游世界，研究各种加密项目，并意识到可以通过将图灵完备的通用编程语言集成到区块链中来概括大多数项目的功能。

Buterin 试图说服其他项目的开发人员中的比特币核心开发人员，区块链技术可以产生比货币交易所更广泛的影响，而区块链需要一种通用的编程语言来配合它。然而，在被现有项目拒绝后，他决定自己做。他继续提出用图灵完备的编程语言（Solidity）开发一个新的区块链平台，这就是我们所知道的以太坊.

2014 年，该项目公开宣布，核心团队由 Vitalik Buterin、Mihai Alise、Anthony Di Iorio、Charles Hoskinson、Joe Lubin 和 Gavin Wood 组成。几个月后，该团队为 Ether 代币举行了首次代币发行 (ICO) 以资助开发。该团队筹集了超过 31,000 BTC，当时约为 1800 万美元。然后，他们成立了以太坊基金会，这是一家总部位于瑞士的非营利组织，其任务是尽早看到以太坊的开源开发。

### 什么是以太?

以太坊有一种本地货币，称为“以太”或“Eth”。该代币需要为在以太坊网络上进行的交易支付交易费用。

### 什么是智能合约?

智能合约是小型计算机程序，无需中央协调器即可在以太坊网络上的所有计算机上复制和处理。智能合约允许您编写可以由计算机代码自动执行的合约。

以太坊的通用性允许在其之上构建任意数量的可能应用程序，这些应用程序都继承了在以太坊区块链上运行所带来的安全性和去中心化优势。

### ERC20 代币

除了以太币，人们还可以在以太坊上创建和使用自己的货币。最常见的货币形式是 ERC20 代币。ERC20 代币是符合特定标准的智能合约。开发人员可以超越标准，但在制作自己的代币时应满足最低要求。标准化允许数字钱包轻松支持所有类型的代币，而无需为创建的每个代币使用专门的代码。

### ERC721 和 ERC1155 代币

这些也称为 NFT。这两个标准类似于 ERC20，为创建 NFT 时应满足的要求提供了基准。它们也提供类似的好处，允许钱包和 NFT 市场立即与所有 NFT 集合兼容，因为它们都遵循这两个标准之一。

还有很多...

## 必读

- https://bitsonblocks.net/2016/10/02/gentle-introduction-ethereum/

## 推荐读物

- https://ethereum.org/en/whitepaper/
- https://cointelegraph.com/explained/erc-20-tokens-explained
- https://www.youtube.com/watch?v=ZE2HxTmxfrI
- https://blockgeeks.com/guides/smart-contracts/
- https://www.investopedia.com/news/what-erc20-and-what-does-it-mean-ethereum/


![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)