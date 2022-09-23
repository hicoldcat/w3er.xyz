---
title: ERC-1155 标准如何优于 ERC-20 和 ERC-721 标准的组合？
description: null
author: 李留白
weight: 0
date: 2022-09-23T15:14:58.551Z
lastmod: 2022-09-23T15:26:54.415Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220923231513.png
---

[ERC-1155](https://web3.hashnode.com/glossary/what-is-erc-1155)简单来说，是 ERC-20 和 ERC-721 标准的结合。它催生了多代币。

但它远不止于此。❤️

最常见的标准是 ERC-20 和 ERC-721 标准，但 ERC-1155 标准是您需要了解的标准。

这篇文章将深入探讨这些标准中的每一个，并了解 ERC-1155 令牌解决的问题。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220923232120.png)

## Web3 术语📚

这个主题可能包含许多 web3 术语，所以我在下面包含了一个小词汇表。

- [可替代代币](https://web3.hashnode.com/a-complete-guide-to-nfts-definition-minting-and-tech-specs#heading-what-is-a-fungible-token)：每个代币都具有来自同一[智能合约](https://web3.hashnode.com/glossary/what-are-smart-contracts)的相同价值和属性。
- [不可替代的代币](https://web3.hashnode.com/a-complete-guide-to-nfts-definition-minting-and-tech-specs#heading-what-is-an-nft-and-what-does-nft-stand-for)（NFT）：每个代币可以与另一个代币具有不同的价值，即使它来自同一个智能合约
- ERC：“以太坊征求意见”
- EIP：“以太坊改进协议”。
- [ERC-20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/)：可替代代币的标准，由 Fabian Vogelsteller 于 2015 年 11 月提出
- [ERC-721](https://ethereum.org/en/developers/docs/standards/tokens/erc-721/)：NFT 标准，由 William Entriken、Dieter Shirley、Jacob Evans 和 Nastassia Sachs 于 2018 年 1 月提出。
- [ERC-1155](https://ethereum.org/en/developers/docs/standards/tokens/erc-1155/)：多代币的标准。

现在让我们继续快速总结 ERC-1155 代币✨

## 什么是 ERC-1155？

ERC-1155 是一种多代币，可以创建可替代代币，提供另一种形式的效用。

多代币旨在提供一个智能合约接口，用于存储和控制各种代币类型，如 ERC-20 代币或 ERC-721 代币。

这意味着 ERC-1155 令牌可以执行 ERC-20 和 ERC-721 令牌功能。这种组合意味着 ERC-1155 可以充当半可替代代币，我们将在后面讨论。

ERC-20 和 ERC-721 代币合约是如此不同，以至于创建某种形式的功能将这两个合约联系在一起是非常低效的。如果已建立连接，则可能存在很高的故障、成本和网络中断风险。

想象一下 GWEI 比现在更高。这已经是一场噩梦了。👹

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220923232246.png)



与以前的标准相比，这不仅使 ERC-1155 更高效，而且还提供了一个机会来纠正其他令牌标准中的问题。

接下来，我们将深入研究其他代币标准，以了解它们的工作和局限性。

## ERC-20

ERC-20 是以太坊生态系统中最常用的标准。它于 2015 年发布，是以太坊网络中开发的第一个标准。

ERC-20 标准使人们可以轻松地在以太坊区块链上开发他们的可替代代币，但它的功能有限。

### ERC-20 标准有六个功能：

- `totalSupply`：确定令牌的总数；
- `Balance0f`：显示地址_owner参数指定的某个地址的账户余额，其中_owner是想要的地址；
- `transfer`功能：将代币从主地址（DAO/ICO）转移到用户地址。
- transferFrom` 函数：用于将代币从一个用户发送到另一个用户；
- `approve`功能：检查智能合约中的代币数量，并允许从账户中提取资金至最大允许金额，该金额指定为函数的参数；
- `allowance`功能：保证发件人地址有足够的代币发送到收件人地址。

### ERC-20 也有两个事件：

- 转让——账户之间转移代币的事件；
- 批准 - 当上述批准功能成功执行时启动事件。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220923232323.png)

如您所见，ERC-20 提供了创建代币的基本知识。它专注于创建一个令牌并能够发送它们。

没有比这更复杂的了。

2015 年推出 ERC-20 代币标准时，区块链仍处于早期阶段。目前还没有多少人有以太坊的用例。直到[Dapper Labs](https://www.dapperlabs.com/)在 2017 年推出 ERC-721 代币标准。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220923232338.png)

## ERC-721

ERC-721 代币标准适用于 NFT。不可替代的代币是人们现在谈论的一切。

猴子卖几百万，在web3里很普通💰

ERC-721 是由[Dapper Labs](https://www.dapperlabs.com/)团队的[CyptoKitties](https://www.cryptokitties.co/) 🐱 项目引入的。他们的游戏 Crypto Kitties 是基本的，但 NFT 的概念流行起来，人们为它们疯狂。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220923232352.png)

NFT 可以以多种方式使用，但主要用于数字艺术品。就像现实世界中的艺术品一样，每件艺术品都可以有不同的价值。该价值仅取决于买方。

我可以将 X 件估价 2000 美元，而其他人可能会估价 5000 美元。艺术是主观的，NFT 具有技术性，即使你从同一个智能合约中出售同一件艺术品，它也可以具有不同的价值。我在下面使用[Amrit Singh 的作品](https://twitter.com/amritpaldesign)展示了一个示例。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220923232421.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220923232426.png)

[这个NFT](https://opensea.io/assets/0x495f947276749ce646f68ac8c248420045cb7b5e/72447778157756570100330710626210482401116286028903088274161283607113414737950)有 30 个所有者。每个人都可以以他们想要的任何价值出售它，无论是 0.5 ETH 还是 2.5 ETH。

[我在这里](https://web3.hashnode.com/a-complete-guide-to-nfts-definition-minting-and-tech-specs#heading-a-brief-history-of-nfts)谈到了 NFT 的历史和技术细节，但总而言之，NFT 拥有一个独特的 tokenid，这使得它们独一无二。

ERC-721 还具有其他功能，例如：

- 它允许您在账户之间转移 NFT，允许将 NFT 交易为其他货币。
- 它允许您识别网络上一组 NFT 的总供应量。
- 它允许您查询特定资产的所有者。

然而，随着区块链空间的发展，ERC-721 的技术细节中发现了许多问题，这些问题催生了 ERC-1155。

一个关键问题是每个 NFT 都必须在自己的单独交易中发送，这使得它非常昂贵。ERC-1155 将改善诸如此类的低效率问题。

## ERC-1155 及其改进🛠️

与以前的代币相比，ERC-1155 代币在技术上更先进的方式有多种，例如：

- 可替代性不可知论者
- 半同质代币
- 交易更高效、更安全。
- 交易可以捆绑在 ERC-1155 中，从而降低转移代币的成本。
- ERC-1155 令牌是可互换的，使它们更加高效和有价值。
- 使用 SafeTransferForm 进行安全令牌传输
- 降低部署成本和复杂性。
- 防止双花攻击。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220923232440.png)



## 其中一些实现是如何工作的❓

### 可替代性不可知论者

正如我们之前提到的，ERC-1155 令牌具有表示可替代令牌和不可替代令牌的能力。

它可以支持同一个合约中的两个状态，可替代和不可替代。这使得它在区块链游戏世界中非常有用。在游戏中使用货币很常见，例如黄金（可替代代币）和物品（不可替代代币）。

通过 ERC-1155 合约运行此示例允许这种情况发生。否则，我们将不得不将 ERC-20 合同与 ERC-721 合同联系起来，这意味着开发人员需要付出更多的努力，并且游戏效率低下。

### 半同质代币

半可替代代币可以充当可替代代币和不可替代代币。最初，代币可以作为可替代代币发放，但一旦被赎回，它将成为 NFT。

将其视为解锁一个可替代令牌的宝箱。所有的宝箱都具有相同的价值。一旦你打开宝箱并找出里面的东西，你的奖励（即一把剑）现在就是 NFT。

这在 ERC-1155 合同中都是可能的，看起来很简单。然而，事情说起来容易做起来难。

### 批量交易

这种组合意味着 NFT 有权在一次交易中而不是多次交易中进行转移，这要求每个 NFT 如果通过 ERC-721 代币进行交易，则需要进行交易。

想象一下，当区块链游戏更普遍时，您想将物品从您的帐户转移到另一个或您朋友的帐户。如果这是在 ERC-721 代币标准上，这将需要对每个 NFT 进行单独的交易，由于 gas 成本，游戏成本太高而无法玩。

如果游戏使用的是 ERC-1155 代币合约，您可以将所有游戏物品批处理到一个交易中，然后将它们发送到另一个帐户。

这是区块链和用户的双赢。这意味着网络将减少拥塞并降低用户的成本。

### 安全转账表格

如果交易未成功完成，所涉及的代币将退还给原所有者。过去，有些人将代币发送到错误的地址并损失大量金钱是很常见的。

这是区块链世界对日常用户所需要的功能，因为诸如丢失代币到虚空之类的事件可能会给人留下错误的印象。

例如， Golem 合约目前持有价值数万美元的[超过 35 万个 GNT 代币，并且缺乏将它们从那里取出的方法。](https://etherscan.io/token/0xa74476443119A942dE498590Fe1f2454d7D4aC0d?a=0xa74476443119A942dE498590Fe1f2454d7D4aC0d)几乎每个 ERC20 支持的项目都会发生这种情况，通常是由于用户错误。

### 双花攻击

双花是指用户提交交易然后复制第一个交易。用户将复制原始交易以使其看起来自然，然后在删除原始交易的同时使用副本。

例如，如果用户在交换交易中提交 2 BTC，然后复制交易，他们将“花费”2 BTC 并免费收到产品。

有多种方法可以进行双花攻击。

ERC-1155 标准可以防止双花攻击，因为代码更严格，并且必须满足某些条件才能将交易提交到区块链。

ERC-1155 中更严格的规则防止人们像 ERC-20 中的漏洞那样利用他人。

## 结论

ERC-1155 代币解决了我们当前使用 ERC-20 和 ERC-721 代币所面临的问题。它解决了根本原因，并设法改善了以前代币中的许多低效问题，并使它们更加安全。

这些改进来自于区块链领域的创新。必须认识到，我们仍处于这一领域的早期阶段，并且将继续存在诸如 ERC-20 和 ERC-721 标准中的问题。

我们目前知道 ERC-1155 是当前用例的绝佳代币，并解决了我们现在面临的许多问题。但是，这是否意味着它也不需要改进。

将来可能会有另一个 ERC 改进 ERC-1155 等等。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
