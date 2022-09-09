---
title: 5个获取用于dapp测试和开发的测试网代币的地方
description: null
author: 李留白
weight: 0
date: 2022-09-09T10:07:28.128Z
lastmod: 2022-09-09T10:17:39.824Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091807551.png
draft: true
---

## 介绍

无论你是铸造 NFT、部署智能合约，还是在以太坊网络上开发下一个大型 dApp，你都必须支付[gas 费](https://web3.hashnode.com/glossary/what-is-eth-gas)。

然而，在开发过程中在以太坊区块链上支付真正的汽油费可能非常昂贵。因此建议在开发过程中使用 Testnet Token 或 Devnet Token。

本文讨论了各种类型的以太坊区块链测试网，并提供了 5 个来源，您可以在其中为基于以太坊的 dApp 开发请求测试网（假）令牌，然后再将它们部署到生产环境。

## 什么是汽油费？💰

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091808664.png)

汽油费或交易费是以太坊区块链上每笔交易的网络费用，用于补偿矿工验证区块链上的交易。

区块链矿工是使用软件解决与交易相关的算法来检查和确认区块链交易的人。

## 什么是测试网代币？

测试网代币或硬币是从水龙头获得的真实加密货币的伪造版本，可用于在开发过程中支付以太坊汽油费。

加密水龙头是一个应用程序或网站，它为用户提供少量加密货币，以换取完成简单的任务，例如解决[CAPTCHA](https://support.google.com/a/answer/1217728)（完全自动化的公共图灵测试，以区分计算机和人类）。

> 从水龙头获得的测试网代币不是真正的资金，没有价值。

## 以太坊测试网的类型

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091809646.png)

在将 dApp 部署到以太坊主网上之前，我们可以开发和测试不同类型的以太坊测试网络。我们将在下面讨论 Rinkeby、Goerli、Ropsten 和 Kovan 测试网络：

### 1. Rinkeby 测试网

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091809231.png)

[Rinkeby](https://www.rinkeby.io/#stats)是一个权威证明区块链，由 Geth 团队于 2017 年 4 月创立。这是一个由 Geth 网络、Nethermind 网络和 Hyperledger Besu 网络支持的以太坊主网分叉。

Rinkeby 网络旨在在开发过程中测试 dApp，然后再将它们部署到以太坊主网络上。

Rinkeby 测试网络包含在您的 Metamask 钱包中

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091809195.png)

**区块浏览器**：[explorer.anyblock.tools/ethereum/ethereum/rinkeby](https://explorer.anyblock.tools/ethereum/ethereum/rinkeby/)。

**网站**：[www.rinkeby.io](https://www.rinkeby.io/#stats)

### 2. Goerli 测试网

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091809779.png)

> 不要以封面来判断一本书。

[Goerli](https://goerli.net/)，也称为 Görli 测试网，是 2018 年 9 月推出的第一个以太坊权威证明跨客户端测试网。它旨在在多个网络上运行，包括 Parity Ethereum、Geth、Nethermind、Hyperledger Besu（以前的 Pantheon ) 和 EthereumJS。

它也是一个开源的以太坊测试网络，可以在部署到主网（以太坊主网络）之前进行区块链开发测试。

Goerli 测试网络包含在您的 Metamask 钱包中：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091810531.png)

**区块浏览器**：[explorer.anyblock.tools/goerli](https://explorer.anyblock.tools/goerli/)。

**网址**：[goerli.net](https://goerli.net/)

### 3. Ropsten 测试网

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091810707.png)

[Ropsten](https://faucet.ropsten.be/)网络是支持 Geth 和 Parity 客户端的首批测试网络之一（自 2016 年以来）。它基于工作量证明，并且成功了第一个名为“Morden”的测试网络。

Ropsten 使用与以太坊主网相同的算法，它被认为是以太坊主网络的原始副本，这使其非常适合开发。

它在 2017 年 2 月至 2017 年 3 月期间经历了多次网络攻击，这迫使团队关闭了该项目，并在 2017 年 4 月将其重新启动。

Ropsten 测试网络包含在您的 Metamask 钱包中：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091810308.png)

**区块浏览器**：[explorer.anyblock.tools/ethereum/ethereum/ropsten](https://explorer.anyblock.tools/ethereum/ethereum/ropsten/)。

**网站**：[faucet.ropsten.be](https://faucet.ropsten.be/)

### 4. Kovan 测试网

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091810673.png)

Kovan 测试网络是以太坊的权威证明测试网络，最初由 Parity 团队（现为 OpenEthereum）于 2017 年 3 月启动。

就像 Rinkeby 测试网一样，Kovan 是以太坊主网的一个分支，这使得它非常适合基于以太坊的 dApp 开发。

Kovan 测试网络包含在您的 Metamask 钱包中：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091811297.png)

**区块浏览器**：[explorer.anyblock.tools/ethereum/ethereum/kovan](https://explorer.anyblock.tools/ethereum/ethereum/kovan/)

**网站**：[kovan-testnet.github.io/website](https://kovan-testnet.github.io/website/)

## 5 个获取测试网代币的地方

### 1. 链环水龙头

Chainlink (LINK) 是一个加密货币和技术平台，使非区块链企业能够安全地连接到区块链平台。

LINK 测试网还分发测试网代币以支付用于测试目的的交易费用。

如何从 Chainlink 获取测试网代币：

- 访问：[faucets.chain.link](https://faucets.chain.link/)。
- 连接你的钱包。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091811258.png)

- 选择您喜欢的网络（即**Kovan**）。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091811737.png)

- 确保将您的 Metamask 切换到所选网络。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091811057.png)

- 解决验证码
- 然后，单击“向我发送 0.1 testnet ETH”按钮。
- 等待交易确认：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091812700.png)

- 交易完成后，您请求的代币将记入您的钱包：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091812606.png)

