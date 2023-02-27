---
title: 设置 Web3 开发环境的 5 个必备工具 | dApp 开发基础
description: null
author: 李留白
weight: 0
date: 2023-02-27T12:40:21.052Z
lastmod: 2023-02-27T12:47:27.058Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230227204041.png
---

2023 年是 dApp 元年。尽管加密货币、NFT、web3 游戏以及其他解决方案的兴衰让人们相信 Web3 绝不是一种时尚。

事实上，市场研究表明，尽管与过去几年的 Web2 同类应用相比，dApp 似乎没有流行起来，但其价值一直在稳步增长，到 2027 年将达到 3682.5 亿美元。

显然，出于显而易见的原因，Web3 开发也将保持重要。如果无法找到可能为所述生态系统构建下一个“杀手级应用程序”的第三方开发人员，任何技术都没有生存的机会，更不用说繁荣了。

说到这一点，在这个领域起步的新手开发人员会认为重要的第一件事就是建立自己的开发环境。

## 什么是开发环境？

为了构建应用程序，了解您在工具和流程方面的需求可以帮助简化和加速开发。

能够编写代码、测试和完成将在单一环境中上线的构建，可以让开发人员自己的生活变得简单。流行的开发环境示例包括 Eclipse 和 Microsoft Visual Studio。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230227204120.png)

能够复制这一点对于 Web3 开发同样重要，即使重点从采用集中式技术转移到分散式技术。尽管编写代码、测试和部署所付出的努力完全是一种全新的体验。

说到这一点，编写智能合约代码的想法是 Web3 本身独有的，这就是为什么在构建标准 dApp 时非常值得研究它适合的地方。

## dApp的组件

当您看一下下图时，您会明白 dApp 的“移动部件”比传统的 Web2 应用程序多得多。虽然前端看起来很熟悉，但由于这个空间和区块链技术的独特性，所有其他组件都与 Web3 截然不同。

对于初学者，Metamask 提供身份证明，用于以用户身份访问 dApp。此外，Infura、Alchemy 和 QuickNode 等区块链节点提供商可帮助开发人员避免在构建自己的 dApp 之前设置和运行节点的麻烦。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230227204138.png)

现在，一旦您通过这些提供商中的任何一个设置了一个节点，与区块链通信和访问数据的任务就由标准的 JSON-RPC 协议 API 处理。您会发现作为所述区块链的智能合约的一部分存储的数据以及与用户或区块相关的数据。

现在我们了解了 dApp 的组件，让我们看看将用于构建其中一些组件的工具。

## 每个 Web3 开发人员必备的 5 个工具

如果这是您第一次接触 dApp 开发，您会注意到它不仅不同于传统的 Web 开发，而且要复杂得多。

为了应对这种复杂性，这里列出了 5 个 Web3 开发人员必备的工具，这些工具必须成为其开发环境的一部分：

### 工具 1：Metamask

几乎所有熟悉 Web3 的人都知道 Metamask 的用途。大多数人不仅用它来处理和交易各种加密货币和代币，而且还用它作为连接 dApp 和智能合约的一种方式。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230227204157.png)

必须记住，Web3 完全是关于隐私和安全的，因此使用电子邮件地址、Facebook 甚至谷歌作为一种身份验证形式的想法是不可接受的，原因很明显。

另一方面，当用户想要访问 dApp 时，允许用户的 Metamask 公共地址作为标识符而不需要密码会更加安全，同时在构建他们的第一个 dApp 时使用它在测试网和主网上支付 gas 费用使得这也是一个方便的工具。

### 工具 2：前端 JS 框架

当提到网站或 Web 应用程序的前端开发时，这通常涉及用户交互，这被认为与 Web2 中的课程相同。有趣的是，Web3 中的前端开发没有什么不同，这就是可以使用相同技术的原因。

