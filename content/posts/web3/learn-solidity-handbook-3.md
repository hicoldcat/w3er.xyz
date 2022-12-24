---
title: 学习 Solidity——智能合约开发手册(3)
description: null
author: 李留白
weight: 0
date: 2022-12-24T02:32:24.761Z
lastmod: 2022-12-24T02:32:36.654Z
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

## Solidity数据类型

内置于语言中并“开箱即用”的类型通常被称为“原语”。它们是语言固有的。您可以组合基本类型以形成更复杂的数据结构，这些数据结构成为“自定义”数据类型。

例如，在 JavaScript 中，[原语](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)是是不属于JS对象*并且*没有方法或属性的数据。JavaScript 中有 7 种原始数据类型：`string`、`number`、`bigint`、`boolean`、`undefined`、`symbol`和`null`。

Solidity 也有自己的原始数据类型。有趣的是，Solidity 没有*“* undefined”或“null”。相反，当您声明一个变量及其类型，但不为其分配值时，Solidity 将为该类型分配一个[默认值](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#default-value)。该默认值究竟是什么取决于数据类型。

Solidity 的许多原始数据类型都是相同“基本”类型的变体。例如，类型本身具有基于该类型可以容纳`int`的二进制位数的子类型。`integer`

如果这让您有点困惑，请不要担心 - 如果您不熟悉位和字节，这并不容易，我将很快介绍整数。

在我们探索 Solidity 类型之前，您必须了解另一个非常重要的概念 - 它是编程语言中许多错误和“意外陷阱”的来源。

这就是值类型和引用类型之间的区别，以及程序中数据“按值传递”与“按引用传递”之间的区别。[我将在下面进行快速总结，但您可能还会发现在继续之前观看这段简短的视频](https://www.youtube.com/watch?v=1HHjKG1372E)以加强您的思维模型很有用。

### 引用传递 vs 值传递

在操作系统级别，当程序运行时，程序在执行期间使用的所有数据都存储在计算机 RAM（内存）中的位置。当您声明一个变量时，会分配一些内存空间来保存有关该变量的数据以及分配给或最终分配给该变量的值。  

还有一种数据，就是常说的“指针”。该指针指向可以找到该变量及其值的内存位置（计算机 RAM 中的“地址”）。因此，指针实际上包含了对计算机内存中数据所在位置的  *引用。*

因此，当您在程序中传递数据时（例如，当您将值分配给新变量名称时，或者当您将输入（参数）传递给函数或方法时，语言的编译器可以通过两种方式实现这一点。它可以通过指向计算机内存中数据位置的指针，**或者**它可以复制数据本身，并传递实际值。

第一种方法是“通过引用传递”。第二种方法是“按值传递”。

Solidity 的数据类型基元分为两类——它们要么是值类型，要么是引用类型。

换句话说，在 Solidity 中，当你传递数据时，数据的类型将决定你传递的是值的副本还是对值在计算机内存中位置的引用。  

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221224093128.png)

Solidity 中的值类型和引用类型

在 Solidity 的“值类型”中，整数分为两类——`uint`是无符号的（只有正整数，所以它们没有正负号）和`int`是有符号的（可以是正数或负数，如果你把它们写下来，它们会有加号或减号）。

整数类型还可以指定它们有多少位长 - 或者有多少位用于表示`integer`.  

`uint8`是由8位二进制数字（位）表示的整数，最多可以存储256个不同的值（2^8=256）。由于`uint`是无符号（正）整数，这意味着它可以存储从 0 到 255（不包括 1 到 256）的值。

但是，当您有带符号的整数时，例如`int8`，其中一位将用完以表示它是正数还是负数。这意味着我们只剩下 7 位，因此我们最多只能表示 2^7 (128) 个不同的值，包括 0。因此 一个`int8`可以表示从 -127 到 +127 的任何值。

通过扩展，一个`int256`长度为 256 位，可以存储 +/- (2^255) 值。

位长度是[8 的倍数](https://docs.soliditylang.org/fr/latest/types.html#integers)（因为 8 位构成一个字节），因此您可以一直`int8`、`int16`、`int24`到256（32 字节）.

地址指的是以太坊账户类型——智能合约账户或外部拥有账户（又名“EOA”。你的 Metamask 钱包代表一个 EOA）。所以地址也是 Solidity 中的一种类型。  

地址的默认值（如果您声明一个地址类型的变量但不为其分配任何值，它将具有的值）`0x0000000000000000000000000000000000000000`也是此表达式的结果：`address(0)`。

布尔值表示真值或假值。最后，我们有[固定大小的字节数组](https://docs.soliditylang.org/en/v0.8.17/types.html#fixed-size-byte-arrays)，例如`bytes1`, `bytes2` … `bytes32`。这些是包含字节的固定长度数组。所有这些类型的值在代码中传递时都会被复制。

对于“引用类型”，我们有数组，它们可以在声明时指定固定大小，或者动态大小的数组，它们以固定大小开始，但可以“调整大小”为数据元素的数量数组增长。  

字节是一种低级数据类型，指的是编码为二进制格式的数据。编译器最终将所有数据还原为二进制形式，以便 EVM（或者在传统计算中，处理器）可以使用它。  

与其他更易读的数据类型相比，存储和使用字节通常更快、更高效。

您可能想知道为什么我没有在上图中的任何一种数据类型中引用字符串。这是因为在 Solidity 中，字符串实际上是动态大小的数组，数组存储以 UTF-8 编码格式编码的字节序列（只是二进制数）。

他们不是 Solidity 中的原始人。在 JavaScript 中，它们被称为原语，但即使在 JavaScript 中，字符串也与数组相似（但不相同）并且[是整数值的序列](https://tc39.es/ecma262/#sec-ecmascript-language-types-string-type)，以 UTF-16 编码。

在智能合约中将`string`作为`bytes`类型存储通常更有效，因为在`strings`和`bytes`之间进行转换非常容易。因此，存储`strings`为`bytes`但在函数中将它们作为字符串返回是很有用的。您可以在下面看到一个示例：

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;


contract StringyBytes {

     // Returns 0x5a7562696e when passed the string input “Zubin”
    function stringIntoBytes(string memory input) public pure returns (bytes memory ){
        return bytes(input);
    }


    // Returns “Zubin” when passed the bytes input “0x5a7562696e”
    function bytesIntoString(bytes memory input) public pure returns (string memory ){
        return string(input);
    }
}
```

除了 Solidity 字符串，`bytes`数据类型是[动态大小的字节数组](https://docs.soliditylang.org/en/v0.8.17/types.html#dynamically-sized-byte-array)。此外，与它的固定大小字节数组表亲不同，它是一种引用类型。Solidity 中的`bytes`类型是“字节数组”的简写，在程序中可以写成`bytes`或`byte[]`。

如果您对字节和字节数组感到困惑……我表示同情。

字符串和字节数组的底层细节与本手册不太相关。现在的重点是一些数据类型通过引用传递，而另一些数据类型通过复制它们的值来传递。

可以说没有指定大小的 Solidity 字符串和字节是引用类型就足够了，因为它们都是动态大小的数组。

最后，在 Solidity 的原语中，我们有`structs`和`mappings`。有时这些被称为“复合”数据类型，因为它们是由其他原语组成的。  

一个`struct`将一段数据定义为具有一个或多个属性或属性，并指定每个属性的数据类型和名称。结构使您能够定义自己的自定义类型，以便您可以将数据片段组织和收集到一个更大的数据类型中。

例如，您可以拥有定义 `Person` 的结构，如下所示：

```solidity
struct Person {
    string name;
    uint age;

    bool isSolidityDev;

    Job  job  // Person struct includes a custom type of Job
}


struct Job {

    string employer;

    string department;

    boolean isRemote;

}
```

您可以通过以下方式实例化或初始化`Person`结构：

```solidity
// dot notation updating. Job struct is uninitialized
// which means that its properties will have their respect default values
Person memory p;
P.name = "Zubin"
p.age = 41;
p.isSolidityDev = true;

// Or in a function-style call. Note I'm initializing a Job struct too!
Person p =  Person("Zubin",  "41", "true", Job("Chainlink Labs", "DevRel", true));

// Or in a key-value style
Job j = Job({ employer: "Chainlink Labs", "DevRel",  true});
p.job = j // this is done in dot notation style.
```

映射类似于hashtables、字典或 JavaScript 对象和地图，但功能少一些。  

`mapping`也是一个键值对，对于可以作为键的数据类型有限制，你可以在[这里](https://docs.soliditylang.org/en/v0.8.17/types.html#mapping-types)阅读。与映射键关联的数据类型可以是任何原语、结构，甚至是其他映射。

以下是映射的声明、初始化、写入和读取方式——以下示例来自[Chainlink Link Token Smart Contract 源代码](https://github.com/smartcontractkit/LinkToken/blob/f307ea6d4c02dd87ea4c1f1032e3e384ceac20c2/contracts/v0.4/token/linkStandardToken.sol)。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221224095551.png)

在Solidity中声明和使用Mappings类型

如果您尝试使用映射中不存在的键访问值，它将返回存储在映射中的类型的[默认值。](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#default-value)

在上面的示例中，`balances`映射中所有值的类型都是`uint256`，其默认值为`0`。因此，如果我们调用`balanceOf()`并传入一个没有任何 LINK 令牌的地址，我们将得到返回值`0`。  

这在这个例子中是合理的，但是当我们想要找出一个键是否存在于映射中时，它可能有点棘手。

目前还没有办法枚举映射中存在哪些键（也就是说，没有什么等同于[JavaScript 的`Object.keys()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)方法）。使用键检索只会返回与数据类型关联的默认值，这并不能清楚地告诉我们该键是否实际存在。

映射有一个有趣的“陷阱”。与您可以将键值数据结构作为参数传递给函数的其他语言不同，Solidity 不支持将映射作为参数传递给函数，除非函数可见性标记为`internal`。因此，您无法编写接受键值对作为参数的外部或公共可调用函数。

## 如何在 Solidity 中声明和初始化数组

Solidity 带有两种类型的数组，因此了解声明和初始化它们的不同方式很有用。

Solidity 中的两种主要数组类型是固定大小数组和动态大小数组。

为了刷新您的记忆，固定大小的数组按值传递（在代码中传递时复制），动态大小的数组按引用传递（指向内存地址的指针在代码中传递）。

它们的语法和容量（大小）也不同，这决定了我们何时使用其中一种。

这是固定大小的数组在声明和初始化时的样子。它的固定容量为 6 个元素，一旦声明就不能更改。6 个元素的数组的内存空间已分配且无法更改。

```solidity
string[6] fixedArray; // Max capacity is 6 elements.


fixedArray[0] = ‘a’; // first element set to ‘a’

fixedArray[4]=‘e’; // 5th element set to ‘e’

fixedArray.push(‘f’) // Not OK. Push() not available for fixed-size arrays.


fixedArray[5]=‘f’; // 6th element set to ‘f’

fixedArray[6]=‘g’; // Not OK.  Exceeds array’s fixed size.
```

也可以通过使用以下语法声明一个变量和数组的大小及其元素的类型来声明固定大小的数组：

```solidity
// datatype arrayName[arraySize];

string myStrings[10]; // string array of size 10.

myStrings[0] = “chain.link”;
```

将其与按如下方式声明和初始化的动态大小数组进行对比。它的容量是不确定的，您可以使用 push() 方法添加元素：

```solidity
uint[] dynamicArray;


// Push appends a value to the array

// The array length is increased by 1.

dynamicArray.push(1);
dynamicArray.push(2);
dynamicArray.push(3);

// dynamicArray is now [1,2,3]

dynamicArray.length; // 3


// Pop removes the last element.

// The array length is reduced by 1.

uint lastNum = dynamicArray.pop() 

dynamicArray.length; // 2


// The delete keyword resets the value at the index to its default value

delete dynamicArray[1]; // second element is no longer 2 but 0.
```

您还可以在同一行代码中声明和初始化数组的值。  

```solidity
string[3] fixedArray = ["a", "b", "c"]; // Fixed sized string array
fixedArray.push("abc"); // Won't work for fixed size arrays.

String[] dynamicArray =["chainlink", "oracles"]; /// Dynamic sized array
dynamicArray.push("rocks");  // Works.
```

这些阵列在存储中可用。但是，如果您只需要函数内的临时内存数组怎么办？在这种情况下，有两条规则：只允许使用固定大小的数组，并且您必须使用`new`关键字。

```solidity
function inMemArray(string memory firstName, string memory lastName)
        public
        pure
        returns (string[] memory)
    {

        // New in memory fixed array of size 2.
        string[] memory arr = new string[](2);
        arr[0] = firstName;
        arr[1] = lastName;
        return arr;
    }
```

显然，有几种方法可以声明和初始化数组。当您想要针对 gas 和计算进行优化时，您需要仔细考虑需要哪种类型的阵列、它们的容量是多少，以及它们是否可能在没有上限的情况下增长。  

这也会影响您的代码设计并受其影响——您是需要存储数组还是只需要内存中的数组。

## 什么是函数修饰符？

在编写函数时，我们通常会收到一些输入，这些输入需要在我们继续处理其余“业务”逻辑之前对这些输入进行某种验证、检查或其他逻辑运行。  

例如，如果您使用纯 JavaScript 编写，您可能需要检查您的函数接收的是整数而不是字符串。如果它在后端，您可能需要检查 POST 请求是否包含正确的身份验证标头和密码。  

在 Solidity 中，我们可以通过声明一个称为修饰符的类似函数的代码块来执行这些类型的验证步骤。

修饰符是一段代码，可以在运行主函数（即应用了修饰符的函数）之前或之后自动运行。

修饰符也可以从父合约继承。它通常用作避免重复代码的一种方法，方法是提取通用功能并将其放入可在整个代码库中重用的修饰符中。

[修饰符](https://docs.soliditylang.org/en/v0.8.17/contracts.html#modifiers)看起来很像函数。观察修饰符的关键是`_`（下划线）出现的位置。该下划线就像一个“占位符”，指示主函数何时运行。它看起来好像我们在当前下划线所在的位置插入了主函数。  

因此，在下面的修饰符片段中，我们运行条件检查以确保消息发送者是合约的所有者，*然后*我们运行调用此修饰符的函数的其余部分。请注意，单个修饰符可以由任意数量的函数使用。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221224100227.png)

函数修饰符怎么写，以及下划线符号的作用

在此示例中，`require()`语句在下划线 ( `changeOwner()`) 之前运行，这是确保只有当前所有者才能更改谁拥有合同的正确方法。

如果您切换修饰符的行并且`require()`语句排在第二位，那么 `changeOwner()`中的代码将首先运行。只有在那之后`require()`语句才会运行，那将是一个非常不幸的错误！

修饰符也可以接受输入——您只需将输入的类型和名称传递给修饰符即可。

```solidity
modifier validAddress(address addr) {
    // address should not be a zero-address.
    require(addr != address(0), "Address invalid");
    

    // continue with the rest of the logic
    _; 
}

function transferTokenTo(address someAddress) public validAddress(someAddress) {
    // do something....
}
```

修饰符是打包逻辑片段的好方法，这些逻辑片段可以在各种智能合约中重复使用，这些智能合约共同为您的 dApp 提供动力。重用逻辑使您的代码更易于阅读、维护和推理——因此遵循[DRY](https://en.wikipedia.org/wiki/Don't_repeat_yourself)（不要重复自己）原则。

## Solidity 中的错误处理——require, assert, revert

Solidity 中的错误处理可以通过几个不同的关键字和操作来实现。

当出现错误时，EVM 将恢复对区块链状态的所有更改。换句话说，当抛出异常并且未在 try-catch 块中捕获时，该异常将“冒泡”被调用的方法堆栈, 并返回给用户。当前调用（及其子调用）中对区块链状态所做的所有更改都将被撤销。

有一些例外情况，在`delegatecall`、`send`、`call`等低级函数中，错误将返回布尔值`false`给调用者，而不是冒出一个错误。

作为开发人员，您可以采用三种方法来处理和抛出错误。您可以使用`require()`,`assert()`或`revert()`。

require 语句评估您指定的布尔条件，如果为假，它将抛出没有数据或您提供的字符串的错误：

```solidity
function requireExample() public pure {
    require(msg.value >= 1 ether, "you must pay me at least 1 ether!");
}
```

我们使用 `require()` 来验证输入，验证返回值，以及在我们进行代码逻辑之前检查其他条件。

在此示例中，如果函数的调用者未发送至少 1 个以太币，则该函数将恢复并抛出一条错误消息：“*你必须至少付给我 1 个以太币！*”。

您要返回的错误字符串是`require()`函数的第二个参数，但它是可选的。没有它，您的代码将抛出一个没有数据的错误——这不是很有帮助。

`require()`的好处是，它将返回尚未使用的气体，但`require()`语句之前使用的气体将丢失。 这就是为什么我们尽可能早地使用`require()`。

`assert()`函数与`require()`非常相似，只是它[抛出的错误类型是`Panic(uint256)`而不是`Error(string)`](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#panic-via-assert-and-error-via-require)。

```solidity
contract ThrowMe {   
    function assertExample() public pure {
        assert(address(this).balance == 0);
	  // Do something.
    }
}
```

断言也用于略有不同的情况——需要不同类型的保护。

大多数情况下，您使用断言来检查“不变”的数据片段。在软件开发中，不变量是一个或多个数据，其值在程序执行时永远不会改变。

在上面的代码示例中，合约是一个微型合约，并不是为了接收或存储任何以太币而设计的。它的设计旨在确保它始终具有零合约余额，这是我们使用断言测试的不变量。

`Assert()`调用也用于内部函数。他们测试本地状态不包含意外或不可能的值，但由于合约状态变得“脏”，这些值可能已经改变。

就像`require()`一样， `assert()`也将恢复所有的变化。在 Solidity 的 v0.8 之前，`assert()`用于耗尽所有剩余的气体，这与`require()`.

一般来说，您可能会使用`require()`超过`assert()`.

第三种方法是使用`revert()`调用。这通常用于与 `require()`相同的情况，但您的条件逻辑要复杂得多。

此外，您可以在使用`revert()`. 你可以抛出自定义定义的错误。使用自定义错误通常可以在气体使用方面更便宜，而且从代码和错误可读性的角度来看，通常信息量更大。

请注意我是如何通过在自定义错误名称前加上合约名称来提高错误的可读性和可追溯性的，这样我们就知道是哪个合约引发了错误。

```solidity
contract ThrowMe {   
    // custom error
    error ThrowMe_BadInput(string errorMsg, uint inputNum);

    function revertExample(uint input) public pure {
        if (input < 1000 ) {
	    revert ThrowMe_BadInput("Number must be an even number greater than 999",  input);
       }

      if (input < 0) {
          revert("Negative numbers not allowed");
      }
    }
}
```

在上面的例子中，我们使用了一次`revert`，其中有一个需要两个特定参数的自定义错误，然后我们又使用了一次`revert`，只有一个字符串错误数据。在任何一种情况下，区块链状态都会被还原，未使用的气体将被返回给调用者。

## Solidity 中的继承

[继承](https://www.freecodecamp.org/news/inheritance-in-java-explained/)是面向对象编程 (OOP) 中的一个强大概念。我们不会在这里详细介绍[OOP](https://www.freecodecamp.org/news/object-oriented-concepts/)是什么。但是，在编程中推理继承的最佳方式是将其视为代码片段通过导入和嵌入其他代码片段“继承”数据和函数的一种方式。  

Solidity中的继承还允许开发人员访问、使用和修改继承自合约的属性（数据）和功能（行为）。

接收这种继承材料的合约称为派生合约、子合约或子类。其材料可用于一个或多个派生合同的合同称为父合同。  

继承促进了方便和广泛的代码重用——想象一下从其他代码继承的应用程序代码链，这些代码又从其他代码继承，等等。我们可以只使用几个关键字来“扩展”继承链中所有应用程序代码捕获的功能和数据，而不是打出整个继承层次结构。这样子合约就可以从其层次结构中的所有父合约中受益，就像每一代都继承下来的基因一样。

与 Java 等某些编程语言不同，Solidity 允许多重继承。多重继承是指派生合约能够从多个父合约继承数据和方法。换句话说，一个子合约可以有多个父合约。

您可以通过查找关键字`is`来发现子合约并识别其父合约。

```solidity
contract A {
    string public constant A_NAME = "A";

    function getName() public pure returns (string memory) {
        return A_NAME;
    }
}

contract B is A {
    string public constant B_NAME = "B";
}
```

[如果您使用浏览器内的Remix IDE](http://remix.ethereum.org/)仅部署合同 B，您会注意到合同 B 可以访问该`getName()`方法，即使它从未作为合同 B 的一部分编写。当您调用该函数时，它返回“A ”，这是在合约 A 中实现的数据，而不是合约 B。合约 B 可以访问存储变量`A_NAME`和`B_NAME`，以及合约 A 中的所有函数。

这就是继承的工作原理。这就是合约 B 如何重用合约 A 中已经编写的代码，这些代码可能是由其他人编写的。

Solidity 允许开发人员更改父合约中的函数在派生合约中的实现方式。修改或替换继承代码的功能称为“覆盖”。为了理解它，让我们探讨一下当合约 B 尝试实现自己的`getName()`功能时会发生什么。

通过在合约 B中添加 a 来修改代码`getName()`。确保函数名称和签名与合约 A 中的相同。子合约在`getName()`函数中的逻辑实现可以与在父合约中的实现方式完全不同，只要因为函数名称及其签名是相同的。

```solidity
contract A {
    string public constant A_NAME = "A";

    function getName() public returns (string memory) {
        return A_NAME;
    }
}

contract B is A {
    string public constant B_NAME = "B";

    function getName() public returns (string memory) {

     // … any logic you like. Can be totally different

     // from the implementation in Contract A.
        return B_NAME;
    }
}
```

编译器会给你两个错误：

1. 在合约 A 中，它会提示你“*trying to override non-virtual function*”，并通过询问你是否忘记添加`virtual`关键字来提示你。
2. 在合同 B 中，它会抱怨`getName()`函数缺少说明`override`符。

这意味着您在合约 B 中的 new `getName`试图覆盖父合约中同名的函数，但父合约的函数未标记为`virtual`– 这意味着它无法被覆盖。

您可以更改合约 A 的功能并添加`virtual`如下内容：

```solidity
function getName() public virtual returns (string memory) {
    return A_NAME;
 }
```

添加关键字`virtual`不会改变函数在合约 A 中的运行方式。并且它不要求继承合约**必须**重新实现或覆盖它。它只是意味着如果开发人员选择，此功能**可能**会被任何派生合约覆盖。

添加`virtual`修复了编译器对合约 A 的投诉，但不是对合约 B 的投诉。这是因为`getName`在合约 B 中还需要添加`override`关键字，如下所示：

```solidity
 function getName() public pure override returns (string memory) {
    return B_NAME;
 }
```

我们还为合约 B 的 getName() 添加`pure`关键字，因为此函数不会更改区块链的状态，并从常量中读取（常量，你会记得，在编译时被硬编码到字节码中，不在存储中数据位置）。

请记住，如果名称和签名相同，您只需要覆盖一个函数。

但是对于名称相同但参数不同的函数会发生什么情况呢？当这种情况发生时，它不是覆盖，而是超载。并且没有冲突，因为这些方法有不同的参数，所以它们的签名中有足够的信息向编译器表明它们是不同的。  

例如，在合约 B 中，我们可以有另一个接受参数的函数`getName()`，与父合约 A 的`getName()`实现相比，它有效地为函数提供了不同的“签名”。重载函数不需要任何特殊关键字：

```solidity
// getName() now accepts a string argument. 

// Passing in “Abe Lincoln” returns the string “My name is: Abe Lincoln”

function getName(string memory name) public pure returns (string memory) {
    bytes memory n = abi.encodePacked("My name is:  ", name);
    return string(n); 
}
```

不用担心`abi.encodepacked()`方法调用。稍后当我们谈论编码和解码时，我会解释这一点。现在只需了解`encodepacked()`将字符串编码为字节，然后将它们连接起来，并返回一个字节数组。  

我们在本手册的前一节（在类型下）讨论了 Solidity 字符串和字节之间的关系。

此外，由于您已经了解了函数修饰符，因此这是添加修饰符也是可继承的一个好地方。以下是您的操作方式：

```solidity
contract A {
    modifier X virtual {
        // … some logic

    }
}

contract B is A {
    modifier X override {
        // … logic that replaces X in Contract A

    }
}
```

您可能想知道如果继承链中存在具有相同名称和签名的函数，将调用哪个版本的函数。  

例如，假设有一条继承合约链，如A→B→C→D→E，它们都有一个`getName()`，它覆盖了前一个父合约中的`getName()`。

哪个`getName()`被调用？答案是最后一个——合约层次结构中“most derived”的实现。

子合约中的状态变量不能与其父合约具有相同的名称和类型。  

例如，下面的合约 B 将无法编译，因为它的状态变量“隐藏”了父合约 A 的状态变量。但请注意合约 C 如何正确处理此问题：

```solidity
contract A {
    string public author = "Zubin";

    function getAuthor() public virtual returns (string memory) {
        return author;
    }
}


// Contract B would not compile

contract B is A {

    // Not OK.  author shadows the state variable in Contract A!
    string public author = "Mark Twain";
}


// This will work.
contract C is A {
    constructor(){
        author = "Hemingway";
    }
}
```

值得注意的是，通过在合同C的构造函数中向变量`author`传递一个新的值，我们实际上是覆盖了合同A中的值。然后调用继承的方法`C.getAuthor()`将返回'Hemingway'而不是'Zubin'!

还值得注意的是，当一个合约继承自一个或多个父合约时，区块链上只会创建一个（组合）合约。编译器有效地将所有其他合约及其父合约等编译成一个单一的编译合约（称为“扁平化”合约）。


（未完待续）

> 原文：https://www.freecodecamp.org/news/learn-solidity-handbook/

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)