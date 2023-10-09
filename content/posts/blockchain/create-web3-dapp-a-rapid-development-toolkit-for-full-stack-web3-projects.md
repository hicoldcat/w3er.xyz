---
title: create-web3-dapp：全栈 Web3 项目的快速开发工具包
description: null
author: 李留白
weight: 0
date: 2023-10-09T05:22:09.271Z
lastmod: 2023-10-09T05:27:43.423Z
tags: []
categories:
    - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231009132202.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231009132202.png)

## 什么是 Create-web3-dapp ？

Create-web3-dapp 是一个 npm 软件包，可在几分钟内简化 Web3 应用程序的创建。它基于 Next.js 构建，并与 RainbowKit、Alchemy SDK、ethers 以及您选择的 Hardhat 或 Foundry 无缝集成。

## create-web3-dapp 中包含什么？

在开始之前，您的环境中将包含以下内容：
1. Next.js
2. Wagmi Hooks
3. Ethers.js
4. Rainbowkit
5. Alchemy SDK

## 创建 web3 dapp 项目设置

1 **安装 CW3D NPX 包**

在终端中，导航到要在其中创建项目的文件夹并运行：

```shell
npx create-web3-dapp@latest
```

2 **插入项目名称和模板**

当您运行 `create-web3-dapp` 时，系统会提示您输入项目名称并在两个选项之间进行选择：“创建空的全栈 dapp”或“创建预构建模板”。此外，您可以选择要使用的区块链网络。

### 创建空的全栈 dapp

通过“创建空的全栈 dapp”选项，您可以在空白画布上从头开始构建 Web3 应用程序，并根据您的特定需求和要求进行定制。此选项为您的项目提供了完全的灵活性和控制。

### 创建预建模板

如果您希望抢先一步，“创建预构建模板”选项适合您。它提供了一个带有预定义智能合约的精选模板，例如已集成的 ERC721 或 ERC721A。这可以快速启动您的开发流程，节省您的时间和精力。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231009132400.png)

选择您的链：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231009132417.png)

创建什么类型的智能合约

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231009132430.png)

还有更多满足您的要求。最终您的项目结构如下： 类型：
当您使用 Create-web3-dapp 创建项目时，它会设置两个主要文件夹：“Frontend”和“Backend”。

**前端**：“Frontend”文件夹包含使用 Next.js 构建的应用程序的客户端代码。在这里，您将找到 Next.js 应用程序，其中包含创建强大的交互式用户界面所需的所有文件和配置。

    ![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231009132440.png)

**后端**：您可以在这里找到智能合约代码（通常用 Solidity 编写），以及部署脚本、测试文件和用于管理应用程序后端逻辑的其他必要组件。

   ![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231009132449.png)

前端和后端文件夹的分离可以实现干净且有组织的项目结构。

### 参考：

[create-web3-dapp 的文档](https://docs.alchemy.com/docs/create-web3-dapp)

## 总结

总体而言，Create-web3-dapp 简化了 Web3 开发过程，提供了全面的工具包和无缝的工作流程。它使开发人员能够专注于构建创新的去中心化应用程序，同时提供灵活性、模块化和易用性。

> 原文：https://web3fronted.hashnode.dev/create-web3-dapp-a-rapid-development-toolkit-for-full-stack-web3-projects