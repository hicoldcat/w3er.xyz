---
title: 学习 Solidity——智能合约开发手册(1)
description: null
author: 李留白
weight: 0
date: 2022-12-19T14:15:29.151Z
lastmod: 2022-12-19T14:59:44.217Z
tags: []
categories:
  - 区块链
  - WEB3.0
  - Solidity
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221219221551.png
---

```txt
目录
    1.这本手册是为谁准备的？
    2.必要的前期知识
    3.什么是 Solidity？
    4.什么是智能合约？
    5.如何在 Solidity 中声明变量和函数？
    6.智能合约中的变量作用域
    7.可见性操作符如何工作
    8.什么是构造函数？
    9.接口和抽象合约
    10.智能合约示例 #2
    11.什么是合约状态？
    12.状态可变关键字（修饰符）
    13.数据位置——存储、内存和堆栈
    14.typing如何工作
    15.Solidity数据类型
    16.如何在 Solidity 中声明和初始化数组
    17.什么是函数修饰符？
    18.Solidity 中的错误处理——require, assert, revert
    19.Solidity 中的继承
    20.用构造器参数进行继承
    21.Solidity 中的类型转换和类型构造
    22.如何在Solidity中处理浮点数
    23.哈希、ABI编码和解码
    24.如何调用合约和使用 fallback 函数
    25.如何发送和接收以太币
    26.Solidity库
    27.Solidity 中的事件和日志
    28.Solidity 中的时间逻辑
    29.结论和更多资源
```

## 这本手册是为谁准备的？

