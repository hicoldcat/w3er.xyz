---
title: 学习 Solidity——智能合约开发手册(4)
description: null
author: 李留白
weight: 0
date: 2022-12-26T01:21:22.408Z
lastmod: 2022-12-26T01:59:36.096Z
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

## 带有构造函数参数的继承

一些构造函数指定输入参数，因此它们需要您在实例化智能合约时将参数传递给它们。  

如果该智能合约是父合约，则其派生合约也必须传递参数以实例化父合约。  

有两种方法可以将参数传递给父合约——在列出父合约的语句中，或者直接在每个父合约的构造函数中。您可以在下面看到这两种方法：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221226092536.png)

在合约的方法 2 `ChildTwo`中，您会注意到传递给父合约的参数首先提供给子合约，然后才向上传递到继承链。  

这不是必需的，但却是一种非常常见的模式。关键是父合约构造函数期望将数据传递给它们的地方，我们需要在实例化子合约时提供它们。

## Solidity 中的类型转换和类型构造

有时我们需要将一种数据类型转换为另一种数据类型。当我们这样做时，我们需要非常小心地转换数据以及计算机如何理解转换后的数据。  

正如我们在关于类型化数据的讨论中看到的那样，JavaScript 有时会对数据做一些奇怪的事情，因为它是动态类型化的。但这也是为什么一般介绍类型转换和类型转换的概念很有用。

采用以下 JavaScript 代码：

```solidity
var a = "1"
var b = a +  9 // we get the string '19'!!
typeof a // string
typeof b // string 
```

有两种方法可以将变量`a`转换为整数。第一个称为类型转换，由程序员显式完成，通常涉及一个类似构造函数的运算符，它使用`()`.

```solidity
a = Number(a) // Type casting the string to number is explicit.
typeof a // number
```

```solidity
var b = a +  9 // 10. A number. More intuitive!
```

现在让我们重置`a`为字符串并进行隐式转换，也称为类型转换。这是编译器在执行程序时隐式完成的。

```solidity
a = '1'
var b = a * 9 // Unlike addition, this doesn't concatenate but implicitly converts 'a' to a number! 


b // number 9, as expected!
typeof b // number

typeof a // still a string…
```

在 Solidity 中，类型转换（显式转换）在某些类型之间是允许的，并且看起来像这样：

```solidity
uint256 a = 2022;
bytes32 b = bytes32(a);


// b now has a value of 
// 0x00000000000000000000000000000000000000000000000000000000000007e6

// which is 32 bytes (256) bits of data represented in 
// 64 Hexadecimal Characters, where each character is 4 bits (0.5 bytes).
```

在此示例中，我们将大小为 256 位的整数（因为 8 位构成 1 个字节，这是 32 个字节）转换为大小为 32 的字节数组。

由于 2022 的整数值和字节值的长度均为 32 字节, 转换中没有信息“丢失”。

但是，如果您尝试将 256 位转换为 18 位（1 字节），会发生什么情况？尝试在基于浏览器的 Remix IDE 中运行以下命令：

```solidity
contract Conversions {
   function explicit256To8() public pure returns (uint8) {
       uint256 a = 2022;
       uint8 b = uint8(a);
       return b; // 230.   

   }
}
```

为什么整数 2022 会转换为 230？这显然是一个不受欢迎且意外的价值变化。一个错误，对吧？

