---
title: 如何在 2023 年真正成为 Web3 开发人员
description: null
author: 李留白
weight: 0
date: 2023-01-08T00:30:40.108Z
lastmod: 2023-01-07T07:20:21.883Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230107141156.png
---

这是一份关于在2023年成为web3开发者的建议指南。

我将概述我个人在[thirdweb](https://thirdweb.com/)担任开发人员关系工程师的具体步骤；thirdweb 一套用于在 web3 中构建的开发人员工具。

与其他路线图不同的是，我不会给你300个要学的东西，让你陷入教程的地狱。这是一个可操作的技术序列，包括我在整个旅程中发现的最有帮助的资源，供你学习。

让我们深入了解一下吧! 下面是我们将涵盖的技术栈的每一层的预览。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230107141527.png)

## 重要免责声明（阅读此文）

单靠消耗资源永远不会教你如何成为一名开发人员。

每个开发者都必须在了解如何在编码的过程中不断地进行自己的战斗，并在这一过程中把巨大的拼图的碎片拼起来。

**一旦你知道如何开始构建，就退出路线图，独自去做。试着建造一些东西。这将会很糟糕。这就是问题的关键!**

**当你遇到困难的时候，再回来找资源来填补你的知识空白；了解有哪些工具可以用来解决本指南中的问题。**

你所做的每一个项目都会比上一个更好，你很快就会对你的旧代码感到尴尬。

不要*只是*消耗这些资源，它们只是帮助您在**实际构建自己的项目时以逻辑顺序为您指明正确的方向。**

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/l0Iy3tcRNXtp0jFXa.gif)

这并不容易，但值得。系好安全带，让我们开始吧！

## 路线图

我们将在本路线图中涵盖很多主题，我将为您提供我个人用来在路线的每一步中加深理解的资源。以下是我们将介绍的一些工具的快速预览：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230107142032.png)

## 基础知识：HTML 和 CSS

每个网站都包含三样东西：

**HTML**：页面上元素的结构

**CSS** : 页面元素的样式

**JavaScript**：页面上元素的交互性

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230107142049.png)

您构建的任何应用程序，无论您使用何种技术，最终都会转换为这三种语言并在网页上提供给用户。

因此，在深入了解任何“编程”（向您的应用程序添加交互性或逻辑）之前，首先了解 HTML 和 CSS 的基础知识是一项重要的基础知识。

### HTML & CSS

