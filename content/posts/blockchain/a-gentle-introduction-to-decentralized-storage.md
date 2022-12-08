---
title: 去中心化存储简介
description: null
author: 李留白
weight: 0
date: 2022-12-08T01:58:57.006Z
lastmod: 2022-12-08T02:19:55.964Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221208095919.png
---

嘿，朋友！早上好。☀️

最近，我在做一个 NFT 项目，需要 IPFS 来存储 NFT 图像和元数据。这激发了我更多地了解去中心化存储的兴趣，它已经让我着迷了一段时间。

所以，今天我们将在这篇博文中了解去中心化存储（我们简称为**DStorage**）和一些最流行的 DStorage 协议。

让我们开始吧！

------

## 什么是DStorage？

顾名思义，DStorage是一个不依赖中央服务器或权威机构的存储系统。

与由单个集中式实体管理和操作的集中式存储系统不同，用户操作节点的对等网络运行 DStorage，每个节点都存储数据副本，从而创建一个弹性文件存储共享系统。

去中心化使其比传统存储系统更能抵抗审查和攻击。

------

## 为什么需要 DStorage？

如今，Google Drive、AWS 和 Dropbox 等云服务已成为托管文件和网站的首选。

虽然这些服务帮助个人、初创公司和大公司摆脱了管理其存储基础设施的麻烦，但此类服务的中心化性质存在一些严重缺陷。

中心化存储服务的众多缺陷之一是它们很容易被审查。如果政府或其他机构不希望某些信息公开，他们可以命令存储提供商将其删除，并且以后不再存储。

此外，提供商甚至可以更改存储数据的内容，从而导致信息完整性不佳。

另一个缺陷是中心化存储服务容易受到攻击。如果黑客能够访问集中式存储提供商的服务器，他们就有可能访问甚至删除您存储在这些服务器上的所有数据。

由于这些服务的数据托管在中央服务器上，单点故障意味着任何中断都可能导致数据暂时甚至永久丢失。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/silicon-valley.gif)

DStorage 通过提供抗审查、安全、分布式、高效、健壮、具有成本效益和弹性的数据存储来解决所有这些问题。

------

## DStorage 是如何工作的？

DStorage模型的工作方式是将整个数据的副本或部分分布在一个点对点的网络中，每个节点都被激励去存储数据。

DStorage 系统通过跨多个节点冗余存储数据，确保数据的安全性和可访问性。当您跨多个节点存储相同的信息时，即使有几个节点宕机，您仍然可以从剩余的存储节点中检索数据。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221208100053.png)

图片来源：[LeewayHertz](https://www.leewayhertz.com/)

### 对节点运营商有什么好处？

我们的计算机和移动设备存储空间中都有未使用的可用空间。DStorage 系统使用节点运营商磁盘驱动器上的可用存储空间，激励节点运营商将其设备上未使用的存储空间出租给 DStorage 网络。

这是对DStorage如何工作的总体概述。让我们了解流行的 DStorage 协议、平台及其内部工作原理。

------

## DStorage协议和平台

我们可以将以太坊网络视为一个用于智能合约存储的 DStorage 系统。但是当存储大量数据（如图像和视频）时，它变得不可行，更不用说 gas 效率低下了，因为这与以太坊最初设计的目的不同。

因此，我们需要考虑其他为存储目的量身定制的解决方案。

### 1.IPFS

IPFS 是一种点对点协议，用于在分布式文件系统中存储、访问和共享数据。

那个mumbo jumbo到底是什么意思？让我们举个例子。

我们都喜欢 Twitter（小鸟应用程序太酷了）。要访问 Twitter，您在`https://twitter.com`浏览器中输入 URL，由于它指向 Twitter 的 IP 地址，我们可以得到该地址上的服务器存储的网页。

但是如果我们把 Twitter 放在 IPFS 上，我们就得不到 IP 地址。相反，我们得到一个内容标识符 (CID) 来访问 Twitter，看起来像这样：

```
/ipfs/QmfExSLtVQwsFJNcN6AaW8DZsrL9CYsbHmxVdeLWkRzuyj
```

#### IPFS 是如何工作的？

Web2 Internet 上存在一个问题：您可以根据位置查找内容。

你想看 Netflix 吗？酷，去`https://netflix.com`。

您想阅读 Developer DAO 博客吗？访问`https://blog.developerdao.com`。

但是，如果内容的位置由于某种原因发生变化怎么办？🤔

内容寻址解决了这个问题。通过内容寻址，每个内容都有一个基于指向 IPFS 中数据的数据加密散列的 CID。

这意味着两个完全相似的文件将具有相同的 CID。内容稍有不同，就会产生完全不同的 CID。IPFS 默认使用 SHA256 哈希算法生成 CID。

IPFS 使用内容寻址来识别和查找内容，而不是查看内容所在的位置。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221208100127.png)

图片来源：[Infura 博客](https://blog.infura.io/)

>这些DStorage协议有很多内容，每个都值得写一篇详细的博文。例如，IPFS使用Merkle DAGs来表示文件和目录。在这篇博文中，我们暂时只谈基础知识。

### 2. Filecoin

IPFS 背后的同一个团队创建了 Filecoin。它是一个建立在 IPFS 上的点对点网络，通过奖励 Filecoin 的原生代`FIL`币来激励用户出租未使用的存储空间。

在 Filecoin 中，用户为存储空间付费，任何想要存储其他用户文件的人都可以加入 Filecoin 网络并获得报酬。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221208100311.png)

