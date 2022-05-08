---
title: 别人有的你也要有，发行自己的第一款NFT(Non-Fungible Token)数字藏品
description: null
author: 李留白
weight: 1
date: 2022-05-08T06:09:19.920Z
lastmod: 2022-05-08T06:55:02.721Z
tags: []
categories:
  - 区块链
featuredImage: https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/pexels-steve-johnson-1812960.jpg
---

> 注意：该项目仅供学习区块链知识，不作为任何投资建议。市场有风险，投资需谨慎。

> 传送门：[区块链入门：在本地网络开发自己的加密数字货币(Token)-傻瓜币(FoolCoin)](https://hicoldcat.com/posts/blockchain/my-token/)

本文项目代码：

[https://github.com/hicoldcat/nft-web3-example](https://github.com/hicoldcat/nft-web3-example)

原文地址：

[https://hicoldcat.com/posts/blockchain/my-nft/](https://hicoldcat.com/posts/blockchain/my-nft/)

## NFT概念
NFT（Non-Fungible Token），官方学名非同质化代币，区别于同质化代币，表示的是具有不可分割性的数字资产。通俗上来理解，10美元可以拆分为10份，每一份都是1美元，每1份（1美元）都是等价的，这就是同质化代币。而类比于1副画价值10美元，是不可以拆分成10份，每份1美元的。所以NFT就是一个不可分割的数字资产，可以用来作为价值交换的代币，但是只能一个独立的整体存在。

## 初始化项目
> [Hardhat](https://hardhat.org/)是一个编译、部署、测试和调试以太坊应用的开发环境。它可以帮助开发人员管理和自动化构建智能合约和DApps过程中固有的重复性任务，并围绕这一工作流程轻松引入更多功能。这意味着hardhat最核心的地方是编译、运行和测试智能合约。

创建一个文件夹如`nft-web3-example`，并使用`npm init`初始化项目。

添加hardhat依赖,

```bash
npm install --save-dev hardhat
```

使用hardhat初始化项目，选择`Create a basic sample project`, 生成项目结构文件模板

```bash
npx hardhat
```

![init](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220508144827.png)

## 开发合约
> [Solidity](https://soliditylang.org/)是一门面向合约的、为实现智能合约而创建的高级编程语言。

> [openzeppelin](https://docs.openzeppelin.com/contracts/4.x/)是一个用于安全智能合约开发的库，内置了很多常用合约的标准实现。

开发之前，先安装openzeppelin合约库，里面内置了许多智能合约的实现和一些常用的工具代码。

```bash
npm install @openzeppelin/contracts
```



## 编译合约

## 部署合约


