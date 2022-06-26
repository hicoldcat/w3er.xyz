---
title: Web3系列教程之新手篇---Level 4：加密钱包
description: null
author: 李留白
weight: 0
date: 2022-06-19T16:27:47.998Z
lastmod: 2022-06-26T10:25:06.425Z
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

在本模块中，您将了解什么是加密钱包以及如何下载。🤔

## 介绍

要完全理解加密钱包，我们必须了解一些关于区块链的概念，这将有助于我们理解钱包如何帮助我们。让我们开始吧。

## 什么是地址？🤨

地址是使用加密技术生成的一串文本，用于表示您在区块链上的帐户。这个地址可以与其他人公开共享，这样做是完全安全的。您可以从您的钱包地址发送和接收资金。基本上，地址是您在区块链上的唯一标识符，代表您的“帐户”。以太坊地址的一个例子是：0x01573Df433484fCBe6325a0c6E051Dc62Ab107D1.

##什么是私钥？🔐

私钥是地址的对应物。每个地址都有一个关联的私钥。顾名思义，这意味着保密，不与任何人共享。

你可以把它想象成一个密码，一个非常强大的密码，它包含一堆字母和数字，可以让你证明对你的地址的所有权。任何拥有私钥的人都可以从您的地址进行交易，即从您的地址向他们的地址汇款。

私钥看起来像这样：E9873D79C6D87DC0FB6A5778633389F4453213303DA61F20BD67FC233AA33262

如果您将您的地址视为您帐户的用户名，那么私钥就是它的密码。因此，分享您的地址是可以的，但永远不要分享您的私钥，否则有人可能会窃取您的资金——然后就无能为力了。

*注意：由于区块链是去中心化的，因此没有“忘记密码”选项。如果您丢失了您的私钥，您将无法访问您的帐户。同样，如果有人窃取了您的私钥并窃取了您的资金，您将无能为力。保持此私钥的安全非常重要。*

对于开发人员，我们经常使用私钥作为代码库的一部分来执行某些交易，例如将我们自己的智能合约部署到以太坊网络。当您仍在学习时，我们强烈建议您完全使用单独的帐户进行开发，而不是用于存储任何类型的资金。不幸的是，初学者开发人员经常使用他们拥有资金的同一个帐户，并且不小心公开共享他们的代码库 - 黑客可以在代码库中看到您的私钥并最终窃取资金。请把它当作一个谨慎的故事。

## 什么是助记词？👮‍♀️

助记词就像一个主密码 - 密码的密码！

想想密码管理器，比如 Lastpass 或 1Password。这些应用程序在其中安全地存储您用于其他应用程序的用户名和密码，并且它们本身也有密码。因此，如果有人破解了您的密码管理器，他们还可以访问存储在其中的所有帐户。

加密钱包有点像密码管理器，您可以在其中管理多个区块链帐户。如果私钥是单个帐户的密码，则种子短语有点像该钱包的主密码。

当你创建一个新的加密钱包时，你会看到一个助记词，你应该绝对安全地存储和备份。您从该钱包中生成的任何新帐户都将链接到助记词。那一个助记词将始终生成相同的帐户，每个帐户具有相同的私钥和地址。

因此，例如，如果您创建了一个钱包，然后在其中创建了 5 个帐户，则您的助记词管理所有 5 个帐户。如果您想切换到新钱包，您可以单独导入 5 个钱包 - 通过使用它们各自的私钥 -或者只是使用助记词导入，它会重新生成相同的 5 个帐户。

种子短语的一个例子是：`dove lumber quote board young robust kit invite plastic regular skull history`

## 那么什么是加密钱包呢？😛

加密钱包是您账户的管理者，主要是他们的私钥。它们还允许您与去中心化应用程序交互，并允许通过钱包连接到 dApp，充当构建在区块链上的所有应用程序的单点登录。

在 LearnWeb3 上，您也可以进入仪表板并连接您的加密钱包（在您设置之后），这将让我们知道您的地址是什么，这样我们就可以在您从我们的轨道毕业时向您发送一些生病的 NFT！

## 设置钱包🎉

对于以太坊，有许多可用的钱包选项。Metamask 或 Coinbase 钱包是最容易上手且对开发人员最友好的。

两者都是以太坊加密钱包，可以作为浏览器扩展或移动应用程序安装。您可以在下面找到下载链接。我们建议下载其中任何一个并进行设置，然后再继续进行跟踪。

- [下载MetaMask](https://metamask.io/download.html)
- [下载 Coinbase 钱包](https://www.coinbase.com/wallet)

其他替代品包括 Trust Wallet、Atomic Wallet、Rainbow Wallet、Frame.sh 等。

- [Trust Wallet](https://trustwallet.com/)
- [Atomic Wallet](https://atomicwallet.io/)
- [Rainbow Wallet](https://rainbow.me/)
- [Frame.sh](https://frame.sh/)


![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/my.png)