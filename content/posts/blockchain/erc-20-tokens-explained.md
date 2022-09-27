---
title: 简单了解ERC-20代币标准
description: null
author: 李留白
weight: 0
date: 2022-09-27T13:28:02.931Z
lastmod: 2022-09-27T13:53:29.967Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220927212845.png
---

## 什么是 ERC-20 代币？

*ERC-20 代币是仅在以太坊平台上设计和使用的代币。*

它们遵循一系列标准，以便可以共享、交换其他代币或转移到加密钱包。

以太坊社区创建了这些标准，其中包含三个可选规则和六个强制性规则。

**可选的**

- 令牌名称
- 缩写符号Symbol
- 位数Decimal （最多 18 位）

**必须包含的函数**

- totalSupply
- balanceOf
- transfer
- transferFrom
- approve
- allowance

有点困惑？让我们先往下看。

##  首先，什么是以太坊？

*[以太坊](https://cointelegraph.com/tags/ethereum)是一个分散的计算机网络，具有两个基本功能。*

它们是：可以记录交易的区块链，以及可以生成智能合约的虚拟机。

由于这两个功能，以太坊能够支持去中心化应用程序（[DApps](https://cointelegraph.com/tags/dapps)）。这些 DApp 建立在现有的以太坊区块链之上，搭载其底层技术。作为回报，以太坊向开发人员收取他们网络中的计算能力，只能以唯一的跨平台货币以太币支付。

根据其目的，DAPP 可能会创建 ERC-20 代币以用作货币、公司股份、忠诚度计划中的积分，甚至是所有权证明，例如一定数量的黄金或房屋契约.

##  智能合约在这一切中处于什么位置？

*智能合约用于创建 ERC-20 代币。*

它们还用于促进代币交易，并在账户中记录代币余额。

智能合约是在 If-This-Then-That (IFTTT) 逻辑的基础上用编程语言“Solidity”编写的。

把它想象成一台自动售货机。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220927213715.png)

## 智能合约创建代币后会发生什么？

*这就是 ERC-20 的用武之地。*

创建代币后，它可以交易、使用或赠送给其他人。

ERC-20 是以太坊网络上所有代币使用的通用语言。它允许一个代币与另一个代币进行交易。

假设我们想做一个加密赌.场。就像在实体赌场中一样，为了简单起见，我们希望我们的玩家使用我们的筹码。

因此，玩家将他们的法定货币兑换成我们的代币并前往扑克桌。

## 明白了这个类比，但这究竟是如何工作的？

*让我们在我们的“加密赌.场”示例中查看 ERC-20 的每条规则。*

它们对于开发人员来说非常重要。

让我们从可选规则开始：

代币名称：Blu Chip

符号：BLU

小数：2*

**我们希望我们的代币是可分割的，这样玩家的最低赌注是 0.01 BLU。我们可以将小数点保留为 0，并使 1 BLU 成为最小值，或者将小数点提高到 18，从而使 0.000000000000000001 BLU 成为可能的最低除法，但让我们保持简单。*

## 现在到强制性规则：什么是 [totalSupply]？

*[totalSupply] 标识创建的 ERC-20 代币的总数。*

我们的赌场需要拥有的第一件事是总共有多少 BLU 代币在流通。假设我们的扑克桌共有 10 个 BLU，有 10 个玩家。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220927214214.png)

## 【transfer】呢？

*[transfer] 允许将一定数量的代币从总供应量转移到用户帐户。*

在游戏开始之前，玩家必须从庄家那里收到他们的 BLU。

每个玩家获得 1 个 BLU。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220927214308.png)

##  函数 [balanceOf] 有什么作用？

*当执行 [balanceOf] 函数时，它返回给定地址在其帐户中的代币数量。*

在我们扑克游戏的第一手牌中，有 5 位玩家看了看自己的牌并决定不玩。其余 5 人中的每一个都决定下注 0.5 BLU。使用 [balanceOf]，我们看到五个玩家有 1 个 BLU，五个玩家有 0.5 个 BLU。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220927214530.png)

##  我如何从其他用户那里获得 ERC-20 代币？

*[transferFrom] 是允许用户将代币转移给另一个用户的功能。*

