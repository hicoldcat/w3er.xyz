---
title: Aptos 区块链上的 DeFi 生态系统：深入探讨
description: null
author: 李留白
weight: 0
date: 2023-08-27T12:49:21.947Z
lastmod: 2023-08-27T13:14:29.107Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230827185111.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230827185111.png)

Aptos 一直处于 Move 生态系统创新和真正价值积累的前沿。经过Aptos核心开发人员孜孜不倦的努力；无论是 Aptos 实验室还是生态系统中的开源贡献者，该链一直致力于成为现有 L1 中的顶级参与者。尽管成立还不到一年，该链已经通过在 web3 的不同方面（特别是去中心化金融）方面令人难以置信的合作关系而闻名。 Liquidswap、Tsunami Finance、Baptswap、Thala labs 等基于 Aptos 的 dApp 等为Aptos 和 Move 生态系统的 DeFi 格局树立了先例。在本文中，我们将概述 Aptos 上的 DeFi 生态系统。

## 去中心化交易所：Liquidswap、Tsunami 和 BAPTSwap

在开始讨论之前，让我们深入探讨 DeFi 领域的主要交易方式，即去中心化交易所，通常称为 DEX。谈论 Aptos 上的去中心化交易所而不提及第一个 Aptos 原生 DEX Liquidswap 将对 Pontem 的人造成伤害。从该项目的[文档](https://docs.liquidswap.com/)来看，*Liquidswap 是 Aptos 区块链上的第一个 AMM（Automated Market Maker），旨在实现安全和去中心化的代币交换。该协议使用由 Pontem Network 团队开发的智能合约，用 Move 语言编写，并发布在 Aptos 主网上。* Automated Market Maker是持有可进行交易的流动性池的智能合约。

![液体交换](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230827185335.png)

[Tsunami Finance](https://tsunami.finance/trade)的总交易量超过 1200 万美元，是另一个具有集成去中心化交易所的 DeFi 协议。它既是保证金交易平台又是现货交易所。Tsunami 是为交易者考虑的。根据Tsunami 文档中的信息，它提供了一种价格影响为 0%、已知退出流动性、低融资费用、低点差以及随着时间的推移而增值的抵押品的交易方式，而 Tsunami Seasons 则为忠诚和活跃的用户提供激励交易者，基于 Tsunami 提供的代币和 NFT。每个人都喜欢免费赠品，Tsunami 肯定会在某个时候承诺这一点。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230827185432.png)

我们在 DEX 下最后提到的是 BAPT Labs 著名的交换[Baptswap](https://baptswap.com/swap)。尽管目前还没有引起太多关注，但 Baptswap 简单的用户界面和忠诚的团队很可能会在未来几个月内让该项目成为众人瞩目的焦点。他们是 Aptos DeFi 生态系统的早期参与者之一。BAPT 代币旨在捕捉生态系统中产生的价值，为 BAPTSWAP 生态系统提供动力。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230827185445.png)

## 贷款：Thala

作为 Aptos DeFi 生态系统的首批参与者之一，[Thala](https://www.thala.fi/)实现了 Move Dollar 的无缝借贷。该协议有两个主要产品，即 Thala Swap 和 Move Dollar。在 Thala 上，用户可以将抵押品存入智能合约中，借入 Move Dollar，MOD，以便日后偿还贷款。这些抵押债务头寸被称为金库。

### 闪贷：

Thala 在其协议中实施了闪电贷。通过闪电贷，只要在同一笔交易中偿还流动性，就可以在不需要抵押品的情况下借入资产。要启动闪电贷款，您需要创建一个请求贷款的合约，并且该合约必须完成指定的操作并在单笔交易中偿还贷款和费用。Thala 收取 1 个基点的闪贷费用。[Thala 文档中有更多关于此的详细信息。](https://docs.thala.fi/thala-protocol-design/thalaswap/flash-loans)

### 风险：

Thala 认识到不同类型的风险；智能合约风险、交易对手风险、中心化风险、流动性风险、波动性风险和预言机风险。他们还提出了不同的措施来减轻这些风险。

### Oracles：

Thalalabs 团队决定限制与从 Orcales 提供信息相关的风险的方法之一是实施分层 Oracle 设计。拥有一个主预言机和一个辅助预言机类似于制定 A 计划和 B 计划来缓冲不可预见情况的影响，以防任何价格和数据源失败。虽然[Pyth](https://pyth.network/)和[Switchboard](https://switchboard.xyz/)作为主要预言机的两个选项，但该协议利用计划任务从其他 DEX 和价格 API 提供商检索价格。值得注意的是，辅助预言机只有在 A 计划失败时才会生效。

## 流动质押：Ditto 和 Tortuga

权益证明生态系统中传统质押的挑战在于，质押资产不能用于借贷等其他目的。为了规避这一障碍，引入了流动性质押。流动质押是将质押代币转换为流动形式的过程，允许代币持有者使用其资产作为区块链内其他金融活动的抵押品。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230827185602.png)

Ditto 是 Aptos 流动性质押的先驱。在 Ditto 上，一旦 APT 被质押，用户就会收到 stAPT 代币作为交换。这些stAPT代币的价值几乎与APT相同。为了进一步保护网络并实现去中心化，用户质押的 APT 被委托给 Aptos 区块链上的 Ditto 验证者之一。

将您的 APT 委托给 Ditto 每年可获得高达 7% 的 APT 收益。此外，Ditto 协议还有一个折扣代币 DTO，可以在 Ditto 代币推出后兑换。该折扣代币允许用户在任何情况下以原价一半的价格购买DTO；类似于 TradFi 中的股票期权分配和赎回。多么棒的概念啊！最终，stakers 和 Ditto 获胜。

另一方面，[Tortuga声称目前的费用为零，未来协议](https://tortuga.finance/)[治理](https://coinmarketcap.com/alexandria/glossary/governance)有可能确定一个协议委员会，该委员会安全地保存在 Tortuga 的金库中。这是跨多个网络的 DeFi 协议的已知收入来源。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230827185616.png)

tAPT 是 Tortuga 生态系统的流动性代币。这是您将 APT 委托给 Tortuga 上的验证者所获得的代币。通过一些简单的步骤，它可以在 Aptos 区块链原生的不同 DEX 上进行交易[。](https://docs.tortuga.finance/product-docs/stake-apt/tutorial-buy-tapt)要退出 tAPT，用户可以执行以下任一操作：将其交易为 APT 或从 Tortuga 取消质押。虽然直接将 tAPT 交易为 APT 可以立即完成，但从 Tortuga 取消质押可能需要长达 30 天的时间，这是因为 Aptos 有 30 天的锁定期。Tortuga 的 APT 发行 APY 为 7%，与 Ditto 类似。

*正如 Tortuga 的文档中所示，当用户从 Tortuga 取消质押时，他们会收到一张票证。当验证者提供此票证时，APT 即可领取该票证。取消质押以获得票据，然后领取票据以获得 APT。门票按先进先出顺序处理，永不过期。当用户从 Tortuga 取消质押时，未质押的 tAPT 的 0.3% 会被销毁（该数字由治理控制）。这不是协议费。相反，与销毁的 tAPT 相关的 APT 可由其他持有 tAPT 的人索取。这是为了通过大量取消质押来抑制波动时期。*

## 总结

剖析 Aptos 上的每个 DeFi 协议将是一项艰巨的任务，可能需要数周或数月的时间才能完全解开。这就是我们尝试触及每个 DeFi 协议重要方面的原因。值得注意的是，所有上述协议都有相互交织的实用程序。连同所有提到的协议，其他协议（如 Abel Finance、Aries Markets、Aptin Finance、Argo、Cetus 等）的[TVL 总计约为 5400 万美元。](https://defillama.com/chain/Aptos)

为什么不在 Aptos 网络 DeFi 生态系统上卷起袖子、亲手做一些产品呢？这对于每个利益相关者来说肯定是双赢的。

>原文：https://aptosnoob.hashnode.dev/the-defi-ecosystem-on-aptos-blockchain-a-deep-dive