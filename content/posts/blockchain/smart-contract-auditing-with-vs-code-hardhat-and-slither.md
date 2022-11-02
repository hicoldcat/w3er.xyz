---
title: 使用 VS Code、Hardhat 和 Slither 进行智能合约审计
description: null
author: 李留白
weight: 0
date: 2022-11-02T10:47:16.529Z
lastmod: 2022-11-02T11:05:20.508Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211021905513.jpeg
---

## 介绍

直到一两个星期前，我还了解智能合约安全和审计，但没有接触到当前真实和正确的流程，即在自动化部署和测试过程中使用威胁分析器从组装层面进行合约审计。。

可以在[此处](https://rabmcmenemy.medium.com/my-smart-contract-bot-encounter-2ae9381fa2f5)找到所发生事件的概要。

我深刻地认识到，我们必须使用一些工具，在发布到外面之前充分测试我们的合约，否则它们会被机器人利用，这些机器人的行动速度比你开始思考的速度快得多。

一个Python脚本可能会检查你的智能合约的弱点，进行任何已知的攻击，并通过将你的虚拟*KNOX*堡垒中的NFT或货币保护转移到一个匿名的钱包中，在一分钟内将其价值完全耗尽到零。

当这种情况发生时，你无力阻止，而你肚子里的坑会扩大成一个深不可测的黑洞。

我学会了如何在我用来部署合约的环境中审计合约，我希望这个教程对人们有帮助，这样他们就不会成为我所遭受的同样攻击的受害者，废话不多说，我向你介绍一种从VSCode中创建、审计和部署合约的方法

## 前提条件

### Node

Node 对于运行Hardhat和本地链是必不可少的，因此它是本教程的要求，它可以从[这里](https://nodejs.org/en/download/)安装。。

### Python

static solidity analyzer需要Python和pip来执行，在Windows、Mac和Linux上完成这个任务的最好方法是通过GUI安装程序，因为它将为你修改PATH环境变量，使你在CLI执行过程中避免任何全局关键字的问题。它可以在这里找到。

### Hardhat

Hardhat 是一个出色的本地链开发环境，可让您在将合约发布到主网之前彻底测试、部署和验证合约。

它是任何智能合约开发人员的武器库不可或缺的环境，可以从[这里](https://hardhat.org/hardhat-runner/docs/getting-started)安装。

### Slither

一旦你有了合约、部署脚本和hardhat环境，你现在就可以为静态分析器安装Slither程序集库了。。

以管理员或根用户身份打开一个新的命令提示符或终端窗口，输入以下内容

> pip install slither-analyzer

### Visual Studio Code

一旦slither和hardhat都设置好了，你现在就可以准备安装Visual Studio代码了，可以在[这里](https://code.visualstudio.com/download)找到。

安装后，单击左侧菜单中的扩展图标并搜索slither。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211021857569.png)

安装扩展程序后，重新启动 Visual Studio 代码并重新打开您的hardhat项目，您现在应该会在左侧任务栏中看到slither图标。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211021857044.png)

如果扩展未加载，请检查输出控制台，这很可能是您的环境变量的路径问题，您可以通过键入 slither 来测试它。在任何合约目录的终端窗口中，如果它抛出命令未找到错误，您将需要设置环境变量。

## 编译你的合约

### 合约目录

如果将所有合约保存在一个文件夹中，维护起来会更容易，并记住在删除、创建或更改任何合约后，您需要使用这些命令清理本地类型链工件。

```

npx hardhat clean

npx hardhat compile

```

### 分析/审计

要运行审计，只需导航到slither图标并单击“播放”按钮，这将扫描您当前目录中所有已编译的合约以查找任何漏洞。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211021858099.png)

### 研究修复

通过使用 google 查找被slither标记的问题并研究和测试可能的修复，优先修复最严重的错误。

> 请记住，每次更改solidity 文件时，您都必须在扫描之前再次清理和重新编译。

这个阶段可能非常复杂，为了充分理解、测试和建模控制流，您需要具备良好的开发知识水平，以便真正测试逻辑并确保其按预期工作。

实现这一点的最佳方法是通过自动化测试脚本，可以在[此处](https://ethereum.org/en/developers/docs/smart-contracts/testing/)找到一篇关于此的精彩文章。

### 部署和验证

[下面概述的是带有构造函数参数的721A](https://www.erc721a.org/)合约的示例部署脚本，将此文件放入hardhat项目的脚本目录中，可以对其进行修改以启动或部署任何类型的合约，而不仅仅是721A。

https://gist.github.com/Arkay92/f0ec3ed3b866607802c3f2b69c83c135.js

在我的示例中，testnet 链接到以太坊的 Goerli 网络，但它可以被命名并定向到您喜欢的任何网络。最后一个标志对应于您的网络名称，该名称在hardhat项目的 config.ts 文件中设置。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211021858465.png)

为了验证，请务必在此配置文件中设置您的 etherscan / polygonscan api 密钥。

配置完成后，确保将部署脚本放入脚本目录并运行以下命令。

> npx hardhat run scripts/myDeployScript.ts — 网络测试网

就是这样，您现在已经学会了如何在一个地方部署、测试、审计和验证合约！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
