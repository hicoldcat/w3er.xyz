---
title: Web3技术栈有哪些？程序员的入门指南
description: null
author: 李留白
weight: 0
date: 2022-06-14T14:29:29.821Z
lastmod: 2022-06-14T16:08:37.446Z
tags: []
categories:
  - 区块链
featuredImage: https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220614222854.png
---

> 你想参与建设web3项目吗？本文带你快速了解web3领域最流行的技术栈和开发环境。当然，由于Web3生态的广泛性，本文只能简单介绍一部分技术栈。

伴随着去年元宇宙概念的走红，Web3已经成为了今年的火热领域，受到了越来越多投资者和企业的关注，引起了很多的热议。也有很多人放弃了大厂和独角兽企业的工作机会，转而投向了web3领域的创业公司，来寻求未来更广阔的的职业前景。

如果你也感受到了一丝心动，希望尝试去了解更多web3的知识，那么，从哪里开始呢？
当然首先推荐你关注我文末的公众号，或者我的个人网站[https://hicoldcat.com/](https://hicoldcat.com/)。这篇文章完全是为那些希望直接进入Web3开发的人准备的。我们的重点将放在各种不同类型的技术栈上，每一个类别都包含了有价值的一些资源，让你的web3之旅可以顺利开始。

为了便于查阅，我将把这些资源分为不同的部分。他们是：

- *Layers*：在开发中使用到的最流行的区块链
- *Dev environment*：帮助你部署到这些Layer上的工具
- *Identity*：如何围绕钱包身份建立并使用加密签名
- *Storage*：利用P2P技术创建永久性数据存储。
- *API*：直接访问主网来获取信息
- *Front-end*：前端流程框架

技术栈是一个术语，用于定义和实现特定目标的特定技术组合。就像无论是全栈开发，还是Web3技术栈一样。值得注意的是，构建Web3应用程序和产品所涉及的技术是相当 "不稳定"的。

从某种意义上说，随着越来越多的人开始在区块链上构建软件，很多事情都在发生着很多的变化。因此，我们这个参考资料的目标仅仅是那些已经在实际项目中使用的技术。


## 1、Layers
> 任何Web3项目的基础都是从一个Layer开始的，也就是你的应用或者产品将要部署和运行的特定的区块链网络。

### 1.Ethereum

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/What-is-Ethereum.png)

以太坊[https://ethereum.org/zh/](https://ethereum.org/zh/)凭借其原生的智能合约提案而广受欢迎。

从那时候开始，开发人员将其作为构建下一代Web3应用程序的首选区块链层。它也是很多NFT项目的首选层，所以如果您想开发NFT相关的应用程序，以太坊可以提供很多现成的功能。

### 2.Polygon

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/Polygon-layer.png)

