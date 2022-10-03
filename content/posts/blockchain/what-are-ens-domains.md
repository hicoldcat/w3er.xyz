---
title: 什么是 ENS 域？
description: null
author: 李留白
weight: 0
date: 2022-10-03T04:50:31.507Z
lastmod: 2022-10-03T05:02:00.689Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221003125020.png
---

你见过有人用“.eth”以太坊钱包地址，而不是常规的长串数字和字母吗？好吧，我的朋友，这是一个 ENS 域。

## ENS 代表什么？

ENS 代表以太坊名称服务。

## 什么是[ENS 域](https://twitter.com/ensdomains)？

ENS 是以太坊区块链上的一个协议，它将一个长加密地址缩短为一个短的、人类可读的 URL。

新地址由前缀和后缀组成。前缀可以是任何你喜欢的！无论是你的名字、公司品牌，还是只是一个愚蠢的词——这取决于你。ENS 的原生后缀是“.eth”。但是，它们也支持常规 DNS 后缀，例如*.com*、*.org*和*.xyz*。

域作为域存储在区块链上。这意味着它必须由某人铸造，然后可以在[OpenSea](https://opensea.io/collection/ens)等 NFT 市场上进行交易。

购买或铸造域名时，您必须设置“注册期”。这基本上是您拥有该域的时间。通过[ENS 应用程序](https://app.ens.domains/)，这从 1 年开始，可以随心所欲。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/iASzBXINFJd9Ofirmk.gif)

### 你可以用它们做什么？

您购买 ENS 域名的主要原因有两个。

第一个，我们已经讨论过，是替换你的长加密钱包地址。

或者，您可以使用您的 ENS 域托管分散的网站。由于它们的去中心化性质，它们将是“[抗审查的](https://ens.domains/)”。

> 如果你对如何做到这一点感兴趣，ENS 的开发人员[@makoto_inoue](https://twitter.com/makoto_inoue)发布[了一篇 Medium 帖子来解释](https://medium.com/the-ethereum-name-service/cloudflare-and-fleek-make-ens-ipfs-site-deployment-as-easy-as-ever-262c990a7514)。

拥有 ENS 后，您还可以创建子域。例如，如果您拥有“hashnode.eth”，则可以创建“web3.hashnode.eth”的子域。有了这个，您可以为自己创建子域或将所有权分配给其他人。你可以自由地对他们做你喜欢的事情。

最后，就像加密领域的很多东西一样，它可以成为一种投资工具。ENS 社区的人认为，这些域将在互联网的未来发挥巨大作用。因此，他们正在购买[优质域名以高价出售](https://blog.hedgehog.app/why-this-ens-og-turned-down-a-1-million-bid/)。

### 有什么我不能用的名字吗？

并非如此，可以注册的内容没有限制。这包括外来字符（例如ñ）、符号（例如☺）和冒犯性用语。但是，有一个字符最小值 (3) 和最大值 (255)。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221003125434.png)

## ENS 域如何工作？

ENS 建立在两个组件之上：注册表和解析器——它们是两个独立的智能合约。

注册表记录所有注册的域；以及域的所有者、域的解析器和[缓存的生存时间](https://www.imperva.com/learn/performance/time-to-live-ttl/)（域到期前的时间）。该组件的工作是记录所有 ENS 域的日志。

解析器翻译域名。这要么是从人类可读的 URL 到长字符串，要么是相反的方式。任何执行 ENS 标准的人都可以充当协议的解析器。将域与其用户配对是该组件的工作。

解析 ENS 域需要两个步骤：

1. 向注册表询问哪个解析器负责 ENS 域。
2. 向解析器请求地址以解析域并处理交易。

![显示“用户代码”、“ENS 注册表”和“解析程序”之间信息流的图形。 用户代码向 ENS 注册表询问解析器的地址，然后用户代码向解析器询问原始地址。](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221003130145.png)

> 来源：[ENS 文档](https://docs.ens.domains/)。

## 如何购买 ENS 域名？

前往[app.ens.domains](http://app.ens.domains/)，连接你的钱包，然后输入你想要的域名。

接下来，选择您的注册期限，然后单击“请求注册”按钮。然后您将被要求确认您的交易。

处理完该交易后，您将不得不等待大约一分钟，“注册”按钮才可用。点击它，确认第二笔交易，然后等待它被处理。

### 如何将其设置为我的主要 ENS 名称？

您应该看到“主要 ENS 名称（反向记录）：未设置”。单击下拉菜单，选择您的 ENS 名称，然后单击“保存”。

如果显示“Primary ENS Name (reverse record): [YOUR ENS NAME]”，请跳过上一步。

您现在已经购买了您的 ENS 域名并将其设为您的主要 ENS 名称，这意味着，它将代表您的钱包地址。有关示例，请参见 ENS 域应用程序屏幕的左侧。

## ENS 域与 DNS 域

这一切与传统的[域名系统 (DNS)](https://www.cloudflare.com/en-gb/learning/dns/what-is-dns/)域有何不同？

DNS 将 IP 地址转换为人类可读的 URL。这里的关键区别在于 ENS 将以太坊地址转换为人类可读的 URL。他们都扮演着相似的角色，只是在不同世代的网络中。

理论上，DNS 域不如 ENS 域安全。这是因为与 ENS 的分散性质相比，DNS 的集中式服务器更容易成为攻击目标。

DNS 域也受商标法的约束。这意味着，如果您拥有[Coinbase.com](http://coinbase.com/)（在 Coinbase 拥有它之前），该公司可能会对您采取法律行动。

我们还没有看到这在 ENS 空间中被强制执行，但由于其设计，它可能难以强制执行。这是因为域所有者可能是匿名的，并且没有控制域的集中公司。如果您找不到所有者，也无法让公司取代它们，您将如何执行商标法？

这对投资者有利，但对希望进入该领域的公司不利。投资者有机会从想要创建 ENS 去中心化网站或钱包地址的公司那里赚大钱。

## ENS 域的 Web3 替代方案

ENS 有一个主要的竞争对手——Unstoppable Domains

### 不可阻挡的领域

Unstoppable Domains 是另一种加密命名服务。他们提供*.crypto*、*.nft*和*.bitcoin*等后缀。

与 ENS 不同，没有续订费。一旦你购买它，你就拥有它终身。它们还支持以太坊之外的多个链——例如[使用 Yoroi 钱包的 Cardano 区块链](https://unstoppabledomains.com/blog/unstoppable-domains-cardano)。

此外，它们还允许您重命名钱包地址并托管去中心化网站。

#### 不可阻挡的域与 ENS 域

两者的主要区别在于 ENS 需要一个注册期，而 Unstoppable Domains 让您在一次性付款后永远拥有该地址。

由于品牌保护，Unstoppable Domains 还保留了一些域名。关于这个话题，[Unstoppable Domains 表示](https://unstoppabledomains.com/blog/protected-brands-policy-sunrise)，“为了让下一个 10 亿用户加入加密货币，我们需要加入现有的互联网社区。在过去的 25 年中，公司投入了大量资源来建立在线品牌。我们希望这些企业能够拥抱并采用区块链领域。”

ENS 域不是这种情况；您可以在未经 ENS 或任何公司许可的情况下购买任何域名。

当 ENS 的运营总监[Brantly Millegan](https://twitter.com/brantlymillegan)被问及这两个项目之间的区别时，[他说](https://www.reddit.com/r/ENSMarket/comments/qd0jkl/comment/hhrzkz5/?utm_source=share&utm_medium=web2x&context=3)：“ENS 是一个开放的公共协议，由一个没有投资者的非营利组织开发，具有 EIP 和以太坊的支持基础，有更多的功能，并被广泛的行业采用。UD 是一个私有化的副本，有一个公司看门人，功能更少（他们只是复制 ENS），而且采用率要低得多。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
