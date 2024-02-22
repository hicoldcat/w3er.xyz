---
title: Web3系列教程之新手篇---Level 8：加密货币教程(ERC20)
description: null
author: 李留白
weight: 0
date: 2022-06-19T16:27:47.998Z
lastmod: 2022-07-01T16:47:00.733Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/freshman.png
---


> 原文：[https://www.learnweb3.io/tracks/freshman](https://www.learnweb3.io/tracks/freshman)<br/>
> 翻译：李留白

本文是[learnweb3](https://www.learnweb3.io/)的新手篇，包括：
- [Level 0：前置基础编程知识](https://w3er.xyz/posts/web3/freshman-track-0)
- [Level 1：什么是区块链？](https://w3er.xyz/posts/web3/freshman-track-1)
- [Level 2：什么是Web3？](https://w3er.xyz/posts/web3/freshman-track-2)
- [Level 3：什么是ETH？](https://w3er.xyz/posts/web3/freshman-track-3)
- [Level 4：加密钱包？](https://w3er.xyz/posts/web3/freshman-track-4)
- [Level 5：Remix IDE介绍](https://w3er.xyz/posts/web3/freshman-track-5)
- [Level 6：Solidity介绍](https://w3er.xyz/posts/web3/freshman-track-6)
- [Level 7：dApp介绍](https://w3er.xyz/posts/web3/freshman-track-7)
- [Level 8：加密货币(ERC20)简介](https://w3er.xyz/posts/web3/freshman-track-8)
- [Level 9：NFT介绍](https://w3er.xyz/posts/web3/freshman-track-9)

在这个分步骤的教程中，你将学习如何在以太坊上创建和部署`ERC-20`代币。

我们将在本教程中使用[Metamask](https://metamask.io/)和[Remix IDE](https://remix.ethereum.org/)。

## 喜欢视频？

如果您想从视频中学习，我们的 YouTube 上有本教程的录音。单击下面的屏幕截图观看视频，或继续阅读教程！

[https://www.youtube.com/watch?v=5yM5bojHbmQ](https://www.youtube.com/watch?v=5yM5bojHbmQ)

## 什么是 ERC-20？

`ERC`代表`Ethereum Request for Comment`。从本质上讲，它们是已获得社区批准的标准，用于传达某些用例的技术要求和规范。

`ERC-20`特别是一个标准，它概述了可替代代币的技术规范。

> 可替代令牌是令牌的所有“部分”都相同的令牌。将 1 ETH 换成不同的 1 ETH 不会改变任何东西。你还有 1 ETH。因此，ETH 是一种可替代的代币。所有法定货币也是可替代的。

> NFT 是不可替代代币的示例（稍后会详细介绍），其中每个代币都不同于不同的代币。

以太坊上的大多数代币都符合ERC-20规范。遵循类似标准ERC-20允许使用ERC-20令牌的应用程序开发人员轻松支持所有 ERC-20令牌，而无需单独为它们编写专门的代码。

例如，像[Uniswap](https://uniswap.org/)这样的去中心化交易所允许您将任何代币换成任何其他代币。这是可能的，因为几乎所有代币都遵循ERC-20标准，因此 Uniswap 可以编写适用于所有遵循标准的代币的代码。


## 先决条件

- 确保您已下载并安装[Metamask](https://metamask.io/)。
- 选择Rinkeby Testnet要使用的网络
- 通过以下任一水龙头在 Rinkeby 上请求一些测试网以太：
  - [Metamask Faucet](https://faucet.metamask.io/)
  - [Chainlink Faucet](https://faucets.chain.link/rinkeby)
  - [Paradigm Faucet](https://faucet.paradigm.xyz/)

一旦你设置了所有这些，让我们开始吧！

## 编写代码

我们正在使用[Remix IDE](https://remix.ethereum.org/)来编写智能合约。

在 Remix 中，创建一个新的合约文件，我将其命名为 mine `LW3Token.sol`- 你可以随意命名！

在合约中，编写如下代码：

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol";

contract LW3Token is ERC20 {
    constructor(string memory _name, string memory _symbol) ERC20(_name, _symbol) {
        _mint(msg.sender, 10 * 10 ** 18);
    }
}
```

让我们逐行分解并了解发生了什么：

```
pragma solidity ^0.8.0;
```

此行指定要使用的 Solidity 的编译器版本。`^0.8.0`表示任何大于`0.8.0`. 通常，您会希望使用最新的 Solidity 编译器版本，因为新版本通常意味着新功能或优化。

```
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol";
```

此行从[OpenZeppelin](https://openzeppelin.com/) (OZ)导入ERC-20令牌标准。OZ 是一家以太坊安全公司。除其他外，OZ 为流行的智能合约标准开发参考合约，这些标准经过全面测试且安全。每当实施需要符合标准的智能合约时，请尝试找到 OZ 参考实施，而不是从头开始重写整个标准。

`ERC-20`如果您愿意，可以通过以下链接查看标准合约的实施- https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol

> 注意：在下个系列课程中，我们将更深入地研究 ERC-20 标准合同，以了解该合同中发生的一切。

```
contract LW3Token is ERC20 {
    ...
}
```

这在我们的 Solidity 文件中指定了一个名为 LW3Token 的新合约。此外，它表示该合同is是ERC20. ERC20这里指的是我们从 OpenZeppelin 导入的标准合约。

本质上，我们正在扩展ERC20从 OpenZeppelin 导入的标准合约。所以内置的所有功能和逻辑ERC20都可供我们使用，我们可以在其上添加自己的自定义逻辑。

如果您熟悉面向对象的编程原则，您可以将其视为扩展另一个类的类。

```
constructor(string memory _name, string memory _symbol) ERC20(_name, _symbol) {
     ...
}
```

这个位有一些你以前可能没有见过的奇怪的语法。`Kotlin`实际上有一些类似的语法，但我离题了。

本质上，我们创建了`constructor`在首次部署智能合约时调用的函数。在构造函数中，我们需要来自用户的两个参数——`_name`它们`_symbol`指定我们加密货币的名称和符号。例如。名称 = 以太坊，符号 = ETH。

之后发生的事情更有趣。在指定构造函数后，我们立即调用`ERC20(_name, _symbol)`.

`ERC20`我们从 OpenZeppelin 导入的合约有它自己的构造函数，它需要`name`和`symbol`参数。由于我们正在扩展 ERC20 合约，因此我们需要在部署 ERC20 合约时初始化 ERC20 合约。所以，作为我们构造函数的一部分，我们还需要调用`ERC20`合约上的构造函数。

因此，我们为我们的合约提供`_name`和`_symbol`变量，我们立即将其传递给`ERC20`构造函数，从而初始化ERC20智能合约。

```
_mint(msg.sender, 10 * 10 ** 18);
```


`_mint`是标准合约中的一个`internal`函数`ERC20`，这意味着它只能被合约本身调用。外部用户不能调用该函数。

由于您作为开发人员希望在部署此合约时收到一些代币，因此我们调用该`_mint`函数将一些代币铸造到`msg.sender`.

`_mint`接受两个参数 - 铸造地址和铸造代币数量

`msg.sender`是由以太坊虚拟机注入的全局变量，也就是进行此次交易的地址。由于您将是部署此合约的人，因此您的地址将在`msg.sender`.

`10 * 10 ** 18`指定您希望将 10 个完整令牌铸造到您的地址。

> 注意：您可能想知道为什么我们不只写10金额，而不是10 ** 18（实际上是 10 ^ 18）。

本质上，Solidity 不支持浮点数——即小数。此外，由于 ERC20 代币处理货币，使用浮点数是一个坏主意。

例如，考虑使用`(1/3) * 3`支持浮点数的语言进行的简单计算。你认为这会带来什么回报？

如果你认为它会返回 1，那你就错了。

由于浮点计算的不准确性，由于计算机不能表示无限数量的数字，因此`(1/3) * 3`实际上会产生类似`0.999999999`.

因此，在表示金融货币时，由于计算错误，不使用小数。作为替代方案，我们将每种货币表示为相对于该货币的最小不可分割部分的数量。例如，1 美元表示为 100 美分，因为在处理美元时，您的收益不能低于 1 美分。在那个编号系统中，1 美分只是 1，而不是 0.01。0.33 美元表示为 33，而不是 (1/3)。

`ERC20`默认情况下，令牌使用 18 位小数。所以 1 full` LW3Token`在这种情况下，实际上表示为`10 ^ 18`。因此，要获得 10 个完整`LW3Tokens`的，我们使用`10 * 10 ** 18`.

## 编译

通过按保存（在 Windows 上为 CTRL + S，在 Mac 上为 Command + S）或转到`Compiler` Remix 中的选项卡，选择`LW3Token.sol`并点击来编译您的合约`Compile`。

## 部署

转到 Remix中的`Deployer`选项卡。

选择`Injected Web3`环境（确保您在 Rinkeby 测试网络上），然后连接您的 Metamask 钱包。

选择合约，并为构造函数参数和`LW3Token.sol`输入值。`_name`` _symbol`

单击`Transact`并批准来自 Metamask 的交易以部署您的合约！

部署后，合同应显示在该`Deployed Contracts`部分下。点击`Copy Address`按钮复制合约地址。

转到[Rinkeby Etherscan](https://rinkeby.etherscan.io/)并搜索你的合约地址，你应该在那里看到它！

## 在 Metamask 中查看令牌

您可能会注意到，即使您将代币铸造到您的地址，它们也不会出现在 Metamask 中。

这是因为 Metamask 无法检测随机的 ERC20 代币余额（因为实际上有数十万个）。他们有一个最知名的 ERC20 代币列表，可以自动显示，但除此之外，对于您自己的代币，您通常需要告诉 Metamask 手动将其添加到您的钱包中。

为此：

- 复制你的合约地址
- 打开 Metamask 并单击选项`Import Tokens`卡`Assets`
- 输入你的代币合约地址，它应该会自动检测名称和小数位数
- 点击添加，您将在 Metamask 中看到您的余额！

恭喜！您已经成功部署并铸造了您自己的 ERC20 代币！从这里开始向上！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)