图片来源：[Filecoin](https://filecoin.io/)

------

### 3.Swarm

Swarm 是另一个 DStorage 协议，它是以太坊三位一体的一部分：

- 用于计算能力的以太坊
- 用于消息传递的Whisper
- 用于存储的Swarm

Swarm 提供了一个完整的 DStorage 基础架构，允许来自世界各地的人们成为存储提供商并获得报酬。

它的创建者将 Swarm 设计为具有高度可扩展性和弹性，并为需要高安全性和抗审查性的应用程序提供平台。

Swarm 的想法由 Gavin Wood 提出，其开发主要由以太坊基金会资助。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221208100332.png)

图片来源：[Swarm Docs](https://www.ethswarm.org/why)

------

#### IPFS 和 Swarm 之间的区别

虽然从高层次上看，这两种协议可能看起来很相似，但当我们深入研究这些协议背后的内部工作原理和理念时，就会存在细微差别。

他们之中有一些是：

- Swarm 的核心存储组件使用不可变的内容寻址块存储，而 IPFS 使用分布式哈希表来查找哪些节点正在托管内容。

> 💡 哈希表是键到值的数据库。分布式哈希表是在分布式网络中的所有对等点之间拆分表的表。

- Swarm 在激励系统上与以太坊深度结合，而 IPFS 没有激励系统，而是利用 Filecoin 添加激励层。
- 从开发的角度来看，IPFS 在代码成熟度、采用和社区参与方面比 Swarm 更进一步。因此，IPFS 在文档方面有很多可用的内容。相比之下，Swarm 没有庞大的文档库（哎呀，甚至我在研究 Swarm 时发现的信息都来自第三方博客😞）。

> ↗️ 这些协议之间有很多相同点和不同点。有关详细信息，请查看此[wiki](https://github.com/ethersphere/swarm/wiki/IPFS-&-SWARM)（虽然有点过时）。

------

### 4.Arweave

Arweave 是一个去中心化存储平台，它使用一种称为 blockweave 的新数据结构。Blockweaves 允许 Arweave 提供可扩展、有弹性和高效的存储。

那么，什么是 Blockweaves？🤔

我们都知道，区块链只是包含交易数据的链接块链。Blockweaves 类似于区块链，因为它们是区块链，但它具有连接到网络中多个先前区块的存储数据。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221208100349.png)

图片来源：[Arweave 白皮书](https://www.arweave.org/whitepaper.pdf)

Blockweaves 强制矿工为旧数据提供“访问证明”以添加新块。

与更传统的区块链不同，矿工花费计算能力和电力并竞争挖掘区块以赚取代币，Arweave 网络鼓励矿工存储和复制有价值的数据以赚取代币。

#### Permaweb

与传统网络一样，Permaweb 是永久存储的互连文档和应用程序的集合。Permaweb 位于 Arweave 数据存储层之上。

由于 Arweave 网络是建立在 HTTP 之上的，就像传统的网络一样，网络浏览器可以访问存储在网络中的数据。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221208100407.png)

图片来源：[Arweave 文档](https://arweave.org/)

------

### 5.Storj

Storj 是一个 DStorage 平台，可提供安全、可扩展、私有、高效且与 S3 兼容的去中心化云对象存储。

与其他 DStorage 平台一样，Storj 将拥有未使用带宽和存储空间的人与需要廉价、可访问和私有文件存储的人联系起来。

Storj 使用 ERC-20 代币 STORJ 来激励人们出租带宽和存储空间。

------

### 6.Sia

Sia 是另一个流行的去中心化云存储平台，它在点对点网络中连接租用者（租用存储空间来托管他们的文件和应用程序）和主机（将存储空间借给租用者）。Sia 拥有自己的区块链，主办方将获得 Sia 自己的本地实用程序代币 SiaCoin 作为奖励。

一旦承租人将他们的文件上传到 Sia 网络，它就会被分割、加密并发送到世界各地。网络通过制作多个副本确保文件始终可访问。

由于文件被分成多个部分并被加密，主机无法访问它们。

------

## 结论

随着过去十年集中式存储网络和集中式云服务（如 Google 和 AWS）的主流采用，为个人和组织提供快速且廉价的存储基础设施，信息完整性、隐私和审查制度的威胁无处不在。

DStorage 模型通过在节点网络中分布数据来解决集中存储的问题，每个节点都存储数据的副本。

这使其更能抵抗审查和攻击，具有比传统存储系统更好的信息完整性和数据可用性。

## 推荐资源

- [去中心化云存储详解](https://www.leewayhertz.com/decentralized-cloud-storage-solution/)- Leeway Hertz
- [IPFS 文档](https://docs.ipfs.tech/)
- [Filecoin 文档](https://docs.filecoin.io/)
- [Swarm文档](https://docs.ethswarm.org/)
- [Arweave 白皮书](https://www.arweave.org/whitepaper.pdf)

> 原文链接：https://blog.developerdao.com/a-gentle-introduction-to-decentralized-storage

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)