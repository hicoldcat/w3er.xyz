---
title: 创建您自己的加密货币：ERC-20 代币分步指南
description: null
author: 李留白
weight: 0
date: 2023-06-15T10:24:28.823Z
lastmod: 2023-06-15T10:29:04.466Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230615182721.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230615182721.png)

你有没有想过创建你的加密货币？随着比特币和以太坊等加密货币的兴起，许多人正在寻找创建数字资产的方法。一种流行的选择是使用 ERC-20 标准在以太坊区块链上创建代币。

ERC-20 是开发人员在以太坊区块链上创建可替代代币必须遵循的一组规则。可替代代币是可互换的资产，这意味着代币的每个单位都与其他单位相同。这使它们成为货币或其他需要易于分割和转移的资产的理想选择。

要创建 ERC-20 代币，您需要编写一个智能合约来定义代币的属性和功能。我们将使用 Remix IDE 来编写这个智能合约，因为它很容易在 Remix IDE 中部署和测试合约。以下是创建您自己的 ERC-20 代币的分步指南：

1. 为合约定义变量和映射。balanceOf 映射用于跟踪每个地址持有多少代币，而 allowance 映射用于跟踪一个地址允许另一个地址花费多少代币。

   ![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230615182801.png)

2. 定义构造函数以初始化令牌变量的状态。在这里，您将定义代币的名称、符号、小数位和总供应量。这里，名称是令牌的名称，例如 ojucoin，符号是一个简短的 3 个字符的单词，唯一定义任何令牌。例如对于 ojucoin，它可以是 OJU。Decimal 定义小数点到令牌值的位置。总供应量是我们最初供应的代币总数。最后两行定义，balanceOf 映射中的初始余额来自地址（0）。

   ![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230615182809.png)

3. 为合同定义事件。事件用于通知外部应用程序合约中发生的重要事件。当代币从一个地址转移到另一个地址时会触发 Transfer 事件，而当一个地址批准另一个地址花费一定数量的代币时会触发 Approve 事件。

   ![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230615182827.png)

4. 定义合约的功能。有许多功能要添加到合约中。

   ![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230615182835.png)

   transfer() 函数从发送方地址中扣除一定数量的代币，并将其转入接收方地址。

   ![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230615182841.png)

   approve() 函数用于授予另一个帐户从发送者帐户中花费一定数量代币的权限。

   ![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230615182849.png)

   transferFrom() 函数用于从发送者的账户中扣除代币并将其添加到支出者的账户中。

   ![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230615182855.png)

5. 部署和测试智能合约。您可以通过将令牌发送到特定地址并在 etherscan 上验证交易来测试传输功能。

创建您自己的 ERC-20 代币是一种有趣且有益的体验。它允许您创建可用于多种用途的数字资产，从创建您的加密货币到为您的企业创建奖励计划。按照上述步骤，您可以创建自己的 ERC-20 令牌并加入不断发展的加密货币世界。

> 原文：https://ojasaklecha.hashnode.dev/creating-your-own-cryptocurrency-a-step-by-step-guide-to-erc-20-tokens