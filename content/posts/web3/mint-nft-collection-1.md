---
title: 建立你自己的以太坊 NFT 集合（1）
description: null
author: 李留白
weight: 0
date: 2022-12-10T02:31:40.366Z
lastmod: 2022-12-10T03:34:16.501Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221210103130.png
---


## 目录

**入门**

- 我们在建造什么？

**铸造你的第一个 NFT**

- 什么是 NFT

- 启动并运行您的本地环境

- 在本地铸造 NFT

- 创建一个铸造 NFT 的合约

**在链上生成 NFT**

- 什么是链上数据。

- 使用静态 SVG 铸造 NFT

- 在链上动态生成 SVG NFT

**创建 WEB 客户端**

- 设置 React 应用程序和钱包

- 将钱包连接到网络应用程序

- 创建一个调用合约和铸造 NFT 的按钮

**完成并交付**

- Web 应用程序的最后完善

- 交付

## 入门

### 🤔 我们在建造什么？

#### 👋 欢迎

你来啦，太棒了，表示热烈欢迎。我叫Farza，是这些项目的课程导师。这个项目是为那些想要深入探索加密技术的开发人员而设计的。

为了更好的完成这个项目，你需要**对terminal、javascript、React.js有所了解**，剩下的我们会在你学习的道路上慢慢补齐。

在开始本次项目之前，你需要完成以下事情:

1. **在项目仪表板中连接您的Discord**。一些不错和有趣的知识会发布在我们的私密频道，只有当你连接Discord后才有权访问。
2. **在项目仪表板中连接以太坊钱包**。**如果您没有可以与dApp交互的钱包，您需要立即免费安装Metamask。没有钱包，你就无法完成项目！
3. **请务必出席现场直播讨论**。如果你错过了，不用担心。它不是必须参加的!现场录音将发布在Discord的频道。

#### 🚀 这会很棒

我很高兴向您展示如何从零开始 -> 发布您自己的 NFT。

“交付”是什么意思？好吧，这意味着您将实际制作一些东西，然后将其部署到互联网上供全世界查看和使用。您将能够向您的朋友、潜在雇主甚至您的狗展示最终项目！最棒的是，您的同学将成为您项目的第一批用户！！

这个东西很新，围绕它有很多炒作，要理解最好的学习方法可能会非常困难。

通过这个项目，您将通过实际编写代码并跳入好东西来了解加密技术。没有 BS 我们还将讨论理论和更深入的概念，但是，它们都将围绕您的项目进行构建。

#### 🛠 我们正在建设什么

您知道那些人们赚取数百万美元的网站，用户可以来“铸造”NFT 吗？我们正在构建它。

您的项目将让用户连接他们的以太坊钱包，并在他们的钱包中铸造一个 NFT，这样他们就可以真正拥有它。他们甚至可以在 OpenSea 上转售 NFT。NFT 本身可以根据您的需要进行定制。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221210105454.png)

他们甚至会出现在 OpenSea 上 :)。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221210105518.png)

最后，您将能够随心所欲地定制 NFT 本身，随心所欲。

但是，我将在这些指南中介绍的 NFT 基本上是一个方框，中间有一个随机生成的三个单词组合。为什么？因为我觉得这很有趣哈哈。其中一些三词组合真的很有趣。

最酷的部分？一切都将发生*在链上*。这意味着所有 NFT 数据都将存在于区块链本身。如果这没有意义，请不要担心 — 它会的 :)！

#### 🤚怎样获得帮助

当你遇到问题的时候，在Discord中找到相应的频道来寻求帮助。

这个项目分为4个部分。您可以通过查看buildspace仪表板页面顶部的进度条来查看您正在进行的部分。例如，如果你在第三部分的某个课程卡住了，你可以点击Discordd`#section-3-help`频道。

如果只是项目的某一部分，一定要在正确的项目频道中提出你的问题!

#### 🤘遇到问题了?想要改进?解决它吧

