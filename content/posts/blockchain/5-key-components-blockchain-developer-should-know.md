---
title: 每个开发人员都应该知道的区块链的 5 个关键组件
description: null
author: 李留白
weight: 0
date: 2023-01-11T12:00:42.176Z
lastmod: 2023-01-11T12:14:15.986Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230111200035.png
---

如果您对以太坊区块链开发感兴趣，请查看我在[此处](https://dev.to/siddharthsing/roadmap-to-blockchain-development-on4)讨论的路线图。

在这篇文章中，我将讨论区块链的五个基本概念，每个开发人员都应该知道他们是否想进入 web3 环境。此外，[dev.to](http://dev.to/)上的本系列文章将是以太坊区块链开发的完整指南，敬请期待。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/lMLHUwacd.gif)

## 理解文章的一些关键字：

- **公钥**- 它是一个加密密钥（一长串十六进制数字），任何人都可以获得并使用它来加密针对特定收件人的消息或交易，这样加密的消息只能通过使用第二个密钥来解密只有收件人知道（私钥）。
- **私钥**——如上所述，私钥是由相应的公钥加密的消息或交易的解密器或解码器。私钥不得与任何人共享，因为它能够从您的地址进行交易。
- **共识机制**——共识机制是指用于在分散的计算机网络中达成协议、信任和安全的任意数量的方法。在区块链和加密货币中，工作量证明 (PoW) 和权益证明 (PoS) 是两种最普遍的共识机制，区块链通过这些机制同步每个节点的相同数据。

## 1. 匿名

所以，像以太坊和比特币这样的区块链确实是透明的，换句话说，例如，每个 BTC 的历史都可以从它被开采的区块到当前存储它的钱包进行追踪。同样，也可以跟踪所有地址的交易历史，因为地址是由公钥识别的，而且这些对每个人都是可见的。

尽管如此，区块链还是以某种方式保持匿名：

### 化名或假名

假名，意思是某人使用的名字，而不是他/她的真名。区块链做同样的事情，而不是像传统银行系统那样使用你的原始名字，区块链通过你的地址（公钥）进行交易。例如，您的真实姓名可以是 Mike，但在区块链环境中，您将被识别为“1ExAmpLe0FaBiTco1NAdDr3sSV5tsGaMF6hd”或其他名称，更长的名称。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230111200206.png)

上图显示了传统银行系统如何暴露用户的姓名、地址甚至联系电话，而区块链交易使用别名即公钥（钱包地址）进行交易。

区块链是强大的，因此仅仅隐藏我们的名字并不能使我们在这个非常广阔的环境中安全，因为强大的力量会带来更大的威胁。**观看这个 5 分钟的 Youtube 视频以了解区块链匿名性**

https://www.youtube.com/watch?v=4pKBvJEk6Nc

## 2.权力下放

现在我们经常听到“去中心化为**王**”这样的话。那么，这是什么意思，什么是去中心化？

- **去中心化**是将控制和决策从中心化实体（个人、组织、政府或其团体）转移到分布式网络。

为了清楚地理解它，假设您与您的朋友进行了交易，并且您的朋友收到了这笔交易。现在在这里，交易发生在你和你的朋友之间，但是交易的知识对四个实体是可用的，你、朋友、涉及的银行（你的和你朋友的），以及支付网络处理器（VISA、万事达卡等） .).

支付流程是这样的（见下图）。在这个传统的银行系统中，假设你向你的银行“A”发送了一个请求，要求向你的朋友发送 500 美元，现在银行需要验证你和你的朋友，但是假设你朋友的账户在银行“B”中，那么银行“A”无法验证您朋友的详细信息，因为银行不共享客户信息（由于竞争）。支付网络处理器（VISA、MasterCard 等）来了，每家银行都将所有客户的数据出售给这些公司，他们代表银行验证用户。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230111200318.png)

这就是为什么去中心化为王，在去中心化系统中，如加密货币（BTC、ETH、DogeCoin 等），如果用户将加密货币发送给他们的朋友或任何人，只有发送者和接收者会知道交易。下图比较了中心化金融（CeFi）和去中心化金融（Defi）。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230111200331.png)

## 3.容错

去中心化的区块链网络看起来像这样（见下图）。世界各地的几个节点（或区块）在没有任何中央当局监管的情况下连接在一起。所有节点都通过共识协议连接，因此每个节点都是同步的，并且在整个区块链网络中具有相同的交易账本。由于所有节点中都存在相同的数据，因此如果一个或两个节点受到威胁或受到威胁，系统仍然不会出现故障。这些故障节点将从网络中移除，区块链继续运行。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230111200345.png)

## 4.不变性

不可变性是 web3 社区中一个被大量创造的术语。区块链是一个不可变的数据库，你不能操纵已经存在于区块链中的数据。怎么来的？为此，让我们看看哈希码是如何生成的以及块结构是什么样的。

**Hash Code** - 哈希码是通过各种算法（如 SHA-1）生成的长十六进制序列。只需更改您姓名的字母，哈希码就会有很大差异。例如，**DEV**可以散列为“ **FA489B5C2003** ”，但是，**DAV**将变为“ **A8DC567B89E** ”。因此，黑客试图操纵区块链中的交易会立即导致哈希码发生变化，系统会指示节点出现故障。

> 请参阅下面的猴子 gif，以了解哈希码通过更改一些内容而变化的范围。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/og36gX_bFr.gif)

**块结构**- 区块链中的每个块都包含两个长哈希码。一个是区块本身的哈希值，另一个是前一个区块的哈希值（见下图）。前一个块的哈希值用于将两个块连接在一起，类似于链表。现在，假设任何黑客更改了任何一个区块中的交易细节，其哈希码将被完全更改，从而导致链从故障节点中断。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230111200445.png)

这就是更新区块链节点如此困难的原因，因为如果有人试图更新一个节点，所有连续的节点都应该同时更新，因此区块链几乎是不可变的。

## 5. 无需信任

无信任是去中心化的特质。区块链网络被称为无信任，不是因为你不能信任它，而是因为你不必为你的交易信任任何第三方。加密环境中的交易完全是基于点对点（P2P）的交易，没有中心化的机构（政府、银行等）参与。因此，区块链被称为一个无信任的环境。虽然没有一个系统可以完全无信任，但在某些时候，我们必须信任任何组织，但在区块链中，任何人都将是一个代码，而不是一个人。

> 原文：https://web3maniac.hashnode.dev/5-key-components-blockchain-developer-should-know

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)