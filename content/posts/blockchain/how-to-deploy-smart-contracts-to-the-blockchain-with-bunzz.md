---
title: 如何使用 Bunzz 将智能合约部署到区块链
description: null
author: 李留白
weight: 0
date: 2023-04-16T12:58:04.774Z
lastmod: 2023-04-16T13:09:15.870Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230416205814.png
---

要编写和部署智能合约，需要了解 Solidity 和 Rust 等编程语言。但是，如果您不了解这些语言，或者您不想经历编写智能合约的麻烦，我可以为您提供解决方案。

在本教程中，我们将学习如何使用[Bunzz](https://dev.tourl/)已经创建的智能合约，并在 10 分钟内将它们部署到区块链。

**先决条件**
要跟进本教程，您应该：

- 区块链基础知识
- [MetaMask](https://metamask.io/)钱包已安装在您的浏览器中
- 在您的 MetaMask 钱包上添加并切换到[Polygon](https://wiki.polygon.technology/docs/develop/metamask/config-polygon-on-metamask/)网络。

## 什么是 Bunzz？

Bunzz是一个为用户提供智能合约服务的软件平台。他们为你执行编写这些智能合约的任务，你所要做的就是选择你想要的智能合约并将其部署到区块链上。

## 使用 Bunzz 选择和部署智能合约的步骤

1.**注册**[Bunzz](https://www.bunzz.dev/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230416205918.png)

2.单击**连接钱包**将您的 MetaMask 钱包连接到 Bunzz。在您的 MetaMask 钱包上切换到 Polygon 网络。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230416205928.png)

3.单击**创建 Dapp**。给你的 Dapp 取一个你选择的名字。然后单击下一步。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230416205937.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230416205943.png)

4.选择**Polygon Mumbai Testnet**作为网络名称。然后单击 **“下一步”**。
注意：因为这只是我们将部署到 Polygon Mumbai Testnet 的教程。但是当你构建一个实际的 Dapp 时，将它部署到 Polygon Mainnet。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230416205950.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230416205957.png)

5.转到**准备好的模板**。您将看到等待部署的已编写模板/智能合约的列表。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230416210004.png)

了解智能合约中所有可用功能的所有信息并查看代码。单击下拉按钮

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230416210011.png)

6.我们将使用名为**Simple Token**的第一个模板。单击**使用此模板**

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230416210018.png)

7.给你的令牌一个名字（任何选择的名字）和一个独特的符号。例如，如果代币名称是“JRocks Hospital Token”，那么符号可以是“JHT”。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230416210034.png)

8.在部署之前，您的钱包中需要有足够的代币，否则您的合约将不会被部署到区块链上。转到[Polygon 水龙头](https://faucet.polygon.technology/)并获得免费的测试网以用于部署。

9.点击**部署**。您的 MetaMask 钱包将在您的屏幕上弹出，供您确认交易。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230416210040.png)

10.单击**返回仪表板**

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230416210046.png)

11.复制你的**合约 ABI**和**合约地址。**

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230416210052.png)

12.要验证您的交易，请将您的合约地址粘贴到[PolygonScan 测试网上](https://mumbai.polygonscan.com/address/0x06e78A9205151802DC88FE095F81F15dB199eD54)

恭喜你🥳！您只是部署了一个智能合约而没有自己编写。

**接下来是什么？**
在我们的下一个教程中，我们将学习如何使用 ethers.js 与我们的智能合约进行交互并构建真实的应用程序。确保您保留刚刚部署的智能合约中的 ABI 和合约地址，因为您将需要它。

> 原文：https://manyo-rock.hashnode.dev/how-to-deploy-smart-contracts-to-the-blockchain-with-bunzz

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)