---
title: 探索 EPNS，一种对 DApp 友好的通知系统
description: null
author: 李留白
weight: 0
date: 2022-11-17T13:06:47.545Z
lastmod: 2022-11-17T13:25:55.324Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211172106971.png
---

去中心化应用程序 (DApps) 每天都变得越来越流行，许多处理交易的智能合约部署到需要通知系统的各种区块链网络来跟踪区块链上的活动。

推送通知显然已经存在了一段时间：简单地说，它们提供了从服务器接收消息的过程，该过程会触发客户端应用程序上的弹出窗口，对于 Web、移动和桌面平台可以有不同的形式。

在以太坊推送通知服务 (EPNS) 出现之前，没有真正的方法可以在智能合约和 DApp 上实现推送通知功能。

虽然像 Metamask 这样的一些钱包会通知用户在他们的账户上进行的每一笔交易，但智能合约通知以前是不可能的。但是，在这篇文章中，我们将介绍以下内容以了解 EPNS 如何在 DApp 中工作。

## 什么是 EPNS？

EPNS 是一种去中心化的通知协议，它可以无缝地为以太坊钱包地址（或用户）提供访问权限，以接收链上或链下活动的通知。

借助 EPNS DApp，智能合约和后端应用程序可以以与平台无关的方式向用户发送通知。

EPNS 通知之所以成为可能，是因为开放的通信网络（也称为推送节点）与钱包地址相关联，使加密钱包、DApp 前端和其他服务能够向用户显示通知。

EPNS 还有一个名为 $PUSH 的代币，使 EPNS 用户能够根据直接或间接的行为获得奖励。

## EPNS 特点

### 以用户为中心和选择加入的通知

EPNS 协议允许用户直接控制他们从哪些服务接收通知并对服务施加严格的规则，包括对用户的垃圾邮件保护。

### 激励用户的通知

想要向订阅者发送通知的服务必须抵押 DAI。在此过程中赚取的利息将部分分配给选择接收通知的订阅者。EPNS 通过使用奖励来激励发送者和接收者。

### 平台无关

EPNS 协议还启用了可连续启用通用通知的每个通知的可检索信息。它向任何加密钱包、移动应用程序、网络浏览器、应用程序或集成该协议的其他平台发送通知。

### 垃圾邮件分数和节流

频道包含范围从`0`到的垃圾邮件分数`1`。`0`表示通道得分很高，`1`表示通道极度不健康。

### 治理

协议治理旨在激励继续采用 EPNS 协议，这是通过为所有相关用户提供激励来实现的。

## 推送节点和 $PUSH 代币

EPNS 是一种协议，它有自己的节点（PUSH 节点），由 $PUSH 令牌管理。PUSH 节点监听 EPNS 协议智能合约中的事件，并发送由相应接收者的事件触发的通知。

### 什么是 $PUSH 代币？

$PUSH 代币用于 EPNS 生态系统中的治理。EPNS 协议根据 EPNS 协议上的特定操作为用户提供激励，包括直接和间接订阅频道，用户因参与 EPNS 协议而获得这些激励。

