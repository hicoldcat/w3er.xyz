---
title: 学习 Solidity——智能合约开发手册(5)
description: null
author: 李留白
weight: 0
date: 2022-12-27T12:18:40.135Z
lastmod: 2022-12-27T12:19:28.546Z
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

## 如何发送和接收以太币

要从您的智能合约向目标合约发送以太币，您需要使用以下三种内置 Solidity 方法之一调用目标合约：`transfer`,`send`或`call`。

`transfer`失败时将抛出异常，并`send`返回`call`一个布尔值，您必须在继续之前检查该值。在这三个中，出于安全原因不再推荐`transfer`和`send`，尽管你仍然可以使用它们，而且它们也可以工作。

除以下情况外，智能合约无法接收以太币：

- 他们实现了`payable`回退或`payable`接收特殊功能，或者
- 当调用合约调用`selfdestruct`并强制目标合约接受其所有剩余的以太币时强制执行。然后调用合约从区块链中删除。这是一个单独的主题，经常被开发者恶意使用。

如果您希望您的智能合约接收 Ether ，通常建议您使用`receive()`函数。您可以通过创建`payable`回退函数来逃避，但推荐的做法是改用`receive()`函数。

如果你只依赖 fallback 函数，你的编译器会向你发出以下消息：“*Warning: This contract has a payable fallback function, but no receive ether function. Consider adding a receive ether function.*”

如果你同时拥有`receive`和`fallback`，你可能很想知道 Solidity 如何决定用哪个函数接收以太币。该设计决策还告诉您这些功能旨在做什么。

接收意味着接收以太币。而 fallback 是为了处理合约已经被调用的情况，但是正如我们在上一节中讨论的那样，合约中没有匹配的方法可以处理调用。  

Solidity 通过检查调用者发送的交易中的`msg.data`字段来匹配要调用的方法。如果该字段是一个非空值，并且该值与被调用合约中声明的任何其他函数不匹配，则会触发回退方法。  

如果`msg.data`为空，那么它将检查是否有已实现的接收函数。如果是这样，它将使用它来接受以太币。如果不存在 receive，它将默认为 fallback 函数。因此，回退是...回退（默认）方法，当没有其他任何意义时。  

`receive`函数是使您的合约能够接收以太币的更好方式。您可以在调用智能合约但没有任何东西可以“处理”该调用的任何情况下使用回退函数。

这里有一个超级方便的逻辑树，显示了receive和fallback的目的是处理什么。

```
 Which function is called, fallback() or receive()?

           send Ether
               |
         msg.data is empty?
              / \
            yes  no
            /     \
receive() exists?  fallback()
         /   \
        yes   no
        /      \
    receive()   fallback()
```

（来源：[Solidity By Example](https://solidity-by-example.org/sending-ether/)）

回到我们探索回退函数的示例，我们可以给`Target`添加一个`receive`函数，如下所示：

```solidity
contract Target {
    int256 public count;

    function decrement() public payable {
        count = count - num;
    }

    fallback() external payable  {
        count++;
    }

    

    receive() external payable  {
        count+=5;
    }
}

interface ITarget {
    function decrement(int num) external payable;
    function nonExistentFunction() external;
}

contract TargetCaller {
    function callFallback(address _target) public {
        ITarget target = ITarget(_target);
        target.nonExistentFunction();


    }
}
```

我们已经看到`callFallback`将如何更改`Target`中的计数值。但是如果我们部署一个新的`Target`实例，我们现在可以给它发送 10 wei，如下所示，因为它现在有一个`payable` `receive`函数。在发送 10 wei（或任何其他金额）之前，`Target`余额为零，如下所示。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221227202413.png)



使用空的calldata ( `msg.data`) 点击交易按钮将改变余额，如下图所示。我们可以查看`count`，它增加了5，这是`receive`函数中的逻辑。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221227202433.png)

（将 Wei 发送到目标合约并观察更新后的余额）

如果我们调用`callFallback`并给它新`Target`实例的地址，我们会注意到它只递增`1`. 如果我们包括一些 wei，那也会增加 Target 的余额。

因此，任何向智能合约转移以太币的行为，都需要接收的智能合约有可以接收它的支付功能。最起码，接收的智能合约需要一个`payable`回退函数，尽管`payable` `receive`函数是接收以太币支付的更好方法。

