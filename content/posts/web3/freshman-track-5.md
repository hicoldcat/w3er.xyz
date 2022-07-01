---
title: Web3系列教程之新手篇---Level 5： Remix IDE
description: null
author: 李留白
weight: 0
date: 2022-06-19T16:27:47.998Z
lastmod: 2022-07-01T03:31:29.649Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/freshman.png
---

> 原文：[https://www.learnweb3.io/tracks/freshman](https://www.learnweb3.io/tracks/freshman)<br/>
> 翻译：李留白

本文是[learnweb3](https://www.learnweb3.io/)的新手篇，包括：
- [Level 0：前置基础编程知识](https://hicoldcat.com/posts/web3/freshman-track-0)
- [Level 1：什么是区块链？](https://hicoldcat.com/posts/web3/freshman-track-1)
- [Level 2：什么是Web3？](https://hicoldcat.com/posts/web3/freshman-track-2)
- [Level 3：什么是ETH？](https://hicoldcat.com/posts/web3/freshman-track-3)
- [Level 4：加密钱包？](https://hicoldcat.com/posts/web3/freshman-track-4)
- [Level 5：Remix IDE介绍](https://hicoldcat.com/posts/web3/freshman-track-5)
- [Level 6：Solidity介绍](https://hicoldcat.com/posts/web3/freshman-track-6)
- [Level 7：dApp介绍](https://hicoldcat.com/posts/web3/freshman-track-7)
- [Level 8：加密货币(ERC20)简介](https://hicoldcat.com/posts/web3/freshman-track-8)
- [Level 9：NFT介绍](https://hicoldcat.com/posts/web3/freshman-track-9)

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220626183258.png)

Remix 是用于以太坊开发的开源、Web 和桌面集成开发环境 (IDE)。它是开始在以太坊上构建的最简单的开发工具，并且拥有大量插件来扩展其体验。

Remix 可帮助您直接在浏览器中编写 Solidity 代码，并提供用于测试、调试和将智能合约部署到区块链的工具。

你可以在[https://remix.ethereum.org/](https://remix.ethereum.org/)访问 Remix.


## 导航到Remix

当您第一次打开 Remix 时，您会看到这样的屏幕。

File Explorer在左侧边栏中，您可以在、Solidity Compiler、Deployer和Extensions面板之间切换。

在底部，有一个输出面板，显示编译、部署和函数调用的输出。

中间是您将编辑代码的地方。目前它显示 IDE 的主屏幕，但是一旦我们打开一个文件，它将成为代码编辑器。

## Remix工作流程

在侧边栏中，如果您查看contracts文件夹下方 - Remix 附带 3 个基本智能合约，以帮助人们学习 Solidity。让我们来看看1_Storage.sol。

我们现在可以看到代码编辑器了。

在文件资源管理器中，我们还可以看到创建新文件或目录、上传本地文件或从 Github 导入文件的选项。

为了编译我们的合约，我们切换到Solidity Compiler选项卡，我们将在侧边栏中看到类似的内容。

在这里，我们可以选择Compiler Version我们想要的、我们使用的智能合约编程语言（大多数情况下你只会使用 Solidity），以及一些进一步的配置选项。

注意：Remix 中列出的另一种编程语言Yul是低级语言。它用于中间编译，比 Solidity 更接近硬件。99% 的时间你不会在 Yul 中编码。在此处阅读有关 Yul 的更多信息 - https://docs.soliditylang.org/en/v0.8.9/yul.html

单击Compile 1_Storage.sol将编译合约并使其准备好部署。

转到Deployment选项卡，我们将在侧边栏中看到类似的内容。

这里首先要注意的是Environment. Remix 附带一个Javascript VM- 这是浏览器中以太坊虚拟机 (EVM) 的模拟器。只要您的合约不依赖于部署到真实以太坊网络的另一个合约，这便可以快速测试和调试您的智能合约。谢天谢地，我们的存储合约没有，所以我们可以在 Javascript VM 中测试它。

要部署到实际网络，我们将希望将我们更改Environment为那里列出的其他选项之一（稍后会详细介绍）。

除了Javascript VM，Remix 还创建了一组虚假账户，所有账户都装有 100 ETH，以供测试。

从下拉列表中选择1_Storage.sol合约，然后单击Deploy以部署合约。

部署合约后，您将在该Deployed Contracts部分下看到它 - 您现在可以在其中调用智能合约上的函数。

调用该retrieve函数将返回一个0right now 值，这是 Solidity 中整数的默认值。

此外，我们将在“输出”面板中看到一些关于Storage.retrieve我们的函数调用的日志。

现在，让我们尝试store使用 number 调用该值5。

同样，我们在输出面板中看到一些关于调用的日志Storage.store。现在，如果我们再次尝试retrieve，输出将是5.

注意- 我们所做的这些函数调用/交易都没有打开您的数字钱包 (Metamask)。这是因为我们Javascript VM目前正在测试，而那只是一个使用假账户的模拟器。部署到真实网络（测试网或主网）时，需要通过您的数字钱包确认和签署交易。

## 推荐

要了解有关 Remix 的更多信息，我们建议：

- 浏览[Remix IDE Docs上的文档](https://remix-ide.readthedocs.io/en/latest/)
- 使用 Remix 附带的默认智能合约来处理工作流程

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/my.png)