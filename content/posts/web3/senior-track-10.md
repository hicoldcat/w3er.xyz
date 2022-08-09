---
title: Web3系列教程之高级篇---10：构建可随时间升级的智能合约
description: null
author: 李留白
weight: 0
date: 2022-08-09T14:07:15.116Z
lastmod: 2022-08-09T15:11:15.597Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/R3RYera.png
---

我们知道，以太坊上的智能合约是不能升级的，因为代码是不可变的，一旦部署就不能更改。但第一次写出完美的代码是很难的，作为人类，我们都很容易犯错。有时，即使是经过审计的合同，也会发现有错误，导致其损失数百万。

在本文中，我们将学习一些可以在Solidity中使用的设计模式，以编写可升级的智能合约。

## 它是如何工作的？

为了升级我们的合同，我们使用了一种叫做`Proxy Pattern`的东西。`Proxy`这个词对你来说可能听起来很熟悉，因为它不是一个web3的原生词。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220809221348.png)

从本质上讲，这种模式的工作原理是，一个合同被分成两个合同--`Proxy Contract`和`Implementation Contract`。

`Proxy Contract`负责管理合同的状态，涉及持久性存储，而`Implementation Contract`负责执行逻辑，不存储任何持久性状态。用户调用`Proxy Contract`，`Proxy Contract`进一步对实现合同进行`delegatecall`，这样它就可以实现逻辑。记得我们在以前的文章中学习过`delegatecall`👀。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220809221733.png)

当`Implementation Contract`可以被替换时，这种模式就变得有趣了，这意味着执行的逻辑可以被另一个版本的`Implementation Contract`所替换，而不影响存储在代理中的合同的状态。

主要有三种方式，我们可以替换/升级`Implementation Contract`:

1. Diamond Implementation
2. Transparent Implementation
3. UUPS Implementation

然而，我们将只关注Transparent和UUPS，因为它们是最常用的。

要升级`Implementation Contract`，你必须使用一些方法，比如`upgradeTo(address)`，这基本上会把`Implementation Contract`的地址从旧的变成新的。

但重要的部分在于我们应该把`upgradeTo(address)`函数放在哪里，我们有两个选择，要么把它放在`Proxy Contract`中，这基本上是`Transparent Proxy Pattern`的工作方式，要么把它放在`Implementation Contract`中，这就是UUPS合同的工作方式。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220809222024.png)

关于这个`Proxy Pattern`，另一个需要注意的是，`Implementation Contract`的构造函数永远不会被执行。

当部署一个新的智能合约时，构造器内的代码不是合约运行时字节码的一部分，因为它只在部署阶段需要，并且只运行一次。现在，因为当`Implementation Contract `被部署时，它最初没有连接到`Proxy Contract`，因此，任何在构造器中发生的状态变化现在在`Proxy Contract`中不存在，而`Proxy Contract`是用来维护整体状态的。

因为`Proxy Contracts`不知道构造函数的存在。因此，我们不使用构造函数，而是使用一个叫做`initializer`函数的东西，一旦`Implementation Contract`与之相连，它就会被`Proxy Contract`调用。这个函数所做的正是构造函数应该做的事情，但是现在它被包含在运行时字节码中，因为它的行为就像一个普通的函数，并且可以被`Proxy Contract`调用。

使用OpenZeppelin合约，你可以使用他们的`Initialize.sol`合约，确保你的`initialize`函数只被执行一次，就像一个构造函数一样

```
// contracts/MyContract.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

contract MyContract is Initializable {
    function initialize(
        address arg1,
        uint256 arg2,
        bytes memory arg3
    ) public payable initializer {
        // "constructor" code...
    }
}
```

