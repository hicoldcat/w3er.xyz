---
title: 数据如何在区块链中处理和存储
description: null
author: 李留白
weight: 0
date: 2022-09-15T13:41:33.998Z
lastmod: 2022-09-15T14:21:35.975Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220915214140.png
---

随着区块链技术接管网络，用户仍然发现理解传统网络技术和 web3 技术之间的区别具有挑战性。

在本文中，我们将分解区块链的位置、区块链的存储位置以及数据在其中的处理方式。

## 区块链技术基础

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220915214235.png)

简单地说，区块链是一个在网络中的多台计算机之间共享和同步的数据库。在区块链中，交易安全地存储在对等计算机网络中，也称为节点。它使用哈希，这是一个不可更改的加密签名。散列将每种类型的数据转换为一组独特的字符，提高了区块链的完整性。区块链技术的关键特征是透明性、安全性和不变性。这意味着交易一旦被记录，就无法更改或删除。

要想清楚地了解区块链存储在哪里，我们首先要清楚地了解传统网络是如何存储和处理信息的。

## 数据的传统存储方式

在传统的 Web 中，计算机将数据存储在具有表、行和列的集中式数据库中，数据库管理员可以读取、写入、更新或删除这些数据。

集中式数据库更易于管理且可扩展性强；但是，缺乏透明度、安全性和完整性。这与透明且不可变的区块链网络非常不同。

## 区块链中的交易是如何发生的

要在区块链中进行交易，必须存在区块链的三个组成部分：节点、区块和矿工。

### 1. 节点

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220915214854.png)

节点可以是连接到区块链网络以验证和中继交易的任何计算机。区块链上的所有节点都链接在一起，它们经常相互交换最新数据，确保所有节点都是最新的。

节点的三个主要角色是：验证区块链上的交易，并根据真实性接受或拒绝交易。在区块链上存储区块交易。与区块链上的其他节点共享交易信息。

### 2. 区块

在区块链中，区块是单个、唯一的交易记录单元。区块可以根据它们在网络中的角色来区分。

- **创世块**：网络中的创始块。换句话说，区块链中的第一个区块。该块通过允许创建和链接后续块使网络不可变。
- **有效区块**：已开采且现在是区块链一部分的区块。有效块包含已被矿工验证/验证的交易。
- **孤块**：不再属于区块链网络的块。孤立块是在同时挖掘多个块但未添加到区块链时创建的被拒绝块。
- 矿工

在区块链上添加新交易的过程称为**挖矿**。矿工验证/验证交易，然后将其添加到区块链中。

### 交易如何发生

区块链交易生命周期分为三个阶段：身份验证、授权和工作量证明（PoW）。

#### 1. 认证

在进行任何交易之前，用户必须使用钱包的私钥成功验证自己的身份。最流行的身份验证方法是使用助记词。

种子短语是一组随机选择的单词，代表一长串随机数字和字母（您的私钥）。

私钥如下所示：

```
0C28FCA386C7A227600B2FE50B7CAE11EC86D3BF1FBE471BE89827E19D72AA1D
```

这有点难记住，所以我们使用这样的助记词：

```
spare govern hawk eternal decline session load grace peasant west bargain always meadow ensure quality hill neglect hand toss lyrics stereo call snap about
```

通常，使用助记词访问你的钱包比使用你的私钥更容易，因为它更容易记住。种子短语身份验证增强了区块链交易的匿名性和隐私性，因为没有中心机构管理身份验证。

#### 2.授权

授权是指网络上的每个节点都相互同意以批准交易。换句话说，在区块链中，必须达成共识才能验证交易。一旦满足智能合约设定的标准，节点将批准交易。

交易获得批准后，将存储在区块链上。一旦交易存储在区块链上，就无法更改。

#### 3.工作证明（PoW）

简而言之，工作量证明是加密货币矿工用来验证交易的复杂算法。使用此算法，您可以确认交易以在区块链中创建新块。

矿工们相互竞争以完成这个复杂的算法，因为每次将新区块添加到链中时，他们都会获得经济补偿。这是因为解决这些复杂的算法需要大量的计算能力。

> 权益证明（PoS）：权益证明是区块链中使用的替代共识机制。通过权益证明，持有最多网络原生货币的成员拥有验证交易的最大权力。

