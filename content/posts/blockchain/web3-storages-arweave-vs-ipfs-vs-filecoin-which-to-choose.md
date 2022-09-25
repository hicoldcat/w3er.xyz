---
title: Web3 存储：Arweave 与 IPFS 与 Filecoin，如何选择？
description: null
author: 李留白
weight: 0
date: 2022-09-25T12:53:34.379Z
lastmod: 2022-09-25T13:07:43.632Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220925205456.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220925205634.png)

## web2 storage 和 web3 storage 有什么区别？

- 在 web2 中，当我们将文件/媒体上传到云服务器时。如果文件/媒体的内容发生变化，则 url 不会更改，但如果服务器更改，则 url 将更改。
- 它也存储在集中式服务器中。
- 在 web3 中，我们有不同的存储协议，如 IPFS、Arweave 及其开源。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220925205656.png)

## 什么是 IPFS？

- 与 arweave 相比，IPFS 是旧服务之一。
- IPFS 全程是 InterPlanetary File System ，是一种协议、超媒体和文件共享**点对点 (p2p)网络，用于在** **Protocol Labs**创建的分布式文件系统中**存储和共享数据**。

优点 ：

- IPFS 使用内容寻址来唯一标识每个文件，即当我们将媒体文件上传到其中时，如果媒体文件发生更改，则 url 会更改。

缺点：

- 它不完全像区块链，即像比特洪流机制一样工作
- 数据不是永久的。为了维持数据，我们需要集成称为**固定服务的服务，例如 Infra、Pinata。**
- 这些固定服务将固定文件，即**在 IPFS 网络上保存数据，这样它就不会被删除**并且数据可以维持。
- [但是这些固定服务存在一些问题——它们并没有真正提供稳定、可靠、真正去中心化、长期/永久的解决方案。](https://medium.com/web-design-web-developer-magazine/pinata-is-not-a-viable-long-term-solution-for-facilitating-decentralized-nft-file-storage-on-ipfs-f27896da2ea5)

为了优化这个 IPFS，**Protocol Labs**提出了另一种名为**Filecoin的解决方案**

## 什么是 Filecoin 以及它与 IPFS 的区别？

- [Filecoin 和 IPFS 是两个独立](https://docs.ipfs.tech/concepts/faq/#contributing-to-ipfs)的互补协议，均由 Protocol Labs 创建。
- **IPFS**允许对等点彼此存储、请求和传输可验证的数据。
- **Filecoin**旨在提供一个**持久的数据存储**系统。它遵循[时空证明和复制证明，](https://filecoin.io/filecoin.pdf)以保证矿工正确存储了他们承诺存储的数据。
- Infact **filecoin 建立在 IPFS 之上**，允许用户租用未使用的硬盘空间。

优点 ：

- 与将信息存储在一个地方的集中式网络不同，Filecoin 正在创建一个分散式网络，在该网络中，数据可以跨多个位置复制，并且可以从任何地方访问。
- Filecoin 正试图通过一种低成本的临时存储替代方案来扰乱当前的存储市场。
- 它的可扩展性——将全球数百万台计算机聚集在一起，创建一个巨大的存储网络，并激励存储数据。
- 它没有为黑客提供攻击中心点。它的持久数据存储。

缺点：

- 它不支持一次性付款来存储数据，而是只支持基于月租合同的系统来存储数据。
- 示例——将 Filecoin 视为数据存储的 Airbnb。就像人们在 Airbnb 上租用他们家中的房间或套房一样，Filecoin 想从世界各地的服务器上出租剩余的数据存储空间。这些服务器的所有者可以向您和我出租空间，以便按月保存我们的数据。
- 类似于 Airbnb 成为（有时）住宿酒店的更便宜的选择，Filecoin 旨在成为比云存储行业的大玩家更便宜的选择。

主要是**Filecoin 的经济模型**：**基于合约的存储**可以更简单地认为是一种[现收现付的模型](https://coinmarketcap.com/alexandria/article/the-decentralized-storage-war-filecoin-vs-arweave)。用户向节点网络支付费用以永久存储数据，并且它还提供[**不信任**的保证](https://www.coindesk.com/layer2/2022/02/15/what-are-ipfs-and-filecoin-and-how-can-they-be-used-for-nfts/)，以确保有人在约定的时间内实际存储了他们所说的数据

事实是每个企业都有自己的替代用例，在这种情况下——

- 如果他们需要永久存储，并且只需要一次性支付费用来永久存储数据，而不是像文件币那样按月支付合同付款和固定时间来存储数据，**那么 Arweave**就出现了**。**

## 什么是**Arweave？**

- **Arweave**向市场引入了一种[全新的经济模式，这种模式在无](https://coinmarketcap.com/alexandria/article/the-decentralized-storage-war-filecoin-vs-arweave)**许可**加密网络出现之前是不可能实现的：**永久存储**。
- 与 arweave 交互的简单方法是使用 Bundlr，与 arweave 交互位挑战，因为我们需要 arweave 令牌来执行任何操作，而这些令牌在很少的平台上列出，因此使用 bundlr 对您的应用程序来说非常容易。
- Arweave（以及 Bundlr）不支持可变数据。它是永久数据存储，因此这意味着它不能被编辑。但是，您可以上传更新版本，因此您可以构建一个系统来促进可变数据的出现，但具有其编辑的永久历史

优点 ：

- 通过永久存储，用户只需支付一次性的预付费用即可*永久*存储数据。
- 该协议通过利用加密经济博弈论和创建捐赠基金来补偿矿工以确保数据的可用性、可靠性和持久性来实现这一点。
- 它使用经济学来激励人们有史以来第一次长时间存储数据。这种组合使公共或私人数据永久化。
- Arweave 区块链每秒能够处理超过[5,000 笔交易](https://arweave.medium.com/what-is-arweave-explain-like-im-five-425362144eb5)。

缺点：

- 无法删除或更改的数据
- 根据[一些研究](https://news.coincu.com/64635-what-is-arweavear/)，它在解决全球市场时解决了一些可扩展性问题

**总结**：

- 如果您需要短期临时存储 - IPFS with pining
- 如果您需要以固定期限存储 - Filecoin
- 如果您需要永久存储——Arweave

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220925210004.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