上面给出的代码来自[Openzeppelin的文档](https://docs.openzeppelin.com/upgrades-plugins/1.x/proxies#the-constructor-caveat)，它提供了一个例子，说明`initializer`修改器如何确保初始化函数只能被调用一次。这个修改器来自`Initializable Contract`

我们现在将详细研究代理模式🚀 👀

## 透明代理模式

透明代理模式是一种简单的方法来分离`Proxy`合同和`Implementation`合同之间的责任。在这种情况下，`upgradeTo`函数是`Proxy`合同的一部分，而`Implementation`可以通过在Proxy上调用`upgradeTo`来升级，从而改变未来函数调用的委托位置。

不过也有一些注意事项。可能有这样一种情况：`Proxy Contract`和`Implementation Contract`有一个名称和参数相同的函数。想象一下，如果`Proxy Contract`有一个`owner()`函数，`Implementation Contract`也有。在透明代理合约中，这个问题由`Proxy Contract`来处理，`Proxy Contract`根据`msg.sender`全局变量来决定用户的调用是在`Proxy Contract`本身还是在`Implementation Contract`中执行。

所以如果`msg.sender`是代理的管理员，那么代理将不会委托调用，如果它理解的话，将尝试执行调用。如果它不是管理员地址，代理将把调用委托给`Implementation Contract`，即使它与代理的某个函数相匹配。

## 透明代理模式的问题

我们知道，`owner`的地址必须存储在存储器中，而使用存储器是与智能合约互动的最低效和最昂贵的步骤之一，每次用户调用代理时，代理会检查用户是否是管理员，这给大多数发生的交易增加了不必要的气体成本。

## UUPS代理模式

UUPS代理模式是另一种在`Proxy`合同和`Implementation`合同之间分离责任的方式。在这种情况下，`upgradeTo`函数也是`Implementation`契约的一部分，并且通过代理被所有者使用`delegatecall `。

在UUPS中，不管是管理员还是用户，所有的调用都被发送到`Implementation Contract`中。这样做的好处是，每次调用时，我们不必访问存储空间来检查开始调用的用户是否是管理员，这提高了效率和成本。另外，因为是`Implementation Contract`，你可以根据你的需要定制功能，在每一个新的`Implementation`中加入诸如`Timelock`、`Access Control`等，这在`Transparent Proxy Pattern`中是做不到的。

## UUPS代理模式的问题

现在的问题是，因为`upgradeTo`函数存在于`Implementation contract`的一侧，开发者必须担心这个函数的实现，这有时可能很复杂，而且因为增加了更多的代码，增加了攻击的可能性。这个函数也需要在所有被升级的`Implementation contract`的版本中出现，这就引入了一个风险，如果开发者忘记添加这个函数，那么合同就不能再被升级了。

## 构建

让我们开发一个例子，你可以体验如何建立一个可升级的合同。在这个例子中，我们将使用UUPS可升级模式，当然你也可以用透明代理模式来开发一个。

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

- 我们将使用openzeppelin的库，它支持可升级的合同。要安装这些库，在同一个文件夹中执行以下命令。

```
npm i @openzeppelin/contracts-upgradeable @openzeppelin/hardhat-upgrades @nomiclabs/hardhat-etherscan --save-dev
```

用以下代码替换你的`hardhat.config.js`中的代码，以便能够使用这些库。

```
require("@nomiclabs/hardhat-ethers");
require("@openzeppelin/hardhat-upgrades");
require("@nomiclabs/hardhat-etherscan");

module.exports = {
  solidity: "0.8.4",
};
```

首先，在`contracts`目录下创建一个新文件，名为`LW3NFT.sol`，并在其中添加以下几行代码

```
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts-upgradeable/token/ERC721/ERC721Upgradeable.sol";
import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";


contract LW3NFT is Initializable, ERC721Upgradeable, UUPSUpgradeable, OwnableUpgradeable   {
    // Note how we created an initialize function and then added the
    // initializer modifier which ensure that the
    // initialize function is only called once
    function initialize() public initializer  {
        // Note how instead of using the ERC721() constructor, we have to manually initialize it
        // Same goes for the Ownable contract where we have to manually initialize it
        __ERC721_init("LW3NFT", "LW3NFT");
        __Ownable_init();
        _mint(msg.sender, 1);
    }
    function _authorizeUpgrade(address newImplementation) internal override onlyOwner {

    }
}
```

让我们试着更详细地了解这份合同中所发生的事情。

如果你看一下`LW3NFT`导入的所有合约，你就会明白为什么它们很重要。首先是Openzeppelin的`Initializable`契约，它为我们提供了`initializer`修改器，确保`initializer`函数只被调用一次。`initializer`函数是需要的，因为我们不能在`Implementation Contract`中拥有一个构造器，在这种情况下，`Implementation Contract`就是`LW3NFT`的契约

它导入了`ERC721Upgradeable`和`OwnableUpgradeable`，因为原始的`ERC721`和`Ownable`合约有一个构造函数，不能用于代理合约。

最后，我们有`UUPSUpgradeable Contract `，它为我们提供了`upgradeTo(address)`函数，在`UUPS`代理模式的情况下，它必须被放在`Implementation Contract`上。

在合同的声明之后，我们有一个带有`initialize`修饰的`initialize`函数，我们从`Initializable`合同中得到。`initialize`修饰符确保`initialize`函数只能被调用一次。还请注意，我们初始化`ERC721`和`Ownable`合约的新方式。这是初始化可升级合约的标准方式，你可以在这里看一下[这个函数](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/token/ERC721/ERC721Upgradeable.sol#L45)。之后，我们就用通常的mint函数来造币。

```
function initialize() public initializer  {
    __ERC721_init("LW3NFT", "LW3NFT");
    __Ownable_init();
    _mint(msg.sender, 1);
}
```

另一个有趣的功能是`_authorizeUpgrade`，我们在正常的`ERC721`合约中没有看到这个功能，当开发者从Openzeppelin导入`UUPSUpgradeable Contract`时，需要实现这个功能，它可以在[这里](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/proxy/utils/UUPSUpgradeable.sol#L100)找到。现在，为什么这个函数必须被覆盖，这很有趣，因为它让我们有能力在谁能真正升级给定的合同上添加授权，它可以根据要求改变，但在我们的案例中，我们只是添加了一个`onlyOwner`修改器。

```
function _authorizeUpgrade(address newImplementation) internal override onlyOwner {

}
```

现在让我们在`contracts`目录下创建另一个新文件，名为`LW3NFT2.sol`，这将是`LW3NFT.sol`的升级版。

```
//SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "./LW3NFT.sol";

contract LW3NFT2 is LW3NFT {

    function test() pure public returns(string memory) {
        return "upgraded";
    }
}
```

这个智能合约要简单得多，因为它只是继承了`LW3NFT`合约，然后添加了一个名为`test`的新函数，它只是返回一个`upgraded`的字符串。

很容易吧？🤯

Wow 🙌 ，好了，我们已经写完了`Implementation Contract`，现在我们还需要写` Proxy Contract`吗？

好消息是，不，我们不需要写`Proxy Contract`，因为当我们使用`Openzeppelin`的库来部署`Implementation Contract`时，Openzeppelin会自动部署和连接一个`Proxy Contract`。

因此，让我们尝试这样做，在你的`test`目录中创建一个新的文件，命名为`proxy-test.js`，让我们用代码尝试一下

```
const { expect } = require("chai");
const { ethers } = require("hardhat");
const hre = require("hardhat");

describe("ERC721 Upgradeable", function () {
  it("Should deploy an upgradeable ERC721 Contract", async function () {
    const LW3NFT = await ethers.getContractFactory("LW3NFT");
    const LW3NFT2 = await ethers.getContractFactory("LW3NFT2");

    let proxyContract = await hre.upgrades.deployProxy(LW3NFT, {
      kind: "uups",
    });
    const [owner] = await ethers.getSigners();
    const ownerOfToken1 = await proxyContract.ownerOf(1);

    expect(ownerOfToken1).to.equal(owner.address);

    proxyContract = await hre.upgrades.upgradeProxy(proxyContract, LW3NFT2);
    expect(await proxyContract.test()).to.equal("upgraded");
  });
});
```

让我们看看这里发生了什么，我们首先使用`getContractFactory`函数获得`LW3NFT`和`LW3NFT`实例，这是我们到现在为止一直在教的所有文章都通用的。在这之后，最重要的一行出现了。

```
let proxyContract = await hre.upgrades.deployProxy(LW3NFT, {
  kind: "uups",
});
```

这个函数来自于你安装的`@openzeppelin/hardhat-upgrades`库，它基本上是使用upgrades类来调用`deployProxy`函数，并指定种类为`uups`。当该函数被调用时，它将部署`Proxy Contract`、`LW3NFT Contract`并将它们连接起来。关于这个的更多信息可以在[这里](https://docs.openzeppelin.com/upgrades-plugins/1.x/api-hardhat-upgrades)找到。

请注意，`initialize`函数可以用任何其他名字，只是`deployProxy`默认调用`initialize`函数，但你可以通过改变默认值来修改它 😇

部署后，我们通过调用代币ID 1的`ownerOf`函数来测试合约是否真的被部署，并检查NFT是否真的被铸造。

现在，下一部分来了，我们要部署`LW3NFT2`，这是`LW3NFT`的升级版合同。

为此，我们再次执行`@openzeppelin/hardhat-upgrades`库中的`upgradeProxy`方法，该方法将`LW3NFT`升级并替换为`LW3NFT2`，而不改变系统的状态。

```
proxyContract = await hre.upgrades.upgradeProxy(deployedLW3NFT, LW3NFT2);
```

为了测试它是否真的被替换，我们调用了`test()`函数，并确保它返回 `"upgraded"`，尽管该函数在最初的`LW3NFT`合同中并不存在。

你今天学会了如何升级一个智能合约。

LFG 🚀

## 阅读

- 在文章中提到了`Timelock`，要了解更多关于它的信息，你可以阅读[以下文章](https://blog.openzeppelin.com/protect-your-users-with-smart-contract-timelocks/)
- 还提到了`Access Control`，你可以在[这里](https://docs.openzeppelin.com/contracts/3.x/access-control)阅读更多关于它的信息

## 参考

- [OpenZeppelin Docs](https://docs.openzeppelin.com/upgrades-plugins/1.x/proxies)
- [OpenZeppelin Youtube](https://www.youtube.com/watch?v=kWUDTZhxKZI)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