**[这里所有的内容都是开源](https://github.com/buildspace/buildspace-projects?utm_source=buildspace.so&utm_medium=buildspace_project)** 如果你遇到一些诸如拼写错误的问题，你可以自己轻松解决，并提交一个PR(Pull requsts)!

如果你需要帮助，请在Discord私信`@alec`😁

让我们给你找一些开源项目的代表吧!!

如果你觉得不错，可以在Github仓库点一个⭐!

## 铸造你的第一个 NFT

### 🤠 什么是 NFT

我见过许多文章将此复杂化。但我会尽量将它解释的简洁明了😀

NFT 是一个人人都可拥有的`Token`，它会链接到一些`数据`（例如：数字艺术、视频、图像等）。 NFT 的与众不同在于每个`Token`都有唯一的标识符，让所有者可以证明它是独一无二的。我们稍后会看到代码😘

现在，让我们快速回顾一个例子。

#### 🎨毕加索的素描

假设毕加索想要创作100幅他的素描画来出售。

我们知道每一个草图都是独一无二的，因为我们可以看到它是一个实体铅笔画。所以，很明显毕加索不只是打印了一堆复制品。

所以我们知道这些都是毕加索画的。怎么实现的呢?因为毕加索说这幅画是他画的，还签了名!你现在就可以购买毕加索的这幅素描原画，并从毕加索出售这幅画的画廊获得一张证明，证明你拥有一幅`毕加索原作`。

您必须相信画廊证书具有长久的公信力，总的来说非常的轻松容易! 

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221210105538.png)

#### 👨‍💻毕加索NFTs

现在让我们在**NFT领域**看看这个例子。

假设毕加索想要收集100幅他的草图，其中每幅草图都是数字绘图，就像JPEG文件一样。