- 检查您的 Metamask 钱包是否有新余额：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091812739.png)

Chainlink 支持的网络：**Ethereum Rinkeyby**、**Avalanche Fuji**、**BSC Testnet**、**Ethereum Görli**、**Ethereum Rinkeby**、**Fantom Testnet**、**POA Network Sokol**和**Polygon Mumbai**。

### 2.炼金龙头

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091812216.png)

> “Alchemy 的目标是成为考虑在区块链或主流区块链之上构建产品的开发人员的起点 - [techcrunch.com](https://techcrunch.com/2022/02/08/alchemy-which-aims-to-be-the-de-facto-platform-for-developers-to-build-on-web3-raises-another-200m-and-is-now-valued-at-10-2b/) ”。

他们还分发免费的 Ethereum Rinkeby 代币，供开发人员在 dApp 开发期间使用。

如何从 Alchemy 获取测试网代币：

- 访问[炼金术水龙头网站](https://rinkebyfaucet.com/)。
- 在输入框中复制粘贴你的以太坊钱包地址，点击“Send Me ETH”按钮，如下图：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091813666.png)

- 接下来，等待请求被处理，如果请求成功，您将看到这样的屏幕：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091813972.png)

- 检查您的 Metamask 钱包是否有新余额：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091813197.png)

> 您可以通过免费创建 Alchemy 帐户来请求更多**Rinkeby 代币。**

Alchemy 支持的网络：仅限**Rinkeby**。

### 3.计算水龙头

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091813843.png)

- 访问[Komputing faucet](https://fauceth.komputing.org/)，并输入您的钱包地址。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091814758.png)

- 选择您喜欢的网络，并解决验证码：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091814242.png)

- 单击“请求资金”按钮，然后等待您的请求得到处理：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091814506.png)

- 检查您的 Metamask 钱包是否有新余额：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091815739.png)

### 4. Kovan Gitter 聊天室

您可以通过加入聊天并发送您的钱包地址使用 Kovan Gitter 聊天室接收 Kovan ETH。

- 访问[Kovan Gitter 聊天室](https://gitter.im/kovan-testnet/faucet#)。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091815743.png)

- 您可以使用 GitHub、GitLab 或 Twitter 帐户加入聊天：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091815903.png)

- 接下来，将您的钱包令牌复制并粘贴到聊天中，您的请求将被处理：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091815151.png)

- 检查您的 Metamask 钱包是否有新余额：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091816094.png)

### 5. MetaMask 以太龙头

- 访问[MetaMask 以太水龙头](https://faucet.metamask.io/)。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091816507.png)

- 点击“request 1 ether from faucet”按钮，连接你的 Metamask：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091816036.png)

- 连接钱包后，将显示您的钱包地址和余额。然后，您可以通过单击“从水龙头请求 1 个以太币”按钮来请求 1 个以太币。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091816442.png)

- 检查您的 Metamask 钱包是否有新余额：
- 如果您的钱包中有足够的以太币，您可能会收到以下错误消息：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202209091817145.png)

> Metamask Ether Faucet 不会将测试代币分发到具有足够代币的钱包地址。

## 结论

测试网是主区块链的副本环境，允许开发人员测试他们的区块链应用程序。

测试网是某个区块链的副本。

在本文中，我们回顾了四个以太坊测试网（Rinkeby、Goerli、Ropsten 和 Kovan 测试网络），以及我们可以在哪里获得他们的（假）代币以用于基于以太坊的 dApp 开发。

<!-- https://web3.hashnode.com/5-places-to-get-testnet-tokens-for-dapp-testing-and-development -->