原因是大小为 256 位的无符号整数将包含 256 个二进制数字（0 或 1）。因此`a`保留整数值“2022”，该值（以位为单位）将有 256 位数字，其中大部分为 0，除了最后 11 位数字将是...（通过将 2022 从十进制系统转换为[这里](https://decimaltobinary.com/256-in-binary)是二进制）。

另一方面，`b`的值将只有 8 位或数字，即 11100110。这个二进制数在转换为十进制时（您可以使用相同的转换器 - 只需填写另一个框！）是 230。不是 2022。

哎呀。

所以发生了什么事？当我们将整数的大小从 256 位减少到 8 位时，我们最终去掉了数据的前三位数字 (11111100110)，这完全改变了二进制值！

伙计们，这是信息丢失。

因此，当您显式转换时，编译器在某些情况下会让您这样做。但是您可能会丢失数据，并且编译器会假设您知道自己在做什么，因为您明确要求这样做。这可能是许多错误的根源，因此请确保正确测试代码以获得预期结果，并在将数据显式转换为较小尺寸时要小心。  

投射到更大的尺寸不会导致数据丢失。由于 2022 只需要 11 位来表示，您可以将变量`a`声明为类型`uint16`，然后将其向上转换为`uint256`类型`b`变量而不会丢失数据。

另一种有问题的转换是当您从无符号整数转换为有符号整数时。尝试以下示例：

```solidity
contract Conversions {
   function unsignedToSigned() public pure returns (int16, uint16) {
       int16 a = -2022;
       uint16 b = uint16(a);
       // uint256 c = uint256(a); // Compiler will complain
       return (a, b); // b is 63514
   }
}
```

请注意，`a`作为大小为 16 位的有符号整数，将 -2022 保存为（负整数）值。如果我们明确地将其类型转换为*无符号*整数（仅正数）值，编译器会让我们这样做。

但是如果你运行代码，你会发现`b`不是-2022 而是 63,514！因为`uint`无法保存有关减号的信息，它丢失了该数据，并且生成的二进制被转换为大量十进制（以 10 为底）数字 - 显然不受欢迎，并且是一个错误。

如果你更进一步，取消`c`注释分配值的行，你会看到编译器抱怨“*不允许从“int16”到“uint256”的显式类型转换”*。即使我们在`uint256`向上转换到更多的位，因为`c`是一个无符号整数，它不能包含负号信息。

因此，在显式转换时，请务必考虑在强制编译器更改数据类型后该值的计算结果。它是许多错误和代码错误的根源。

[Solidity 类型转换和类型转换还有更多内容，您可以在本文中深入了解一些细节](https://betterprogramming.pub/solidity-tutorial-all-about-conversion-661130eb8bec)。

## 如何在 Solidity 中使用浮点数

Solidity 并不处理小数点。这在未来可能会改变，但目前您无法真正使用固定（浮动）点数，如 93.6。事实上，在您的 Remix IDE 中键入`int256 floating = 93.6;` 会抛出如下错误：*Error: Type rational_const 468 / 5 is not implicitly convertible to expected type int256*

这里发生了什么？468 除以 5 是 93.6，这似乎是一个奇怪的错误，但这基本上是编译器说它不能处理浮点数。

按照错误的提示，将变量的类型声明为`fixed`或`ufixed16x1`。

```solidity
fixed floating = 93.6;
```

您将收到“ *UnimplementedFeatureError: Not yet implemented - FixedPointType* ”错误。

因此，在 Solidity 中，我们通过将浮点数乘以 10 提高到小数点右侧小数位数的指数，将浮点数转换为整数（无小数点）来解决这个问题。  

在这种情况下，我们将 93.6 乘以 10 得到 936，我们必须在某处的变量中跟踪我们的因子 (10)。如果数字是 93.2355，我们会将其乘以 10 的 4 次方，因为我们需要将小数点右移 4 位以使数字完整。

使用 ERC 代币时，我们会注意到小数位通常为 10、12 或 18。

例如，1 Ether 是 1*(10^18) wei，即 1 后跟 18 个零。如果我们想用浮点数表示，我们需要将 1000000000000000000 除以 10^18（这将得到 1），但如果它是 1500000000000000000 wei，那么除以 10^18 将在 Solidity 中抛出编译器错误，因为它无法处理 1.5 的返回值。

在科学计数法中，10^18 也表示为 1e18，其中 1e 表示 10，后面的数字表示 1e 的指数。

所以下面的代码会产生一个编译器错误：“*Return argument type rational_const 3 / 2 is not implicitly convertible to expected type…int256* ”：

```solidity
function divideBy1e18()public pure returns (int) {
    return 1500000000000000000/(1e18); // 1.5 → Solidity can’t handle this.

}
```

上述除法运算的结果是 1.5，但是有小数点，Solidity 目前不支持。因此 Solidity 智能合约返回非常大的数字，通常最多 18 位小数，这超出了 JavaScript 的处理能力。因此，您需要在前端使用 JavaScript 库（例如为 [BigNumber](https://docs.ethers.io/v5/api/utils/bignumber/) 类型实现辅助函数的 Ethersjs）适当地处理它。

## 哈希、ABI 编码和解码

随着您更多地使用 Solidity，您会看到一些听起来很奇怪的术语，例如哈希、ABI 编码和 ABI 解码。

虽然这些可能需要花费一些精力来理解，但它们对于使用加密技术（尤其是以太坊）来说是非常基础的。它们原则上并不复杂，但一开始可能有点难以掌握。

让我们从哈希开始。使用加密数学，您可以将**任何**数据转换为（非常大的）唯一整数。此操作称为散列。哈希算法有一些关键属性：

1. 它们是确定性的——相同的输入每次**都会产生相同的输出。**但是使用不同的输入产生相同输出的机会是极不可能的。
2. 如果您只有输出，则不可能（或计算上不可行）对输入进行逆向工程。这是一个单向过程。
3. 输出的大小（长度）是固定的——无论输入大小如何，算法都会为所有输入生成固定大小的输出。换句话说，哈希算法的输出将始终具有固定的位数，具体取决于算法。

有许多算法是哈希的行业标准，但您可能会最常见地看到 SHA256 和 Keccak256。这些非常相似。256 指的是大小——生成的散列中的位数。

例如，转到[此站点](https://emn178.github.io/online-tools/keccak_256.html)并将“FreeCodeCamp”复制并粘贴到文本输入中。使用 Keccak256 算法，输出将（始终）为`796457686bfec5f60e84447d256aba53edb09fb2015bea86eb27f76e9102b67a`.

这是一个 64 字符的十六进制字符串，由于十六进制字符串中的每个字符代表 4 位，因此该十六进制字符串为 256 位（32 字节长）。

现在，删除文本输入框中除“F”之外的所有内容。结果是一个完全不同的十六进制字符串，但它仍然有 64 个字符。这是 Keccak265 哈希算法的“固定大小”性质。

现在粘贴回“FreeCodeCamp”并更改任何字符。你可以把“F”变成小写。或者加一个空格。对于您所做的每个单独更改，哈希十六进制字符串输出都会发生很大变化，但大小不变。

这是散列算法的一个重要好处。最细微的变化都会大大改变散列。这意味着您始终可以通过比较它们的哈希来测试两个事物是否相同（或根本没有被篡改）。

在 Solidity 中，比较哈希比比较原始数据类型要高效得多。  

例如，比较两个字符串通常是通过比较它们的 ABI 编码（字节）形式的哈希值来完成的。在 Solidity 中比较两个字符串的常见辅助函数如下所示：

```solidity
function compareStrings(string memory str1, string memory str2)
        public
        pure
        returns (bool)
    {
        return (keccak256(abi.encodePacked((str1))) ==
            keccak256(abi.encodePacked((str2))));
    }
```

稍后我们将讨论什么是 ABI 编码，但请注意`encodePacked()`的结果是一个`bytes`,使用了keccak256 算法（这是 Solidity 使用的本机哈希算法）对其进行哈希处理的数组。比较散列输出（256 位整数）是否相等。  

现在让我们转向 ABI 编码。首先，我们记得 ABI（应用程序二进制接口）是指定如何与部署的智能合约进行交互的接口。ABI 编码是将给定元素从 ABI 转换为字节以便 EVM 可以处理它的过程。  

EVM 在位和字节上运行计算。因此，编码是[将结构化输入数据转换为字节](https://stackoverflow.com/questions/14822323/why-is-encoding-and-decoding-needed-for-any-programming-language-database)以便计算机可以对其进行操作的过程。解码是将字节转换回结构化数据的逆过程。有时，编码也称为“序列化”。

你可以在[这里](https://docs.soliditylang.org/en/latest/cheatsheet.html#global-variables)阅读更多关于全局变量abi提供的solidity内置方法，这些方法做不同类型的编码和解码。编码数据的方法将它们转换为字节数组（`bytes`数据类型）。反过来说，对其输入进行解码的方法希望将字节数据类型作为输入，然后将其转换成被编码的数据类型。

您可以在以下代码片段中观察到这一点：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract EncodeDecode {


// Encode each of  the arguments into bytes
    function encode(
        uint x,
        address addr,
        uint[] calldata arr
    ) external pure returns (bytes memory) {
        return abi.encode(x, addr, arr);
    }

    function decode(bytes calldata bytesData)
        external
        pure
        returns (
            uint x,
            address addr,
            uint[] memory arr
        )
    {
        (x, addr, arr) = abi.decode(bytesData, (uint, address, uint[]));
    }
}
```

我在Remix中运行了上述程序，并在encode()中使用了以下输入：1981, 0x3C44CddB6a900fa2b585dd299e03d12FA4293BC, [1,2,3,4]。

```
And the bytes I got returned were represented in hexadecimal form as: 0x00000000000000000000000000000000000000000000000000000000000007bd0000000000000000000000003c44cdddb6a900fa2b585dd299e03d12fa4293bc000000000000000000000000000000000000000000000000000000000000006000000000000000000000000000000000000000000000000000000000000000040000000000000000000000000000000000000000000000000000000000000001000000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000000000000000000000000000000030000000000000000000000000000000000000000000000000000000000000004.

```

我把这个作为输入参数输入到decode()函数中，得到了我原来的三个参数。

因此，编码的目的是将数据转换成EVM处理数据所需的字节数据类型。而解码则把它带回到人类可读的结构化数据中，让我们的开发者可以使用。

## 如何调用合约和使用回溯函数

根据智能合约的设计和其中存在的可见性指定器，该合约可以被其他智能合约或外部拥有的账户互动。

通过 Remix 从你的钱包调用是后者的一个例子，就像使用 Metamask 一样。您还可以通过 EthersJS 和 Web3JS、Hardhat 和 Truffle 工具链等库以编程方式与智能合约进行交互。

出于本 Solidity 手册的目的，我们将使用 Solidity 与另一个合约进行交互。

一个智能合约调用其他智能合约有两种方式。第一种方式通过使用接口（我们之前讨论过）直接调用目标合约。或者，如果将 Target 合约导入到调用合约的范围内，则直接调用它。

这种方法如下所示：

```solidity
contract Target {
    int256 public count;

    function decrement() public {
        count--;
    }
}

interface ITarget {
    function decrement() external;
}

contract TargetCaller {
    function callDecrementInterface(address _target) public {
        ITarget target = ITarget(_target);
        target.decrement();

    }

    function callDecrementDirect(Target _target) public {
        _target.decrement();
    }
}
```

在 Remix 中，您可以先部署`Target`，然后调用`count()`以查看 count 变量的默认值为`0`，正如预期的那样。如果您调用`decrement()`方法，该值将减 1 。

然后你可以部署`TargetCaller`，你可以调用两种方法，这两种方法都会减少`Target`中`count`的值。

请注意，这两种方法中的每一种都使用略有不同的语法访问`Target`合约。当使用`ITarget`接口进行交互时，第一个方法接受`Target`的地址，而第二个方法将`Target`视为自定义类型。

第二种方法只有在`Target`合约与`TargetCaller`在同一文件中声明或导入时才有可能。最常见的是，你会与第三方部署的智能合约进行互动，他们会为这些合约发布ABI接口。

每次你调用这些方法，`Target`中的`count`值就会减少1。这是与其他智能合约互动的一种非常常见的方式。

第二种方法是使用 Solidity 提供的“低级”调用语法。当您还想向目标合约发送一些以太币（价值）时，您可以使用它。将在下一节讨论发送值，但现在只需将 Remix 中的代码替换为以下内容：

```solidity
contract Target {
    int256 public count;

    function decrement(int num) public payable {
        count = count - num;
    }
}

interface ITarget {
    function decrement(int num) external payable;
}

contract TargetCaller {
    function callDecrementLowLevel(address _target) public {
        ITarget target = ITarget(_target);
        target.decrement{value:0}(5);

    }

    // other decrementing functions….
}
```

你会注意到`decrement()`现在需要一个参数，并且接口和`Target`合约都被这个新的输入数据所更新。

接下来注意到`TargetCaller`实现了一个新的函数，该函数以新的语法调用`decrement()`，解释如下。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221226095406.png)

在下一节中，我们将看到这些调用目标智能合约以向其发送 Ether 的低级方法的示例。

但是当你调用一个合约而它实际上并没有你试图调用的函数时会发生什么？

这可能会恶意发生，以利用 Solidity 在 EVM 上的工作方式。或者，更常见的是，它可能会意外发生。例如，当接口中存在错误并且编译器无法将您发送的函数和参数与合约中实际包含的任何内容相匹配时，就会发生这种情况。那么会发生什么？

对于这些情况，许多合约采用了一个特殊的函数，称为回退函数。该函数看起来像一个普通函数，但它不需要`function`关键字。如果你想让它也处理你的合约被发送一些以太的情况，你也必须标记它为`payable`。但这不是使您的合约能够接收付款的推荐方式。

让我们通过重新利用我们之前的`Target`,`ITarget`和`TargetCaller`并添加一个回退函数来看一下，如下所示：

```solidity
contract Target {
    int256 public count;

    function decrement() public payable {
        count = count - num;
    }

    fallback() external payable  {
        count++;
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

一旦我们部署了一个新的`Target`实例，我们可以调用`count()`并看到它被设置为默认值零。  

接下来我们就可以部署`TargetCaller`并调用`callFallback()`，其内部调用`nonExistentFunction()`。

值得注意的是，接口上说`nonExistentFunction()`是可用的，但实际`Target`合约并没有这样的功能。这就是为什么`Target`回退函数被触发，`count`的值现在被增加了1。

回退函数的目的是在没有其他函数可用于处理它的情况下处理对合约的调用。如果 fallback 被标记为`payable`，则 fallback 函数还将使智能合约能够接收 Ether（尽管这不是 fallback 的推荐用途）。我们将在下一节中介绍这一点。

（未完待续）

> 原文：https://www.freecodecamp.org/news/learn-solidity-handbook/

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)