当在协议中向用户提供令牌激励时，用户可以随时领取它们。这些代币激励是使用[AAVE aDAI (AAVE Interest Bearing DAI)](https://aave.com/aTokens)生成的。然后 aDAI 在提款时转换为 $PUSH 代币。

一个 `$PUSH` 代币的总供应量为 100,000,000，并且只会创建 100,000,000。在[撰写本文时，$PUSH 代币的价值为 1.13 美元](https://coinmarketcap.com/currencies/epns/)，流通供应量为 $PUSH 代币总量的 15,287,926（约 15%）。

## 为什么我们需要 Web3 推送通知？

推送通知是 Web2 生态系统中不可或缺的功能，并成为向用户发送信息和留住用户的重要策略。但是，Web2 通知完全由应用程序自己创建和规定。

用户几乎无法选择他们希望收到什么样的通知；他们可以关闭或打开应用程序通知。

另一方面，像 EPNS 这样的去中心化通知为 Web3 生态系统提供了强大和改进的通知生态系统的好处，而没有 Web2 通知的集中式缺点。

要接收 Web2 通知，用户必须安装他们希望从中接收通知的应用程序，或者访问他们的网站并接受他们的通知提示。

使用 EPNS，用户无需安装应用程序即可接收来自 Coindesk 等平台的通知；他们所做的只是选择加入他们的频道，为用户提供更多权力来决定他们选择接收哪些通知。

与 Twitter、Discord 或电子邮件等平台相比，应用程序需要更好的方式以实时和事件启动的方式与用户进行通信。用户还应该有一个更简单的过程来控制他们收到的通知，并且应该从[Web3 的开源性质](https://blog.logrocket.com/best-practices-web3-providers/)中受益。

## 受益于 EPNS 的去中心化平台

Web3 生态系统中的几乎所有应用程序都可以从推送通知中受益。其中一些类型的 DApp 是：

- [去中心化金融（DeFi）](https://blog.logrocket.com/top-5-decentralized-app-development-frameworks/#defi)
- 去中心化交易所 (DEXes)
- 以太坊名称服务 (ENS)/消费者应用
- 不可替代代币 (NFT) 和游戏

### 去中心化金融

在 DeFi 中使用 EPNS 时，通知对借贷协议用户很有用。有了这个，用户可以设置通知，以便在他们质押的代币清算时得到通知。他们现在可以接收无缝通知，而不是定期手动检查。

### 去中心化交易所

DEX 用户可以设置通知，在交易完成时通知他们，而不是不断地检查。EPNS 使他们能够得到无缝通知；例如，用户可以创建有关天然气价格何时有利的通知，以帮助他们决定何时进行交易。

### ENS

诸如以太坊名称服务 (ENS) 之类的应用程序可以在订阅即将到期时通知用户，而不是让用户定期检查以提醒自己到期日期。

### NFT 和游戏

在市场上交易的 NFT 或游戏中的商品可以连接到 EPNS，以在某些商品开始销售或拍卖开始时通知用户。

NFT 艺术家还可以在对他们的 NFT 出价或出价时收到通知，并为他们的 NFT 转售创建通知。

## EPNS协议解决方案的组成部分

EPNS解决方案由四个模块组成：服务、渠道、用户和订阅者。
服务是 DApp、钱包所有者或任何想要发送通知的 Web3 服务的地址，而通道是在 EPNS 协议上激活自身并可以创建通知的服务。

用户是 Web3 参与者，他们希望接收有关 DApp、钱包或智能合约的通知，订阅者是选择从任何特定渠道接收通知并可以选择发送特定通知的特定渠道的用户。

## 如何将 EPNS 集成到您的 DApp 中

要将 EPNS 集成到您的 DApp 中，您必须创建一个 EPNS 通道。你可以在 EPNS the [DApp mainnet website](http://app.epns.io/)或[staging website](http://staging-app.epns.io/)上执行此操作。访问该网站后，请按照以下步骤创建频道。

首先，系统会提示您连接您的加密钱包（Metamask、Ledger 或 Portis，因为 EPNS 暂存 DApp 仅支持 Kovan 测试网络）。

连接您的钱包后，点击**创建您**的频道并上传频道徽标（徽标的尺寸必须为 128px x 128px）。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211172110827.png)

要激活该通道，您需要质押[DAI 代币](https://blog.logrocket.com/develop-solidity-smart-contracts-hardhat/)以贡献给 AAVE 上的生息质押池。

50 DAI 是最低质押费用，质押的 DAI 越多，渠道产生的利息就越多。反过来，这会激励订阅者（在登台时，您会收到 50 DAI 用于测试）。

质押 DAI 后，您可以为您的频道命名和描述。然后单击**SETUP CHANNEL**将其激活。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211172110030.png)

成功创建通道后，您可以将其集成到您的 DApp 后端或前端应用程序中。

### 将 EPNS 集成到 Node.js 后端

要在后端集成 EPNS，您必须首先安装 EPNS 后端生产或登台 SDK：`npm install @epnsproject/backend-sdk`用于主网或`npm install @epnsproject/backend-sdk-staging`用于staging。

接下来复制创建EPNS通道地址的私钥；[你可以在这个 GitHub 要点中找到代码](https://gist.github.com/elraphty/185250443a36d0495839d782364bef3b)：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211172110317.png)

### 将 EPNS 集成到 React 前端

要在 React 前端集成 EPNS，您必须安装 EPNS 前端生产或暂存 SDK `npm install @epnsproject/backend-sdk`以用于生产或`npm install @epnsproject/backend-sdk-staging`测试或开发。

[您可以在此 GitHub 要点中找到以下代码](https://gist.github.com/elraphty/5c1e25200d4a55b53600cd1b8ab392d2)：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211172111146.png)

### 在前端应用程序上订阅 EPNS 频道

[用户还可以使用以下代码](https://gist.github.com/elraphty/a2fa428876fa981693ecadc1cbad3d9d)从前端应用程序订阅 EPNS 频道：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211172111412.png)

## 如何下载 EPNS 和选择加入频道

您还可以通过从 Google Playstore 或 Apple Appstore[下载手机上的应用程序来使用 EPNS 。](https://epns.io/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211172111679.png)

您还可以使用 EPNS 桌面和网络应用程序，或在您的网络浏览器上下载 EPNS 网络扩展，[然后连接您的钱包](https://app.epns.io/)并根据您希望接收的通知选择不同的渠道。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211172112726.png)

## 结论

推送通知仍然是用户流行的应用程序功能，但对于 EPNS，Web3 的这一不可或缺的功能为用户提供了对通知设置、他们希望接收的通知以及参与 EPNS 协议的奖励的更多控制。

EPNS 改进了当前的 Web2 推送通知协议，Web3 用户将从该解决方案中受益匪浅。我希望每个 Web3 用户都使用 EPNS 进行无缝通知。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
