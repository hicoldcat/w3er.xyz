---
title: Web3系列教程之高级篇---4：智能合约中访问private数据
description: null
author: 李留白
weight: 0
date: 2022-08-03T14:44:24.917Z
lastmod: 2022-08-03T14:45:24.091Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/R3RYera.png
---

当我们开始编写智能合约并遇到诸如`public`,`private`等可见性修饰符时，我们可能会认为如果您希望某些变量的值可以被公众读取，您需要声明它`public`，而`private`除了智能合约本身外，任何人都不能读取。

但是，以太坊是一个公共区块链。那么`private`数据到底意味着什么呢？

在这一层次中，我们将看到你如何能够真正从任何智能合约中读取`private`变量的值，同时也展现了`private`实际上代表什么--这绝对不是私有数据!

走吧🚀 

##  `private` 意味着什么?

函数（和变量）可见性修改器只影响函数的可见性--并不阻止对其值的访问。我们知道，`public`函数是那些既可以由用户和智能合约在外部调用，也可以由智能合约本身调用的函数。

同样，`internal`函数是指只能由智能合约本身调用的函数，外部用户和智能合约不能调用这些函数。`external`函数则相反，它们只能由外部用户和智能合约调用，但不能由拥有该函数的智能合约本身调用。

`private`，类似的，只是影响到谁可以调用该函数。`private`的和`internal`的行为基本相似，只是`internal`函数也可以被继承合约调用，而`private`函数则不行。

因此，举例来说，如果`Contract A`有一个标记为`internal`的函数`f()`，那么另一个继承`Contract A`的`Contract B`，如

```
contract B is A {
  ...
}
```

仍然可以调用`f()`。

然而，如果`Contract A`有一个标记为`private`的函数`g()`，`Contract B`不能调用`g()`，即使它继承了`A`。

变量也是如此，因为变量基本上只是函数。`private`变量只能由智能合约本身访问和修改，甚至继承合约也不能。然而，这并不意味着外部各方不能读取该值。

## 构建

我们将建立一个简单的合约，以及一个Hardhat测试，来证明这一点。我们的合约将尝试把数据存储在`private`变量中，希望没有人能够读取它的值。

该合同将在其结构体中接受`password`和`username`，并将它们存储在私有变量中。

用户将以某种方式能够访问这些私有变量。

## 概念

为了理解这一点，回顾一下以太坊存储和执行层面，Solidity中的变量被存储在32字节（256位）的存储槽中，而数据是根据这些变量的声明顺序依次存储在这些存储槽中。

存储也被优化了，如果一堆变量可以放在一个槽里，它们就会被放在同一个槽里。这被称为变量打包，我们将在后面进一步了解这个问题。

- 要设置一个Hardhat项目，请打开终端，在一个新的文件夹中执行这些命令

```bash
npm init --yes
npm install --save-dev hardhat
```

- 如果你使用的是Windows系统，请做这个额外的步骤，同时安装这些库 :)

```
npm install --save-dev @nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers
```

- 在你安装Hardhat的同一目录下运行。

```bash
npx hardhat
```

- 选择`Create a basic sample project`
- 对已指定的`Hardhat Project root`按回车键
- 如果你想添加一个`.gitignore`，请按回车键。
- 按回车键`Do you want to install this sample project's dependencies with npm (@nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers)?`

现在你有一个准备好的hardhat项目了!

让我们先在`contracts`文件夹内创建一个`Login.sol`文件。在你的文件中添加以下几行代码

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract Login {

    // Private variables
    // Each bytes32 variable would occupy one slot
    // because bytes32 variable has 256 bits(32*8)
    // which is the size of one slot

    // Slot 0
    bytes32 private username;
    // Slot 1
    bytes32 private password;

    constructor(bytes32  _username, bytes32  _password) {
        username = _username;
        password = _password;
    }
}
```

由于这两个声明的变量都是`bytes32`的变量，我们知道每个变量正好占用一个存储槽。由于顺序很重要，我们知道`username`将占用`Slot 0`，`password`将占用`Slot 1`。

因此，与其试图通过调用合约来读取这些变量值，这是不可能的，我们可以直接访问存储槽。由于以太坊是一个公共区块链，所有节点都可以访问所有状态。

让我们写一个Hardhat测试来演示这个功能。

在`test`文件夹中创建一个新文件`attack.js`，并添加以下几行代码

```javascript
const { ethers } = require("hardhat");
const { expect } = require("chai");

describe("Attack", function () {
  it("Should be able to read the private variables password and username", async function () {
    // Deploy the login contract
    const loginFactory = await ethers.getContractFactory("Login");

    // To save space, we would convert the string to bytes32 array
    const usernameBytes = ethers.utils.formatBytes32String("test");
    const passwordBytes = ethers.utils.formatBytes32String("password");

    const loginContract = await loginFactory.deploy(
      usernameBytes,
      passwordBytes
    );
    await loginContract.deployed();

    // Get the storage at storage slot 0,1
    const slot0Bytes = await ethers.provider.getStorageAt(
      loginContract.address,
      0
    );
    const slot1Bytes = await ethers.provider.getStorageAt(
      loginContract.address,
      1
    );

    // We are able to extract the values of the private variables
    expect(ethers.utils.parseBytes32String(slot0Bytes)).to.equal("test");
    expect(ethers.utils.parseBytes32String(slot1Bytes)).to.equal("password");
  });
});
```

在这个测试中，我们首先创建`usernameBytes`和`passwordBytes`，它们是短字符串的`bytes32`版本，作为我们的用户名和密码。然后我们用这些值部署`Login`合约。

合约部署后，我们使用`provider.getStorageAt`直接读取`loginContract.address`中0号和1号槽的存储槽值，并从中提取字节值。

然后，我们可以比较检索到的值--`slot0Bytes`与`usernameBytes`，`slot1Bytes`与`passwordBytes`，以确保它们事实上是相等的。

如果测试通过，意味着我们能够成功地直接读取私有变量的值，而根本不需要调用合同上的函数。

最后，让我们运行这个测试，看看它是否有效。在你的终端键入。

```bash
npx hardhat test
```

测试应该通过了。哇，我们真的可以读取密码了

## 预防

**永远**不要在公共区块链上存储私人信息。没有其他办法。

>原文：[https://www.learnweb3.io/tracks/senior/re-entrancy](https://www.learnweb3.io/tracks/senior/re-entrancy)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)