好消息！你赢了第一手牌，从其他玩家那里获得了 2.5 BLU。

但是为了从他们那里拿走它，你需要[transferFrom]。没有这个，有什么办法阻止别人偷你的蓝队？

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220927214546.png)

##  有什么方法可以伪造令牌吗？

*不行，因为 [approve] 根据代币的总供应量检查交易。*

它确保没有遗漏或多余的东西。

保护我们假设的扑克游戏完整性的另一种方法是确保没有人将额外的 BLU 带到桌面上。因此，[approve] 通过检查表上 BLU 的总数是否等于 10 来允许交换。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220927214648.png)

##  我可以撒谎说我拥有的代币比实际拥有的多吗？

*不行。*

在交易发生之前，[allowance] 功能会检查用户账户的余额，如果没有足够的代币将取消交易。

我们不允许在我们的“加密赌场”中有贷款，因此我们需要确保每个玩家都有足够的 BLU 进行投注。如果他们只有 1 个 BLU，那么他们不能下注 2 个 BLU。

##  ERC-20 有什么好处？

*基本上，它使一切变得更加简单。*

在 ERC-20 代币之前，开发人员可能会在代码中使用其他术语 - 例如一个令牌使用 [totalAmount] 而另一个使用 [totalNumber]。

交易所和钱包需要建立自己的平台以适应每一个令牌的代码。

根据通用标准，新的代币可以放在交易所，或者在创建后自动转移到钱包中。

ERC-20 还使新代币的创建变得非常容易，这就是为什么以太坊[成为](https://techcrunch.com/2017/06/08/how-ethereum-became-the-platform-of-choice-for-icod-digital-assets/)2017 年最受欢迎的 ICO 平台的原因。

## ERC-20有什么问题吗？

*ERC-20 并不完美。*

ERC-20 代币标准没有解决一些问题。

在某些情况下，当代币被用作智能合约的支付而不是使用以太币时，它们可能会被无意销毁。估计因此[损失](https://github.com/Dexaran/ERC223-token-standard)了 300 万美元。

为了修复这个错误，以太坊社区目前正在制定一个新标准

命名为[ERC-223](https://medium.com/cryptomover/what-are-erc20-and-erc223-tokens-307badcca5a)。但是，这些标准与 ERC-20 不兼容，因此鼓励开发人员继续使用 ERC-20，直到实现兼容性。

2018 年 4 月，由于[batchOverflow](https://medium.com/@peckshield/alert-new-batchoverflow-bug-in-multiple-erc20-smart-contracts-cve-2018-10299-511067db6536)漏洞，一些交易所[暂停了基于以太坊的代币的存款和取款。](https://cointelegraph.com/news/multiple-exchanges-suspend-erc20-token-trading-due-to-potential-batchoverflow-bug)它被描述为“经典整数溢出问题”，并可能允许攻击者“拥有大量令牌”。 值得注意的是[，](https://medium.com/@peckshield/alert-new-batchoverflow-bug-in-multiple-erc20-smart-contracts-cve-2018-10299-511067db6536)目前没有传统的安全方法可以修复这些漏洞。

## 现实世界有什么例子？

*以太坊平台上的每个代币都是 ERC-20 代币。*

截至发稿时[编号为](https://etherscan.io/tokens)82815，让我们来看看其中的一些。

[EOS](https://eos.io/)（EOS）是目前市值近 120 亿美元 的[第五大加密货币，它正在尝试建立一个可以利用区块链间通信的网络，](https://coinmarketcap.com/currencies/eos/)

[TRON](https://tron.network/enindex.html)（TRX）在撰写本文时在所有加密货币中[排名](https://coinmarketcap.com/currencies/tron/)第 10，被描述为“数字娱乐行业的开源协议”。它旨在推出一个具有生态系统的内容平台，将所有创建不同类型内容的人联系起来。

“企业级公共区块链平台”[唯](https://www.vechain.org/)链（VEN）是市值[排名第 15](https://coinmarketcap.com/currencies/vechain/)的加密货币，它正计划实施物联网（IoT）技术，为每个产品提供私钥，从而可以跟踪它们。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
