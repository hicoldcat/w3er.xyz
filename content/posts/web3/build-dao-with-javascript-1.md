---
title: Web3教程：仅仅一个周末，只用Javascript就可以构建出你自己的DAO（1）
description: null
author: 李留白
weight: 0
date: 2022-11-03T12:50:57.332Z
lastmod: 2022-11-03T13:25:22.756Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211032050628.png
---

### 目录

**🏗 开始**

- 🥑 让我们构建一些很酷的东西。
- 🤔 什么是 DAO？

**💻 为 DAO 设置客户端应用程序**

- 🛠 获取客户端代码 + 获取设置。
- 👜 将“连接到钱包”添加到您的 DAO 仪表板。

**😎 创建会员 NFT**
- 🌈 部署你的 NFT 包。
- 😤 部署 NFT 元数据。
- 😲 让用户铸造你的 NFT。

**💎 创建代币+治理**

- 😈 部署 ERC-20 合约。
- 😇 在 DAO Dashboard 上展示代币持有者。
- 👩‍⚖️ 建库+治理。
- ✅ 让用户对提案进行投票。

**🌟收尾工作**
- 🤠 删除您的管理员权限并处理基本错误。
- 🎉 完成并庆祝。



### 👋 欢迎

欢迎 ：）。我的名字是[Farza](https://twitter.com/FarzaTV?utm_source=buildspace.so&utm_medium=buildspace_project)，我只是一个在这里和你们聊天的人。顺便说一句——[非常感谢 Nacho lacovino](https://twitter.com/nachoiacovino?utm_source=buildspace.so&utm_medium=buildspace_project)和[Jonas](https://twitter.com/jnsdls?utm_source=buildspace.so&utm_medium=buildspace_project)帮助我们编写这些文档。

该项目适用于希望通过自己构建一个 DAO 来更好地了解 DAO 到底是什么的开发人员。

你真正需要知道的只是 **一些终端技能、一些 JavaScript 和一些 React.js**。你会在学习的路上捡起其他的知识。不需要用到 Solidity。我们将使用这个名为[thirdweb](https://thirdweb.com/?utm_source=buildspace&utm_medium=buildspace_project)的奇特工具，它让我们只使用JavaScript 来处理智能合约。

在继续之前为您提供的一些操作项：

1. **在项目仪表板中连接您的 Discord。** 所有好东西都将发生在 Discord 中的一个秘密类别中，只有连接 Discord 后您才能访问该类别。（译者注：可以进入原文网站查看）
2. **在项目仪表板中连接您的以太坊钱包。** 完成此操作后，我们将向您空投 NFT，如果您没有连接钱包，我们将无法向您空投！顺便说一句 - 您不需要支付任何汽油费，我们会为您支付！（译者注：可以点击阅读原文，进入原文网站）
3. **一定要参加现场启动仪式**。如果你错过了，不用担心。不需要参加！

### 🤔 我们在建造什么？

在此处查看我制作的涵盖该项目的快速视频：

https://www.loom.com/share/e9b6c6fe11fa41f3bf3bdf6ee0683335


### 💎 赚取 NFT

当您完成该项目时，您将能够获得 NFT。

**只有在注册后 60 天内完成 NFT，您才有资格获得 NFT。**所以确保你安排时间完成，你得到了这个！

**完成项目后，您将在 7 天内获得 NFT 空投**

这是它的样子：

https://www.loom.com/share/b315bcdd07cf4e78b9b29806ef931ad2

**注意：您的 NFT 最多可能需要 7 天才能到达（您还会收到一封电子邮件！）。如果超过 7 天，请联系 Discord 上的 Raza (TooManyBugs#3359) 或 Alec (alec#8853)！**

### 🤚 如何获得帮助

当您有问题时，请为该项目找到合适的渠道。

这个项目分为4个部分。您可以通过查看构建空间仪表板页面顶部的进度条来查看您所在的部分。因此，例如，如果您卡在第 3 节的某个部分，您应该进入 `#section-3-help` Discord。如果您参与了多个项目，请务必在正确的项目类别中提问！

**当您需要帮助时，请随时直接联系thirdweb 团队。**

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211032111281.png)

### 🤘 看到问题了吗？想要改进一些东西？自己修复；）

**[所有这些内容都是完全开源的](https://github.com/buildspace/buildspace-projects?utm_source=buildspace.so&utm_medium=buildspace_project)**。如果您发现问题、拼写错误等——您可以轻松地自行修复并提交 PR！如果您需要帮助，请 `@alec` 在 Discord 中私信 :)。

至少，如果你觉得很满意，请在 repo 上放一个 ⭐！

🤔 什么是 DAO？



## 👻 DAO 是如何工作的？

什么是 DAO？嗯。

关于 DAO 有很多不同的定义和解释。我刚用谷歌搜索它，它说：“去中心化自治组织 (DAO) 是一个没有中央领导的实体。决策是自下而上做出的，由围绕区块链上执行的一组特定规则组织的社区管理”。

我觉得即使读了这篇文章，我仍然不明白 DAO 到底是什么。简单地说，DAO 是一个拥有共享银行账户的社区。通过对成员创建的不同提案进行投票来决定如何使用该银行账户。当一个提案获得足够多的选票时，它就会在链上执行！

## 💰 Y-Combinator

[Y-Combinator (YC)](https://www.ycombinator.com/?utm_source=buildspace.so&utm_medium=buildspace_project)是著名的创业加速器。他们有一个庞大的银行账户，资金来自不同的人 + 已经退出的初创公司的利润。“YC 合伙人”是一群人，他们决定接受哪些公司加入计划并投资。合伙人投资了各种价值爆炸的公司：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211032113927.png)

YC 有一个非常集中的方法，一小部分关键人物决定投资谁——这不是一件坏事！我想强调的是，仅仅因为某些东西是中心化**的并不意味着它很糟糕，哈哈。**

## 🤠 Y-Combinator 作为 DAO

让我们把 YC 想象成一个 DAO，我们称之为 YCDAO。

投资者可以将资金投入 YCDAO 并取回 $YCDAO 代币。这笔钱将存在于任何人都能看到的链上“金库”中。国库只是共享的银行账户。

例如，如果我将 5 美元投入 YC DAO，我可能会取回 5 美元的 YCDAO 代币。如果我投入 100,000 美元，我可能会取回 100,000 美元的 YCDAO 代币。这取决于创建者如何设置规则。

酷，所以现在我有一些 $YCDAO。拥有代币的 YCDAO 成员现在可以对申请 YCDAO 的不同公司的提案进行投票，并决定是否投资。

“投票权”将取决于它最初的设置方式。

例如，1 个 $YCDAO 代币可能等于一票。但也许你认为这不公平，因为这意味着超级富豪会用他们的选票来统治这个组织。没关系，您可以将规则更改为：“每个人都有一票，无论他们拥有多少代币”。真正由 DAO 的创建者来思考什么对成员最有利。但是，我只想让您知道**投票权公式可以自定义！**这就是 DAO 的绝对魅力🌸。

假设 YCDAO 中 90% 的人投票“是”来投资 DogDAO - 一个为狗主人服务的DAO。很好。该提案将自动在链上执行。YCDAO 会自动将资金发送到 DogDAO 的金库。作为交换，DogDAO 会自动将 $DOG 发送到 YCDAO 的金库。砰。投资完成。

假设DogDAO起飞，并通过销售狗训练课程或其他东西而变得疯狂地盈利。这意味着YCDAO拥有的$DOG代币将增值，如果YCDAO愿意，他们将通过出售代币来获利。$YCDAO代币本身也会升值，因为YCDAO的国库由于一项奇妙的投资而增值了。

这里的神奇之处在于，您无需信任中央实体即可确保投资顺利进行。这一切都是自动的，并且围绕着 DAO 在创建时设置的规则。

你绝对应该把这个例子多读几遍! 我想为你提供一些具体的东西，以使你的头脑更加清晰。当然，YCDAO不是真的，但有不少人在追求这种模式。[下面](https://www.hyperscalefund.com/?utm_source=buildspace.so&utm_medium=buildspace_project)是一个例子。

**请记住，归根结底，DAO 只是一群拥有共享银行账户的陌生人，他们投票决定如何使用该银行账户。**

如果您仍然不明白，请不要担心。**让我们进入代码并自己构建一个:)。**

### 🚨 进度报告

嘿！`#progress`让我们都知道你将为这个项目构建什么样的 DAO 。越有趣越好。如果你还不知道，那就扔东西吧！例子：

**AnimeDAO** — 一个动漫爱好者社区，每当他们与社区互动并谈论动漫时，他们都会收到 $ANIME。

**PizzaDAO——**一个专注于赠送披萨的慈善 DAO。会员可以向慈善机构捐赠 ETH 并获得 $PIZZA 作为回报。

**FarzaDAO** — Farza 粉丝的 DAO，只要他们在推特上发布有关 Farza 的好消息，就会收到 $FARZA。

**ArtistDAO** — 一个由艺术家组成的 DAO，为各级艺术家举办活动和研讨会。每当会员举办活动或研讨会时，他们都会收到 $ARTIST。

**PakistanDAO** — 一个供巴基斯坦创业社区成员相互支持的 DAO。会员只要为其他创始人提供价值，例如通过向他们提供建议、对他们的产品进行 Beta 测试或在社交媒体上支持他们，就可以获得 $PAKISTAN。

请不要复制我的确切例子嘿嘿。发挥创意。**我真的希望你认真思考一些让你微笑的事情，或者你的朋友会觉得有趣的事情，哈哈。**

> Github源码：https://github.com/buildspace/buildspace-projects/tree/main/JS_DAO/
>
> 原文链接：https://buildspace.so/p/build-dao-with-javascript

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