## Solidity库

在任何编程语言中，库都是指帮助程序和实用程序函数的集合，这些函数旨在可跨多个代码库重用。这些函数解决了特定的、重复出现的编程问题。

在 Solidity 中，库服务于相同的目的，但有一些特殊的属性。

首先，它们是无状态的——也就是说，它们不存储数据（常量除外，因为它们不会改变区块链的状态）。他们也无法获得价值（这意味着他们无法拥有`payable receive`或`fallback`发挥作用）。

它们也不能从其他合约或库继承，库也不能有子（派生）合约。  

库中声明的所有函数都不能是抽象的——也就是说，它们必须都有具体的实现。

由于 Solidity 库是无状态的，因此其中的任何方法都不能修改区块链的状态。这意味着库中的所有方法都是`pure`或`view`函数。

Solidity 库的另一个有趣属性是它们不需要导入到您的智能合约中。它们可以作为独立合约部署，然后在所有使用智能合约中通过它们的接口调用——就像传统工程世界中的 API 服务一样。

但是，只有在库包含公共或外部方法的情况下才是正确的。然后，该库可以部署为具有自己的以太坊地址的独立合约，并可被所有使用智能合约调用。

如果库仅包含内部方法，那么 EVM 只是将库代码“嵌入”到使用该库的智能合约中（因为无法从其他智能合约访问内部函数）。

Solidity 中的库具有超越代码重用的优势。在区块链上一次性部署一个库可以避免重复部署或导入库的代码，从而节省未来的 gas 成本。

让我们看一个简单的库，然后剖析代码以了解如何使用库的代码。

```solidity
library WeirdMath {
    int private constant factor = 100;

    function applyFactor(int self) public pure returns (int) {
        return self * factor;
    }

    function add(int self, int numberToAdd) public pure returns (int) {
        return self + numberToAdd;
    }
}
```

该库有两种对`int`数据类型进行操作的方法。调用第一个参数`self`的原因很快就会清楚。其中一个方法接收一个数字，然后将其乘以一个常数，这个常数存储在库的代码中。第二个方法接收两个数字并将它们相加。

现在让我们看看我们如何在一个消耗性的智能合约中使用这个。

```solidity
// SPDX-License-Identifier: MIT

pragma solidity >=0.5.22 <=0.8.17;

contract StrangeMath {
    // Method 1 - using Library name with dot notation 
    function multiplyWithFactor(int num) public pure returns (int) {
        return WeirdMath.applyFactor(num);
    }


    // Method 2 - the 'using' keyword and dot notation.
    // Syntax: using <<Library Name>> for data type of the first argument in the method to be called.
    using WeirdMath for int;
    function addTwoNums(int num1, int num2) public pure returns (int) {
        return num1.add(num2);
    }
}
```

首先要注意的是，有两种使用`WeirdMath`库的方法。

您可以通过以下任一方式使用它：

1. 调用库的名称后跟要调用的函数，或者
2. 直接在您希望函数操作的数据类型上调用函数。此数据类型必须与库函数中的`self`参数类型相同。

第一种方法由代码片段中的方法1演示，我们用`WeirdMath.add(num1, num2);`调用该库。 

第二种方法使用 Solidity`using`关键字。该表达式`return num1.add(num2);`将`WeirdMath`库的 add 函数应用于`num1`变量。这与把它作为self传入相同，它是add函数的第一个参数。

## Solidity 中的事件和日志

智能合约可以发出事件。这些事件包含开发人员指定的数据片段。  

事件不能被其他智能合约消费。相反，它们作为日志存储在区块链上，并且可以通过从区块链读取的 API 进行检索。

这意味着您的应用程序（最常见的是您的前端应用程序）可以从区块链“读取”包含事件数据的日志。通过这种方式，您的用户界面可以响应区块链上的事件。

这就是更新应用程序用户界面以响应链上事件的方式。由于可以查询区块链上的这些日志，因此日志是一种廉价的存储形式，正如前面关于存储区域的讨论中所讨论的那样。

可以使用相关的区块链浏览器检查智能合约发出的事件，因为公共区块链上的所有内容都是公开可见的。但是如果智能合约的字节码没有经过验证，事件数据可能不是人类可读的（它将被编码）。经过验证的智能合约的事件将是人类可读的。  