本手册适用于有兴趣探索[“Web3”背后的愿景](https://chain.link/education/web3)，并希望获得实现该愿景所必需的紧缺技能的人编写的。  

不要死记硬背！阅读它，然后将其用作“桌面参考”伴侣。当您学习任何一门新语言时，您会发现概念、习语和用法会变得有些混乱，或者您的记忆会随着时间的推移而消失。没关系！这就是本手册旨在帮助您的目的。

随着时间的推移，我可能会为此添加一些更高级的主题，或者创建一个单独的教程。但就目前而言，这本手册将为您提供构建前几个 Solidity dApp 所需的大部分知识。

本手册假设您至少有几个月的编程经验。我所说的编程是指至少你用JavaScript或Python或一些编译过的语言编写过（因为HTML和CSS实际上不是 "编程 "语言，只知道它们是不够的）。

唯一的其他要求是你要有好奇心、坚定不移，不要给自己设定任何的截止日期。

只要您有一台笔记本电脑和一个可以连接互联网的浏览器，您就可以运行 Solidity 代码。您可以在浏览器中使用Remix来编写本手册中的代码。不需要其他 IDE！

## 必要的前期知识

我还假设您了解[区块链技术](https://blog.chain.link/what-is-blockchain/)的基础知识，尤其是您了解以太坊的基础知识以及[智能合约是什么](https://chain.link/education/smart-contracts)（提示：它们是在区块链上运行的程序，因此提供特殊的信任最小化的便利！）。

您不太可能需要他们来理解本手册。但实际上，拥有像[Metamask](https://metamask.io/)这样的浏览器钱包并了解[以太坊合约账户和外部拥有账户之间的区别](https://info.etherscan.com/understanding-ethereum-accounts/)，将有助于你从本手册中获得最大的收益。

## 什么是Solidity？

现在，让我们先来了解一下什么是Solidity。Solidity 是一种[面向对象的编程语言](https://www.freecodecamp.org/news/what-is-object-oriented-programming/)，受 C++、JavaScript 和 Python 的影响。

Solidity 旨在编译（从人类可读代码转换为机器可读代码）为在以太坊虚拟机 (EVM) 上运行的字节码。这是 Solidity 代码的[运行环境](https://www.techopedia.com/definition/5466/runtime-environment-rte)，就像你的浏览器是 JavaScript 代码的运行环境一样。  

所以，你在 Solidity 中编写智能合约代码，编译器将其转换为字节码。然后该字节码被部署并存储在以太坊（以及其他 EVM 兼容的区块链）上。

[您可以在我制作的这个视频](https://www.youtube.com/watch?v=Z7UNjk_roXI&t=1052s)中获得对 EVM 和字节码的基本介绍。

## 什么是智能合约？

这是一个开箱即用的简单智能合约。它可能看起来没有用，但你会从这一点上了解到很多 Solidity 的知识！你可以在这里找到很多关于 Solidity 的信息。

连同每条评论一起阅读以了解正在发生的事情，然后继续学习一些关键知识。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.8.0;

contract HotFudgeSauce {
    uint public qtyCups;

    // Get the current hot fudge quantity
    function get() public view returns (uint) {
        return qtyCups;
    }

    // Increment hot fudge quantity by 1
    function increment() public {
        qtyCups += 1; // same as  qtyCups = qtyCups + 1;
    }

    // Function to decrement count by 1
    function decrement() public {
        qtyCups -= 1; // same as  qtyCups = qtyCups - 1;
        // What happens if qtyCups = 0 when this func is called?
    }
}
```

我们很快就会讨论一些细节，比如`public`和`view`是什么意思。

现在，从上面的例子中学习七个关键知识：

1. 第一个注释是机器可读的行 ( `// SPDX-License-Identifier: MIT`)，它指定涵盖代码的许可。

   强烈建议使用 SPDX 许可证标识符，尽管您的代码在没有它的情况下也能编译。[在这里](https://docs.soliditylang.org/en/v0.8.6/layout-of-source-files.html#spdx-license-identifier)阅读更多。此外，您可以添加注释或“注释掉”任何行，方法是在其前面加上两个正斜杠“ `//`”。

2. 该`pragma`指令必须是任何 Solidity 文件中的第一行代码。Pragma 是一个指令，它告诉编译器应该使用哪个编译器版本将人类可读的 Solidity 代码转换为机器可读的字节码。

   Solidity是一门新语言，更新频繁，所以不同版本的编译器在编译代码时会产生不同的结果。当使用较新的编译器版本编译时，一些较旧的 solidity 文件会抛出错误或警告。

   在较大的项目中，当您使用像 Hardhat 这样的工具时，您可能需要指定多个编译器版本，因为导入的 solidity 文件或您依赖的库是为旧版本的 solidity 编写的。在此处阅读有关[Solidity 的编译指示指令的](https://docs.soliditylang.org/en/develop/layout-of-source-files.html)更多信息。

3. 该`pragma`指令遵循语义版本控制 (SemVer) - 这是一个系统，其中每个数字表示该版本中包含的更改的类型和范围。如果您想对 SemVer 进行实际操作解释，请查看本教程 - 理解它非常有用，并且如今在开发（尤其是 Web 开发）中广泛使用。

4. 分号在 Solidity 中是必不可少的。如果少了一个，编译器就会失败。Remix会提醒您！

5. 关键字`contract`告诉编译器你正在声明一个智能合约。如果您熟悉面向对象编程，那么您可以将合约视为类。如果你不熟悉OOP，那么你可以把合约看作是保存数据的对象--包括变量和函数。你可以结合智能合约来给你的区块链应用提供它所需要的功能。

6. 函数是封装单个想法、特定功能、任务等的可执行代码单元。通常我们希望函数一次只做一件事。

   函数最常出现在智能合约中，尽管它们可以在智能合约代码块之外的文件中声明。函数可以接受 0 个或多个参数，也可以返回 0 个或多个值。输入和输出是静态类型的，这是您将在本手册稍后部分了解的概念。
7. 在上面的示例中，变量`qtyCups`称为“状态变量”。它持有合约的状态——这是程序需要跟踪运行的数据的技术术语。

   与其他程序不同，智能合约应用程序即使在程序未运行时也会保持其状态。数据与应用程序一起存储在区块链中，这意味着区块链网络中的每个节点都在区块链上维护和同步数据和智能合约的本地副本。  

   状态变量就像传统应用程序中的数据库“存储”，但由于区块链需要在网络中的所有节点之间同步状态，因此使用存储可能非常昂贵！稍后会详细介绍。

## 如何在 Solidity 中声明变量和函数

让我们分解一下`HotFudgeSauce`智能合约，以便我们更多地了解每个部分。

在 Solidity 中定义事物的基本结构/语法类似于其他静态类型语言。我们给函数和变量一个名字。

但在类型语言中，我们也需要指定创建的数据的类型，作为输入传递或作为输出返回。如果你需要了解什么是类型化的数据，你可以跳到本手册中的类型化数据部分。

下面，我们看到声明一个 "状态变量 "是什么样子的。我们还可以看到声明一个函数的样子。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221219224821.png)

第一个片段声明了一个名为`qtyCups`. 这只能存储`uint`无符号整数类型的值。“整数”是指零以下（负）和零以上（正）的所有整数。  

由于这些数字附有 + 或 - 符号，因此称为有*符号*整数。因此，无符号整数始终是正整数（包括零）。

在第二个片段中，我们在声明函数时也看到了一个熟悉的结构。最重要的是，我们看到函数必须为函数返回的值指定数据类型。

在这个例子中，由于`get()`返回了我们刚刚创建的存储变量的值，我们可以看到返回值必须是一个`uint`.

`public`是可见性操作符。稍后会详细介绍。`view`是状态可变修饰符。下面还有更多内容！

这里值得注意的是，状态变量也可以是其他类型 -`constant`和`immutable`. 它们看起来像这样：

```solidity
string constant TEXT = "abc";
address immutable owner = 0xD4a33860578De61DBAbDc8BFdb98FD742fA7028e;
```

常量和不可变型变量的值只分配一次，而且只有一次。在它们的第一个值被分配后，它们不能被赋予另一个值。

因此，如果我们将`qtyCups`状态变量设为常量或不可变，我们将无法再对其调用`increment()`或者`decrement()`函数（事实上，代码将无法编译！）。

常量必须在代码本身中硬编码它们的值，而不可变变量可以将它们的值设置一次，通常是通过构造函数中的赋值（我保证，我们很快就会讨论构造函数）。[您可以在此处的文档](https://docs.soliditylang.org/en/v0.8.16/contracts.html#constant-and-immutable-state-variables)中阅读更多内容。

## 智能合约中的变量作用域

智能合约可以访问三个变量作用域：

1. 状态变量：通过记录区块链上的值，在智能合约中存储永久数据（称为持久状态）。
2. 局部变量：这些是“瞬态”数据，在运行计算时会在短时间内保存信息。这些值不会永久存储在区块链上。
3. [全局变量](https://docs.soliditylang.org/en/v0.8.17/units-and-global-variables.html#special-variables-and-functions)：这些变量和函数由 Solidity“注入”到您的代码中，无需专门创建或从任何地方导入它们即可使用。这些提供了有关代码运行的区块链环境的信息，还包括程序中一般使用的实用函数。

您可以按如下方式区分范围：

1. 状态变量通常位于智能合约内部，但位于函数外部。
2. 局部变量位于函数内部，不能从该函数范围之外访问。
3. 全局变量不是由您声明的——它们“神奇地”可供您使用。

这是我们的`HotFudgeSauce`示例，稍作修改以显示不同类型的变量。我们给出`qtyCups`一个起始值，然后向除我以外的每个人分发几杯软糖酱（因为我正在节食）。

您可能已经注意到函数声明中的一个新关键字`payable`。这允许调用者将 Eth 发送到智能合约，该主题将在本手册后面的单独部分中介绍。  

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221219224946.png)

## 可见性操作符如何工作

“可见性”这个词的使用有点令人困惑，因为在公共区块链上，几乎所有东西都是“可见的”，因为透明度是一个关键特征。但是在这种情况下，可见性意味着一段代码可以被另一段代码看到和访问的能力。

可见性指定变量、函数或合约可以从定义它的代码区域之外访问的程度。可以根据软件系统的哪些部分需要访问它来调整可见范围。

如果您是 JavaScript 或 NodeJS 开发人员，那么您已经熟悉可见性 - 每次导出对象时，您都会使其在声明它的文件之外可见。

### 可见性类型

在 Solidity 中有[4 种不同类型的可见](https://docs.soliditylang.org/en/v0.8.16/cheatsheet.html#function-visibility-specifiers)性：`public`、`external`、`internal`和`private`。

**公共**函数和变量可以在合约内部、外部、其他智能合约和外部账户（位于您的[Metamask](https://metamask.io/)钱包中的那种）访问——几乎可以从任何地方访问。这是最广泛、最宽松的可见性级别。

当一个存储变量被赋予`public`可见性时，Solidity 会自动为该变量的值创建一个隐式的 getter 函数。  

所以在我们的`HotFudgeSauce`智能合约中，我们真的不需要这个`get()`方法，因为 Solidity 会隐式地为我们提供相同的功能，只是通过给`qtyCups`一个`public`可见性修饰符。

**私有**函数和变量只能在声明它们的智能合约中访问。但是它们不能在包含它们的智能合约之外访问。`private`是四个可见性操作符中限制性最强的。

**内部**可见性类似于`private`可见性，因为内部函数和变量只能从声明它们的合约中访问。但是标记为内部的函数和变量也可以从派生合约（即从声明合约继承的子合约）访问，但不能从合约外部访问。稍后我们将讨论继承（和派生/子合约）。

`internal`是存储变量的默认可见性。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221219225028.png)

4 个 Solidity 可见性操作符以及它们可以从哪里访问

**外部**可见性操作符不适用于变量 - 只有函数可以指定为外部。

不能从声明合约或继承自声明合约的合约内部调用外部函数。因此，它们只能从封闭合约之外调用。

这就是它们与公共函数的不同之处——公共函数也可以从声明它们的合约*内部*调用，而外部函数则不能。

## 什么是构造函数？

构造函数是一种特殊类型的函数。在 Solidity 中，它是可选的，仅在合约创建时执行一次。

在下面的示例中，我们有一个显式构造函数，它接受一些数据作为参数。您必须在创建智能合约时将此构造函数参数注入到您的智能合约中。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221219225158.png)

带输入参数的 Solidity 构造函数

要了解何时调用构造函数，记住智能合约是在几个阶段创建的：

- 它被编译为字节码（您可以[在此处阅读有关字节码的更多信息](https://blog.chain.link/what-are-abi-and-bytecode-in-solidity/)）。这个阶段称为“编译时间”。
- 它被创建（构造）——这是构造函数开始运行的时候。这可以称为“构建时间”。
- 然后字节码被部署到区块链。这就是“部署”。
- 部署的智能合约字节码在区块链上运行（执行）。这可以被认为是“运行时”。

在 Solidity 中，与其他语言不同，程序（智能合约）仅*在*构造函数完成其创建智能合约的工作后才会部署。  

有趣的是，在 Solidity 中，最终部署的字节码并不*包含*构造函数代码。这是因为在 Solidity 中，[构造函数代码是创建代码](https://blog.openzeppelin.com/deconstructing-a-solidity-contract-part-ii-creation-vs-runtime-6b9d60ecb44c/)（构造时）的一部分，而不是运行时代码的一部分。它在创建智能合约时用完了，因为它只在这个阶段不需要时被调用，并且被排除在最终部署的字节码中。

因此，在我们的示例中，构造函数创建（构造）`Person`智能合约的一个实例。我们的构造函数希望我们传递一个调用`_name`它的字符串值。

当构建智能合约时，该值`_name`将存储在调用的状态变量中`name`（这通常是我们将配置和其他数据传递到智能合约的方式）。然后当实际部署合约时，状态变量`name`将保存我们传递给构造函数的任何字符串值。

### 了解原因

您可能想知道为什么我们费心将值注入构造函数。为什么不把它们写进合同呢？  

这是因为我们希望合约是可配置的或“参数化的”。我们想要的不是硬编码值，而是在需要时注入数据所带来的灵活性和可重用性。  

在我们的示例中，假设`_name`指的是将要部署合约的给定以太坊网络的名称（如 Rinkeby、Goerli、Kovan、Mainnet 等）。

我们如何将这些信息提供给我们的智能合约？将所有这些值都放入其中会很浪费。这也意味着我们需要添加额外的代码来确定合约在哪个区块链上运行。然后我们必须从我们存储在合约中的硬编码列表中选择正确的网络名称，这会在部署时占用 gas。  

相反，我们可以在将智能合约部署到相关区块链网络时将其注入构造函数。这就是我们编写一个可以使用任意数量参数值的合约的方式。

另一个常见的用例是当您的智能合约继承自另一个智能合约并且您需要在创建合约时将值传递给父智能合约。但是继承是我们后面要讨论的。

我提到构造函数是可选的。在`HotFudgeSauce`中，我们没有编写显式构造函数。但是 Solidity 支持隐式构造函数。因此，如果我们不在智能合约中包含构造函数，Solidity 将[采用默认构造函数](https://docs.soliditylang.org/en/v0.8.13/contracts.html#constructors)，看起来像`constructor() {}`.

如果你在脑海中评估它，你会发现它什么都不做，这就是为什么它可以被排除（隐式）并且编译器将使用默认构造函数。

（未完待续）

> 原文：https://www.freecodecamp.org/news/learn-solidity-handbook/

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)