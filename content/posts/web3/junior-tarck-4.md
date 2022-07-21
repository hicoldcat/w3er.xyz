---
title: Web3系列教程之进阶篇---4. 去中心化的代码协作协议 Radicle
description: null
author: 李留白
weight: 0
date: 2022-07-21T13:24:56.533Z
lastmod: 2022-07-21T13:41:28.586Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/Dcx1zWU(1).png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220721212545.png)

> 注意：当我们说到Git时，我们并不是指GitHub。GitHub是一个集中式平台，而Git是一个协议。你可以使用Git而不使用GitHub，而是将你的代码存储在GitLab或BitBucket等平台。

现在的代码协作大多是通过GitHub进行的。也有像GitLab和BitBucket这样的替代品，但GitHub是迄今为止使用最多的Git平台。

然而，使用GitHub并不是免费的午餐。GitHub为微软所拥有，而集中化就意味着要进行权衡。既然你在GitHub的平台上托管你的代码和内容，这意味着如果他们想的话，可以对你进行审查。让我们看一个例子。

## youtube-dl

youtube-dl是一个免费和开源的下载管理器，用于下载YouTube以及其他1000多个网站的视频和音频。它是GitHub上星级最高的项目之一，有超过100,000颗星。

2020年10月，GitHub根据美国唱片业协会（RIAA）的要求，关闭了youtube-dl存储库，以及该项目其他各种公开分叉。

这引发了很多争议，并强调了开源软件和代码是如何因其被托管在一个集中的平台上而被取缔的。尽管GitHub在2020年11月因公众的反感而公开恢复了该仓库，但这仍然表明他们对你的代码拥有多大的权力。

## 禁止帐户

由于 GitHub 是一家美国公司，所以他们必须按照美国政府的规定行事。作为一个中心化平台意味着 GitHub 可以禁止任何他们想要的人进入平台，并阻止他们通过全球最大的开源平台参与开源。

迫于美国政府的压力，GitHub 目前已封禁所有伊朗、叙利亚和克里米亚账户。这与建设自由开放社区的价值观和使命严重不一致。

------------------------

本着去中心化和消除中介控制的精神，Radicle 成为一种去中心化的代码协作工具。它提供了与 Git 类似的功能，没有集中的平台风险。

## 为什么是Radicle？

> 开源运行世界

你可能以前听过这个。免费和公共代码使构建软件变得更加容易和便宜，行业的创新也因此呈指数级增长。

像 GitHub 这样的代码协作平台无疑发挥了巨大的作用，并且永远改变了开发人员编写和维护软件的方式。但是，它们是中心化的，您在这些平台上所做的一切都被锁定并仅存在于这些平台上。

作为实现真正目标的替代方案，Radicle 建立在以下原则之上：

1. Radicle 必须优先考虑用户自由。这意味着用户可以自由地运行、复制、分发、研究、更改和改进软件。
2. Radicle 必须是可访问且不可审查的。
3. Radicle 必须是用户友好的。
4. Radicle 必须先离线，并且不需要互联网连接即可运行。
5. Radicle 不得在安全性上妥协，并使用加密签名保护系统的各个方面，以确保去中心化系统的安全性。

## Radicle Link

Radicle 网络建立在名为 Radicle Link 的点对点协议之上。Radicle Link 扩展了 Git 协议，并添加了广播消息，以分散的方式找到对相同数据感兴趣的对等方。

Gossip协议是一种去中心化的通信协议，所有节点随机与其他节点对话并请求信息或共享他们想要的信息，并通过将知识转发给其他节点，“Gossip”最终会给那些想要你所拥有的，或拥有你所想要的的节点让路。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220721213407.png)

与IPFS分享数据的方式非常相似，Radicle网络的参与者通过保留一份本地拷贝并分享他们感兴趣的数据来分享和传播。由于它是Git协议的扩展，它通过点对点网络保持了Git的数据传输协议的效率。

在Radicle中，存储库被称为项目，它们被复制并由同伴共享。如果你曾经使用过torrent，"peers "这个词可能听起来很熟悉。从本质上讲，他们是寻找他们感兴趣的数据的人或组织，或者将他们已经拥有的数据公开给其他同行使用。