毕竟，我们是从后端获取信息——无论使用何种技术——并将其呈现在屏幕上以供用户使用。尽管如此，在 Web3 的情况下，与去中心化节点的连接是如何发生的，而不是与中心化服务器的连接。因此，必须记住，在让这两者协同工作时存在一些差异。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230227204215.png)

说到技术，vanilla JS 可以用于前端开发，但如果你是认真的，最好使用适当的 JS 框架，如 React、Angular、Vue 和 Svelte。这些框架有几个好处，因为它们在构建前端时提供可扩展性和效率。

### 工具 3：Remix IDE

如上所示，智能合约是位于后端和区块链上的业务逻辑。当满足某些条件时，智能合约会促进某种价值交换，这些条件在合约代码本身中有规定。

对于以太坊平台，Solidity 语言被设计用来编写这些智能合约，并且在语法上与 C 系列和 Java 等流行语言非常相似。

作为一名崭露头角的 Web3 开发人员，您可以使用浏览器中提供的 Remix IDE 学习如何使用 Solidity 语言编写智能合约。无需运行复杂的安装步骤。这很简单。

为了设置 Remix 集成开发环境，您所要做的就是启动浏览器并输入：https://remix.ethereum.org/

**结果**

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230227204245.png)

在屏幕的左上角，您有文件资源管理器、文件搜索、编译器、部署和运行事务以及调试器选项。一旦您熟悉了如何使用这些选项，您就可以开始使用 Solidity 或 Vyper 编写代码。

### 工具 4：Ganache

现在，即使我们已经将 Remix 作为一种工具来使用 Solidity 编写智能合约并使用 Metamask 在上述测试网上对其进行测试，但在使用该工具时，其局限性往往会显现出来。

这将我们带到了 Ganache，它是 Truffle Suite 的一部分，可用于执行相同的功能，但在以太坊区块链的模拟本地版本上。您可以使用此工具开发、测试和部署智能合约，需要具备 Solidity 和 JavaScript 的应用知识。

您所要做的就是导航至https://trufflesuite.com/ganache/并选择“下载”以获取适用于您的操作系统的版本。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230227204308.png)

如下所示，您可以在后面的 Github 存储库中找到必要的设置文件。

**结果**

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230227204320.png)

### 工具 5：Etherscan

被归类为区块链浏览器，这个以以太坊为中心的工具将帮助你在你的 dApp 部署到所述测试网或主网上后搜索交易、钱包地址和智能合约相关信息。

导航到[https://etherscan.io](https://etherscan.io/)是访问此工具的唯一方法，如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230227204333.png)

现在，假设您想检查由上述以太坊地址（也称为“beaverbuild”）进行的交易：0x95222290DD7278Aa3Ddd389Cc1E1d165CC4BAfe5

将地址复制并粘贴到字段中，然后按 Return 键：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230227204400.png)

结果：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230227204429.png)

### 奖励工具：节点即服务/全栈 dApp 开发平台

如果说构建 dApp 与常规 web2 应用程序有什么区别，那就是需要一个节点，该节点是您希望为其构建 dApp 的区块链的一部分。

如您所知，节点是区块链网络本身的一部分，它保存对区块链所做的所有状态更改。然而，并不是每个人都想运行和维护这样的节点。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230227204447.png)

这就是为什么诸如 Infura、Moralis、QuickNode 或 Alchemy 等众多 web3 平台不仅提供节点即服务，而且还为所述网络提供全栈 dApp 开发平台。

选择他们的服务将确保你不必花时间管理或运行节点，而是专注于你最擅长的事情：基于可靠的用例构建 dApp。

也就是说，我们正在构建一个以开发人员为中心的 metaverse，供您学习、赚钱和受雇。注册加入 Lumos Metaverse 白名单以获得独家访问权限：

[https://forms.lumoslabs.co/whitelist](https://forms.lumoslabs.co/whitelist)


> 原文：[Daniel Chakraborty] https://medium.com/lumos-labs/5-tools-to-set-up-web3-development-environment-dapp-development-basics-22547df945ab

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)