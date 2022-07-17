---
title: Web3系列教程之入门篇---12. DeFi交换理论
description: null
author: 李留白
weight: 0
date: 2022-07-17T02:28:08.260Z
lastmod: 2022-07-17T02:41:42.440Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703215340.png
---

我们大多数人习惯于通过中心化交易所购买加密货币——Binance、Coinbase 等。通常，我们在不同加密货币之间进行交易时会选择相同的平台。

然而，中心化交易所充满了问题。他们可能会被黑客入侵并失去所有用户的钱，或者更糟糕的是，交易所背后的公司可能会关门大吉，带着所有的钱逃跑。

这可能看起来很极端，但这不是虚构的。

## Mt.Gox 🏔

Mt.Gox 是 2010-2014 年领先的比特币交易所。在巅峰时期，它负责所有比特币交易的 70%。2014 年初，该公司报告称他们“丢失”了数十万比特币，并宣布破产。今天，那些丢失的比特币价值数十亿美元。

在接下来的几年里，Mt.Gox 面临着数起诉讼，其中一些诉讼在 8 年后仍在继续。

[您可以在此处阅读有关 Mt.Gox 发生的事情的更多信息](https://www.investopedia.com/terms/m/mt-gox.asp#:~:text=The%20name%20Mt.,the%20largest%20shareholder%20and%20CEO.)

## QuadrigaCX 🍁

QuadrigaCX 是一家总部位于加拿大的中心化交易所，于 2018 年倒闭。Quadriga 的创始人神秘地“去世”，平台上的所有加密货币也随之消失。用户报告损失了近 2 亿美元的资金。

安大略省证券委员会对该公司的活动进行了深入研究，并宣布 Quadriga 的创始人简直就是一个骗子。

[您可以在此处阅读有关 Quadriga 发生的事情的更多信息](https://www.cbc.ca/news/business/osc-quadriga-gerald-cotten-1.5607990)

-----------------------

无论如何，这不是一个详尽的清单，但它给了你一个想法。Web3有句俗语

> 不是你的钥匙，也不是你的硬币

这意味着，如果您不拥有自己的私钥，而是信任一个中心化的交易所来为您管理它们，那么您并不真正拥有您的加密货币硬币。这是真实的。

## 去中心化交易所的诞生🐣

去中心化交易所的想法很简单——允许用户通过智能合约直接在链上交易他们的加密货币，而无需放弃对其私钥的控制。

虽然听起来很简单，但实际情况要复杂得多。简而言之，去中心化交易所是一个美丽的数学和软件结合在一起的部分。我们希望在本文结束时，您将分享同样的感受。

现代去中心化交易所的诞生主要是由[Uniswap 引领](https://uniswap.org/)的。Uniswap 不仅是以太坊上领先的去中心化交易所，它还是以太坊上领先的 dApp。

在 Vitalik Buterin 在 2017 年发表了一篇关于[路径独立](https://vitalik.ca/general/2017/06/22/marketmakers.html)的博客文章后，Hayden Adams 受到启发，试图在最终成为 Uniswap 的项目中实施 Vitalik 的想法。

在花了一年多的时间编写代码之后，Hayden 终于在 2018 年 11 月宣布并推出了 Uniswap。[您可以在创始人的这篇博文中了解更多有关 Uniswap 历史的信息。](https://uniswap.org/blog/uniswap-history)

在本文中，我们将尝试回顾 Uniswap 存在和发挥作用的数学原理，并希望让您深入了解它为何如此神奇。

## 为什么复杂？

你可能想知道——“为什么我们不能在链上重建一个中心化交易所？”

好吧，你可以，但这还不够好。

集中式交易所通常在订单簿系统上工作。Alice 提出一个清单，说她愿意以 50 个“TokenB”出售 100 个“TokenA”，并且该清单被添加到订单簿中。在某个时候，如果 Bob 出现并说他想用 50 个 'TokenB' 购买 100 个 'TokenA' - 他们的订单匹配在一起，并且交易被执行。

在以太坊上尝试了基于订单簿的交易，最重要的例子是[0xProject](https://0x.org/)，但由于所有存储和匹配算法所需的高gas，吸引用户具有挑战性。

需要一种新方法，一种允许用户在任何两个代币之间任意交换而无需订单簿的方法。此外，如果用户实际上可以通过使用 Uniswap 赚钱，cookie 会指出。

## Uniswap V1、V2、V3

截至 2022 年 1 月，Uniswap 已推出三个版本。

第一个版本于 2018 年 11 月推出，只允许在以太币和代币之间进行交换。链式交换也可能允许代币交换。链式交换将允许TokenA <> TokenB通过首先将其中一个交换为 ETH，然后将 ETH 交换为第二个代币来进行交换。

V2 于 2020 年 3 月推出，它是 V1 的巨大改进，允许任何 ERC20 代币之间的直接交换，以及任何对之间的链式交换。

V3 于 2021 年 5 月推出，它显着提高了资本效率，这使得流动性提供者能够从资金池中移除更大一部分的流动性，并且仍能继续获得相同的回报（或在更小的价格范围内挤压资本并获得高达 4000 倍的利润） ）。

出于本教程的目的，我们将专注于 Uniswap V1 的设计，在接下来的级别中，我们将实际实现它的一个稍微简化的版本，允许在以太币和代币之间进行交换。

## 造市者

Uniswap 是一个自动造市者。让我们试着理解这意味着什么。

造市者是为交易市场提供流动性（资产）的实体。在非订单系统中，流动性使交易成为可能。这意味着如果你想出售 BTC 来购买 ETH，交易所必须有一个 ETH 余额，你可以从中购买以换取 BTC。一些交易对的流动性非常高（例如 BTC/ETH 交易对），但有些交易对的流动性极低或根本没有（例如诈骗代币或新创建的代币）。

DEX 必须具有足够的流动性才能发挥作用并作为中心化交易所的替代品。

获得这种流动性的一种方法是开发商（或投资者）投入自己的资金并成为造市者。然而，这是不现实的，因为他们需要大量资金来为所有可能的交易对提供足够的流动性。此外，这会损害权力下放，因为开发商/投资者将掌握市场的所有权力。

Uniswap 实施的另一种方式是让任何人成为造市者——这就是使 Uniswap 成为自动化造市者的原因。任何用户都可以将资金存入特定的交易对并增加流动性，作为交换，通过从用户那里收取的交易费来赚钱。

## 功能要求

考虑到我们所学到的，我们至少需要允许以下功能来构建一个自动化的做市商：

- 任何人都可以增加流动性成为流动性提供者
- 流动性提供者可以随时移除他们的流动性并取回他们的加密货币
- 假设有足够的流动性，用户可以在交易池中的资产之间进行交换
- 向用户收取少量交易费用，这些费用在流动性提供者之间分配，因此他们可以通过提供流动性来赚钱

## XY = K

Uniswap 的核心是一个数学函数：

`x * y = k`

假设我们有一个交易对`ETH / LW3 Token`

x = 交易池中的ETH储备余额


y = 交易池中LW3代币的储备余额


k = 一个常数

这个公式负责计算价格，决定用多少LW3代币换取一定数量的ETH，或者反之亦然。


*注意：如果我们用x代表ETH或LW3代币的储备，只要y代表反面，这并不重要。*

该公式指出，无论储备（x和y）是什么，k都是一个常数。每一次交换都会增加ETH或LW3代币的储备，减少另一个的储备。

让我们试着把它写成一个公式。

`(x + Δx) * (y - Δy) = k`

其中，Δx是用户提供的销售金额，Δy是用户从DEX换来的Δx金额。

由于k是一个常数，我们可以比较上述两个公式，得到。

`x * y = (x + Δx) * (y - Δy)`

现在，在互换发生之前，我们知道x、y和Δx的值（由用户提供）。我们感兴趣的是计算Δy - 这是用户将收到的ETH或LW3代币的数量。

我们可以简化上式，求解Δy，得到以下公式。

`Δy = (y * Δx) / (x + Δx)`

让我们试着用Solidity来编码。

```
function calculateOutputAmount(uint inputAmount, uint inputReserve, uint outputReserve) private pure returns (uint) {
    uint outputAmount = (outputReserve * inputAmount) / (inputReserve + inputAmount);
    return outputAmount;
}
```

假设我们有100个ETH和200个LW3代币在合约中。

*如果我想用1个ETH交换LW3代币，会发生什么？让我们来算一算。*

```
inputAmount = 1 ETH 
inputReserve = 100 ETH 
outputReserve = 200 LW3 Tokens

=> outputAmount = 1.98019802 LW3 Tokens

```

*如果我想用2个LW3代币换取ETH，会发生什么？*

```
inputAmount = 2 LW3 Tokens 
inputReserve = 200 LW3 Tokens 
outputReserve = 100 ETH

=> outputAmount = 0.999 ETH

```

这些数额非常接近于合约储备中存在的1:2的代币比例，但略微小一些。为什么呢？

我们用于价格计算的产品公式实际上是一个双曲线。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220717100150.png)

双曲线永远不会在x=0或y=0处相交--这意味着，仅仅作为交易的产物，储备都不会是0!储备是无限的.

## 滑坡

由于我们没有以确切的储备比例获得代币，这导致了数学的一个有趣的含义。价格函数导致价格滑点。

交易的代币数量相对于其储备价值越大，价格就越低。

假设我想尝试排空整个池子并出售200 ETH.

```
inputAmount= 200 ETH 
inputReserve= 100 ETH 
outputReserve= 200 LW3 代币

=> outputAmount= 133.333LW3 代币

```

如您所见，当我们试图排空池时，我们只接近预期的一半。

有些人可能认为这是自动做市商的缺陷`x*y = k`，但实际上并非如此。它与保护池不被完全耗尽的机制相同。*这也符合供求规律：相对于供应的需求越高，购买该供应的成本就越高。*

## 谁设定初始价格？

当创建新的加密货币时，涉及该代币的交易对没有流动性。因此，没有办法计算它的价格。

因此，第一个向池中添加流动性的人可以设定价格。*添加流动性涉及从交易对的两侧添加代币 - 您不能只为一侧添加流动性。*

当第一个人添加流动性时，它会创建储备余额并设置初始值x和y值。从那时起，我们可以在代币之间交换时进行价格计算。

Solidity中`addLiquidity`函数的一个简单实现看起来是这样的。

```
function addLiquidity(uint256 tokenAmount) public payable {
    IERC20 token = IERC20(tokenAddress);
    token.transferFrom(msg.sender, address(this), tokenAmount);
}
```

该函数接受用户的ETH和一个代币。

然而，这个实现是不完整的!

第二个人可以出现，并以完全不同的储备比例增加流动性，这将大规模地影响价格的计算。我们不希望允许这种价格操纵，我们希望去中心化交易所的价格尽可能地接近中心化交易所的价格。

因此，我们必须确保任何向资金池添加额外流动性的人，其比例与资金池中已建立的流动性相同。我们只想在资金池完全清空时允许任意的比例。

这导致了一个看起来像这样的实现。

```
function addLiquidity(uint tokenAmount) public payable {
    // assuming a hypothetical function
    // that returns the balance of the 
    // token in the contract
    if (getReserve() == 0) {
        IERC20 token = IERC20(tokenAddress);
        token.transferFrom(msg.sender, address(this), tokenAmount);
    } else {
        uint ethReserve = address(this).balance - msg.value;
        uint tokenReserve = getReserve();
        uint proportionalTokenAmount = (msg.value * tokenReserve) / ethReserve;
        require(tokenAmount >= proportionalTokenAmount, "incorrect ratio of tokens provided");
        
        IERC20 token = IERC20(tokenAddress);
        token.transferFrom(msg.sender, address(this), proportionalTokenAmount);
    }
}
```

## LP代币

到目前为止，我们已经讨论了如何增加流动性，以及如何对掉期进行价格计算。但是，如果一个流动性提供者想从池子里撤出他们的流动性，该怎么办？

我们需要一种方法来奖励流动性提供者的代币，因为没有他们，其他用户将无法进行交换。如果他们没有从中获得任何东西，没有人会将代币放入第三方合约中。

唯一好的解决方案是在每次代币交换时收取少量费用，并根据他们提供的流动性在流动性提供者之间分配费用。

如果有人提供了矿池 50% 的流动性，他们应该获得 50% 的费用。说得通。

有一个非常优雅的解决方案可以做到这一点：Liquidity Provider Tokens (LP Tokens)

LP 代币作为股票工作。

您获得 LP 代币以换取您的流动性
您获得的代币数量与您在池中的流动性份额成正比
费用的分配与您拥有的 LP 代币数量成正比
LP-tokens可以换回流动性+赚取的费用
但是，还有额外的要求：

已发行股票必须始终正确。当其他人在您之后存入或移除流动性时，您的股票应保持并保持正确的价值。
将数据写入链可能会很昂贵（gas 费）——我们希望尽可能降低 LP 代币的维护成本。
想象一下，我们发行了很多代币——比如说几十亿。有人第一次添加流动性时，他们拥有池中 100% 的流动性。那么我们是否给他们全部数十亿的代币？

这导致了一个问题，即当第二个人增加流动性时，需要重新计算份额，这由于 gas 费用而变得昂贵。

或者，如果我们最初选择只分配一部分代币，我们就有达到限制的风险，这最终也将迫使我们重新计算现有份额。

唯一好的解决方案似乎根本没有供应限制，并在增加新的流动性时铸造新的代币。这允许无限增长，如果我们仔细计算，我们可以确保已发行股票在流动性增加或移除时保持正确。

*那么，当增加流动性时，我们如何计算要铸造的 LP 代币的数量呢？*

Uniswap V1 计算与 ETH 储备成比例的金额。以下等式显示了如何根据存入的 ETH 数量来计算新 LP 代币的数量：

`amountMinted = totalAmount * (ethDeposited / ethReserve)`

我们可以更新addLiquidity函数，以便在增加流动性时铸造LP-tokens。

```
function addLiquidity(uint tokenAmount) public payable {
    if (getReserve() == 0) {
        ...
        
        uint liquidity = address(this).balance;
        _mint(msg.sender, liquidity);
    } else {
        ...
        uint liquidity = (totalSupply() * msg.value) / ethReserve;
        _mint(msg.sender, liquidity);
    }
}
```

现在我们有了LP代币，我们也可以用它们来计算当有人想提取他们的流动资金来换取他们的LP代币时，要归还多少基础代币。

我们不需要记住他们最初存入了多少钱。由于LP代币与存入的ETH金额成正比，我们可以重新排列上述公式，计算出需要归还的ETH金额，并按比例计算出需要归还的代币金额。

## 费用

现在，为了收取掉期的费用并在流动性提供者之间进行分配，我们需要考虑几件事。

- 我们是以ETH还是代币收取费用？
- 我们是以ETH还是代币来支付奖励？
- 我们如何从每个互换中收取费用？
- 如何在所有流动性提供者之间分配费用？

这些问题可能看起来很难回答，但实际上我们拥有回答这些问题所需的一切。

1. 交易者已经在向合约发送乙醚/代币。我们可以不要求明确的费用，而只是从他们发送的乙醚/代币中扣除一些金额。
2. 我们可以直接把费用加到储备金余额中。这意味着，随着时间的推移，储备金会越来越多
3. 我们可以用交易商存入的资产的货币来收取费用。流动性提供者因此可以得到与他们的LP-tokens份额成比例的均衡的乙醚和代币。

Uniswap从每次交换中收取0.03%的费用。为了简单起见，我们就说拿1%吧。在合约中增加费用就像对我们的价格计算公式做一些编辑一样简单。

我们有`outputAmount = (outputReserve * inputAmount) / (inputReserve + inputAmount)`

现在,

`outputAmountWithFees = 0.99 * outputAmount`

但是，Solidity并不支持浮点运算。所以对于Solidity，我们把公式改写成这样。

`outputAmountWithFees = (outputAmount * 99) / 100`

## 祝贺你!

这是一个很大的教程，有很多浓缩的信息。祝贺你能走到这一步!

虽然数学和思想在最初的理解上可能有点棘手，但我们希望通过阅读材料和在 Discord 上提问，你能对所有这些东西的精美架构有一个了解。

在下一阶段，我们将真正实现完整的合约，以及一个网站，为DEX。

> 原文：[https://www.learnweb3.io/tracks/sophomore/defi-amm-theory](https://www.learnweb3.io/tracks/sophomore/defi-amm-theory)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)