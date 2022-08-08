---
title: Web3系列教程之高级篇---9：检测看似合法但实际上是恶意的合约
description: null
author: 李留白
weight: 0
date: 2022-08-08T14:19:03.070Z
lastmod: 2022-08-08T14:39:06.891Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/R3RYera.png
---

在加密货币世界中，你会经常听到关于看起来合法的合约是如何成为大骗局背后的原因。黑客是如何从一个看起来合法的合约中执行恶意代码
的呢？


我们今天将学习一种方法。👀

## 会发生什么？

将有三个合约--`Attack.sol`、`Helper.sol`和`Good.sol`。用户将能够使用`Good.sol`输
入一个资格列表，它将进一步调用`Helper.sol`来跟踪所有符合条件的用户。

`Attack.sol`将被设计成可以操纵资格名单的方式，让我们看看如何👀

## 构建

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

首先，在`contracts`目录内创建一个新文件，名为`Good.sol`

```
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;
import "./Helper.sol";

contract Good {
    Helper helper;
    constructor(address _helper) payable {
        helper = Helper(_helper);
    }

    function isUserEligible() public view returns(bool) {
        return helper.isUserEligible(msg.sender);
    }

    function addUserToList() public  {
        helper.setUserEligible(msg.sender);
    }

    fallback() external {}
    
}
```

创建`Good.sol`后，在`contracts`目录内创建一个新文件，命名为`Helper.sol`

```
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract Helper {
    mapping(address => bool) userEligible;

    function isUserEligible(address user) public view returns(bool) {
        return userEligible[user];
    }

    function setUserEligible(address user) public {
        userEligible[user] = true;
    }

    fallback() external {}
}
```

我们将在`contracts`目录中创建的最后一个合约是`Attack.sol`。

```
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

contract Attack {
    address owner;
    mapping(address => bool) userEligible;

    constructor() {
        owner = msg.sender;
    }

    function isUserEligible(address user) public view returns(bool) {
        if(user == owner) {
            return true;
        }
        return false;
    }

    function setUserEligible(address user) public {
        userEligible[user] = true;
    }
    
    fallback() external {}
}
```

你会注意到，关于`Attack.sol`的事实是，它将生成与`Helper.sol`相同的ABI，尽管它里面有不同的代码。这是因为ABI只包含公共变量、函数和事件的函数定义。所以`Attack.sol`可以被类型化为`Helper.sol`。

现在，由于`Attack.sol`可以被打造成`Helper.sol`，恶意的所有者可以用`Attack.sol`的地址而不是`Helper.sol`来部署`Good.sol`，用户会认为他确实在使用`Helper.sol`来创建资格名单。

在我们的案例中，该骗局将发生如下情况。诈骗者首先会用`Attack.sol`的地址部署`Good.sol`。然后，当用户使用`addUserToList`函数进入资格列表时，由于`Helper.sol`和`Attack.sol`中该函数的代码相同，所以工作正常。

当用户试图用他的地址调用`isUserEligible`时，将观察到真正的颜色，因为现在这个函数总是返回`false`，因为它调用`Attack.sol`的`isUserEligible`函数，该函数总是返回`false`，除非是主人自己，这本来是不应该发生的。

让我们试着写一个测试用例，看看这个骗局是否真的有效，在`test`文件夹中创建一个新文件，名为`attack.js`。

```
const { expect } = require("chai");
const { BigNumber } = require("ethers");
const { ethers, waffle } = require("hardhat");

describe("Attack", function () {
  it("Should change the owner of the Good contract", async function () {
    // Deploy the Attack contract
    const attackContract = await ethers.getContractFactory("Attack");
    const _attackContract = await attackContract.deploy();
    await _attackContract.deployed();
    console.log("Attack Contract's Address", _attackContract.address);

    // Deploy the good contract
    const goodContract = await ethers.getContractFactory("Good");
    const _goodContract = await goodContract.deploy(_attackContract.address, {
      value: ethers.utils.parseEther("3"),
    });
    await _goodContract.deployed();
    console.log("Good Contract's Address:", _goodContract.address);

    const [_, addr1] = await ethers.getSigners();
    // Now lets add an address to the eligibility list
    let tx = await _goodContract.connect(addr1).addUserToList();
    await tx.wait();

    // check if the user is eligible
    const eligible = await _goodContract.connect(addr1).isUserEligible();
    expect(eligible).to.equal(false);
  });
});
```

要运行这个测试，打开你的终端，指向这个级别的目录根，执行这个命令。

```
npx hardhat test
```

如果你的所有测试都通过了，这就意味着骗局成功了，用户将永远不会被确定为合格者

## 预防

将外部合约的地址公开，同时让你的外部合约得到验证，以便所有用户可以查看代码

创建一个新的合约，而不是在构造函数中把一个地址类型化为一个合约。因此，与其做`Helper(_helper)`，将`_helper`地址类型化到一个可能是也可能不是`Helper`的合约中，不如使用`new Helper()`创建一个明确的新的helper合约实例。

案例：

```
contract Good {
    Helper public helper;
    constructor() {
        helper = new Helper();
    }
}
```

看，还有很多要学的东西吧？🤯

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
