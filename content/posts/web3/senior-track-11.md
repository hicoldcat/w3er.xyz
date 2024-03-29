---
title: Web3系列教程之高级篇---11：优化您的智能合约以构建尽可能便宜的交易
description: null
author: 李留白
weight: 0
date: 2022-08-10T13:57:54.595Z
lastmod: 2022-08-10T13:58:24.555Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/R3RYera.png
---

在本教程中，我们将学习Solidity中的一些气体优化技术。这是我们要求最多的一个级别，话不多说，让我们开始吧，👀。

# 技巧和窍门

## 包装变量

如果你还记得，我们在之前的一个关卡中谈到了存储槽的问题。现在，如果你记得的话，solidity中有趣的一点是，每个存储槽是32个字节。

这种存储可以被优化，这将进一步意味着当你部署你的智能合约时，如果你正确地包装你的变量，那么气体会优化。

包装你的变量是指你把较小的变量包装或放在一起，使它们共同组成`32 bytes`。例如，你可以将32个`uint8`打包到一个存储槽中，但要做到这一点，你必须连续声明它们，因为变量的声明顺序对实体来说很重要。

给两个代码样本:

```
uint8 num1;
uint256 num2;
uint8 num3;
uint8 num4;
uint8 num5;
```

```
uint8 num1;
uint8 num3;
uint8 num4;
uint8 num5;
uint256 num2;
```

第二种情况更好，因为在第二种情况下，solidity编译器会把所有的uint8放在一个存储槽中，但在第一种情况下，它会把uint8 num1放在一个槽中，但现在它看到的下一个uint256本身需要32字节，因为256/8比特=32字节，所以它不能和uint8 num1放在同一个存储槽中，所以现在它需要另一个存储槽。之后，uint8 num3, num4, num5将被放在另一个存储槽中。因此，第二个例子需要2个存储槽，而第一个例子则需要3个存储槽。

还需要注意的是，内存中的元素和`calldata`不能被打包，也不能被solidity的编译器优化。

## 存储器与内存

改变存储变量比改变内存中的变量需要更多的气体。最好是在所有的逻辑都已经实现之后，在最后更新存储变量。

因此，给定两个代码例子：

```
contract A {
    uint public counter = 0;
    
    function count() {
        for(uint i = 0; i < 10; i++) {
            counter++;
        }
    }
    
}
```

```
contract B {
    uint public counter = 0;
    
    function count() {
        uint copyCounter;
        for(uint i = 0; i < 10; i++) {
            copyCounter++;
        }
        counter = copyCounter;
    }
    
}
```


第二段代码样本更加气体优化，因为我们只向存储变量计数器写了一次，而第一段代码样本是在每个迭代中都向存储写。尽管我们在第二个代码样本中多写了一次，但向内存写10次，向存储写1次，仍然比直接向存储写10次便宜。

## 固定长度和可变长度的变量

我们谈到了固定长度和可变长度变量的存储方式。固定长度的变量存储在堆栈中，而可变长度的变量则存储在堆中。

从本质上讲，为什么会发生这种情况，是因为在堆中你确切地知道在哪里可以找到一个变量以及它的长度，而在堆中，考虑到变量的可变性，会有额外的遍历成本。

因此，如果你能使你的变量大小固定的话，这对气体优化总是有好处的。

给出两个代码的例子：

```
string public text = "Hello";
uint[] public arr;
```

```
bytes32 public text = "Hello";
uint[2] public arr;
```

第二个例子有更多的气体优化，因为所有的变量都是固定长度的。

## 外部、内部和公共函数

在solidity中调用函数可能非常耗费气体，与其调用多个函数，不如调用一个函数并从中提取所有数据。

回顾一下，`public`函数是那些既可以在外部（由用户和其他智能合约）又可以在内部（从同一合约的另一个函数）调用的函数。

然而，当你的合同创建的函数只能被外部调用时，这意味着合同本身不能调用这些函数，你最好使用`external`关键字而不是`public`，因为`public`函数中的所有输入变量都被复制到内存中，这需要消耗气体，而对于`external`函数，输入变量被存储在`calldata`中，这是一个用于存储函数参数的特殊数据位置，存储在`calldata`中比存储在内存中需要的气体更少。

同样的原则也适用于为什么调用`internal`函数而不是`public`函数更便宜。这是因为当你调用`internal`函数时，参数是作为变量的引用被传递的，不会再次被复制到内存中，但这不会发生在`public`函数的情况下。

## 函数修改器

这是一个有趣的问题，因为几周前，我正在调试一个错误，他们遇到的错误是 "Stack too deep"。这通常发生在你在函数中声明了大量的变量，而该函数的可用堆栈空间已经无法使用。正如我们在以太坊存储层面所看到的，EVM只允许在一个函数中最多有16个变量，因为它不能在堆栈中执行超过16层深度。

现在，即使在`modifier`中移动了很多require语句，也无济于事，因为函数修改器使用的堆栈与它们所处的函数相同。为了解决这个问题，我们在`modifier`中使用了一个`internal`函数，因为`internal`函数不与原始函数共享相同的限制性堆栈，但`modifier`却可以。

## 使用库

库是无状态的合约，不存储任何状态。现在，当你从你的合同中调用一个库的公共函数时，该函数的字节码不会与你的合同一起部署，因此你可以节省一些气体成本。例如，如果你的合同有排序或做计算等的功能。你可以把它们放在一个库中，然后调用这些库函数来为你的合同做数学或排序。要阅读更多关于库的信息，请点击[这个链接](https://jeancvllr.medium.com/solidity-tutorial-all-about-libraries-762e5a3692f9)。

然而，有一个小的注意事项。如果你正在编写你自己的库，你将需要部署它们并支付气体 - 但一旦部署，它可以被其他智能合约重复使用，而无需自己部署。由于它们不存储任何状态，库只需要被部署到区块链上一次，并被分配一个地址，Solidity编译器足够聪明，可以自己计算出来。因此，例如如果你使用OpenZeppelin的库，它们不会增加你的部署成本。

## 短路条件

如果你使用(||)或(&&)，最好以这样的方式编写你的条件，以便执行或检索最少的函数/变量值，以确定整个语句是真还是假。

由于条件检查在找到第一个满足条件的值时就会停止，所以你应该把最有可能验证/不验证条件的变量放在前面。在OR条件（||）中，尽量把最有可能为真的变量放在前面，在AND条件（&&）中，尽量把最有可能为假的变量放在前面。一旦该变量被检查，条件就可以退出，而不需要检查其他的值，从而节省气体。

## 释放存储空间

由于存储空间需要花费气体，你实际上可以释放存储空间，删除不必要的数据来获得气体退款。因此，如果你不再需要一些状态值，使用Solidity中的`delete`关键字来获得一些气体退款。

## 短的错误字符串

确保你的require语句中的错误字符串的长度很短，字符串的长度越长，花费的气体就越多。

```
require(counter >= 100, "NOT REACHED"); // Good
require(balance >= amount, "Counter is still to reach the value greater than or equal to 100, ............................................";
```

第一项要求比第二项要求更有利于气体的优化。

> 注意：在较新的Solidity版本中，现在有使用error关键字的自定义错误，其行为与events非常相似，并且可以实现类似的气体优化。

感谢大家继续关注这篇文章🚀希望大家喜欢。）

## 参考

- Mudit Gupta - [气体优化技巧](https://mudit.blog/solidity-gas-optimization-tips/)和[气体优化技巧第二部分](https://mudit.blog/solidity-tips-and-tricks-to-save-gas-and-reduce-bytecode-size/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