## 数据如何存储在区块链中

在区块链中，数据由网络中的所有节点访问、验证和记录。那么区块的实际内容是什么，区块链中存储了哪些类型的数据呢？

区块有两个部分：标题和正文。标头是区块的一部分，包含用于标识块的信息，而主体是区块的包含数据（事务）的部分。

### 块头

块头包含与区块关联的元数据。

元数据是：

- **块的版本**。包含区块版本号的 4 字节字段。区块的版本号指定验证区块的规则。例如，在比特币中，有 4 个区块版本，每个版本都是对前一个区块规则的改进。
- **前一个区块的哈希**。区块的身份。它是一组独特的字符串，用于标识特定区块并将其与下一个块联系起来，形成一个块链。换句话说，每个新块都是从前一个区块的哈希生成的。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220915221509.png)

- **时间**。区块被散列时的时间戳。
- **默克尔树**。Merkle 树是一种数据结构算法，其中每个节点都用区块的加密哈希标记。Merkle 树使用 Merkle 根数学公式来验证网络中的数据。这是为了在区块链中高效和安全地验证数据。
- **位**。存储难度目标，这是一个 4 字节的文件，指定矿工为了验证交易而解决的数学问题的难度。
- **随机数**。随机数（使用一次的数字）是一个随机数，用于获取区块的哈希值。定位随机数是矿工在验证区块时所做的事情。难度目标必须等于或小于要验证的区块的随机数。

此元数据在比特币网络中是公开的，并且始终可以使用[Etherscan](https://etherscan.io/)或[Blockchair](https://blockchair.com/)等区块链浏览器进一步探索和查看。

### 块体

区块的主体是存储块的所有交易的地方。一个区块最多可以容纳 500 笔交易，比特币网络就是这种情况。区块的大小越大，区块链中的交易就越快。

## 区块链的类型及其存储数据的方式

不同的区块链具有不同的数据结构。让我们回顾一下比特币和以太坊网络中数据的结构。

### 比特币

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220915221522.png)

比特币是第一个加密货币，也是区块链技术的第一个实际应用。比特币中的交易可以使用未使用的交易输出 (UTXO) 进行建模。UTXO 是指交易发生后剩余的比特币数量。

UTXO 数据库存储来自比特币交易的变化。该数据库的初始状态为 0。随着事务数量的增加，数据库中充满了来自多个事务的更改条目。

到交易完成时仍未使用的任何输出都将作为输入存回数据库，以在后续交易中使用。

每个比特币交易中都会提供以下元数据：

- **tx_in count**：交易输入的总数。
- **tx_ins**：保存所有交易输入的列表。
- **tx_out count**：交易输出的总数。
- **tx_outs**：所有交易输出的列表。
- **脚本见证**：此文件包含所有 SegWit 交易见证数据的序列化。
- **锁定时间**：指定交易**何时**可以包含在区块链中。它是一个 4 字节的值，用于指定交易的块号或时间戳，直到它被锁定。它通常设置为 0，这意味着交易在区块完成后立即生效。

### 以太坊

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220915221539.png)

比特币的主要限制是可扩展性，这意味着比特币网络中的单个区块只能容纳 1MB 的数据。以太坊网络是使用**trie**数据结构构建的，以解决比特币网络面临的性能和可扩展性挑战。

*trie* ，通常称为数字树，是一种数据结构方法，包含一组通过节点之间的链接连接的字符串。以太坊网络有四种尝试：状态、存储、交易和接收尝试。

- **State trie**：存储临时网络数据，例如账户密钥、交易数量、地址和账户余额。随着事务的发生，状态树的数据会不断更新。基本上，状态树将地址映射到帐户状态。
- **Storage trie**：存储智能合约数据。它保存帐户数据，例如帐户余额和与帐户关联的交易数量。每个以太坊账户都有自己的存储尝试。
- **Transaction trie**：事务树特定于每个块。Transaction trie 捕获交易请求向量，如 gas value、transaction value、gas limit、receiver 和 nonce。
- **Receipt trie**：保存交易后数据，例如使用的气体量、交易后状态和交易日志。

## 结论

在本文中，我们分解了区块链网络中的交易是如何发生的以及它工作所需的组件。我们还探讨了区块链的类型以及它们如何存储数据。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)