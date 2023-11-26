---
title: 去中心化应用程序 (dApp) 的架构
description: null
author: 李留白
weight: 0
date: 2023-11-26T13:29:47.398Z
lastmod: 2023-11-26T13:32:13.543Z
tags: []
categories:
    - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231126212956.png
---

去中心化应用程序 (dApp) 为用户提供自主权、安全性和透明度，正在改变数字格局。与依赖集中式服务器的传统应用程序不同，dApp（部分或全部）基于去中心化技术构建。在本文中，我们将探索 dApp 的完整堆栈架构，包括前端、托管、钱包、智能合约、节点访问、数据存储和其他潜在组件。每一层都可以自己填写一个完整的博客，但我们尝试全面总结整个堆栈。

![img](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231126212956.png)

## 🖼 前端

前端（客户端）是 dApp 的用户界面 (UI)。与 web2 一样，它通常是使用流行的 Web 技术（例如 HTML、CSS 和 JavaScript）构建的。[React](https://react.dev/)、[Angular](https://angular.io/)和[Vue.js](https://vuejs.org/)等框架通常用于创建响应式动态 UI。

前端必须以某种方式与后端进行通信。为此，需要连接到节点，并且图书馆利用通信。最流行的 Javascript 库是[Web3.js](https://web3js.readthedocs.io/en/v1.8.2/#)和[Ethers.js，](https://docs.ethers.org/v5/)最流行的 Python 库是[web3.py](https://web3py.readthedocs.io/en/stable/)。虽然我们看到越来越多的 Javascript 库和包，尤其是支持 React 的库和包正在发布，但 Python 生态系统还不够发达（提示：有一个明显的空白你可以填补🙌）。这些库提供了用于处理前端逻辑的实用程序，包括签署交易、检索帐户信息、管理 web3 钱包等。

dApp 还要求前端在向 dApp 后端发送请求之前连接到区块链节点，后端由部署在链上的智能合约组成。虽然自己运行区块链节点可能会减少对任何提供商的依赖，但它是资源密集型的。[Alchemy](https://www.alchemy.com/)、[QuickNode](https://www.quicknode.com/)、[Tatum](https://tatum.io/)、[Ankr](https://www.ankr.com/)或[Infura](https://www.infura.io/)等提供商提供了合适的解决方案。

向 Alchemy 的朋友们致敬，尤其是[Vitto](https://twitter.com/VittoStack)，他们构建了 npx 包[create-web3-dapp](https://github.com/alchemyplatform/create-web3-dapp)，其中包括开始超快速构建 dApp 所需的一切。

## 🌐 托管

托管是指存储和提供应用程序文件的过程，允许用户通过互联网访问它并与之交互。在传统的集中式托管中，一台或多台专用服务器负责提供应用程序的内容。这种方法通常依赖于单个实体，即托管提供商。如今绝大多数 dApp 都使用集中式托管。这会造成潜在的单点故障以及潜在的审查。分散式应用程序托管与集中式模型不同，它将应用程序的文件分布在节点网络上，每个节点都贡献存储和带宽资源。可以使用[IPFS](https://ipfs.tech/)或[Swarm](https://www.ethswarm.org/)等解决方案。如果您想开始托管您的去中心化 dApp，一定要查看[Fleek](https://fleek.xyz/)。如果你觉得去中心化托管太过分了，仍然可以看看 Fleek（他们的团队做得非常酷，呵呵）。

## 🪪 钱包

钱包用于管理用户的数字资产、验证用户身份以及在区块链上签署交易。在 dApp 的背景下，钱包存储用户的私钥并促进与底层智能合约的交易。关于什么样的钱包能够提供合理的用户体验、进入门槛低，但最重要的是为用户提供高安全性，有很多不同的观点（不幸的是，有太多的坏人试图窃取私钥并获取私钥）。通过社会工程访问钱包！）

两种不同类型的钱包一方面是浏览器内置钱包与浏览器扩展，以及托管钱包与非托管钱包。

一些网络浏览器（例如[Brave](https://try.bravesoftware.com/ttb861/?gclid=CjwKCAjw0ZiiBhBKEiwA4PT9zx8tZN6CPsdtsl1HRYbXGc-mnYXpLsBp2GrJB4UO_60cBgTPzX8kTRoCKngQAvD_BwE)或[Opera](https://www.opera.com/de)）带有内置钱包，允许用户管理其数字资产并直接在浏览器内与 dApp 交互。微软[最近刚刚宣布，](https://www.theverge.com/2023/3/21/23649834/microsoft-edge-cryptocurrency-wallet-feature)他们还将在其[Edge](https://www.microsoft.com/de-de/edge?form=MA13FJ)浏览器中内置原生钱包。浏览器扩展钱包（例如[MetaMask](https://metamask.io/)或[Trust Wallet ）](https://trustwallet.com/)是单独的附加组件，用户可以在其首选浏览器中安装。这些钱包通常更加通用，支持更广泛的加密货币和区块链网络。虽然如今内置钱包支持的区块链数量通常受到限制，但扩展程序更容易受到网络钓鱼等攻击。

托管钱包由第三方服务提供商管理，例如交易所或钱包服务，它们保持对用户私钥的控制。用户通过服务提供商的平台访问钱包，依靠它们来实现安全和资产管理。非托管钱包使用户可以完全控制自己的私钥，使他们能够在不依赖第三方的情况下管理自己的数字资产。通常可用于与 dApp 交互的钱包是非托管的，因此用户必须自行管理私钥。非托管钱包有多种不同的方法，例如[Magic](https://magic.link/)、[Argent](https://www.argent.xyz/)或[Metamask](https://metamask.io/)。

## 🔌 节点

节点是通过验证和中继交易参与区块链网络的单独服务器。要与 dApp 交互，客户端/前端必须连接到区块链网络内的节点。然而，用户也可以直接通过节点或另一个智能合约与 dApp 交互！这意味着如果您的智能合约部署在公共区块链上，您的前端并不是用户与后端交互的唯一方式。

[Alchemy](https://www.alchemy.com/)和[QuickNode](https://www.quicknode.com/)等服务提供对远程节点的访问，使开发人员能够专注于构建他们的 dApp，而无需维护自己的基础设施。对于启动专用节点但仍无法自行管理的情况，[Chainstack](https://chainstack.com/)是一个可行的解决方案。[或者，开发人员可以使用Geth](http://started/)（以太坊）或[Solana 的 Validator](https://docs.solana.com/running-validator)等软件运行自己的节点。

节点支持读写操作。如果您的应用程序仅允许从区块链读取数据，则无需支付交易费用。如果您的 dApp 支持写入操作，则需要支付交易费（= Gas 费）。[例如Tatum](https://tatum.io/)不仅提供了节点，还为 dApp 开发者提供了支付用户 Gas 费的解决方案，因此用户不必自己支付 Gas 费（这随后保证了你的用户可以与你的 dApp 充分交互）即使他们的数字资产可能不足以支付天然气费用。）

## 📜 智能合约

智能合约是任何 dApp 的支柱，允许写入和读取区块链。智能合约采用 Solidity (Ethereum) 或 Rust (Solana) 等编程语言编写，定义了管理 dApp 操作的逻辑和规则。
在典型的智能合约中，一旦部署，其代码就无法更改，这可能会导致尝试修复错误或添加新功能时遇到困难。可升级（代理）合约通过将合约逻辑与合约数据存储分离来解决这个问题。这些合约由两个主要部分组成（基本上是 2 个或更多智能合约）：

1. 代理合约：代理合约充当中间人，将传入请求转发到逻辑合约，同时维护合约的状态。本合同在升级过程中保持不变。
2. 逻辑契约：逻辑契约包含应用程序的实际代码和业务逻辑。开发者可以部署新版本的逻辑合约并更新代理合约以指向新的逻辑，从而在不影响合约数据的情况下实现无缝升级。

dApp不仅可以利用内部开发的智能合约，还可以与其他人部署的智能合约（协议）连接。例如，流行的 dApp [1inch （DEX 聚合器）开发了智能合约，可以与](https://1inch.io/)[Uniswap](https://uniswap.org/)和[Sushiswap](https://www.sushi.com/)等各种去中心化交易所的智能合约进行交互。

部署在一条链（例如以太坊）上的智能合约不能简单地与部署在另一条链（例如Polygon）上的另一个智能合约交互。支持多个链将有助于满足更多的用户群体，并可能带来零知识证明或更快的交易吞吐量等高级功能。这种跨链交互的唯一方法是在 dApp 中实现像[Wormhole这样的桥梁或像](https://wormhole.com/)[Cosmos](https://cosmos.network/)这样的互操作协议。

## ⚙️ 索引解决方案

索引解决方案使区块链数据更易于访问和查询，在去中心化应用程序 (dApp) 生态系统中发挥着至关重要的作用。随着区块链规模和复杂性的增长，直接从链中检索特定数据可能会很慢并且占用资源。索引解决方案通过创建结构化索引数据库来应对这一挑战，从而实现更快、更高效的数据检索。许多索引解决方案提供实时数据同步，确保在相关事件发生时立即通知 dApp。这使得 dApp 能够快速准确地触发 webhook 或操作来响应链上事件。

最常见的解决方案是[The Graph](https://thegraph.com/en/)，它是一种去中心化索引协议，用于开发子图，而且许多节点提供商（例如 QuickNode）现在也提供索引功能。

## 🗄 数据存储

虽然区块链适合存储交易数据，但由于可扩展性和成本问题，它并不适合大规模数据存储。[IPFS](https://ipfs.tech/)或[Filecoin](https://filecoin.io/)等去中心化存储解决方案通常用于链下存储 dApp 数据，提供更高效、更具成本效益的存储选项。这些服务采用加密和分片来确保数据隐私和完整性。构建 dApp 时也可以使用集中式数据存储，但它不会再完全去中心化（与上面讨论的托管相同）

## 🔮 预言机

智能合约根据区块链网络中可用的信息执行预定义的逻辑。然而，许多用例需要来自外部源的数据（例如天气、股票价格或体育比分）才能有效运行。预言机通过向智能合约安全地提供链下数据来满足这一需求。流行的预言机是[Chainlink](https://chain.link/)和[UMA](https://uma.xyz/)。

## 💡结论

去中心化应用程序的完整堆栈由许多层和组件组成，并且目前可以决定实施许多不同的供应商或方法。更重要的是，新工具正在高速构建，并以更快的速度增加 dApp 的功能、复杂性和可用性。

> 原文：https://medium.com/@blocktorch/architecture-of-decentralized-applications-dapps-d583db198a6f