[Polygon](https://polygon.technology/)是以太坊的侧链，提供更复杂的安全实践，同时保持较低的交易（gas）费用。对于交易数量极高但交易价格较低的项目来说，这是一种流行的选择。

由于Polygon是一个侧链，它也经常被称为Layer 2区块链。然而，Polygon的快速发展使其不仅成为NFT应用程序的热门选择，而且也是dApps和支付系统的首选。

其他值得注意的Web3 Layer:

- [Solana](https://solana.com/)：为大规模构建DeFi应用而构建的区块链
- [NEAR Protocol](https://near.org/)：拥有巨大开发者资源的智能合约区块链
- [Polkadot](https://polkadot.network/)：建立治理项目的跨区块链协议。
- [Tezos](https://tezos.com/)： 一个专注于能源的平台，用于建立分散的创意平台。
- [Fantom](https://fantom.foundation/)：用于构建dApp的开源智能合约平台。

## 2、Dev Environment
> 开发环境让你跳过整个 "从头开始 "的部分。这些暂时大部分是基于以太坊的。

### 1. Hardhat

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/Hardhat-edited.png)

[Hardhat](https://hardhat.org/)是一个为Ethereum开发者提供的开发环境。它的工作原理是帮助开发者自动构建和组合去中心化的应用程序，也包括智能合约。它的受欢迎程度源于内置的一个以太坊网络1比1复制还原的Hardhat网络，你可以用它来测试你的应用程序。

### 2. Truffle

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/Truffle-edited.png)

[Truffle](https://trufflesuite.com/)是一个基于JavaScript的用于构建智能合约的框架。它假设你使用EVM（以太坊虚拟机），并给你提供一些工具包括自动测试、合约编译器以及直接从控制台访问你的联系人的单独工具，来加速开发过程。

其他值得注意的Web3开发环境：

- [Foundry](https://github.com/gakonst/foundry)：基于Rust的工具包，用于在Ethereum上构建应用程序。
- [Brownie](https://eth-brownie.readthedocs.io/en/stable/)：基于Python的框架，用于构建和测试智能合约。
- [Anchor](https://project-serum.github.io/anchor/)：一个与Solana合作的框架，用Rust编写。
- [Embark](https://framework.embarklabs.io/)：构建在以太坊之上的多合一框架。
- [Waffle](https://getwaffle.io/)：定制的框架来测试智能合约。

## 3、Identity
> 与传统的用户名和密码认证不同，在Web3中，开发者既实现了钱包的使用，也实现了公钥的使用。

### 1. WalletConnect

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/WalletConnect.png)

[WalletConnect](https://walletconnect.com/)是一个开源协议，用于通过移动钱包连接到dApps。使用WalletConnect的主要概念是围绕扫描二维码，然后用户可以用它来验证他们的身份并批准与Web3应用程序的新连接。

如果你知道Pillar或MetaMask等钱包--它们都直接与WalletConnect整合。这也意味着你可以获得大量的开发者资源。最后但并非最不重要的一点是，该协议与所有主要的区块链合作，更多的区块链正在被频繁添加。

### 2. Spruce

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/Spruce.png)

[Spruce](https://www.spruceid.com/)是一家Y Combinator支持的创业公司，旨在将去中心化的身份概念带到区块链之外的领域。这个工具包旨在帮助用户在各种媒介上验证其数字身份：社交媒体、个人网站和区块链网络。

## 4、 Storage
> 如何在Web3上存储媒体（视频、照片等）？这主要是通过P2P文件存储协议实现的。

### 1. IPFS

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/IPFS-edited.png)

[IPFS](https://ipfs.io/)是世界领先的P2P（对等）存储协议。一旦你上传一个文件到IPFS，这个文件就会被调整大小，通过一个自定义的散列来保护，并分配一个指纹作为它的唯一标识符。

之后，属于该网络的其他节点（系统或人）可以访问该文件，对其进行验证，并将其存储在其缓存中。一旦被缓存，该文件就可以被任何拥有正确访问方法的人访问。

最终，IPFS的目标不仅仅是提供一个分散的存储系统。但也要确保内容的跨度是几十年，而不是几年。例如，一个网站在一段时间后消失了，其所有的内容，包括媒体，都会丢失。IPFS为此提供了一个真正的解决方案。

### 2. Skynet

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/Skynet.png)

[Skynet](https://siasky.net/)利用Sia（去中心化）云网络提供无障碍文件共享。实际上，你可以直接从主页上使用它。只要上传一个文件，就会给你一个链接。除非您注册API密钥，否则它将持续90天。不过，在开发环境中，Skynet使您可以轻松地在自己的分散应用程序中复制相同的用例。

### 3. Filebase

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/Filebase.png)

[Filebase](https://filebase.com/)是一家SaaS初创公司，提供与Amazon S3 API的直接链接。您可以使用此API从S3存储桶中提取数据，然后让它们跨各种分散的存储选项重新分布。

目前，Filebase支持基于Web3的存储选项，如Skynet、IPFS、Sia和Storj等。作为开发人员，这意味着您可以简化为客户机提供分散文件存储服务的过程。

作为新用户，您还可以免费获得5GB的存储空间，尽管即使是付费计划，价格也是合理的。

其他值得注意的Web3存储解决方案：

- [Arweave](https://www.arweave.org/)： Arweave是一个协议，它允许你用一笔预付费用永久和可持续地存储数据。
- [Filecoin](https://filecoin.io/)：在一个分散的云环境中存储任何种类的媒体。
- [Swarm](https://www.ethswarm.org/)：以太坊智能合约激励的自我维持P2P存储节点。
- [Aleph](https://aleph.im/)：构建DAPP并让Aleph为您管理基于云的存储。

## 5、 API
> 与传统的基于数据库的技术栈不同，开发者使用API与区块链进行互动，并在上面请求或存储数据。API有时也被称为客户端。

### 1. The Graph

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/The-Graph-web3.png)

[The Graph](https://thegraph.com/en/)是一个多合一的API解决方案，用于连接各种区块链。这个API的目标是帮助开发者访问数据，否则就需要建立单独的算法。例如，区块链中的数据点源于简单的地址到基于智能合约的复杂交易。

这方面的一个例子是跟踪NFT的历史。

虽然个人购买历史可能相当容易追踪，但当你试图了解与不同购买的关系时，它就会逐渐变得困难。在这种情况下，The Graph API提供了预制的调用和索引目录，以帮助你快速实现特定用例的可视化。

### 2. Web3.js

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/web3.png)

[Web3.js](https://web3js.readthedocs.io/)是最流行的JavaScript API，可直接与以太坊互动。它主要用于开发与以太坊链互动的客户端应用程序。Web3.js中包含的子库集合提供了诸如转账、智能合约管理等功能。

### 3. Alchemy

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/Alchemy-Web3-edited.png)

[Alchemy](https://www.alchemy.com/)已经迅速崛起，成为最受尊敬的Web3平台之一。他们的方法是教育开发者如何快速建立和扩展Web3应用程序。他们能够通过提供复杂的区块链API和一套开发者工具来做到这一点。

### 4. useDApp

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/useDApp-edited.png)

[useDApp](https://usedapp.io/)是一个基于React的框架，用于与以太坊区块链交互。它包括React.js、 Ethers.js库、Waffle和Web3 React组件。它通常用于构建易于访问的网站前端，可以根据项目需要显示各种数据查询。

## 6、Front-end
> 开发人员使用什么样的框架来构建Web3项目的前端界面？

### 1. React 

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/ReactJS.png)

[React](https://reactjs.org/)的受欢迎程度是无可比拟的。当得知它也是Web3网站最受欢迎的前端库时，我们并不感到惊讶。组件驱动的架构意味着开发者可以在不影响核心设计的情况下，对前端的多个部分进行处理。

你可以在GitHub上查看[Web3-React库](https://github.com/NoahZinsmeister/web3-react)，这是一个坚实的起点。

### 2. Next

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/NextJS-framework-edited.png)

[Next.js](https://nextjs.org/)是React的继任者，尽管两者都没有直接关联。大规模的Web3项目需要巨大的资源管理，Next.js捆绑了用于静态和动态项目的本地工具，并被Binance、Bitscreener、Hashnode等公司使用。

要想快速入门，请看[Next Web3 Boilerplate](https://github.com/mirshko/next-web3-boilerplate)。

## 开始使用Web3开发

开发web3需要相当多的技术，特别是如果你现在才开始学习的话。事实是，这一领域的情况变化很快。尽管最初的项目都与NFT相关，但如今，人们更强调去中心化的网络和个人区块链平台。

就开始接触Web3项目而言，这个参考资料应该足够入门了。其他的可以查找一些web3社区和github上关于web3的项目来持续学习。

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/profile.jpg)