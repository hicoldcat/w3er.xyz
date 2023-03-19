---
title: 如何成为 Web3 高级用户
description: null
author: 李留白
weight: 0
date: 2023-03-19T14:39:52.546Z
lastmod: 2023-03-19T14:45:46.476Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230319224014.png
---

这是一篇写给对 crypto/web3 生态系统感兴趣的人的帖子。加密货币不仅仅是您可以买卖的股票。如果您想了解更多信息，您应该从动手实践和使用当今可用的工具开始。👩‍💻

成为加密生态系统的用户后，构建工具和成为开发人员变得更加容易。这里的主题也是我们与 Austin 和 Carlos 一起制作的 web3 开发人员课程第一章的一部分：**成为加密/web3 高级用户**。

在这篇文章中，我想介绍八个工具和概念，以启动生态系统！**当有人问我实际上可以在加密货币中做什么**时，这篇文章对我发送给朋友和家人特别有帮助。.

**简短提示：** 🐇 在介绍工具和协议之前，我想提一下[RabbitHole](https://rabbithole.gg/)，这是一个您可以使用 web3 应用程序学习和赚取加密货币的平台。它可以帮助您浏览加密应用程序以及如何使用它们。因此，如果您有兴趣试用当今可用的 web3 应用程序，我建议您前往 RabbitHole 应用程序。

**注意：**我们将在本文中介绍的工具适用于以太坊区块链。大多数概念对于不同的生态系统都是相同的，但工具不同，您可以相应地查找它们。

------

## 1. 加密钱包：Metamask、Rainbow

作为用户，这一切都始于一个[加密钱包](https://www.coinbase.com/learn/crypto-basics/what-is-a-crypto-wallet)，它允许您与区块链对话。它为您提供了与加密货币交互所需的工具。加密钱包生成私钥和公钥。加密钱包的主要功能是存储密钥和签署交易。

- 公钥**，**顾名思义，是公开的。如果您向某人提供您的公共地址，他们可以向您发送加密货币。
- 私钥就像您的密码**。**您应该妥善保管，切勿与任何人分享。任何拥有您私钥的人都可以完全访问您的钱包。这个数字很难记住和存储；这就是助记词的用武之地。它是从私钥生成的短语，人类可读且更易于存储。

有托管钱包和非托管钱包。

- **托管钱包**：第三方（通常是 Coinbase、Binance 或 Robinhood 等交易所）拥有您的私钥并代表您签署交易。您信任第三方持有您的密钥并向他们提供您的信息。
- **非托管钱包**：用户拥有私钥；中间没有第三方。因此，用户是唯一拥有私钥和/或种子短语的人；没有其他人应该知道这些信息。

在托管钱包上，您的功能有限。你可以做平台允许你做的事情，主要是买卖币/代币。然而，当你想玩加密游戏时，这通常是不可能的。

🌟 最受欢迎的非托管加密钱包之一是[Metamask](https://metamask.io/)，前往该网站设置您的钱包。另一个流行的钱包是[Rainbow](https://rainbow.me/)，这是一款手机钱包。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230319224407.png)

设置 Metamask 后，您可以在顶部看到您的公共地址。这就像您的银行帐号；这是一个公共帐号，您可以共享它来接收资金。

您现在可以将您的加密货币从您的交易所转移到您的 MetaMask 钱包。

进行转账会产生交易费。这称为[汽油费。](https://ethereum.org/en/developers/docs/gas/)区块链上的每笔交易都有成本。所以当你想要发送代币时，你需要支付称为 gas fee 的价格。Gas是表示交易成本的单位度量。

[Etherscan](https://etherscan.io/)是以太坊的区块浏览器，它记录所有交易并显示它们。您可以查看支付的汽油费、交易发起人、区块编号以及更多详细信息。

------

## 2. 钱包起名：ENS

钱包地址是一个 42 个字符的十六进制地址。不幸的是，这对人类来说很难记住。

这就是[以太坊名称服务 (ENS) 的](https://ens.domains/)用武之地。它是一种建立在以太坊之上的查找服务。这就像以太坊世界中的域名服务（DNS）。DNS 获取 IP 地址并将其映射到人类可读的域名；ENS 获取加密地址并将其映射到人类可读的字符串。

当您想将钱存入您的帐户时，您可以发送您的 ENS 名称，而不是发送那个复杂而冗长的地址。例如“edatweets.eth”，我可以把这个发给别人，他们可以给我发以太坊上的硬币/代币。这提供了一种基本的身份形式。

[🌟 前往应用程序](https://app.ens.domains/)获取 ENS 。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230319224416.png)

然后，您还可以轻松地在 Etherscan 上搜索您的 ENS 并查看您的交易。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230319224422.png)

------

## 3. NFT：OpenSea、Foundation、Zora

大多数人做的第一件事就是获得一个不可替代的令牌（NFT）；这也是我做的第一件事。

那么让我们解开什么是 NFT：

- **不可替代**：具有独特属性的商品不能相互更改。例如，比特币是一种可替代资产。如果我们每个人都有一个比特币并将它们发送给对方，那么最终我们将拥有相同的东西。另一方面，房子是不可替代的。如果我们决定换家，我们最终会得到不同的结果。
- **Token**：简而言之，区块链是一个共享的、不可篡改的账本，token是一种存在于区块链之上的数字资产。

NFT 是区块链上独一无二的数字资产。任何数字资产都可以是 NFT；最受欢迎的类型是收藏品和艺术品。我们还看到了写作 NFT 和音乐 NFT。

- 艺术是不言自明的。艺术家有机会在区块链上创作他们的作品，并可以直接接触到他们的买家。
- 收藏品，顾名思义，就是NFT形式的资产集合。将它们想象成口袋妖怪卡片，甚至是便士收藏品。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230319224433.png)

最流行的 NFT 平台是[OpenSea](https://opensea.io/)。其他的是[Foundation](https://foundation.app/)和[Zora](https://zora.co/)。

由于以太坊是一个公共分类账，您可以从第三方工具中获取有关正在发生的事情的数据。[Dune Analytics](https://dune.com/)是一个执行此操作的平台。您可以查看价格走势、项目的交易量变化以及在区块链上拥有数据的 NFT 集合的详细信息。

[OpenSea 数据](https://dune.com/rchen8/opensea)如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230319224441.png)

🌟 您可以前往其中一个平台获取 NFT，或者只是看看！

------

## 4.去中心化交易所：Uniswap

[coinmarketcap](https://coinmarketcap.com/)上有许多不同的代币/硬币。所以当你想交易硬币/代币时，你可以在你的中心化交易所或去中心化平台上进行。

首先，让我们回顾一下什么是中心化交易所和去中心化交易所：

- **集中式交易所：**由一个实体拥有的交易所。它们允许您买卖加密货币。Coinbase、Binance 和 Robinhood 是集中式交易平台的流行示例。
- **去中心化交易所：**允许点对点加密货币交易的交易所。没有中介。相反，它基于智能合约。[Uniswap](https://uniswap.org/)是以太坊区块链上的去中心化交易所。它是最受欢迎的加密货币交易平台之一。

注意硬币和令牌之间的区别也很重要。

- 硬币存在于自己的区块链上**。**它是该区块链货币的数字表示。BTC、ETH 或 SOL 是加密货币；他们每个人都有自己的区块链。
- 令牌通过智能合约在**现有**区块链之上运行。NFT 是一种代币。基本上，硬币有自己的区块链并用作交换媒介，而代币存在于区块链之上并且可以具有不同的价值或效用。

🌟 您可以前往 Uniswap 兑换代币。这是 Uniswap 上交换代币的样子。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230319224450.png)

------

## 5.去中心化金融：Aave

在交易数字资产之后，然后是去中心化金融（DeFi）。它是**基于分布式账本的金融技术**。没有任何中央中介机构。相反，交易是点对点的。因此任何人都可以使用这些平台而无需批准；基本上每个人都可以从世界各地的任何人访问相同的平台。

[Aave](https://aave.com/)是一种去中心化的非托管流动性协议。它允许用户管理他们的数字资产。用户可以通过借入和存入资产的方式参与。存入资产意味着您正在向流动性池提供资产，或者换句话说，您正在向智能合约提供资金。

🌟 你可以前往 Aave 了解更多信息。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230319224458.png)

------

## 6. 追踪你的钱包：Zapper

使用你的钱包地址，你可以**追踪你拥有的所有东西。**例如，假设你的钱包里有 NFTs 和其他代币，在各种平台上抵押了一些代币。因为它都在公共区块链上，所以你可以在一个中心位置看到所有这些。

[Zapper.fi](https://zapper.fi/)是一个允许您执行此操作的平台。因此，您可以轻松追踪您的钱包。

你也可以在[Etherscan](https://etherscan.io/)上看到你拥有的东西，但是你将看不到 NFT 图像，而且 UI 侧重于显示交易而不是资产。

🌟 前往[Zapper.fi](https://zapper.fi/)查看您的 web3 投资组合。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230319224504.png)

------

## 7. 多重签名钱包：Gnosis Safe

多重签名（multisig）是一种**具有两个或更多签名者的智能合约钱包。**

它需要两个或多个私钥来设置加密钱包。设置多重签名后，您可以定义规则来签署和执行交易。例如，您可以有一个有五个签名者的钱包，而四个签名者需要批准才能执行交易。

[Gnosis Safe](https://gnosis-safe.io/)是以太坊上的多重签名。您可以在多重签名钱包上管理您的所有数字资产。

Mutlisig 通常用于去[中心化自治组织 (DAO)](https://consensys.net/blog/blockchain-explained/what-is-a-dao-and-how-do-they-work/)。DAO 就像一个具有共同目标的现实世界社区，具有用于治理和激励的代币。多重签名是组织的共享银行账户，交易只有在一定数量的人批准后才能执行。

除了用作团体的共享银行账户外，它还是为您的钱包增加额外安全性的好方法。

[🌟 您可以在Gnosis Safe 应用程序](https://gnosis-safe.io/app/welcome)上设置多重签名。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230319224510.png)

------

## 8. 扩展解决方案：Optimism、Polygon、zkSync

扩展解决方案旨在**提高速度并降低成本，同时又不放弃主区块链（**也称为 L1 基础链）的安全性和安全性或去中心化。

正在使用和研究的以太坊有多种扩展解决方案。例如，第 2 层是一种扩展解决方案，可提供更快、更便宜的交易，同时使用以太坊的安全性。

在这篇文章中，我们不会深入探讨扩展解决方案的世界，但这里有一些资源可以跟进：

- [在 Ethereum.org 上扩展](https://ethereum.org/en/developers/docs/scaling/)
- [Layer 2 扩展解决方案解释（Rollups、Plasma、侧链、通道动画）](https://www.youtube.com/watch?v=9pJjtEeq-N4&t=279s&ab_channel=WhiteboardCrypto)
- [以太坊第 2 层扩展解释（汇总、等离子、通道、侧链）](https://www.youtube.com/watch?v=BgCgauWVTs0&ab_channel=Finematics)

------

这是一个介绍，您可以做的还有很多；这只是冰山一角！我希望给出一个概述并且您是加密超级用户会有所帮助！

> 原文链接：https://eda.hashnode.dev/how-to-be-a-web3-power-user

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)

