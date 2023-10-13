---
title: Goerli 与 Sepolia 测试网比较：哪个更好？
description: null
author: 李留白
weight: 0
date: 2023-10-13T13:44:28.740Z
lastmod: 2023-10-13T13:49:23.842Z
tags: []
categories:
    - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231013214456.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231013214456.png)

Goerli 和 Sepolia 是以太坊开发人员用来测试其应用程序的两个测试网。web3 开发人员在选择测试网之前应考虑多种原因，包括测试 ETH 的可用性、RPC 节点提供商支持、智能合约可用性等。 

在本文中，我们将介绍每个测试网的含义、它们的技术差异，重点介绍三个注意事项，并帮助您为您的项目选择最佳的以太坊测试网。

##### 弃用通知

虽然您可以使用 Goerli 测试网，但我们对此持谨慎态度，因为以太坊基金会已宣布[**Goerli 将很快被弃用**](https://alchemy-com.proxy.usepastel.com/_domains/com/alchemy/www/_url/https://www.alchemy.com/blog/goerli-faucet-deprecation)。因此，我们建议使用[**Sepolia 测试网**](https://alchemy-com.proxy.usepastel.com/_domains/com/alchemy/www/_url/https://www.alchemy.com/overviews/sepolia-testnet) ，因为 Alchemy 具有完整的 Sepolia 支持和免费的[**Sepolia 水龙头**](https://sepoliafaucet-com.proxy.usepastel.com/_domains/com/sepoliafaucet/_url/https://sepoliafaucet.com/)。

## **Goerli测试网是什么？**

Goerli 于 2018 年作为 ETH-Berlin 的一个黑客马拉松项目开始，并于 2019 年正式启动。Goerli[测试网络](https://www.alchemy.com/overviews/goerli-faucet)是一个公共的权益证明 (PoS) 测试网，由开放的验证器集维护，这意味着任何愿意的人都可以使用运行自己的测试网验证器就可以参与。然而，由于网络状态较大，需要更长的同步时间和更多的存储来运行节点。 

### **与 Sepolia 相比的主要区别**

- 打开验证器集
- 无需许可即可达成共识
- 支持跨客户端功能
- 承载大状态并需要高存储承诺
- 需要更长的时间来与网络当前状态同步

### Goerli链资讯

如果您要将 Goerli 区块链添加到钱包中，则需要包含以下[Goerli 链 ID 和网络信息：](https://www.alchemy.com/chain-connect/chain/goerli)

- **网络名称** - Goerli测试网
- **RPC URL** - https://eth-goerli.g.alchemy.com/v2/[您的 API 密钥]
- **链条 ID** - 5
- **货币符号**- GoerliETH
- **区块浏览器 URL** - https://goerli.etherscan.io/

要开始在 Goerli 上进行构建，[请注册](https://dashboard.alchemy.com/signup/?a=sepolia-vs-goerli)一个免费的 Alchemy 帐户，并从 Alchemy 的[公共 Goerli 水龙头](https://goerlifaucet.com/)获取免费的 GoerliETH 代币。

## 什么是 Sepolia 测试网？

Sepolia于 2021 年推出，[是一个经过许可的权益证明测试网络，](https://www.alchemy.com/overviews/sepolia-testnet)由主要由客户和测试团队控制的封闭验证器集维护。虽然开发人员可以公开使用测试网来测试和部署他们的去中心化应用程序（dapp），但并不是每个人都可以选择运行验证器节点，使其成为一个许可网络

由于 Sepolia 作为一个较年轻的测试网，与 Goerli 等更成熟的测试网相比，部署的应用程序较少。它具有较小的状态和历史记录，允许更快的同步并且需要最少的磁盘空间来运行节点。

### **与 Goerli 相比的主要区别**

- 封闭验证器集（权威证明配置）
- 获得共识的访问已获得许可
- 验证器集受到限制，主要由客户和测试团队监督
- 支持跨客户端兼容性
- 较小的区块链状态和历史
- 同步速度快且需要更少的存储承诺

### Sepolia链信息

如果您要将 Sepolia 区块链添加到钱包中，则需要包含以下[Sepolia 链 ID 和网络信息：](https://www.alchemy.com/chain-connect/chain/sepolia)

- **网络名称**- Sepolia 测试网络
- **RPC URL** - https://eth-sepolia.g.alchemy.com/v2/[您的 API 密钥]
- **链条 ID** - 11155111
- **货币符号**- SepoliaETH
- **区块浏览器 URL** - https://sepolia.etherscan.io/

要开始在 Sepolia 上进行构建，[请注册](https://dashboard.alchemy.com/signup/?a=sepolia-vs-goerli)一个免费的 Alchemy 帐户，并从 Alchemy 的公共水龙头[获取免费的 SepoliaETH 代币](https://www.alchemy.com/overviews/sepolia-eth)。

## 选择在 Sepolia 或 Goerli 上进行开发之前需要考虑的事项

在选择 Sepolia 测试网络或 Goerli 之前需要考虑的三件事是：测试 ETH 的可用性和成本、RPC 和 API 支持以及智能合约可用性。

### 1. 测试ETH的可用性

在 Goerli 和 Sepolia 之间进行选择时的主要考虑因素是可用性、可访问性以及获取测试 ETH 代币的成本。测试 ETH 代币用于支付测试网上的 Gas 费用，并且 web3 开发人员需要在与以太坊主网环境密切相关的环境中部署和测试其智能合约。

#### Goerli ETH是免费的吗？

从历史上看，开发者在 Goerli 测试网上使用的测试 ETH goETH 一直是免费的，但由于 goETH 的稀缺性，已经创建了流动性市场，开发者可以在其中买卖 goETH 以获得真正的 ETH。

Web3 开发者仍然可以通过 Goerli Faucet 获取免费的 Goerli ETH，但由于 goETH 总量有硬性上限，每天可以收到的 goETH 数量是有限的。

积累 Goerli ETH 的稀缺性和成本是许多以太坊开发者从 Goerli 转向 Sepolia 的原因之一。

#### Sepolia ETH 是免费的吗？

[是的，Sepolia ETH 是免费的，可以从Sepolia ETH 水龙头](https://sepoliafaucet.com/)接收。由于市场上可用的 sepETH 总数没有上限，因此像 Alchemy 这样的水龙头提供商每次从 Sepolia 水龙头请求测试 ETH 时都能够向开发人员提供更大量的 sepETH。

### 2.RPC和API支持

在决定在 Sepolia 或 Goerli 上构建之前，了解支持每个测试网的 RPC 节点提供商以及它们支持的 API 端点非常重要。例如，一些 RPC 提供商（例如 Alchemy）支持 Sepolia 测试网，而其他提供商可能不支持。此外，一些 RPC 提供程序可能缺乏对重要 API 端点的支持，例如需要 Erigon 节点的跟踪 API。

Alchemy 支持 Sepolia 上的所有核心 JSON-RPC 方法以及存档节点支持和跟踪 API 端点。Alchemy 未来还将支持 Sepolia 上的增强 API 方法。

### 3. 智能合约可用性

如果您的智能合约依赖于其他智能合约，您决定在 Goerli 还是 Sepolia 上构建将取决于依赖的智能合约的部署位置。

例如，如果您的智能合约需要访问 Uniswap v3 合约，而 Uniswap 仅将其合约部署在 Goerli 测试网上，那么您需要将智能合约部署在 Goerli 上，直到 Uniswap 将其测试网迁移到 Sepolia。

### 4. 其他注意事项

在Goerli和Sepolia测试网络之间进行选择时，除了智能合约的可用性和节点提供商的支持外，还应考虑网络稳定性、验证器集和以太坊基金会的长期支持等因素。

## Goerli 或 Sepolia：我应该选择哪个测试网？

选择正确的测试网络是一个重要的决定，可以影响 dapp 开发的成功。每个测试网络都有自己的技术规格、功能和权衡，选择正确的网络可以节省您的时间和资源。

在部署和测试应用程序和智能合约时，Ethereum.org 推荐 Sepolia 作为测试应用程序和智能合约的首选，因为它的验证器集有限且稳定性保证更高。

另一方面，如果您想测试信标链验证器、节点设置、客户端版本，或者想在部署到主网络之前尝试协议升级，Goerli 是一个不错的选择。它是距离以太坊主网最近的测试网，也可用于测试复杂的智能合约交互。

> 原文：https://www.web3.university/article/goerli-vs-sepolia