毕加索将通过编写自己的`智能合约`(运行他的 NFT 系列的所有逻辑的代码）来创建他的 NFT 系列。

他会给每件艺术品一个唯一的标识符（例如 Sketch #1、Sketch #2、Sketch #3 等）。然后，当毕加索准备**推出**他的 NFT 系列时，他会启用自己的Ethereum钱包来**签署**NFT 系列。当一切准备就绪，他会将其放在区块链上--让每个人都可以在公共区块链上看到毕加索的 NFT 收藏。

然后，当毕加索想要出售其中一幅NFT时，他需要做的就是将NFT`转移`给购买它的人。

买NFT的人会很高兴，因为他们得到了**一幅独一无二的毕加索真品--来自毕加索本人**!而且他们可以证明这是最好的部分。

那么这些要如何实现?

1. 买家可以证明NFT系列最初是由毕加索本人签署和创作的，因为NFT会显示它来自毕加索签署的一份智能合约。记住，毕加索用他的公共地址在收藏品上签名!通常，艺人会公开他们的钱包地址，这样就没人能冒充他们了!
2. 买家可以证明NFT本身是藏品中独一的毕加索素描，因为藏品中的每个NFT都有一个唯一的标识符(例如：Sketch #1, Sketch #2, Sketch #3, etc)，来自最初创作该藏品的人。

好吧，希望这能让你觉得有***一些意义***。

这是一个非常强大的概念——任何文件都可以变得独一无二并具有价值，这个想法令人惊叹。这也是为什么你可能会看到推特最近在疯狂谈论NFTs，哈哈。NFT已经改变了艺术、游戏等领域。

我不指望你能完全理解我的科普。如果觉得毫无意义，也许可以再读一遍!只是在继续之前确保它有*一点点*意义，哈哈。我只是想告诉你们一些基础知识。

如果你对任何问题好奇，请在Discord上的`#section-1-help`中提问。

通常，当我开始编写代码时，我才开始真正理解一些东西。所以，让我们搭建我们的本地环境并回到一些代码:)。

### 🤖启动并运行您的本地环境

#### 📚区块链知识入门

在做任何事情之前，我们需要让我们的Ethereum本地网络正常工作。这就是我们编译和测试智能合约代码的方式，你知道需要怎样搭建一个本地环境来工作吗？

目前，你只需要知道智能合约是一段存储在区块链上的代码。区块链是公开的，任何人都可以免费安全地读写数据。你可以把它看成是AWS或Heroku，只不过没有人真正独揽它，它是由数千名被称为`矿工`的随机人员管理的。

我们的路线大概是：

1--编写一份智能合约。该合约具有我们NFT的所有逻辑。

2--将智能合约部署到区块链。这样，世界上任何人都可以访问和运行我们的智能合约，并可以创建NFT！

3--建立一个客户网站，让人们可以轻松地从我们的网页中Mint NFT。

如果你感兴趣的话，我建议你仔细阅读[这些](https://ethereum.org/en/developers/docs/intro-to-ethereum/?utm_source=buildspace.so&utm_medium=buildspace_project)文档。在我看来，这些是互联网上了解Ethereum工作原理的最佳指南！

#### ⚙️配置本地工具

我们经常使用一个名为**Hardhat**的工具，它可以让我们快速编译智能合约并在本地测试它们。首先，您需要获取node/npm。如果你没有，请转到[这里](https://hardhat.org/tutorial/setting-up-the-environment.html?utm_source=buildspace.so&utm_medium=buildspace_project)安装。

**注意:我的版本是v16。我知道有些人会在旧版本的node上出现`out of memory errors`，所以如果发生这种情况，请升级到v1版本6以上!**

接下来，我们去控制台。输入`cd`到你想要工作的目录。一旦你进入到目录，运行这些命令:

```bash
mkdir epic-nfts
cd epic-nfts
npm init -y
npm install --save-dev hardhat
```

当你运行这些命令会发生什么：

1. `mkdir epic-nfts`创建一个名为`epic-nfts`的目录。
2. `cd epic-nfts`进入新创建的目录。
3. `npm init -y`生成一个空的`npm`项目，而不经过交互过程。`-y`代表**yes**。
4. `npm install——save-dev hardhat@latest`表示安装Hardhat。

运行最后一个命令并安装Hardhat后，您可能会看到有关漏洞的消息。每次您从NPM安装某个软件包时，都会进行安全检查，以查看您正在安装的库中的任何软件包是否存在报告的漏洞。你要意识到，这更多的是对你的警告，如果您想了解更多信息，请在 Google 上搜索一下这些漏洞信息！

#### 🔨示例项目

Cool，现在我们安装了`Hardhat`,让我们开始一个示例项目。

```
npx hardhat
```

注意:如果你在Windows上使用Git Bash安装`Hardhat`，你可能会在这一步(HH1)遇到错误。如果遇到麻烦，您可以尝试使用Windows CMD来执行`HardHat`安装。更多的信息可以在[这里](https://github.com/nomiclabs/hardhat/issues/1400#issuecomment-824097242?utm_source=buildspace.so&utm_medium=buildspace_project)找到。

选择选项**Create a JavaScript project**，每一步都按`yes`。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221210105820.png)

示例项目会要求您安装`hardhat-waffle`和`hardhat-ethers`,这些是我们稍后会用到的软件。

继续安装这些依赖项，以防它没有自动执行。

```bash
npm install --save-dev chai @nomiclabs/hardhat-ethers ethers @nomicfoundation/hardhat-toolbox @nomicfoundation/hardhat-chai-matchers
```

您还需要安装名为**OpenZeppelin**的程序库，这是另一个用于开发安全智能合约的库。稍后我们将了解更多信息。现在，只需安装它😜

```bash
npm install @openzeppelin/contracts
```

然后运行：

```bash
npx hardhat test
```

你应该会看到如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221210105836.png)

Boom💥 如果您看到这个，则意味着您的本地环境已搭建好，**并且**您还运行/部署了智能合约到本地区块链。

这是非常具有成就感的。我们将更进一步讨论，但基本上，这里逐步发生的事情是：

1. Hardhat将您的智能合约从实体代码编译为字节码。
2. Hardhat将在您的计算机上启动一个“本地区块链”。它就像一个迷你的本地运行到Ethereum测试版本，以帮助你快速测试合约!
3. Hardhat会将编译好的代码`部署`到本地区块链,就是你在结尾看到的那个地址，也是我们在迷你版Ethereum上部署的合约。

如果您好奇，请随时查看项目内部的代码以了解其工作原理。具体来说是检查智能合约的`Lock.sol`和实际运行合约的`deploy.js`。

完成探索后，让我们进入下一节并开始我们自己的 NFT 合约。

> 原文链接：https://buildspace.so/p/mint-nft-collection

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