[CS50](https://pll.harvard.edu/course/cs50-introduction-computer-science?delta=0)是哈佛计算机科学的入门课程。它是由世界上最好的讲师教授的杰出资源。我已经参加了他们的多门免费在线课程，包括他们前几年的 CS50 课程，我怎么推荐都不为过。

下面是课程的第 9 讲*（从索引 0 开始）*，重点是 HTML、CSS 和 JavaScript。它向您介绍网页工作原理的基础知识，并介绍三种语言如何协同工作以创建交互式网站。

<iframe width="791" height="445" src="https://www.youtube.com/embed/fiR5QMZn3XA?list=PLhQjrBD2T380F_inVRXMIHCqLaNUd7bN4" title="CS50 2022 - Lecture 8 - HTML, CSS, JavaScript" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

即使您已经了解 HTML 和 CSS，您也会惊讶地发现，通过回到基础知识并加深对基础知识的理解，您从这些讲座中学到了多少东西。

### JavaScript

学习 HTML 和 CSS 的基础知识比学习 JavaScript 容易得多；相比之下，这是一个庞然大物；但也更有乐趣！

首先，在深入研究 JavaScript 本身之前，您需要学习**编程的绝对基础知识。**这包括任何编程语言的关键概念；变量、函数、循环和数据结构。

下面的视频“JavaScript 初学者教程”是一个相对简明的资源，可以教您这些基本原理。

<iframe width="791" height="445" src="https://www.youtube.com/embed/W6NZfCO5SIk" title="JavaScript Tutorial for Beginners: Learn JavaScript in 1 Hour" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

上面的视频只是初学者了解 JavaScript 基础知识的内容示例。还有很多其他的，我鼓励你在进一步推进这个路线图之前尽可能多地学习。

这里有一些更棒的互动资源，您可以用来作为初学者学习：

- [Free Code Camp: Full JavaScript Course for Beginners](https://www.youtube.com/watch?v=PkZNo7MFNFg)
- [The Odin Project](https://www.theodinproject.com/)
- [Wes Bos - JavaScript30](https://javascript30.com/)
- [MDN Documentation](https://developer.mozilla.org/en-US/)

### 可选（目前）：计算机科学基础

JavaScript 对初学者友好，并且抽象出许多编程的复杂性；它的设计让您可以快速上手。

如果您愿意花更多时间学习计算机科学基础知识，请考虑观看 CS50 2022 讲座的**完整**[播放列表](https://www.youtube.com/playlist?list=PLhQjrBD2T380F_inVRXMIHCqLaNUd7bN4)；总共约 20 小时。

我个人的观点是，最好以后再来学习这些基础知识，并**尽快开始构建一些东西**。

*我个人直到成为专业软件工程师工作 1-2 年后才详细学习这些计算机科学基础知识（我知道这听起来可能很疯狂）。同样，这些是我的个人意见，其他人可能会不同意这一点。*

### 高级JavaScript

这是我的学习曲线达到陡峭点的地方。

我即将分享的资源一开始可能会让人觉得不知所措；我看了好几遍，写了很多很多页的笔记，才开始理解这些概念。

#### JavaScript 是如何*工作的*

下面的视频深入探讨了 JavaScript 的细微差别，以及这门语言的魔力是如何在幕后运作的！🧙

像作用域、闭包和原型继承这样的话题很有趣，在过去几年的求职面试中我被问过好几次。

这是 2018 年的另一场 CS50 讲座，至今仍是我个人最喜欢的学习 JavaScript 的资源之一！

<iframe width="791" height="445" src="https://www.youtube.com/embed/X52b-8y2Hf4" title="Overview, JavaScript - Lecture 0 - CS50's Mobile App Development with React Native 2018" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

#### 异步JavaScript

该播放列表中的后续讲座也相当复杂，但涵盖了对更深层次理解 JavaScript 非常重要的主题。[您将了解ES6](https://www.w3schools.com/js/js_es6.asp)中引入的一些更现代的 JavaScript 功能，例如回调、Promise和异步函数。

<iframe width="791" height="445" src="https://www.youtube.com/embed/3Ay2lS6tm4M" title="JavaScript, ES6 - Lecture 1 - CS50's Mobile App Development with React Native 2018" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

#### （可选）付费课程：Wes Bos的 "ES6 for Everyone!"课程

我推荐的另一个资源（如果您负担得起的话）是[Wes Bos的“ ](https://twitter.com/wesbos)[ES6 for Everyone！](https://es6.io/) ”课程。这是一门优秀的互动课程，可以教您各种超级有用的语法糖，这些语法糖是[ECMAScript](https://en.wikipedia.org/wiki/ECMAScript)更高版本的一部分。

这不是必需的，但 Wes 是一位出色的老师，该课程是一种有趣的方式，可以让您学习用 JavaScript 更有效地做事的方式。

本课程非常适合初学者，将帮助您编写更现代的 Javascript。

#### （非常可选）付费课程：掌握编码面试

当您在旅途中构建项目时，您编写的一些代码不会像它应该的那样高效。

[Andrei Neagoie开设的](https://twitter.com/andreineagoie)“[Master the Coding Interview: Big Tech (FAANG) Interviews](https://www.udemy.com/course/master-the-coding-interview-big-tech-faang-interviews/)[Master the Coding Interview: Big Tech (FAANG) Interviews](https://www.udemy.com/course/master-the-coding-interview-big-tech-faang-interviews/)”课程是一门付费课程，它教会了我大量关于编写更好代码的知识。

课程的标题有点吸引点击，但它几乎教会了你学习数据结构和算法的一切；有 30 个互动问题供您完成；全部都是用JavaScript编写的！

对我来说，这是非常困难的，我花了好几个月才完成；这就是为什么我在这门课程上贴上“非常可选”的标签。这是一个了不起的资源，我建议您在旅途中的某个时刻使用它，但如果您是初学者，它可能对您来说太难了。

## React - 构建用户界面

如果此时您已经开始构建您的项目，您可能已经意识到仅通过编写 HTML、CSS 和 JS 来构建应用程序是很困难的。

在过去十年中，开发人员提出了许多不同的库和框架来解决这些问题；[jQuery](https://jquery.com/)、[Angular](https://angular.io/)、[Svelte](https://svelte.dev/)、[Vue](https://vuejs.org/) ……不胜枚举！

一个library名列前茅：[React](https://reactjs.org/)，即：

- [2022 年最常用的前端 JS 框架](https://survey.stackoverflow.co/2022/#web-frameworks-and-technologies)。
- [大多数开发人员“希望”在 2022 年使用的框架](https://survey.stackoverflow.co/2022/#web-frameworks-and-technologies)。
- 最受欢迎的前端框架（因为懒得去找这个数据的来源，所以...... "可能"😄）。

React 使用一种称为[JSX](https://reactjs.org/docs/introducing-jsx.html)的语法将您的 UI 结构和设计与您在同一位置的“交互性”结合起来：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230107142635.png)

这个关于用户界面的 CS50W 讲座是了解 React “为什么”的一个很好的资源：

<iframe width="791" height="445" src="https://www.youtube.com/embed/jrBhi8wbzPw" title="CS50W - Lecture 6 - User Interfaces" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

本讲座概述了在没有框架或库的情况下构建应用程序的问题，并介绍了 React 作为您可能已经遇到的一些问题的解决方案。

#### 深入 React

React 核心团队在过去几个月（*或几年）中一直在迭代发布其新的“测试版”文档。*

这份新文档非常棒，我强烈建议您阅读他们的[快速入门](https://beta.reactjs.org/learn)，在一个简单的页面上学习 React 的所有核心概念。

您将了解最常见的 React 模式，例如创建组件、显示数据和响应事件。

在你深入研究单独使用 React 进行构建之前，我将向你介绍 React 之上的另一个抽象层，称为[Next.js](https://nextjs.org/)；这使事情更上一层楼。

## Next.js - 让React变得更好

我将为您省去很多麻烦：**完全跳过 Create React App (CRA)，甚至可以说是 Vite。从一开始就使用 Next.js。**

下面是一个很棒的三分钟解释，解释了为什么 CRA 不再是使用 React 的新项目的理想选择：

<iframe width="791" height="445" src="https://www.youtube.com/embed/o9TJWEPc0Lk" title="Don't Use Create React App in 2023" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

#### 开始使用 Next.js

Next.js 文档绝对精彩。[**Lee Robinson**](https://twitter.com/leeerob)和他的 DX 工程师团队编写了出色的交互式文档，以帮助您学习 Next.js 并在此过程中测验您的知识。

出于这个原因，我建议先浏览交互式文档；然后开始[使用他们的 CLI 创建您的第一个 Next.js 应用程序](https://nextjs.org/docs#automatic-setup)。

## TypeScript

这篇文章的另一个热门话题🌶️：尽快开始使用 TypeScript！JavaScript 对于初学者来说是一门很棒的语言，但这是一把双刃剑。它对初学者有好处的部分原因是它给了你很多编写💩代码的自由，而且它不会报错！

一开始，TypeScript 可能会让 🍑 非常痛苦，但一旦它被点击，你就再也不想回头了。越来越多的开发人员和开发人员工具默认使用 TypeScript，这是有充分理由的；类型安全使您的代码出现错误的可能性大大降低！

我的建议：一开始就关闭[严格模式](https://www.typescriptlang.org/tsconfig#strict)。每当您完全陷入困境并且不知道该怎么做时，请使用[`any`](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#any)类型。这些实际上是不好的做法，但可以帮助您在不破坏键盘的情况下开始使用 TypeScript。

随着您使用 TypeScript 编写越来越多的代码并掌握更多的技巧，您会很乐意启用严格模式并更谨慎地使用`any`类型；直到你最终在你的代码库中拥有完全的类型安全并且厌恶地看着 JavaScript！

## Styling

有许多选项供您构建更漂亮的应用程序，这些应用程序最终只是抽象 CSS 的不同方式，因此它……基本上是 CSS，但更容易。

进入 2023 年的普遍共识是[Tailwind CSS](https://tailwindcss.com/)是向应用程序添加样式的黄金标准。我个人喜欢使用带有预构建组件的名为[Material UI的工具；](https://mui.com/)但是，大多数人在这一点上不同意我的看法。

[Theo的](https://twitter.com/t3dotgg)“ [The Best of CSS](https://www.youtube.com/watch?v=CQuTF-bkOgc) ”是一个很棒的视频，它权衡了可用的不同选项及其优缺点：

<iframe width="791" height="445" src="https://www.youtube.com/embed/CQuTF-bkOgc" title="The Best Of CSS - Tailwind vs MUI vs Bootstrap vs Chakra vs..." frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

简短回答：如果您还不知道也不喜欢 UI 库，请选择 Tailwind CSS。

## 区块链基础

如果您还不了解什么是区块链或智能合约，[Patrick Collins](https://twitter.com/PatrickAlphaC)的区块链课程对初学者很有帮助。

我建议你观看 32 小时课程的**前 2 小时**，该课程介绍了区块链、智能合约和 web3 的核心概念。您可以随心所欲地挑选课程中您感兴趣的其他领域！

<iframe width="791" height="445" src="https://www.youtube.com/embed/gyMwXuJrbJQ" title="Learn Blockchain, Solidity, and Full Stack Web3 Development with JavaScript – 32-Hour Course" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## 了解 Web3 技术栈

我推荐观看的介绍性视频是[Nader Dabit](https://hashnode.com/@dabit3)的“Defining the Web3 Stack” ；它概述了与传统“web2”应用程序相比等效的 web3 技术栈。

<iframe width="791" height="445" src="https://www.youtube.com/embed/f9XRH7bjV8M" title="Defining the Web3 Stack - Nader Dabit - (Next.js Conf 2021)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

作为 Nader 2021 年演讲的更新图表，我在下面提供了我对 web3 应用程序技术栈的想法：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230107142856.png)

构建 web3 应用程序时需要考虑很多移动部分。有大量工具可帮助您构建去中心化应用程序的不同方面，您可以选择最喜欢的工具。

当然，我会在这里提供我在过去一年中尝试使用这些工具后得出的建议。

### 以“艰难的方式”构建全栈 web3 应用程序

可以说，当我进入 web3 空间时，我消耗的最好的资源是[Nader Dabit](https://hashnode.com/@dabit3) 的“ [Full Stack NFT Marketplace](https://www.youtube.com/watch?v=GKJBEEXUha0) ”视频教程。您将学习如何快速构建全栈 web3 应用程序的几乎所有方面，包括：

- NFT收藏智能合约
- NFT市场智能合约
- 智能合约开发环境及测试
- IPFS文件上传和下载
- 前端技术和 Next.js 构建 web3 应用程序
- RPC 节点和 IPFS 网关

<iframe width="791" height="445" src="https://www.youtube.com/embed/GKJBEEXUha0" title="How to Build a Full Stack NFT Marketplace on Ethereum with Polygon and Next.js - [2021 Tutorial]" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

与上一步相比，这可能感觉有点跳跃，您可能无法立即理解本视频中的所有内容，但这没关系！

该资源教会了我很多关于 web3 技术栈的各个部分如何组合在一起的知识，并教你如何使用现代技术构建一个史诗般的项目。

#### 扩展 Nader 的项目

*真正*让我明白发生了什么的事情是为 Nader 的 NFT 市场项目添加了更多功能；具体来说，添加一个功能来获取市场智能合约上的单个列表信息。

将此功能添加到项目中可能听起来很容易，但它会测试您是否真正了解项目的运作方式；并且是在构建本身之上添加的重要一步。

#### 为什么这是“艰难的道路”？

该资源向您介绍了我认为是构建全栈 web3 应用程序的低级抽象；它向您展示了流程的每一步，理解这些步骤非常重要。

*但是，我认为该视频的目的不是要在 2023 年构建“production-ready”应用程序。我这样说是因为自 2021 年年中（这个时间*）以来已经发布了大量工具*制作了视频），*这极大地抽象了构建全栈 web3 应用程序的复杂性。

让我们快速介绍一些其他核心概念，然后深入研究可用于构建 web3 应用程序的更现代的工具。

## RPC 节点

要与区块链进行通信，您需要使用一个节点。除非您想运行自己的节点，否则您需要使用诸如 Alchemy、Coinbase 或 Moralis 之类的服务提供商*（或我们将在下一节中解释的 thirdweb）*来这样做。

下面是一个很好的资源，概述了 RPC 节点是什么以及构建 web3 应用程序需要它的原因。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230107143131.png)

[什么是RPC节点？ RPC节点是一个允许web3应用程序提交远程过程调用的服务器。了解如何使用RPC节点并选择一个RPC节点供应商。](https://www.alchemy.com/overviews/rpc-node)

#### 有趣的项目：Lava网络

我很高兴在 2023 年看到的一个项目是[Lava网络](https://lavanet.xyz/)；它提供了 RPC 节点的去中心化替代方案。如果您有兴趣，请查看！

## 去中心化存储解决方案

并非所有信息都需要存储在区块链上。在区块链上存储数据本身会产生 gas 费用，并且有很大的存储限制；出于这个原因，一种常见的模式是将您的信息“链下”存储，并将该数据的 URL 存储在区块链上。

存在不存储在“链上”但仍不受 AWS 或谷歌云等集中式服务控制的存储解决方案。2023 年两个突出的去中心化存储解决方案是：

1. [IPFS](https://ipfs.tech/)
2. [Arweave](https://www.arweave.org/)

### IPFS

下面简要总结了 IPFS 是什么、它是如何工作的，以及如何使用[thirdweb](https://thirdweb.com/storage)将它集成到您的应用程序中（我们将在接下来详细讨论）。

<iframe width="791" height="445" src="https://www.youtube.com/embed/4Nnu9Cy7SKc" title="How To Easily Add IPFS Into Your Web3 App" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

要在 Google Chrome 等大多数浏览器上访问存储在 IPFS 上的数据，您需要使用 IPFS 网关。这里有几个选项可供您选择：

1. 公共网关（人人可用，不太靠谱）
2. 私有网关——使用[Pinata](https://www.pinata.cloud/)或[nft.storage](http://nft.storage/)等服务来获得更快的速度和更高的可用性*（同样，或者我们接下来讨论的只是使用 thirdweb）*。

### Arweave

Nader Dabit 的另一个优秀视频可供您了解 Arweave 和 Arweave 生态系统的其他方面，例如[Smartweave](https://github.com/ArweaveTeam/SmartWeave)和[Warp](https://warp.cc/)。

<iframe width="791" height="445" src="https://www.youtube.com/embed/cGLMN5A2C4E" title="Building Full Stack Permanent Applications with Arweave, Smartweave, and Next.js" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

我应该提到很多这样的技术，例如 Arweave，如果您正在使用它们进行构建，则有[资助计划；](https://www.arweave.org/get-involved/investment-funding)如果您正在寻找一些资金，这对您来说可能会很有趣。

## thirdweb

**全面披露：我在** [**thirdweb**](https://twitter.com/thirdweb) **团队工作！**

我使用 Nader 的 NFT 市场视频作为我从事的一个自由职业项目的起点，因为我开始提高我在 web3 开发方面的技能。我开始意识到构建智能合约很困难！

在这一点上，我没有信心为我的客户的要求编写一个功能齐全、gas 优化、无错误的市场智能合约；所以我开始问自己：*“一定有人做了这件事并在某个地方开源了！？”*

*好吧，事实证明我是对的！*

我在[thirdweb](https://thirdweb.com/)发布后不久偶然发现了它，发现他们有用于 NFT 集合、市场等的开源智能合约。

我基本上打算将他们的 solidity 智能合约复制粘贴到我现有的项目中，然后继续我的方式😂！这就是证据（不要告诉 [*Furqan*](https://twitter.com/FurqanR)*）：*

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230107143326.png)

但随着我进一步探索该产品，我发现他们有一个 SDK！我不必再使用ether了！他们还有连接用户钱包的工具，并为我管理我的 IPFS 存储！我甚至不需要我的项目中的智能合约代码了，因为他们有一个仪表盘，我可以用它来部署。

我能够删除大量我为市场项目写的代码，用thirdweb的工具来取代它们，最后我爱上了这个产品；在完成自由职业后的几个月，我申请了thirdweb现在的职位。

所以，虽然我在宣传thirdweb的产品时确实得到了报酬，但我是发自内心的，而不是从我的......钱包里拿钱？🥲在我加入团队之前，我确实在这里使用他们的工具。

### 学习thirdweb 

我花了很多时间改进 thirdweb 文档的[入门](https://portal.thirdweb.com/getting-started)流程，让您立即从构建智能合约到构建前端 Next.js 应用程序并开始运行！所以，我的建议是从那里开始！

或者，有一个涵盖相同内容的入门视频：

<iframe width="791" height="445" src="https://www.youtube.com/embed/fzsz-b2JV9U" title="Getting Started with Web3 Development - Deploy your First Smart Contract" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

一旦您获得了 thirdweb 技术栈的强大功能，您将不想回头！🤠 自从我加入以来，我们推出了更多功能，使 web3 构建过程更简单：

- SDK 中免费提供 IPFS 网关和 IPFS 渲染组件。
- 直接包含在 SDK 中的免费 RPC（可以覆盖）。
- IPFS 在 React hooks 中上传和下载[Storage](https://portal.thirdweb.com/storage)。
- [React hooks](https://portal.thirdweb.com/react)适用于您在前端可以想象的一切；用于用户钱包和与智能合约的交互。
- 完整的Solidity SDK , 称为“ [ContractKit](https://portal.thirdweb.com/contractkit) ”，类似于 OpenZeppelin。
- 一个探索[页面](https://thirdweb.com/explore)；为您部署最流行的智能合约标准。
- 更多

使用 thirdweb 部署智能合约并在您的 Next.js 应用程序中连接到它，我保证您永远不想回到旧技术栈。

## 其他 Web3 前端库

为了不至于完全偏颇，我还会向你推荐一些其他的现代库，让你为你的web3应用程序建立更强大的前端。

### wagmi

[wagmi](https://wagmi.sh/)是用于构建 EVM 前端的 React Hooks 库。与[thirdweb React SDK](https://portal.thirdweb.com/react)非常相似，它具有几乎所有你能想到的东西的钩子，并且比单独使用 ethers 或 web3.js 要好得多。

该[文档](https://wagmi.sh/core/getting-started)是您了解如何在 Next.js/React 应用程序中使用它的一个很好的起点。

### RainbowKit

[RainbowKit](https://www.rainbowkit.com/)是一个优秀的库，用于将连接钱包功能添加到您的应用程序中，并为您的用户支持各种不同的连接选项。

他们的[文档](https://www.rainbowkit.com/docs/introduction)简明扼要，包括有用的短视频，可帮助您快速启动和运行。

## 索引解决方案

一些 web3 应用程序需要无法直接从任何智能合约获得的信息；例如一个钱包拥有哪些 NFT，或者一个 NFT 被转移了多少次。

涉及索引解决方案时，可以使用[The Graph](https://thegraph.com/en/)；一个去中心化的解决方案，并再次使用 Nader 的资源来学习如何开始：

<iframe width="791" height="445" src="https://www.youtube.com/embed/JpOLhkmtOak" title="Building on Ethereum with GraphQL, The Graph, and Next.js" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

您可能更喜欢使用 SDK 或 API 端点来获取索引信息，并且您可以使用许多流行的解决方案，每个解决方案在其文档中都有有用的资源和视频：

- [Alchemy](https://www.alchemy.com/sdk)
- [Coinbase Cloud](https://docs.cloud.coinbase.com/node/docs/welcome-to-node)
- [Moralis](https://moralis.io/)

这些工具具有相似的功能，各有优缺点，您可以使用它们的文档来探索哪种工具适合您。

## 智能合约

这可能看起来很疯狂，智能合约部分一直在这里。🤯

我把智能合约放在学习完整的前端技术栈之后的原因是，你经常要建立在其他人已经部署的智能合约之上；例如[Lens Protocol](https://www.lens.xyz/)，或部署符合您需求的 gas 优化、经过审计的智能合约，例如[thirdweb Explore](https://thirdweb.com/explore)上可用的合约。

要在 web3 中找到一份工作，你不需要成为编写自己的智能合约的专家。在 thirdweb，我们有一个团队致力于编写 Solidity 智能合约和构建我们的 Solidity SDK；我们的其他工程师都*了解*Solidity 代码；但并不是每天都在积极地编写智能合约。

这可能是一个热门话题🌶️🥵，但是在学习了如何实际**构建人们与之交互的东西**之后*，你应该学习构建智能合约的细节；这*通常意味着您需要某种前端应用程序技能。

有些人会不同意这一点；因为 web3 中的几乎所有内容都依赖于去中心化应用程序的基础层；**这是智能合约**。我并不是说它是正确的，这只是我个人学习web3所走的路。

您可以使用许多资源来学习 Solidity 本身，例如：

- [CryptoZombies](https://cryptozombies.io/)（互动课程）
- 从字面上看，只需阅读Solidity[文档](https://docs.soliditylang.org/en/v0.8.17/)🥸

### 面向对象编程

OOP。*更像 oof*，我说得对吗？😑

Solidity 是一种面向对象的编程语言，用于构建您自己的智能合约。如果您了解 OOP 原则，那么您已经能够编写基本的 Solidity 了。

通常，您看不到用于面向对象编程的 JavaScript；Java、C# 或 Python 等其他语言更适合这种编写代码的风格。

要了解 OOP 的基础知识，我建议您使用尽可能多的资源来了解基本原理。下面是对 JavaScript 中 OOP 的介绍，将帮助您了解核心概念：

<iframe width="791" height="445" src="https://www.youtube.com/embed/PFmuCDHHpwk" title="Object-oriented Programming in JavaScript: Made Super Simple | Mosh" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

#### Solidity中的OOP

[一旦您了解了 OOP 是什么，我建议您返回到 Patrick在这个时间点](https://youtu.be/gyMwXuJrbJQ?t=11135)（第 3 章） 的视频，以了解继承和覆盖在 Solidity 中是如何工作的：

<iframe width="791" height="445" src="https://www.youtube.com/embed/gyMwXuJrbJQ" title="Learn Blockchain, Solidity, and Full Stack Web3 Development with JavaScript – 32-Hour Course" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

### 使用 OSS 智能合约

通常，您会希望构建各种 ERC 标准，例如 ERC20 可替代代币、ERC721 NFT 或市场，并加入一些变化，使你的项目与众不同。

现在您知道如何在 Solidity 中使用继承，您可以使用[OpenZeppelin Contracts](https://www.openzeppelin.com/contracts)和[thirdweb ContractKit](https://portal.thirdweb.com/contractkit)等库将核心逻辑块导入您的智能合约；并根据需要自定义它们！

#### ContractKit

thirdweb 的 ContractKit 拥有大量即插即用的智能合约，供您扩展、定制和包含在您自己的智能合约中；包括 ERC721、ERC1155、ERC20、质押、空投等等🤠！

<iframe width="791" height="445" src="https://www.youtube.com/embed/G3IHeKhVtpQ" title="Introducing ContractKit: The Web3 Tool for Base Contracts, Basic Contracts, and Extensions" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

### 智能合约安全

了解智能合约最常见的漏洞很重要，Patrick 在第 18 章[这个时间点](https://youtu.be/gyMwXuJrbJQ?t=113313)的 32 小时 Solidity 课程中再次详细介绍了。

<iframe width="791" height="445" src="https://www.youtube.com/embed/gyMwXuJrbJQ" title="Learn Blockchain, Solidity, and Full Stack Web3 Development with JavaScript – 32-Hour Course" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

还有一个详细[的开源存储库](https://github.com/kadenzipfel/smart-contract-attack-vectors)，概述了智能合约的常见攻击向量和使用这些攻击的历史漏洞：

[https://github.com/kadenzipfel/smart-contract-attack-vectors](https://github.com/kadenzipfel/smart-contract-attack-vectors)

## 结论

就是这样！[这些是我个人用来发展我在 web3 方面的知识并在thirdweb](https://twitter.com/thirdweb)找到一份开发者关系工程师工作的资源！

我想重申，要想成为一名开发人员，光靠教程和资源是远远不够的。你需要尽快动手，当你在项目中遇到问题时再来找这些资源，并在未来的许多年里不断地靠自己的力量战斗。

本文的目的不是给您数月的资源供您使用，它的目的是让您在单独构建自己的项目时可以返回可信赖的高质量信息来源！🦸



> 原文：https://blog.jarrodwatts.com/how-to-become-a-web3-developer-in-2023

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
