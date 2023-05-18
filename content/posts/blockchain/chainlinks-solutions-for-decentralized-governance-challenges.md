---
title: Chainlink 针对去中心化治理挑战的解决方案
description: null
author: 李留白
weight: 0
date: 2023-05-18T19:04:00.200Z
lastmod: 2023-05-18T19:10:48.345Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202305190305000.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202305190305000.png)

在我们之前的文章中，我们讨论了去中心化自治组织 (DAO) 如何[集中](https://daotimes.com/the-centralization-paradox-of-decentralized-autonomous-organizations-daos/)以及DAO 中的[不良行为者如何造成重大伤害。](https://daotimes.com/an-interview-with-a-dao-money-grabber-how-they-are-draining-treasuries/)尽管存在这些挑战，但仍然有解决这些问题的潜在解决方案。

在本文中，我们将讨论 DAO 如何从使用 Chainlink 的数据和计算服务中获益。我们将首先概述去中心化治理的挑战，包括代币所有权集中、低参与度和高成本、难以管理的提案以及 DAO 功能的不可靠执行。然后描述了 Chainlink 如何通过提供各种服务来帮助解决这些挑战，例如用于可靠的 DAO 执行的 Chainlink Keepers，用于奖励合适的贡献者的 Chainlink Data Feeds，以及用于随机样本投票的 Chainlink VRF，Chainlink Any API 和 Keepers 可用于中继-chain 在链上投票，Chainlink oracles 可以通过链下投票机制减少提案垃圾邮件。此外，

## 去中心化治理的挑战

去中心化治理依赖于治理代币、链上投票和对提案的开放访问。但是，通常需要额外的支持以确保 DAO 免受不良行为者的侵害并保持去中心化。以下是基本的 DAO 治理结构面临的一些挑战：

- **代币所有权集中：**在简单的代币治理模型中，一个代币等于一票。这可能会导致单个 DAO 成员或一小群成员对 DAO 产生过多的影响和集中控制。
- **低参与度和高成本：**要使 DAO 去中心化，必须有许多成员参与投票。但是，如果只有一小部分持币成员参与投票，那么决策就只掌握在少数成员手中。链上投票可以通过增加在网络活动高峰期变得昂贵的交易费用来使情况变得更糟。
- **难以管理的提案：**开放的提案会吸引垃圾邮件并转移对重要提案的注意力。结构不良的提案和不相关的主题会破坏 DAO 的执行能力。
- **DAO 功能的不可靠执行：** DAO 需要确保可靠地执行通过的提案。这需要直接将通过的投票与特定智能合约功能的执行联系起来，从而造成单点故障。

## Chainlink 如何推动 DAO 的透明度和去中心化

DAO 处于早期阶段，通常基于区块链且完全在线，提供各种服务。[Chainlink](https://chain.link/)为 DAO 提供安全的数据和计算服务，并举例说明随着 DAO 与现实世界的融合程度越来越高，它们如何在现在和未来提供帮助。

- **使用 Chainlink Keepers 解决不可靠的 DAO 执行：** DAO 变得越来越复杂，但 Chainlink 可以帮助他们从头到尾创建自动化和去中心化的流程。Chainlink Keepers 可以协助完成一些常见任务，例如重新平衡资金库、质押或卸载治理代币、在投票期结束时执行功能以及随着时间的推移分散治理代币奖励。DAO 的问题在于智能合约无法自行触发。相反，外部实体必须在投票通过时触发智能合约中的功能。Chainlink Keepers 可以通过让专业人员监控 DAO 投票并在投票通过时触发智能合约来解决这个问题。
- **通过 Chainlink 数据馈送奖励合适的贡献者：**随着 DAO 与现实世界的联系越来越紧密，它们可以跟踪区块链之外的行为，以创建一个强大的代币治理系统，奖励执行任务的成员。例如，Reforestation DAO 希望利用其资金在世界各地重新造林。成员获得一部分预算来组织重新造林项目。为了使代币治理系统发挥作用，DAO 需要监控外部活动。执行好任务的成员通过治理代币获得对国库的更多权力。Chainlink 数据源可以提供帮助。Reforestation DAO 可以使用卫星图片来衡量成功并通过 Chainlink 将真实世界的数据发送到区块链。这样，合适的人就会因帮助 DAO 的使命而获得奖励。
- **使用 Chainlink VRF 进行随机抽样投票：**投票是一个复杂的问题，无论是在链上还是链下。为了确保民主进程，必须有大量的人参与。一种解决方案是随机抽样投票，随机选择一组 DAO 成员对提案进行投票。这简化了达到法定人数要求的同时仍然代表更大群体的意见。然而，随机投票需要一个安全的链上随机数生成器 (RNG) 来确保选择过程的公平和可信。Chainlink VRF 是一种可靠的链上 RNG，经过长期测试，可用于随机抽样投票。它在一些争议裁决模型中也很有用，在这些模型中，一组随机选择的参与者决定争议，类似于法庭陪审团。可验证的随机性对于更公平的投票过程有很多好处。
- **利用 Chainlink Any API 和 Keepers 在链上中继链下投票：**目前，DAO 成员需要支付费用才能在链上投票，这可能会导致 gas 价格高时参与度降低。这可以防止 DAO 做出每个人都同意的决定。为了解决这个问题，Chainlink Any API 可用于监控链下投票并将结果安全地发送到区块链。Chainlink Keepers 可以将投票结果通知智能合约，从而消除了投票的 gas 成本。这有利于在 DAO 中影响力较小的人，因为他们可以在不支付费用的情况下发表意见。使用 Chainlink 服务是一个全面的解决方案，可以降低汽油费，同时仍确保投票记录在区块链上。
- **使用 Chainlink Oracles 通过链下投票减少提案垃圾邮件：**链下投票可以过滤掉不良提案，并通过使用多层提案来防止 DAO 中的提案垃圾邮件。要提出初始提案，DAO 成员必须首先收集一定数量的选票。这一要求阻止了低质量的提案或不良行为者提交的提案成为正式投票的一部分。通过 Chainlink 预言机进行链上交付的链下投票机制可最大限度地降低网络成本，同时过滤掉错误和恶意的提议。

## 使用 Chainlink 的 DAO 示例

- **OlympusDAO**在以太坊主网上集成了 Chainlink VRF，以确保在其去中心化金融储备协议上进行可证明的公平和透明的无损失抽奖。Chainlink VRF 提供了一个安全的随机数生成器，它是防篡改和可在链上验证的，确保抽奖活动不被外部实体操纵。OlympusDAO 还通过 sOHM 提供质押奖励，这些奖励可以兑换为 OHM 并委托给 (3,3) Together 抽奖活动。正在考虑进一步集成 Chainlink 产品，例如 Chainlink Keepers，以自动化 sOHM 的供应基础。
- **MoonDAO**是一个将两人送入太空的 DAO，它在以太坊主网上集成了 Chainlink 可验证随机函数（VRF），通过公开抽奖的方式选出获胜的 NFT 太空票。Chainlink VRF 提供防篡改和可审计的随机源，创造更透明的用户体验。MoonDAO 之所以选择 Chainlink VRF，是因为它基于前沿的学术研究，得到经过时间考验的预言机网络的支持，并通过加密证明的生成和链上验证来确保提供给智能合约的每个随机数的完整性。
- **BitDAO**集成了 Chainlink 的去中心化价格预言机，以优化 BIT 在 DEX 上的交易，并能够在 DeFi 平台上作为抵押品上市。Chainlink 的高质量数据、与区块链无关的基础设施、强大的监控以及安全性和可靠性方面的声誉使其成为支持去中心化经济建设者的 BitDAO 使命的不二之选。与区块链无关的平台允许由 BIT 代币持有者投票表决的提案提交给其国库，包括合作伙伴关系、互换和通过专门的自治实体进行扩展。

> 原文：https://jyotirmoy.hashnode.dev/chainlinks-solutions-for-decentralized-governance-challenges

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/qrcode.png)