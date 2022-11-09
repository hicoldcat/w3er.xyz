---
title: 如何以开发者身份进入以太坊、加密货币和Web3领域
description: null
author: 李留白
weight: 0
date: 2022-11-09T13:13:34.279Z
lastmod: 2022-11-09T13:15:10.312Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211092053647.png
---

最近，我一直在谈论我从传统的网络、移动和云计算背景转换到[Web3](https://ethereum.org/en/developers/docs/web2-vs-web3/)、以太坊和加密货币领域的情况。

自从做出这一举动后，有很多人向我伸出了橄榄枝，他们也在考虑做同样的事情。

看到这么多人对这些领域感兴趣，真是太好了。而且，如果我说实话——知道这么多其他人也在犹豫不决，并且对这个空间也如此感兴趣，这让我感觉很有成就感。

至于我，我对转行感到紧张。进入一个全新的专业领域，使用一种我仍在逐步掌握的技术，以及一个我尚未参与的社区，是一个巨大的飞跃。特别是与我在一家薪酬很高的FAANG公司（和一个我非常喜欢的团队）担任的非常舒适的角色相比。

一个多月后，我对这一改变毫无遗憾。我也是很长一段时间以来最快乐的人，对我每天有机会从事的工作感到兴奋和充满活力。

我决定写这篇文章，为任何想从传统开发背景进入区块链、加密货币、以太坊和Web3的人提供一个蓝图。下次有人问我如何进入这个领域时，我可以指给大家看这篇博文。

### 我将把这篇文章分成几个主要部分：

1. 学习的技术和资源
2. 权衡和考虑
3. 要关注的人
4. 招聘和从事有趣工作的公司
5. 一些提示和发现工作

让我们深入了解一下。

## 了解以太坊和区块链的技术和资源

我最感兴趣的通常是我预测技术在不久的将来会在哪里以及我看到当前势头在哪里的功能。所以这就是我将在这里关注的内容（这就是我个人正在做的事情）。

对我来说，这个领域最令人兴奋的部分是去中心化、[DeFi](https://blog.coinbase.com/a-beginners-guide-to-decentralized-finance-defi-574c68ff43c4)、[治理](https://docs.ethhub.io/ethereum-basics/governance/)/ [DAO](https://www.investopedia.com/tech/what-dao/)和[去中心化网络基础设施](https://www.youtube.com/watch?v=j2rXJLW_93o)。

因此，我专注于以太坊开发和 Solidity。使用 Solidity 编程语言，您可以为以太坊以及许多其他[与 EVM 兼容](https://chainid.network/)的区块链编写智能合约。

在撰写本文时，以太坊还拥有强大而重要的组合，即势头、开发者的思想分享和现有的成功[dapp](https://everest.link/)。

以太坊目前也在转向一种新的共识机制，即[权益证明](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/)。这解决了我过去对加密货币如何在核心层面运作的环境担忧。

一旦你了解了一切如何从根本上运作，我鼓励你再去看看以太坊和EVM之外的其他区块链和项目。

这将使您更好地了解整个行业。它还将帮助您了解是否有其他项目吸引您，或者您认为是实现 Web3 目标的更好方法。

考虑研究[Solana](https://solana.com/)、[Polkadot](https://polkadot.network/)、[Near](https://near.org/)、[Avalanche](https://www.avax.network/)或[Cosmos](https://cosmos.network/)。

要开始使用 Ethereum 和 Solidity 学习区块链开发，我建议您执行以下操作：

### 1. 阅读以太坊文档

浏览[以太坊文档](https://ethereum.org/en/developers/docs/)。请务必查看[以太坊介绍](https://ethereum.org/en/developers/docs/intro-to-ethereum/)部分以及其他任何吸引您眼球的内容。

还请务必查看[dapp 展示](https://ethereum.org/en/dapps/)，以更好地了解当前生态系统中正在构建和使用的成功应用程序。

### 2. 阅读 Solidity 文档

[Solidity文档](https://docs.soliditylang.org/en/v0.8.4/)是一个非常好的起点，尤其是[Solidity by example](https://docs.soliditylang.org/en/v0.8.4/solidity-by-example.html)。这为您提供了一些流行的智能合约示例，例如投票、拍卖、远程购买和小额支付。

您可以将这些合约复制并粘贴到[Remix IDE](https://remix.ethereum.org/)中以开始执行和修改它们以查看它们是如何工作的。

我还在[这里](https://www.youtube.com/watch?v=GB3hiiNNDjk)对投票合同进行了视频演练。

### 3. 熟悉 Remix IDE

[使用Remix IDE](https://remix.ethereum.org/)无需设置任何类型的开发环境，就可以轻松玩转并开始构建智能合约。它是由[以太坊基金会资助的](https://ethereum.foundation/)[Remix 项目](https://remix-project.org/)的一部分。

此 Remix IDE 允许您直接从浏览器创建、编辑和执行智能合约。它为学习 Solidity 的工作原理提供了一个完美的环境。它也非常适合构建各种类型的智能合约并在您学习可靠性以及如何与以太坊交互时使用它们

### 4. 尝试构建一个全栈 dapp

除了 Solidity，开发堆栈的其他部分包括本地以太坊环境（如[Hardhat](https://hardhat.org/)或[Truffle](https://www.trufflesuite.com/)）、钱包（如[Metamask](https://metamask.io/)）以及允许您与区块链交互的客户端库，如[Ethers.js](https://docs.ethers.io/)或[Web3.js](https://web3js.readthedocs.io/)。

要了解所有这些如何组合在一起，从头开始在此技术栈上构建一个完整的全栈 dapp 很有用。您可以设置前端项目以及本地开发环境，并在区块链上部署、运行智能合约并与之交互。

这里有两门入门课程可以帮助您学习：

1. [以太坊编程教程 - DeFi、Solidity、Truffle、Web3.js](https://www.youtube.com/watch?v=xWFba_9QYmc)
2. [全栈以太坊开发的完整指南](https://www.youtube.com/watch?v=a0osIaAOFSE)（[这里也是文章形式](https://www.freecodecamp.org/news/full-stack-ethereum-development/)）

### 5.考虑阅读这些书

这个领域本身发展很快，所以技术书籍往往也很快就会过时了。不过，Web3的基本原理并没有什么变化。

有几本非常棒的书不仅帮助我掌握了所有事物的当前状态，而且还帮助我开阔了眼界，让我看到了其中蕴含的未来可能性和机遇。

#### 代币经济——Web3 如何重塑互联网

如果您只阅读其中一本书，我会说这是最重要的一本书。这是对我们所知道的 Web 的所有缺点、Web3 的目标、它将如何影响我们所知道的我们生活的各个方面以及实现这一愿景需要发生的事情的精妙的深入探讨.

[你可以在这里](https://shermin.net/token-economy-book/)查看这本书。

#### 无限的机器——一群加密黑客如何用以太坊构建下一个互联网

这是关于以太坊如何诞生的惊人故事，带您了解这一切的历史。这是对以太坊起源故事的非常透彻和有趣的描述，我强烈建议您查看。

[你可以在这里](https://www.harpercollins.com/products/the-infinite-machine-camila-russo?variant=32123333836834)查看这本书。

#### 新村——权力还给人民

这是一个关于区块链技术和去中心化将如何影响世界未来的非常酷的故事。

[你可以在这里](https://www.amazon.com/New-Village-Power-Back-People-Blockchain/dp/1718045743)查看这本书

#### 如何使用 DeFi

正如您可能从标题中看出的那样，本书重点介绍了如何从今天开始使用 DeFi。它让您很好地了解如何在今天使用它以及我们将在未来某个时候看到的它的一些应用。

[你可以在这里](https://landing.coingecko.com/how-to-defi/)查看这本书

#### 空间网络

Spatial Web 是一本书，探讨了网络的未来及其所有影响，不仅是 Web3 和去中心化，还有一切将如何结合在一起以实现我们可能尚未考虑的事情。

它很好地权衡了积极和消极因素，以及我们可能能够解决即将发生的任何负面结果的方式。

[你可以在这里](https://www.goodreads.com/book/show/52816204-the-spatial-web)查看这本书

这里有几本可靠的书：

- [使用 Solidity 和以太坊进行动手智能合约开发](https://www.oreilly.com/library/view/hands-on-smart-contract/9781492045250/)
- [掌握以太坊](https://www.oreilly.com/library/view/mastering-ethereum/9781491971932/)

### 6.收听这些博客

以下是一些不错的博客：

- [Web 3 的创始人](https://outlierventures.io/podcasts/)——创造和构建互联网下一阶段的人。
- [Bankless](http://podcast.banklesshq.com/) – 加密金融终极指南
- [进入以太](https://podcast.ethhub.io/)坊——关于以太坊的博客
- [加密 101](https://player.fm/series/crypto-101)
- [Epicenter](https://player.fm/series/epicenter-learn-about-crypto-blockchain-ethereum-bitcoin-and-distributed-technologies-41400) – 了解加密、区块链、以太坊、比特币和分布式技术

### 7. 观看这些 YouTube 频道

- [Ethereum Foundation](https://www.youtube.com/channel/UCNOfzGXD_C9YMYmnefmPH0g)
- [Eat the Blocks](https://www.youtube.com/channel/UCZM8XQjNOyG2ElPpEUtNasA) – 区块链开发短视频
- [Finematics](https://www.youtube.com/c/Finematics/videos) – 分享有趣的 DeFi 视频
- [Dapp University](https://www.youtube.com/channel/UCY0xL8V6NzzFcwzHCgB8orQ)– 以太坊空间中的视频
- [BlockGeeks](https://www.youtube.com/c/BlockGeeks/featured) – 通用区块链培训
- [The Daily Gwei](https://www.youtube.com/channel/UCvCp6vKY5jDr87htKH6hgDA)
- [奥斯汀Austin Griffith格里菲斯](https://www.youtube.com/channel/UC_HI2i2peo1A-STdG22GFsA)

我还开始制作有关 Ethereum 和 Solidity 的视频和教程，因此请考虑查看[我的 YouTube](https://www.youtube.com/channel/UC7mca3O0DmdSG2Cr80sOD7g)频道。

## 转换职业的权衡和考虑

在进行职业转变时，总是有一些事情需要考虑，尤其是在考虑这个领域时。

有很多积极因素，但也有未知数和消极因素。让我们谈谈其中的一些。

### 这是一个新兴技术

虽然有许多现有的 dapps 和公司已经蓬勃发展，但这个领域在许多方面仍在形成中。

我们还有很多问题需要解决，很多问题都没有明确的答案。要解决的问题通常很复杂，有时会结合分布式系统、博弈论、密码学、经济学、社会和政治科学、身份、心理学等的一个或多个方面。

正因为如此，仍有一些东西我们还不能用现有的解决方案来构建。

我个人认为这是所有这一切中最令人兴奋的事情之一，但并不适合所有人。

### 这是一个不稳定的领域

许多项目都是围绕各种类型的令牌构建的。其中许多代币的价值急剧上升和下降，你经常看到人们基于这些波动在整个领域中获得和失去兴奋。

如果你还没有从根本上接受去中心化背后的想法，你可能会发现这些起起落落的心理负担很重。

### 充满了猜测

因为很多人只是以投机的方式购买某些代币，所以它吸引了一些只是为了钱而参与其中的人。

您会看到诸如骗子试图摆脱人们并窃取他们的钱之类的事情，投机者无休止地谈论价格波动，以及经常诋毁整个行业的彻头彻尾的骗局项目。

这是其中一个令人讨厌的部分，我真的不认为它会很快消失。

### 这个主题

我也想看看[这个 Twitter 主题](https://twitter.com/jonsyu/status/1389635626698297344)。虽然我没有经历过所有这些事情，但他肯定对我所看到的一些事情有所启发。

## 一些提示以及如何找到区块链或加密相关的工作

您可以专注于空间内的许多领域，并对团队产生积极影响。我会研究治理、DeFi、NFT 和去中心化网络协议等不同领域，看看你最感兴趣的是什么，然后专注于它。

有很多机会和很多方法可以脱颖而出并引起注意。如果您发现一个有趣的项目并想参与其中，请直接进入他们的社区和生态系统并开始学习。然后看看你可以在哪里提供帮助。加入他们的 Discord 或查看他们的 GitHub 问题以找到您可以贡献的方式。

这将使您有机会结识参与该项目的人员，并为可能与他们一起担任职务展开讨论。事实上，团队中的人注意到活跃的社区参与者是很常见的，然后他们经常会在没有你申请的情况下伸出援手并试图招募你。

[薪水通常还不错](https://cryptocurrencyjobs.co/salaries/solidity-developer/)。取决于你来自哪里，它可能或多或少，但它可能不会达到你在 FAANG 公司看到的高水平。

可能还有更多潜在的上涨空间。大多数公司以数字代币的形式提供基本工资 + 股权的组合，所以如果你坚持下去并帮助项目成功并且代币的价值上升，你通常可以赚得比你在许多公司中所做的更多其他地区。

## 在 Twitter 上关注的人

以下是您可以考虑在 Twitter 上关注的一些人：

[Vitalik](https://twitter.com/VitalikButerin)
[Ashleigh Schapp](https://twitter.com/ashleighschap)
[Arthur Hayes](https://twitter.com/CryptoHayes)
[Stani Kulechov](https://twitter.com/StaniKulechov)
[Gloria Kimbwala Niran](https://twitter.com/gkimbwala)
[Babalola](https://twitter.com/niran)
[Ric Burton](https://twitter.com/ricburton)
[Dennison Bertram](https://twitter.com/dennisonbertram)
[Mana Silvora](https://twitter.com/manasilvora)
[Austin Griffith](https://twitter.com/austingriffith)
[Santiago Palladino](https://twitter.com/smpalladino)
[Zaki Manian](https://twitter.com/zmanian)
[Anthony Sassano](https://twitter.com/sassal0x)

我还在Twitter 上发现了[这个由某人创建的综合列表。](https://twitter.com/i/lists/869994563691319296/members)

我在[Edge & Node](https://twitter.com/edgeandnode)团队中的一些人：
[Yaniv Tal](https://twitter.com/yanivgraph)
[Tegan Kline](https://twitter.com/theklineventure)
[Eva Beylin](https://twitter.com/evabeylin)
[Adam Fuller](https://twitter.com/azacharyf)
[Brandon Ramirez](https://twitter.com/RezBrandon)

## 团队做的有趣的事情（和招聘）

[Compound](https://compound.finance/about#jobs)
[Uniswap](https://jobs.lever.co/Uniswap)
[Chainlink](https://chainlinklabs.com/careers)
[Skynet Labs](https://jobs.lever.co/SkynetLabs)
[Aave](https://aave.com/careers/)
[Matic](https://matic.network/careers/)
[Livepeer](https://livepeer.org/jobs)
[Consensys](https://consensys.net/open-roles/)
[ENS](https://medium.com/the-ethereum-name-service/ens-is-hiring-come-build-a-new-decentralized-internet-with-us-24398dea3ac)
[OpenZeppelin](https://openzeppelin.com/jobs/)
[Foundation](https://foundation.app/careers)
[Zora](https://cryptocurrencyjobs.co/startups/zora/)
[Synthetix](https://synthetix.com/careers)
[Digital Currency Group](https://jobs.dcg.co/companies)

[你还可以在这里](https://cryptocurrencyjobs.co/)找到一份相当不错的加密货币工作机会列表。

此外，我在 Edge & Node 的团队[正在招聘](https://edgeandnode.com/jobs)！

## 结论

我有没有提到这个领域是不稳定的？为一些高点和低点做好准备，同时也为你职业生涯中可能遇到的一些最有趣的事情做好准备。

你将与科技界一些最聪明的人一起努力解决一些最复杂的问题，我认为这些问题最终会对人类产生巨大的积极影响

>原文来自：https://www.freecodecamp.org/news/breaking-into-ethereum-crypto-web3-as-a-developer/
> 翻译：李留白

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
