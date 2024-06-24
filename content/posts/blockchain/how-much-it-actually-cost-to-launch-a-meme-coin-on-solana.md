---
title: 在 Solana 上推出 Meme Coin 的实际成本是多少
description: null
author: 李留白
weight: 0
date: 2024-06-24T04:27:26.439Z
lastmod: 2024-06-24T04:33:03.334Z
tags: []
categories:
    - Web3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240624122738.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240624122738.png?x-oss-process=image/watermark,size_20,text_5YWs5LyX5Y-3wrfmnY7nlZnnmb0,color_FFFFFF,t_60,g_center)

我们中的许多人都考虑发行自己的加密货币代币，尤其是 Meme 币，因为它有可能以最小的投资获得高回报。然而，一个关键问题仍然存在：**所需的最低投资是多少？**

在本文中，我们将提供关于如何推出自己的 meme 币的全面指南，包括我用来铸造和增加流动性的工具。具体来说，我使用了：

## 步骤 1 — 铸造代币

![Meme Coin Creator 平台 — 启动您的 Solana 代币](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/1*K2krNZO9nVY_znf9nVKeaA.png?x-oss-process=image/watermark,size_20,text_5YWs5LyX5Y-3wrfmnY7nlZnnmb0,color_FFFFFF,t_60,g_center)

要创建您的代币，我建议使用[Meme Coin Creator](https://memecoincreator.com/)，这是我个人使用过的值得信赖的平台。此工具已通过Solana 领先的安全审计公司[Blowfish.xyz](https://blowfish.xyz/)的验证，确保了高水平的安全性和可靠性。值得注意的是，Meme Coin Creator 每天促进数百个 meme 币的创建，使其成为代币创建的热门选择。

**你需要什么**

- 代币名称（您的代币名称）
- 硬币符号（醒目的 3 到 5 位数字符号）
- 图像（确保添加合适的 512x512）
- 总供应量（对于 Meme 币来说 10 亿是一个不错的数字）
- 小数（通常设置为 9）

一旦您启动令牌创建过程，系统将提示您授权三个 Web3 签名。

1. 第一个签名是平台费，金额为 0.5 Solana。
2. 第二个签名用于实际创建令牌。
3. 第三个签名用于铸造和设置代币的供应量。

请注意，创建代币的总成本（包括平台费用和网络费用）约为**0.56 Solana。**

## 第 2 步 — 撤销铸造和冻结权限

撤销铸造和冻结权限，以确保您可以增加流动性，这对您的代币交易至关重要。请注意，此步骤至关重要，因为未能撤销权限将阻止您增加流动性。此过程可以在同一平台上完成，每次操作收取约 0.1 SOL 的费用，总计**0.2 SOL。**

## 步骤 3 — 创建市场 ID

Openbook 市场 ID 是与您的代币匹配的唯一 ID。此唯一 ID 是在 Raydium.io 市场（Solana 网络上领先的交易所）上进行交易所必需的。

要创建市场 ID，请导航至[raydium.io/create-market](https://raydium.io/create-market/)，它将引导您进入以下屏幕。

![使用 Raydium 创建市场 ID](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/1*GDdSAVjVcYt1N1pKynWoPQ.png?x-oss-process=image/watermark,size_20,text_5YWs5LyX5Y-3wrfmnY7nlZnnmb0,color_FFFFFF,t_60,g_center)

请按照下列步骤操作：

1. 选择您铸造的代币作为基础代币。
2. 选择 Sol 作为报价代币。
3. 根据代币总供应量来设置适当的大小和刻度大小。例如，如果您的总供应量为 10 亿个代币：

*— 将订单大小设置为 100。*

*— 复制相应的刻度大小。*

*— 确保值正确对齐并填写创建市场字段。*

**请注意，创建市场 ID 总共需要大约2.8 Solana**的费用。此步骤对于建立交易对并为您的代币提供流动性至关重要。

![订单大小和最小变动单位](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/1*3iSM-uxZGsJDxC4QnM5msw.png?x-oss-process=image/watermark,size_20,text_5YWs5LyX5Y-3wrfmnY7nlZnnmb0,color_FFFFFF,t_60,g_center)

![Solana Create Market 的费用](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/1*Ua0Oqz1SOQuoEFuduBwSpQ.png?x-oss-process=image/watermark,size_20,text_5YWs5LyX5Y-3wrfmnY7nlZnnmb0,color_FFFFFF,t_60,g_center)

## 步骤 4 — 增加流动性

这一步至关重要：为您的代币增加流动性。增加足够的流动性以吸引市场关注至关重要。我们建议至少增加价值 1000 美元的流动性，但理想情况下为 2000 美元或更多。这将创造良好的交易环境并鼓励市场参与。

就上下文而言，如果您的代币价值为 0.01 美元，则需要添加：

- 100,000 个代币（= 1000 美元）作为最低建议流动性
- 200,000 个代币（= 2000 美元）为理想流动性金额

请记住，Raydium 会收取**0.4 Sol**的增加流动性费用，因此请将其计入您的计算中。

增加足够的流动性可确保顺畅的交易体验，增强市场信心，并有助于防止流动性问题。不要低估这一步的重要性！

## 第 5 步 — 销毁流动资金池

创建 LP 后，您需要立即销毁流动性池，以便其能够泵送。我使用的工具是https://sol-incinerator.com/

![Burn the LP](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/1*1i8L-nc0H8k0KgMUvRpJWg.png?x-oss-process=image/watermark,size_20,text_5YWs5LyX5Y-3wrfmnY7nlZnnmb0,color_FFFFFF,t_60,g_center)

## 在 Solna 上创建代币的总成本

让我们回顾一下代币创建过程中产生的成本：

- **铸造代币：** 0.56 Solana（包含平台费和网络费）
- **撤销权限：** 0.2 Solana（确保你可以增加流动性）
- **市场 ID 创建：** 2.8 Solana（建立交易对并实现流动性）
- **Raydium 上的流动性池：** 0.4 Solana（初始流动性添加费）

**总费用：**约 3.96 Solana（为简单起见，四舍五入为 4 Sol）

**最低流动性要求：** 1200 美元（相当于 8-10 索尔，取决于代币的价值）

**具有最低流动性的总成本：** 12–15 Solana（包括所有费用和最低流动性）

通过此分类，您应该可以清楚了解在 Solana 上启动 meme 项目所涉及的成本。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/免责声明.png)

> https://medium.com/memecoincreator/how-much-it-actually-cost-to-launch-a-meme-coin-on-solana-711061edcaed