---
title: 学习 Solidity——智能合约开发手册(2)
description: null
author: 李留白
weight: 0
date: 2022-12-19T14:15:29.151Z
lastmod: 2022-12-24T01:25:29.126Z
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
    10.智能合约示例
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

## 接口和抽象合约

solidity 中的[接口](https://docs.soliditylang.org/en/develop/contracts.html#interfaces)是一个需要理解的基本概念。以太坊上的智能合约是公开可见的，因此您可以通过它们的函数与它们进行交互（在可见性说明符允许您这样做的范围内！）。

这就是使智能合约“可组合”的原因，也是为什么如此多的 Defi 协议被称为“money Legos”——你可以编写与其他智能合约交互的智能合约，这些智能合约又与其他智能合约交互等等……你明白了。

所以当你想让你的智能合约A与另一个智能合约B进行交互时，你需要B的接口。界面为您提供了各种函数的索引或菜单，供您调用给定的智能合约。

接口的一个重要特征是它们不能对定义的任何函数有任何实现（代码逻辑）。接口只是函数名称及其预期参数和返回类型的集合。它们不是 Solidity 独有的。

所以我们的`HotFudgeSauce`智能合约的接口看起来像这样（请注意，按照惯例，solidity 接口是通过在智能合约的名称前加上“I”来命名的：

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.7;

interface IHotFudgeSauce {
    function get() public view returns (uint);
    function increment() public;
    function decrement() public;
}
```

就这样了！由于`HotFudgeSauce`只有三个函数，界面仅显示这些函数。  

但这里有一个重要而微妙的点：接口不需要包含智能合约中可调用的所有函数。可以缩短接口以包含您打算调用的函数的函数定义！  

因此，如果你只想在`HotFudgeSauce`上使用`decrement()`方法，那么你绝对可以从你的接口中删除`get()`和`increment()`--但你将无法从你的合约中调用这两个函数。

那么实际上是怎么回事呢？好吧，接口只是给你的智能合约提供了一种方法，让你知道在你的目标智能合约中可以调用哪些函数，这些函数接受哪些参数（以及它们的数据类型），以及你可以期待哪些类型的返回数据。在 Solidity 中，这就是您与另一个智能合约交互所需的全部。

在某些情况下，您可以拥有一个类似于但又不同于接口的抽象合约。

[抽象合约](https://docs.soliditylang.org/en/develop/contracts.html#abstract-contracts)是使用`abstract`关键字声明的，是一种声明了一个或多个函数但未实现的合约。另一种说法是，至少有一个函数已声明但未实现。  

反过来说，抽象合约可以有其函数的实现（不像接口可以有零个实现的函数），但只要至少有一个函数未实现，合约就必须标记为抽象的：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

abstract contract Feline {

    int public age;


    // not implemented.
    function utterance() public virtual returns (bytes32);

    

    // implemented.

    function setAge(int _age) public {

        age = _age;

    }

}
```

您可能（合理地）想知道这有什么意义。好吧，抽象合约不能直接实例化（创建）。它们只能被继承自它们的其他合约使用。

因此，抽象合约通常被用作其他智能合约可以“继承”的模板或“基础合约”，从而迫使继承的智能合约实现抽象（父）合约声明的某些函数。这在相关合约之间强制执行定义的结构，这通常是一种有用的设计模式。

当我们稍后讨论继承时，这个继承的东西会变得更清楚一点。现在，请记住，您可以声明一个不实现其所有函数的抽象智能合约——但如果您这样做，您将无法实例化它，未来继承它的智能合约必须完成实现那些未实现函数的工作。

接口和抽象合约之间的一些重要区别是：

- 接口可以有零个实现，而抽象合约可以有任意数量的实现，只要至少有一个函数是“抽象的”（即未实现）。
- 接口中的所有函数都必须标记为“外部”，因为它们只能由实现该接口的其他合约调用。
- 接口不能有构造函数，而抽象合约可以。
- 接口不能有抽象合约可能有的状态变量。

## 智能合约示例

对于接下来的几个 Solidity 概念，我们将使用以下智能合约。这部分是因为这个例子包含了一个在现实世界中实际使用的智能合约。我选择它也是因为我对 Chainlink Labs 有明显的偏见，因为我在那里工作 (😆) 而且它很棒。但这也是我学到很多 Solidity 的地方，而且通过真实世界的例子学习总是更好。

因此，请先阅读下面的代码和评论。如果您仔细阅读，您已经了解了理解以下合同所需内容的 99%。然后继续从这份合同中学到的关键知识。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

contract PriceConsumerV3 {
    AggregatorV3Interface internal priceFeed;

    /**
     * Network: Goerli
     * Aggregator: ETH/USD
     * Address: 0xD4a33860578De61DBAbDc8BFdb98FD742fA7028e
     */
    constructor() {
        priceFeed = AggregatorV3Interface(0xD4a33860578De61DBAbDc8BFdb98FD742fA7028e);
    }
   

     /**
     * Returns the latest price
     */
    function getLatestPrice() public view returns (int) {
        (
            /*uint80 roundID*/,
            int price,
            /*uint startedAt*/,
            /*uint timeStamp*/,
            /*uint80 answeredInRound*/
        ) = priceFeed.latestRoundData();
        return price;
    }
}
```

该智能合约从实时 Chainlink 价格反馈预言机（[请参阅 etherscan 上的预言机）](https://goerli.etherscan.io/address/0xD4a33860578De61DBAbDc8BFdb98FD742fA7028e#code)[获取 1 Eth 的最新美元价格](https://docs.chain.link/docs/get-the-latest-price/)。该示例使用 Goerli 网络，因此您最终不会在以太坊主网上花费真钱。

现在，您需要了解 6 个基本的 Solidity 概念：

1. 在`pragma`语句之后我们有一个`import`语句。这会将现有代码导入我们的智能合约。

   这太酷了，因为这是我们重用他人编写的代码并从中受益的方式。您可以查看在此[GitHub 链接](https://github.com/smartcontractkit/chainlink/blob/develop/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol)上导入的代码。

   实际上，当我们编译我们的智能合约时，这个导入的代码会被拉入并与它一起编译成字节码。我们马上就会明白为什么我们需要它……

2. 之前您看到单行注释标有`//`。现在你正在学习多行注释。它们可以跨越一行或多行并使用`/*`and`*/`来开始和结束注释。

3. 我们声明一个变量叫做`priceFeed`并且它的类型是`AggregatorV3Interface`。但是这种奇怪的类型是从哪里来的呢？来自我们导入语句中的导入代码——我们开始使用该`AggregatorV3Interface`类型，因为 Chainlink 定义了它。

   如果您查看该 Github 链接，您会看到该类型定义了一个接口（我们刚刚讨论完了接口）。所以`priceFeed`是对某个`AggregatorV3Interface`类型对象的引用。

4. 看一下构造函数。这个函数不接受参数，但我们可以很容易地将 ETH/USD  Price Feed的 oracle 智能合约地址`0xD4a33860578De61DBAbDc8BFdb98FD742fA7028e`作为地址类型的参数传给它。相反，我们在构造函数中硬编码了地址。

   但我们也正在创建对 Price Feed Aggregator 智能合约的引用（使用名为 的接口`AggregatorV3Interface`）。

   现在我们可以调用 `AggregatorV3Interface`上可用的所有方法，因为`priceFeed`变量指的是该智能合约。事实上，我们接下来要做的是……

5. 让我们跳转到函数`getLatestPrice()`。你会从我们在`HotFudgeSauce `中的讨论中认出它的结构，但它正在做一些有趣的事情。

   在这个`getLatestPrice()`函数中，我们调用类型`AggregatorV3Interface`上存在的函数`latestRoundData()`。如果您[查看此方法的源代码，](https://github.com/smartcontractkit/chainlink/blob/develop/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol#L22)您会注意到此  `latestRoundData()`函数返回 5 种不同类型的整数！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221221163327.png)

从我们的智能合约中调用另一个智能合约的方法

在我们的智能合约中，我们注释掉了我们不需要的所有 4 个值。所以这意味着 Solidity 函数可以返回多个值（在这个例子中我们返回了 5 个值），我们可以挑选我们想要的。  

另一种消耗调用` latestRoundData()` 的结果的方式是：`(,int price, , , ) = priceFeed.latestRoundData() `其中我们通过不给它们一个变量名而忽略了5个返回值中的4个。

当我们给一个或多个函数返回的值分配变量名时，我们称之为 "析构赋值"，因为我们对返回的值进行了析构（将每个值分离出来），并在析构时对其进行赋值，就像我们对上面的`price`所做的那样。

既然你已经了解了接口，我建议你看一下Chainlink Labs的[GitHub](https://github.com/smartcontractkit/)存储库，看看在Aggregator检查[合约中实现](https://github.com/smartcontractkit/chainlink/blob/develop/contracts/src/v0.6/AggregatorProxy.sol#L211)[的](https://github.com/smartcontractkit/chainlink/blob/develop/contracts/src/v0.6/AggregatorProxy.sol#L211)的`latestRoundData()`函数，以及`AggregatorV3Interface`如何提供与`Aggregator`合约交互的接口。

## 什么是合约状态？

在我们继续之前，重要的是要确保我们将经常看到的术语对您来说是可以理解的。

计算机科学中的“状态”具有[明确的含义](https://en.wikipedia.org/wiki/State_(computer_science))。虽然它会变得非常混乱，但状态的关键在于它指的是程序在运行时“记住”的所有信息。此信息可以更改、更新、删除、创建等。而且，如果您在不同时间为其拍摄快照，信息将处于不同的“状态”。

所以状态只是程序的当前快照，在其执行期间的某个时间点 - 它的变量持有什么值，它们在做什么，已经创建或删除了哪些对象，等等。

我们之前已经研究了三种类型的变量——状态变量、局部变量和全局变量。状态变量以及全局变量为我们提供了智能合约在任何给定时间点的状态。因此，智能合约的状态是对以下内容的描述：

1. 它的状态变量持有什么值，
2. 区块链相关的全局变量在那个时刻有什么值，以及
3. 智能合约账户中的余额（如果有）。

## 状态可变关键字（修饰符）

现在我们已经讨论了状态、状态变量和函数，让我们了解指定允许我们对状态执行的操作的 Solidity 关键字。  

这些关键字称为[修饰符](https://docs.soliditylang.org/en/v0.8.17/cheatsheet.html#modifiers)。但并非所有这些都允许您修改状态。事实上，他们中的许多人明确*禁止*修改。

以下是您将在任何现实世界的智能合约中看到的 Solidity 修改器：

| **修饰符关键字** | **适用于…**     | **目的**                                                     |
| ---------------- | --------------- | ------------------------------------------------------------ |
| constant         | State variables | 声明同时并赋值一次。硬编码成代码。它的给定值永远不能改变。 当我们知道一个值永远不会改变时使用 - 例如，如果我们永远不会（永远）允许用户购买超过 50 单位的东西，我们可以将 50 声明为常量值。 |
| immutable        | State variables | 这些是在智能合约的顶部声明的，但在构造时给定它们的值（仅一次！）——即通过构造函数。一旦他们收到他们的价值，他们（有效地）是常量。并且它们的值实际上存储在代码本身而不是存储槽中（存储将在后面解释）。 |
| view             | functions       | 您通常会在可见性说明符之后立即看到它。视图修饰符意味着该函数只能“查看”（读取）合约状态，但不能更改它（不能“写入”合约状态）。这实际上是一个只读修饰符。如果该函数需要使用合约状态中的任何值，但不修改该值，则它将是一个视图函数。 |
| pure             | functions       | 纯函数不允许写入（修改）合约状态，甚至不允许从中读取！他们做的事情不会以任何方式与区块链状态交互。通常这些可以是辅助函数，可以进行一些计算或将一种数据类型的输入转换为另一种数据类型等。 |
| payable          | functions       | 该关键字使函数能够接收 Eth。没有这个关键字，你就不能在调用函数时发送 Eth。请注意，在 Solidity 版本 0.8.17 中，有[重大更改](https://docs.soliditylang.org/en/v0.8.17/080-breaking-changes.html?highlight=payable address#new-restrictions)允许使用 payable 作为数据类型。具体来说，我们现在允许通过执行类似于payable( 0xdCad3a6d3569DF655070DEd06cb7A1b2Ccd1D3AF )的类型转换，将地址数据类型转换为payable地址类型。这样做的目的是使给定的以太坊地址成为可支付的，之后我们可以将以太币发送到该地址。       请注意，payable 的这种用法是一种类型转换，与函数修饰符不同，尽管使用了相同的关键字。[我们稍后会介绍地址类型，但您可以在此处](https://docs.soliditylang.org/en/v0.8.16/types.html#address)阅读相关内容。 |
| virtual          | functions       | 这是一个稍微高级的主题，在继承部分有详细介绍。这个修饰符允许函数在从它继承的子合约中被“覆盖”。换句话说，一个带有关键字virtual的函数可以在另一个继承自这个合约的合约中用不同的内部逻辑“重写”。 |
| override         | functions       | 这是虚拟修改器的反面。当子合约“重写”在它继承的基础合约（父合约）中声明的函数时，它会用覆盖标记重写的函数，以表明其实现覆盖了父合约中给出的函数。如果父项的虚函数没有被子项覆盖，则父项的实现将应用于子项。 |
| indexed          | events          | 我们将在本手册后面介绍事件。它们是由智能合约“发出”的小数据包，通常是为了响应发生的值得注意的事件。indexed关键字表示事件中包含的其中一条数据应存储在区块链中，以供以后检索和过滤。一旦我们在本手册后面介绍了事件和日志记录，这将更有意义。 |
| anonymous        | events          | [文档中](https://docs.soliditylang.org/en/v0.8.17/cheatsheet.html#modifiers)说“不将事件签名存储为主题”，这对您来说可能意义不大。但关键字确实表明它使事件的某些部分“匿名”。因此，一旦我们理解了本手册后面的事件和主题，这就有意义了。 |

请注意，不是存储变量的变量（即在给定函数范围内声明和使用的局部变量）不需要状态修饰符。这是因为它们实际上并不是智能合约状态的一部分。它们只是该函数内部局部状态的一部分。那么根据定义，它们是可修改的并且不需要对其可修改性进行控制。

## 数据位置——存储、内存和堆栈

在以太坊和基于 EVM 的链上，系统内的数据可以在多个“数据位置”放置和访问。  

数据位置是EVM的基本设计和架构的一部分。当你看到 "内存"、"存储 "和 "堆栈 "这些词时，你应该开始思考 "数据位置"--也就是说，数据可以被存储（写入）到哪里，以及从哪里检索（读取）。

数据位置会影响代码在运行时的执行方式。但它对智能合约在部署和运行期间使用 的[气体量](https://ethereum.org/en/developers/docs/gas/)也有非常重要的影响。

gas 的使用需要对 EVM 和称为操作码的东西有更深入的了解——我们可以暂时搁置这个讨论。虽然有用，但您并非绝对有必要了解数据位置。

虽然到目前为止我已经提到了 3 个数据*位置，但还有 2 种其他方式可以在智能合约中存储和访问数据：“calldata”和“code”。*但这些不是 EVM 设计中的数据位置。它们只是 3 个数据位置的子集。

让我们从存储开始。在 EVM 的设计中，需要永久存储在区块链上的数据被放置在相关智能合约的“存储”区域。这包括任何合约“状态变量”。  

一旦部署了合约并拥有了它的特定地址，它也会获得自己的存储区域，你可以将其视为键值存储（如哈希表），其中**键** 和值都是 256 位（32 字节）数据“词”。而“词”在计算机体系结构中[具有特定的含义。](https://en.wikipedia.org/wiki/Word_(computer_architecture))

由于存储将数据永久保存在区块链上，因此所有数据都需要在网络中的所有节点之间同步，这就是节点必须就数据状态达成共识的原因。这种共识使存储使用起来很昂贵。

您已经看到了存储变量（也称为合约状态变量）的示例，但这里是取自[Chainlink 可验证随机数消费者智能合约的示例](https://github.com/smartcontractkit/chainlink/blob/develop/contracts/src/v0.8/VRFConsumerBaseV2.sol)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221221163945.png)

存储数据位置。将数据放入合约的存储布局中。

当创建和部署上述合约时，传递给合约构造函数的任何地址都会永久存储在智能合约的存储中，并且可以使用变量`vrfCoodinator`访问。由于此状态变量标记为`immutable`，因此此后无法更改。

为了让您从上一节关于关键字（我们上次讨论的关键字`immutable`和`constant`变量）的记忆中恢复过来，这些值没有存储。在构造合约时，它们成为代码本身的一部分，因此这些值不会像存储变量那样消耗那么多的气体。

现在让我们转到`memory`. 这是临时存储，您可以在其中读取和写入智能合约运行期间所需的数据。一旦使用该数据的函数执行完毕，该数据将被擦除。  

`memory`位置空间就像一个临时记事本，每次触发函数时都会在智能合约中提供一个新的记事本。执行完成后，该记事本将被丢弃。

在理解存储和内存的区别时，您可以将存储视为传统计算世界中的一种硬盘，因为它具有“持久”存储数据的意义。但内存在传统计算中更接近 RAM。  

`stack`是执行大多数 EVM 计算的数据区域。EVM 遵循基于[堆栈的计算模型](https://en.wikipedia.org/wiki/Stack_machine#Comparison_with_register_machines)，而不是基于寄存器的计算模型，这意味着要执行的每个操作都需要使用[堆栈数据结构](https://en.wikipedia.org/wiki/Stack_(abstract_data_type))进行存储和访问。

堆栈的深度——即它可以容纳的项目总数——是 1024，堆栈中的每个项目可以是 256 位（32 字节）长。这与存储数据位置中每个键和值的大小相同。

您可以在此处详细了解 EVM 如何控制对堆栈数据存储区域的[访问](https://docs.soliditylang.org/en/v0.8.17/introduction-to-smart-contracts.html#storage-memory-and-the-stack)。

接下来，我们来说说`calldata`。我假设您对以太坊智能合约[消息](https://ethereum.org/en/whitepaper/#messages)和[交易](https://ethereum.org/en/developers/docs/transactions/#:~:text=An Ethereum transaction refers to,takes place within a transaction.)有基本的了解。如果不这样做，则应先阅读这些链接。  

消息和交易是调用智能合约函数的方式，它们包含执行这些函数所需的各种数据。此消息数据存储在名为 的内存的只读部分中`calldata`，其中包含函数名称和参数等内容。

这与外部可调用函数相关，因为内部函数和私有函数不使用调用数据。此位置仅存储“传入”函数执行数据和函数参数。

记住，`calldata`就是内存，只不过calldata是只读的。你不能向它写数据。

最后，`code`不是数据位置，而是指智能合约的编译字节码，它被永久部署和存储在区块链上。该字节码存储在不可变的 ROM（只读存储器）中，其中加载了要执行的智能合约的字节码。  

还记得我们如何讨论 Solidity 中不可变变量和常量变量之间的区别吗？不可变值被赋值一次（通常在构造函数中），常量变量的值被硬编码到智能合约代码中。因为它们是硬编码的，常量值按字面编译并直接嵌入到智能合约的字节码中，并存储在这个代码/ROM 数据位置。

就像`calldata`一样,`code`也是只读的 - 如果您理解上一段，您就会明白为什么

## Typing是如何工作的

类型化是编程中一个非常重要的概念，因为它是我们赋予数据结构的方式。从这个结构中，我们可以以一种安全、一致和可预测的方式对数据进行操作。  

当一种语言具有严格类型时，这意味着该语言严格定义了每条数据的类型，并且不能为具有类型的变量赋予另一种类型。  

换句话说，在严格类型的语言中：

```
int a =1  //     1 here is of the integer type
string b= "1" //  1 here is of the string type


b=a //  Nope! b is a string. It cannot hold an int value, and vice-versa!
```

但在没有类型的 JavaScript 中，`b=a`完全可以工作——这使 JavaScript 成为“动态类型”。  

同样，在静态类型语言中，您不能将整数传递给需要字符串的函数。但是在 JavaScript 中，我们可以将任何东西传递给函数，程序仍然可以编译，但在执行程序时可能会抛出错误。

例如以这个函数为例：

```js
function add(a,b){
    return a + b 
}

add(1, 2) // output is 3, of type integer

add(1, "2") // “2” is a string, not an integer, so the output becomes the string “12” (!?)
```

可以想象，这会产生一些很难发现的错误。代码编译甚至可以执行而不会失败，尽管它会产生意想不到的结果。

但是强类型语言永远不会让你传递字符串“2”，因为函数会坚持它接受的类型。

让我们看一下这个函数在像 Go 这样的强类型语言中是如何写的

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221221164529.png)

typing在语法中是如何工作的，使用 Golang 进行说明

试图传递一个字符串（即使它代表一个数字）将使程序甚至无法编译（构建）。   你会看到一个类似这样的错误。

```
./prog.go:13:19: cannot use "2" (untyped string constant) as int value in argument to add

Go build failed.
```

[亲自尝试一下](https://go.dev/play/p/SrE1CXVD_pj)！

所以类型很重要，因为对于人类来说似乎相同的数据可能会被计算机以非常不同的方式感知。这可能会导致一些非常奇怪的错误、错误、程序崩溃，甚至是严重的安全漏洞。  

类型还使开发人员能够创建自己的自定义类型，然后可以使用自定义属性（属性）和操作（行为）对其进行编程。

类型系统的存在使得人们可以通过询问“这个数据的类型是什么，它应该能够做什么？”这样的问题来推理数据。并且机器可以完全按照预期进行操作。

这是另一个例子，说明在你我看来相同的数据可能如何被处理器以截然不同的方式解释。取二进制数字序列（即数字只能有 0 或 1 的值，这是处理器使用的[二进制系统](https://www.mathsisfun.com/binary-number-system.html)`1100001010100011`） 。

对于人类来说，使用十进制系统看起来是一个非常大的数字——也许是 11 gazillion 之类的。

但是对于二进制的计算机来说，它不是 11 任何东西。计算机将其视为一个 16 位序列（二进制数字的缩写），在二进制中这可能意味着*正数*（无符号整数）49,827 或带符号整数 -15,709 或英镑符号 £ 的 UTF-8 表示或其他不同的东西！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221221164653.png)



一个bit序列可以被计算机解释为具有非常不同的含义（[来源](https://livebook.manning.com/book/programming-with-types/chapter-1/18)）

所以所有这些解释都是在说类型很重要，并且类型可以“内置”到一种语言中**，即使**该语言不严格强制类型，如 JavaScript。  

JavaScript 已经具有[内置类型](https://www.w3schools.com/js/js_datatypes.asp)，如数字、字符串、布尔值、对象和数组。但正如我们所看到的，JavaScript 并不像 Go 那样坚持类型与静态类型语言一样。

现在回到 Solidity。Solidity 在很大程度上是一种静态类型的语言。声明变量时，还必须声明其类型。更进一步，如果您尝试将字符串传递给需要整数的函数，Solidity 将简单地拒绝编译。

事实上，Solidity 对类型非常严格。例如，不同类型的整数也可能会像下面的示例一样编译失败，其中函数`add()`需要一个无符号整数（正）并且只会与该数字相加，因此始终返回一个正整数。但是返回类型被指定为 `int`这意味着它可以是正数或负数！  

```js
function add(uint256 a) public pure returns (int256){
        return a + 10;
    }

// The Solidity compiler complains saying:

// TypeError: Return argument type uint256 is not implicitly convertible to expected type (type of first return variable) int256.r 
```

因此，尽管输入和输出都是 256 位整数，但函数只接收无符号整数这一事实使编译器抱怨无符号整数类型*不能*隐式转换为有符号整数类型。

那是相当严格的！开发人员可以通过将 return 语句重写为 来强制转换（称为类型转换）`return int256(a + 10)`。但是这种行动需要考虑一些问题，这超出了我们在这里讨论的范围。

现在，请记住 Solidity 是静态类型的，这意味着在代码中声明每个变量时必须明确指定它们的类型。您可以组合类型以形成更复杂的复合类型。接下来，我们可以讨论其中一些内置类型。

（未完待续）

> 原文：https://www.freecodecamp.org/news/learn-solidity-handbook/

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)