如果你想更深入地了解Radicle Link到底是如何工作的，[你可以在这里找到更深入的规范文件](https://docs.radicle.xyz/understanding-radicle/how-it-works)。

## Radicle vs GitHub

虽然它们是为解决相同的问题而构建的，尽管方法不同，但使用 Radicle 的方式与使用 GitHub 的方式有些不同。

1. Radicle 是完全开源的，完全建立在开放协议之上，任何地方都没有中心化方面。
2. Radicle 依赖点对点通信，而不是像 GitHub 这样的客户端-服务器架构
3. 由于它依赖于对等通信，并且期望单个对等方下载存在的每个存储库/项目是不合理的，因此 Radicle默认情况下**不是**全局的。相反，对等点可以跟踪其他对等点和项目，并确定他们可以看到哪些内容并与之交互。
4. Radicle 是一个社区拥有的网络，而不是一家公司。网络的治理是通过拥有以太坊上的 $RAD 代币的 DAO 来实现的。

## 使用 Radicle

与 Git 不同，Radicle 项目没有单一的规范视图（即主/主分支）。相反，Radicle 项目有多个**上游**，即代码的不同分支，由代码维护者和贡献者维护。

要从贡献者那里获取和接收更改，您必须将它们作为**远程**添加到您的项目中。这会自动跟踪他们，您可以订阅他们在上游进行的新代码更新。

要真正开始使用 Radicle，主要方法是下载**Radicle Upstream**桌面客户端。它是一个开源客户端，充当您通往 Radicle 网络的网关。

> 不幸的是，*Radicle Upstream* 桌面客户端目前不支持 Windows。它仅适用于 Linux 和 macOS。

#### 创建项目

在 Radicle 上创建项目的基本工作流程与 GitHub 的工作方式非常相似：

1. 创建新存储库/导入现有存储库 , 您的项目将被分配一个唯一的**Radicle ID**
2. 与其他人共享您的 Radicle ID，让他们查看您的存储库
3. 使用典型命令进行更改、提交代码和推送更改`git push`（但您现在推送到 Radicle，而不是 GitHub）

#### 查看开源项目

要查看在 Radicle 上托管的开源项目，您需要访问该项目的 Radicle ID。

使用桌面客户端，您可以搜索 Radicle ID。这将在 Gossip 协议上发送信息请求消息，一旦您找到可以与您共享该信息的对等方，您将能够查看该项目。

#### 为开源项目做贡献

要为开源项目做出贡献，您可以像往常一样分叉一个项目并对其进行更改。

然后，如果原始存储库将您的分叉添加为存储库的远程，则原始存储库可以跟踪您的更改。

这将让他们自动跟踪您的更改并生成补丁，这些补丁可以自动从您的 fork 合并到原始存储库中。

## Radicle ❤️ 以太坊

Radicle 可选择与以太坊集成。用户可以选择加入集成，这将允许他们通过使用 ENS 为您的个人资料和组织拥有唯一的全局名称，通过将成员链接到以太坊钱包来在 Radicle 上拥有去中心化组织，并能够接受加密货币的贡献你的开源项目。

此外，Radicle 在以太坊上也有 $RAD 代币，这是 Radicle DAO 的治理代币。该代币在控制 Radicle 网络的 DAO 中提供投票权。

## 后续

希望这篇文章对 Radicle 是什么、它与 GitHub 有何不同、为什么它很重要以及它是如何工作的有一个概念性的概念。

在接下来的教程中，我们将对 Radicle 进行实际介绍，并完成在 Radicle 上实际设置存储库并与他人共享的过程。

> 注意：由于我们等待 Radicle 团队发布也可以在 Windows 上运行的跨平台 CLI 客户端，因此实际 Radicle 级别略有延迟。但是，如果您是 Linux 或 macOS 用户，我们强烈建议您下载 Radicle Upstream 客户端并尝试在 Radicle 上创建项目。

> 原文： [https://www.learnweb3.io/tracks/junior/radicle-theory](https://www.learnweb3.io/tracks/junior/radicle-theory)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
