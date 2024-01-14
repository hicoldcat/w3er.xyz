---
title: 2024 年最适合开发人员的 5 款 Web3 工具
description: null
author: 李留白
weight: 0
date: 2024-01-14T13:17:49.242Z
lastmod: 2024-01-14T13:27:10.374Z
tags: []
categories:
    - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240114211807.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240114211807.png)

>原文：https://medium.com/cyfrin/top-5-web3-tools-for-developers-for-2024-a8f1db27ffb7

## 介绍

在本文中，我们将介绍 Web3 开发人员成为一名成功的智能合约开发人员需要熟悉的顶级工具、平台和策略。

*欢迎在这里阅读我们*[*去年的观点*](https://patrickalphac.medium.com/top-6-web3-dev-tooling-for-2023-3a1b3ff73b57)。

## 1. 语言：Solidity 或 Vyper

![顶级智能合约语言](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240114211952.png)

图片来自[defillama](https://defillama.com/languages)

[Solidity](https://docs.soliditylang.org/en/v0.8.23/)仍然是主导语言，大约 94% 的智能合约价值都通过 Solidity 流动。这实际上比去年增长了约 7%，考虑到 rust、huff 和 Vyper 去年取得的所有进步，这有点令人惊讶。

就语言而言，这两种语言继续占据主导地位，我预计[Vyper](https://docs.vyperlang.org/en/stable/)今年将获得很大的吸引力。去年，他们发生了一次不幸的事件，涉及[可重入锁的](https://cointelegraph.com/news/curve-vyper-exploit-whole-story-so-far)问题，但这导致社区大量参与 Vyper 来改进它。我们已经看到一些针对有状态模块引入的 PR，他们已经开始进行[竞争性审计](https://www.codehawks.com/contests/cll5rujmw0001js08menkj7hc)，并且该语言的其他重大跳跃似乎正在进行中。

[我还听到了有关Cyfrin Updraft](https://updraft.cyfrin.io/)的 Vyper 课程的一些传闻……

## 2. 框架：Foundry 和 Hardhat

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240114212102.png)

Foundry 正处于战斗状态。

[Foundry](https://github.com/foundry-rs/foundry)和[Hardhat](https://hardhat.org/)去年再次成为我们排名前 2 的框架。[Brownie](https://eth-brownie.readthedocs.io/en/stable/)仍然是一个出色的框架，但它已经放缓至维护模式。[Apeworx](https://www.apeworx.io/)正在努力获得功能平等，成为新的 Python 框架，但尚未达到 Brownie 的水平。

Hardhat 仍然拥有最多的使用它的存储库，但今年，我们看到大多数新项目都是通过 Foundry 走出大门的。为什么？

- 测试速度提高 20 倍
- 内置模糊测试
- 部署改进

但实际上，Foundry 的速度正在将其推向顶峰。在[Cyfrin](https://www.cyfrin.io/)，我们看到大多数使用 Foundry[寻求安全审查的](https://github.com/Cyfrin/cyfrin-audit-reports?tab=readme-ov-file)新项目，以及大多数寻求[竞争性审计的](https://www.codehawks.com/contests)项目也使用 Foundry。

Foundry 将成为 2024 年新项目的首选工具。对于开发人员和安全研究人员来说都是如此。

[对于 Vyper 爱好者来说，我希望看到Titanoboa](https://github.com/vyperlang/titanoboa)得到更多采用，你可以将其视为“Vyper 的铸造厂”。它由 Vyper 核心团队自己构建，提供与 Foundry 相同的测试和执行方式，但针对的是 Vyper。

当然，Truffle今年[终于和我们告别了。](https://x.com/trufflesuite/status/1704946902393860589?s=20)安息吧 Truffle。你不会被错过，但你会被记住，因为你是 web3 中许多人的起始框架。

## 3. 智能合约要点：Chainlink 和 OpenZeppelin 合约

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240114212302.png)

原始图像来自 Getty Images 的 IncrediVFX

[Chainlink](https://chain.link/)和[Openzeppelin](https://github.com/OpenZeppelin)仍然是每个开发人员都应该了解的工具。去年他们都继续推出出色的产品：

- Openzeppelin [v5.0 最近发布](https://blog.openzeppelin.com/introducing-openzeppelin-contracts-5.0)
- Chainlink [CCIP（桥接）](https://docs.chain.link/ccip)
- Chainlink[数据流（低延迟预言机）](https://docs.chain.link/data-streams)
- Chainlink[函数（自定义 API 调用）](https://docs.chain.link/chainlink-functions)

并继续成为 Solidity 可扩展合约 (OZ) 和预言机 (CL) 的首选资源。Chainlink CCIP 尤其值得密切关注，因为它将带来跨链 dapp 的新时代。

[Solady](https://github.com/Vectorized/solady)已经为其他必需品打造了两年多，不应被低估。虽然他们的合约库比 Openzeppelin 的要小，但他们拥有大量的 Gas 优化合约，新项目应该将其作为 OZ 的替代方案。

我们还看到以下方面的增长：

- [Solana](https://solana.com/)和[Arbitrum 手写笔](https://arbitrum.io/stylus)上基于 Rust 的合约兴奋
- ZK 工具/语言，例如[Cairo](https://www.cairo-lang.org/)和[Noir](https://medium.com/aztec-protocol/introducing-noir-the-universal-language-of-zero-knowledge-ff43f38d86d9)

我也希望在这里看到更多的发展。

此类别中的一些荣誉奖包括：

- [Tenderly](https://tenderly.co/)：交易可视化工具
- [Otterscan](https://github.com/otterscan/otterscan)：开源区块浏览器

## 4. Wallets

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240114212339.png)

图片来自[Cyfrin Web3 钱包指南](https://www.cyfrin.io/blog/what-should-i-use-to-store-my-cryptocurrency-web3-wallet-guide)

对于 dapp 开发者来说，Web3 钱包也一直在升级。Paradigm 团队今年推出了[Rivet](https://www.paradigm.xyz/2023/08/rivet)，它允许开发人员比传统钱包更轻松地与前端交互。您可以在此处观看视频以了解更多信息：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240114212411.png)

不仅如此，我们还看到钱包总体上确实在升级：

- [Metamask](https://metamask.io/)今年推出了snap，可实现钱包的定制
- [Rabby](https://rabby.io/)是我越来越喜欢的钱包，因为它似乎对我的交易进行了很多检查
- [Trezor](https://trezor.io/trezor-safe-3)是游戏中唯一的开源硬件钱包之一，推出了 Trezor 3。

钱包真的在升级。

最后，每个人都应该使用像 Safe 这样的多重签名[。](https://safe.global/)如果您想了解更多有关 Cyfrin 团队为您提供的高级钱包建议，您可以[在此处查看。](https://www.cyfrin.io/blog/what-should-i-use-to-store-my-cryptocurrency-web3-wallet-guide)

## 5. 安全

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240114212445.png)

[CodeHawks](https://codehawks.com/)公告缩略图

今年，我们看到安全方面取得了巨大进步。

竞争性审计平台[CodeHawks与开发人员和安全课程](https://www.codehawks.com/)[Cyfrin Updraft](https://updraft.cyfrin.io/)一起推出，旨在提升每个人的 web3 安全知识，为审计员提供提升的空间，并为协议提供消除 bug 的场所！[他们加入了Code4rena](https://code4rena.com/)等平台的行列，为竞争性审计领域提供支持。

此外，我们看到某些技术中的许多工具得到了更多的曝光。

### 模糊测试

安全研究员[Dacian](https://twitter.com/DevDacian/status/1732396938644119644)对模糊工具进行了深入研究，你可以查看他的研究结果：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240114212529.png)

根据 Dacian 的说法，最重要的工具（按顺序）是：

- Medusa (experimental)
- Echidna
- Foundry

并且是 2024 年所有 web3 项目的要求。如果您没有任何模糊测试，那么您的代码库还没有完成。

### Formal Verification

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240114212615.png)

我们看到Formal Verification得到了应有的喜爱，工具如下：

- [Kontrol](https://docs.runtimeverification.com/kontrol/overview/readme)
- [Certora](https://www.certora.com/)（转型为免费增值模式！）
- [halmos](https://github.com/a16z/halmos)
- [HEVM](https://hevm.dev/)

我们看到项目开始使用 FV 并将智能合约视为硬件。如果它们坏了，那就不好了！

### 学习 attack vectors

当然，为了了解最新的攻击媒介，[Sologit](https://solodit.xyz/)是您的首选工具，可以查看顶级公司和竞争性审计报告的所有内容，以便您知道要注意什么。

## 概括

这些是我今年需要注意的五个顶级工具。我希望你学到了一些东西。我希望您使用的工具能够最好地完成工作。

2023 年我们取得了很大进展，2024 年我们将走得更远。