节点和其他区块链客户端可以监听（订阅）特定事件。其核心是[Chainlink](http://docs.chain.link/)预言机的工作方式——去中心化预言机节点侦听来自智能合约的事件，然后做出相应的响应。他们甚至可以从事件中提取数据，在链下运行复杂且资源密集型的计算，然后将可加密验证的计算结果提交回区块链。  

由于能够通过智能合约发出的事件查询区块链数据，其他网络 API 和[索引服务（如子图）](https://thegraph.com/en/)成为可能。

这是 Solidity 中事件的样子：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;

contract SimpleStorage {
    uint256 favoriteNumber;

    event storedNumber(
        uint256 indexed oldNumber, // up to 3 indexed params allowed
        uint256 indexed newNumber,
        uint256 addedNumber,
        address sender
    );

    function store(uint256 newNumber) public {
        emit storedNumber(
            favoriteNumber,
            newNumber,
            newNumber + favoriteNumber,
            msg.sender
        );
        favoriteNumber = newNumber;
    }

    function retrieve() public view returns (uint256) {
        return favoriteNumber;
    }
}
```

首先声明一个事件，并指定其参数及其数据类型。任何具有`indexed`关键字的数据都由 EVM 索引，以便对区块链日志的查询可以使用索引参数作为过滤器。这使得检索日志更快。

一个事件最多可以存储 4 个索引参数——取决于它是匿名的还是非匿名的。索引事件参数在 Solidity 世界中也称为“主题”。

大多数事件都是非匿名的，这意味着它们包含有关事件名称和参数的数据。

非匿名事件只允许开发人员指定 3 个主题，因为第一个主题保留用于指定 ABI 编码的十六进制形式的事件签名。[您可以在此处](https://docs.ethers.io/v5/concepts/events/#events-solidity)阅读有关匿名和非匿名主题的更多信息。

您还可以在相关的区块链浏览器（如[etherscan.io](http://etherscan.io/)）上探索事件。

您可以从两个入口点之一进行处理。您可以直接查看合约的地址，然后转到“事件”选项卡（它将仅显示该合约发出的事件）。或者您可以转到交易哈希并检查由该交易触及的所有合约发出的所有事件。  

例如，下面是[以太坊主网上 Chainlink VRF 协调器智能合约](https://docs.chain.link/vrf/v2/subscription/supported-networks#ethereum-mainnet)事件的屏幕截图。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221227202649.png)

合同选项卡有一个绿色的勾号，这意味着合同已经过验证，因此事件名称和参数是人类可读的。花点时间研究这张图片，因为它包含很多信息！如果您想直接在 etherscan 上研究它，[请单击此处](https://etherscan.io/address/0x271682DEB8C4E0901D1a1550aD2e64D568E69909#events)。  

该 Chainlink VRF 协调器合约响应对加密可验证随机数的请求，并为请求智能合约提供随机数（称为“随机词”）。

如果你想了解“单词”在计算机科学中的含义，请查看我和我的同事在这个[Chainlink 2022 黑客马拉松视频](https://youtu.be/lu3yDJQqqY0?t=2627)中解决这个问题）。

当 VRF 协调器合约满足随机数请求时，它会发出一个`RandomWordsFulfilled`事件。该事件包含 4 条数据，其中第一个数据`requestID`已编入索引。

Solidity 事件包含三类数据：

1. 发出事件的合约地址。
2. 主题（用于过滤日志查询的索引事件参数）。
3. 非索引参数，称为“数据”，它们采用 ABI 编码并以十六进制表示。此数据需要按照 ABI 编码和解码部分中描述的方式进行 ABI 解码。

在 Remix 中工作时，您还可以在控制台中检查事件，如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221227202709.png)

在 Remix Browser IDE 中检查事件数据

您还可以使用EthersJS 中[的合约接收对象以编程方式访问事件。](https://stackoverflow.com/questions/73230175/does-etherjs-transactionreceipt-have-an-events-object)使用我们上面在 SimpleStorage 合约中使用的代码片段，我们可以使用以下 JavaScript 使用 EthersJS 和 Hardhat 访问事件：

```js
const transactionResponse = await simpleStorage.store(1981)
const receipt = await transactionResponse.wait()

console.log(receipt.events[0].args.newNumber.toString()) // 1981
```

您还可以在前端应用程序中使用诸如 EtherJs 库之类的库来[监听事件](https://docs.ethers.io/v5/getting-started/#getting-started--events)和[过滤历史事件](https://docs.ethers.io/v5/getting-started/#getting-started--history)。当您的应用程序需要响应区块链上的事件时，这两者都很有用。

## Solidity 中的时间逻辑

Solidity 中的时间是根据添加到区块链的每个块指定的。

全局变量`block.timestamp`是指区块生成并添加到区块链的时间，以毫秒为单位。毫秒计数是指自 Unix 纪元开始以来经过的毫秒数（在计算中，这是 1970 年 1 月 1 日）。

与 Web2 以毫秒为单位引用时间戳不同，该值可能不会每毫秒增加一次。

一个块通常包含多个事务，并且由于`block.timestamp`指的是块被挖掘的时间，因此被挖掘的块中的所有事务将具有相同的时间戳值。所以时间戳实际上指的是块的时间，而不是调用者发起交易的时间。

Solidity 支持直接引用以下时间单位：秒、分钟、小时、天和周。

所以我们可以做一些事情，比如`uint lastWeek = block.timestamp - 1 weeks;`计算当前区块被开采前 1 周的时间戳，精确到毫秒。该值将与`block.timestamp - 7 days;`.

您还可以使用它来计算未来的到期日期，例如，您可能希望在现在和下周之间可以进行操作。你可以这样做`uint registrationDeadline = block.timestamp + 1 weeks;`，然后我们可以`registrationDeadline`在函数中使用 作为验证或保护，如下所示：

```solidity
function registerVoter(address voter) public view {
        require(block.timestamp <= registrationDeadline, “registration deadline has passed.”);
        
        // Register the Voter....
    }
```

在此函数中，我们仅在当前区块的时间戳未超过注册截止日期时才注册选民。

当我们想要确保某些操作在正确的时间或在一定时间间隔内执行时，会广泛使用此逻辑。

这也是可以配置[Chainlink Automation](https://docs.chain.link/chainlink-automation/introduction/)的方式之一，这是一种自动执行智能合约的去中心化方式。Chainlink 去中心化预言机网络可以配置为自动触发您的智能合约，您可以通过检查条件（包括与时间相关的条件）来运行各种自动化。这些被广泛用于空投、促销、特殊奖励、赚取收益等。

## 结论和更多资源

恭喜！你完成了这段史诗般的旅程。如果您花时间消化了本手册，并在 Remix IDE 中运行了一些代码，那么您现在已经接受了 Solidity 培训。

从这里开始，这是一个练习、重复和经验的问题。当您着手构建下一个出色的去中心化应用程序时，请记住重新审视基础知识并关注安全性。安全性在 Web3 空间中尤为重要。

您可以从[OpenZeppelin 的博客](https://blog.openzeppelin.com/)和[Trail of Bits](https://www.trailofbits.com/resources)资源等获取有关最佳实践的良好信息。

您还可以通过完成我的同事 Patrick Collins 在 freeCodeCamp 上发布的完整的[端到端全栈区块链开发人员课程](https://www.youtube.com/watch?v=gyMwXuJrbJQ&t=59657s)（它是免费的！）来获得更多实践经验。

还有其他资源，例如 blockchain.education 和 freeCodeCamp 自己即将推出的 Web3 课程，可以巩固您的学习。  

无论如何，无论您的经验水平如何，本手册都可以成为您快速复习基本概念的“桌面伴侣”。

要记住的重要一点是 Web3 技术总是在不断发展。迫切需要愿意应对复杂挑战、学习新技能并解决分散式架构带来的重要问题的开发人员。  

那可能（也应该）是你！所以只要跟随你的好奇心，不要害怕一路上的挣扎。

再一次，我打算不断更新这本手册。因此，如果您发现任何不太正确、过时或不清楚的内容，只需在推文中提及并[标记我](https://twitter.com/zubinpratap)和 freeCodeCamp - 向你们致敬，因为你们是保持这本手册新鲜的一部分。

现在……去做个了不起的人吧！

> 原文：https://www.freecodecamp.org/news/learn-solidity-handbook/

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)