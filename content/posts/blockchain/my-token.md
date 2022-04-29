---
title: 区块链入门：在本地网络开发自己的加密货币(Token)-傻瓜币(FoolCoin)
description: null
author: 李留白
weight: 1
date: 2022-04-29T07:28:45.480Z
lastmod: 2022-04-29T10:53:19.227Z
draft: true
tags: []
categories:
  - 区块链
featuredImage: null
---

> 注意：该项目仅供学习区块链知识，不作为任何投资建议。市场有风险，投资需谨慎。

本文项目代码：

https://github.com/hicoldcat/eth-solidity-token-example

原文地址：

https://hicoldcat.com/posts/blockchain/my-token/

## 1、初始化项目
> [Hardhat](https://hardhat.org/)是一个编译、部署、测试和调试以太坊应用的开发环境。它可以帮助开发人员管理和自动化构建智能合约和DApps过程中固有的重复性任务，并围绕这一工作流程轻松引入更多功能。这意味着hardhat最核心的地方是编译、运行和测试智能合约。

创建npm项目`eth-solidity-token-example`，进入项目文件夹，安装hardhat

```bash
npm init

npm install --save-dev hardhat
```

创建Hardhat项目

```bash
npx hardhat
```

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220429160229.png)

## 2、创建Token文件
> [Solidity](https://soliditylang.org/)是一门面向合约的、为实现智能合约而创建的高级编程语言。

contracts 目录下有`Greeter.sol`文件，这是hardhat提供的一个demo文件，实现了简单的打招呼功能。`.sol`文件是Solidity文件的后缀。Solidity 是在Ethereum 上开发智能合约的一门编程语言，具体语法可以参考上面的官方文档。

在contracts目录下，创建我们自己的代币合约文件`Fool.sol`(傻瓜币)，代码如下：

```sol
//SPDX-License-Identifier: Unlicense
pragma solidity >=0.8.0 <0.9.0;

contract Fool {}

```


## 3、




## 编译合约

在项目根目录下运行如下命令：

```bash
 npx hardhat compile
```
hardhat会查找项目下所有的只能合约，并根据`hardhat.config.js`配置文件生成编译完成之后的`artifacts`文件目录。
