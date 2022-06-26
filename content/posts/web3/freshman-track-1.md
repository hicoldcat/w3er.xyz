---
title: Web3系列教程之新手篇---Level 1：什么是区块链？
description: null
author: 李留白
weight: 0
date: 2022-06-19T16:27:47.998Z
lastmod: 2022-06-26T10:24:47.352Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/freshman.png
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

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220626172515.png)

区块链是计算机网络上多个节点之间共享的共享、分布式和永久数据库。他们以一种不可能修改或破解系统的方式记录数据。

具体来说，就像它的名字一样，区块链将数据记录为一个区块链。每个区块都包含一组交易，这些交易可以在网络上传输资产，或者更新存储在区块链上的信息。

区块链是由匿名人士（或团体）中本聪在 2008 年发布比特币网络时推广的。比特币是一种加密货币网络，它主要处理 BTC 资产在网络上的转移，没有受信任的中间人或权威机构，同时确保网络本身是安全的，不会被黑客入侵。（PS 比特币网络也可能是世界上最大的漏洞赏金——如果你能破解它，你就是一个瞬间的亿万富翁）。

随着时间的推移，比特币的这种设计激发了其他更强大的区块链网络的出现，比如[以太坊](https://ethereum.org/)。在接下来的课程中，我们将学习很多关于以太坊的知识。

## 状态管理

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220626173258.png)

区块链在启动时以创世纪状态开始。比特币的创世状态发生在 2009 年公共网络启动时。以太坊的创世纪状态发生在 2015 年推出时。

区块链上的每笔交易都会修改跨所有节点复制的全局状态。

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220626173458.png)

由于有数百万笔交易，交易被组合成块。由此得名。这些块以可加密验证的方式链接在一起，因此它们在历史上是可追溯的。网络的当前状态可以随时重新计算，从创世块开始，根据每个块的信息转换状态，直到现在。

## 节点

区块链网络通过计算机节点的[点对点分布式网络](https://en.wikipedia.org/wiki/Peer-to-peer)自主管理。无需过多详细说明，您可以简单地将网络中的每个节点视为保存全局交易分类帐的副本。因此，每个节点都可以单独验证和审计网络上发生的交易，并确保没有非法行为。

另一种类型的节点，称为[挖掘节点](https://en.wikipedia.org/wiki/Bitcoin#Mining)，负责将网络上进行的新交易组合成一个区块，对其进行验证，并提议将该区块包含在其他所有人的全球分类账中。挖矿在计算上是困难的，并且安全地进行非常重要，因此区块被接受的矿工会因其辛勤工作而获得代币奖励。

区块链的使用证实了每个价值单位只转移一次，中本聪提出的巧妙机制解决了长期存在的去中心化[双花](https://en.wikipedia.org/wiki/Double-spending)问题。

## 权力下放

通过将数据存储在节点的对等网络中，区块链是一个去中心化的网络。与以集中方式存储数据的传统方法相比，这具有显着优势。有很多集中化问题的重要例子——我们将在这里列出其中的一些：

- 集中式系统中的数据泄露会暴露大量数据
- 中心集权管理可以审查和关闭言论
- 对中心化权威的依赖意味着上游问题会影响下游消费者（例如，AWS 宕机意味着大部分互联网也随之宕机）

另一方面，去中心化带来了相反的好处。

- 没有审查制度，因为没有单一的权威或中间人可以审查你
- 由于整个网络在全球 1000 个节点上运行，因此无需停机
- 高度抗攻击性使其无法操纵或破坏数据

## 用例

- 加密货币
- 智能合约
- 去中心化金融
- 游戏
- 供应链追踪
- 防伪保护
- 数据隐私
- 去中心化治理
- 可验证的资产所有权

## 资源

要了解有关区块链的更多信息，我们强烈建议您使用以下资源：

### 必看的

- [比特币实际上是如何运作的呢？来自3Blue1Brown](https://www.youtube.com/watch?v=bBC-nXj3Ng4)
- [Anders Brownworth 的区块链演示](https://andersbrownworth.com/blockchain)

### 推荐的

- [Bits On Blocks 对区块链技术的大概介绍](https://bitsonblocks.net/2015/09/09/gentle-introduction-blockchain-technology/)
- [简单解释的区块链是如何工作的](https://www.youtube.com/watch?v=SSo_EIwHSd4)


![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/my.png)