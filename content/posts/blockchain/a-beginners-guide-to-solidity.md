---
title: Solidity 初学者指南
description: null
author: 李留白
weight: 0
date: 2023-01-31T01:11:57.036Z
lastmod: 2023-01-31T01:34:37.014Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230131091217.png
---

就在十年前，很难想象一个可以创建可以自行运行的独特数字资产的世界。一个代码取代信任并且没有中间人的世界。今天，这个世界不再是我们想象中的虚构；这是智能合约和区块链的世界。Solidity 是让这个世界成为可能的语言。

Solidity 是支持在以太坊网络上创建智能合约的编程语言。与任何其他语言一样，它有自己的语法、句法和组织。但与其他编程语言不同，Solidity 开辟了一个全新的可能性世界。

在本文中，我将介绍 Solidity，这种语言正在彻底改变我们与数字世界交互的方式。从了解该语言的基础知识到了解使其脱颖而出的独特组件，本初学者指南将为您提供坚实的 Solidity 基础。因此，无论您是编程新手还是只是想扩展您的技能组合，请继续阅读直到最后。

## 基本介绍

Solidity 是一种高级的、面向合约的语言，用于在以太坊虚拟机 (EVM) 上构建智能合约。它受到 C++、Python 和 JavaScript 的影响，并与这些语言共享语法和编程概念。

## 关键零件：

1. **逻辑：** Solidity 允许使用 if-else 语句、for 和 while 循环以及其他逻辑运算符，如 and、or、not。
2. **作用域：** Solidity 具有全局、契约和函数级别的作用域。每个变量或函数都有一个特定的范围，在该范围内可以访问或修改它。
3. **模式：** Solidity 具有可用于编写智能合约的通用设计模式，例如 Open-Close Principle 和 Pull Payment 模式。
4. **函数：** Solidity 支持内部和外部函数。内部函数只能被同一合约内的其他函数调用，而外部函数可以被任何合约或外部调用。
5. **结构：** Solidity 允许创建自定义数据结构，例如结构和数组。
6. **库：** Solidity 支持库的使用，库是可以被多个合约调用的可重用代码块。

让我们亲自动手，使用代码示例和解释来解释这些关键组件中的每一个。

以下是用 Solidity 编写的逻辑示例。

```solidity
function checkAge(uint age) public pure returns (bool) {
    if (age >= 18) {  // this line checks if the input age is greater than or equal to 18
        return true;   // if the above condition is met, it returns true
    } else {
        return false;  // if the above condition is not met, it returns false
    }
}
```

此代码定义了一个名为`checkAge`的函数，该函数接受名为`age`的`uint`类型（无符号整数）的输入。

该函数具有`public`和`pure`可见性，这意味着它不读取或修改状态变量，仅使用传递给它的参数或其中存在的局部变量返回值。它可以在编译期间从外部调用而不会产生任何副作用。

如果年龄大于或等于 18 岁，则该函数还返回一个布尔值`true`，否则返回一个布尔值`false`。

*很简单，对吧？现在让我们在下面的示例中编写一些 Solidity 范围。*

```solidity
contract Enitandev {
    uint private age = 25;  // this line declares a private variable of type uint called age and assigns it the value 25

    function getAge() public view returns (uint) {  // this function is public and read-only
        return age;  // this line returns the value of the private variable age
    }
}
```

注意：局部变量的范围仅限于定义它们的函数，而状态变量可以有三种类型的范围。

> ***公共：***公共状态变量可以在内部和通过消息访问。一个公共状态变量会自动生成一个getter函数。
>
> ***内部：***内部状态变量只能从当前合约或从其派生的合约内部访问。
>
> ***私有：***私有状态变量只能从定义它们的当前合约内部访问，不能从派生合约访问。

上面的代码定义了一个名为`Enitandev`的合约. 它有一个只能在合约中访问的`uint`类型的私有变量`age`。

该合约还定义了一个getAge函数，该函数具有`public`和`view`（只读）可见性，因此它可以被外部调用。该函数然后返回私有变量`age`的值。

*现在让我们继续讨论 Solidity 中的模式*。

***注意：***设计模式是可重用的、解决反复出现的设计问题的传统方法。将资金从一个地址转移到另一个地址是 Solidity 中常见设计模式的一个示例，如下例所示。

```solidity
contract Token {
    mapping(address => uint) public balanceOf;  // this line creates a public mapping called balanceOf that maps addresses to uint values

    function approve(address _spender, uint _value) public {  // this function is public
        require(balanceOf[msg.sender] >= _value && _value > 0);  // this line checks if the msg.sender's balance is greater than or equal to the input _value and that _value is greater than 0
        approve[_spender] = _value;  // this line sets the approved value for the input address _spender to the input _value
    }

    function transfer(address _to, uint _value) public {  // this function is public
        require(balanceOf[msg.sender] >= _value && _value > 0);  // this line checks if the msg.sender's balance is greater than or equal to the input _value and that _value is greater than 0
        require(approve[msg.sender][_to] >= _value); // this line checks if the approved value for msg.sender to send to _to is greater than or equal to _value
        balanceOf[msg.sender] -= _value;  // this line subtracts _value from the msg.sender's balance
        balanceOf[_to] += _value;  // this line adds _value to the balance of address _to
    }
}
```

上面的代码定义了一个名为`Token`的合约. 它有一个称为`balanceOf`的公共映射，将地址映射到 uint 值。

合约还有两个函数，`approve`和`transfer`，它们都是公共的。`approve`函数接受一个address `_spender`和一个uint `_value`作为输入。

该函数首先检查`msg.sender`余额是否大于或等于输入`_value`并且`_value`大于 0。如果满足此条件，它将输入地址`_spender`的approve值设置为输入的`_value`。

`transfer`函数接受一个address类型`_to`和一个uint类型`_value`作为输入。该函数首先检查`msg.sender`的余额是否大于或等于输入`_value`并且`_value`大于 0。它还检查`msg.sender`发送到`_to`的approve值是否大于或等于`_value`。

如果满足所有这些条件，它会从`msg.sender`的余额中减去`_value`并添加`_value`到 address 的余额`_to`中。

*现在让我们谈谈 Solidity 中的函数。*

```solidity
contract Enitandev {
    function internalFunction() internal {
        // code here
    }

    function externalFunction() public {
        // code here
    }
}
```

上面的代码定义了一个名为`Enitandev`的合约. 它有两个功能，`internalFunction`和`externalFunction`。`internalFunction`函数具有`internal`可见性，这意味着它只能被同一合约中的其他函数调用。`externalFunction`函数具有`public`可见性，这意味着它可以被任何合约或外部调用。

*接下来是什么？结构*

在 Solidity 中，struct 是一种创造性的数据结构格式，可以将各种数据类型的变量打包成一个变量或自定义类型。请参见下面的示例。

```solidity
struct Person {
    string name;
    uint age;
}

contract Enitandev {
    Person[] public people;

    function addPerson(string memory _name, uint _age) public {
        people.push(Person(_name, _age));  // this line adds a new person to the people array with the input name and age
    }
}
```

上面的代码定义了一个名为`Enitandev`的合约. 它有一个名为`Person`的结构，它有两个字段，一个是`string`类型`name`，一个是`uint`类型`age`.

合约还有一个公共变量`people`，它是一个`Person`结构数组。合约有一个`addPerson`函数，接受两个输入，一个`string`类型的`_name`和一个`uint`类型的`_age`。

该函数然后将一个new person添加到`people`数组中，并输入姓名和年龄。

*让我们谈谈库来结束关键组件部分*。

Solidity 库类似于包含可重用代码的合约。库包含可以通过其他合约访问的功能。创建库和部署共享代码可以降低 gas 成本。让我们用一个例子来证明

```solidity
library SafeMath {
    function add(uint a, uint b) internal pure returns (uint) {
        require(a + b >= a);  // this line checks that the result of the addition is greater than or equal to a
        return a + b;  // this line returns the result of adding a and b
    }
}

contract Enitandev {
    using SafeMath for uint;  // this line allows the use of the SafeMath library functions for uint type

    uint public total;

    function add(uint a, uint b) public {
        total = a.add(b);  // this line calls the add function from the SafeMath library and assigns the result to the total variable
    }
}
```

该代码定义了一个名为 SafeMath 的库，其中包含一个名为`add`的函数. 该函数有两个输入，`a`和`b`，它们都是无符号整数 (uint)。

该函数以require语句开始，该语句检查`a`和`b`相加的结果是否大于或等于`a`。这是作为一种安全检查，以确保操作不会导致上溢或下溢，这在大数相加时可能发生。如果条件不满足，函数将不执行，require语句将引发一个异常。

然后该函数使用 return 语句返回`a`和`b`相加的结果。

在库下面声明了合约`Enitandev`，它有一个称为`total`的状态变量，它是uint类型的公共变量。

该合约有一个名为`add`的函数，它接受两个输入变量，a和b的类型为uint。在函数内部，`total`变量被分配了从 SafeMath 库调用`add`函数并传入`a`和`b`作为参数的结果。

`using SafeMath for uint;`行允许合约使用 uint 类型的 SafeMath 函数。这意味着您可以使用`.`运算符调用函数而不是直接调用库函数，库将自动检查上溢和下溢。

因此，这段代码提供了一种使用 uint 类型的 SafeMath 库函数的方法，并确保操作安全可靠地完成。

## Solidity 可以用来做什么？

Solidity 可以实现的范围取决于独特的用例和合约要求。然而Solidity常用于以下场景：

**代币创建和管理：**在以太坊区块链上，Solidity 通常用于创建和管理代币等数字资产。

**去中心化金融 (DeFi)：**Solidity可用于开发去中心化金融应用程序，例如借贷平台。

**供应链管理：** Solidity 可用于开发智能合约，使供应链程序自动化，例如跟踪物品的移动和保持合规性。

**游戏：** Solidity 可用于开发去中心化游戏应用程序，例如游戏内对象和虚拟世界。

## 开发者圈子批评 Solidity 是为了什么？

与任何技术一样，Solidity 也受到批评。其中最常见的是：

**复杂性：**用 Solidity 编写智能合约可能很困难，需要透彻了解以太坊环境以及合约的具体要求。由于这种复杂性，开发人员设计安全可靠的合约可能具有挑战性。

**安全性：** Solidity 合约是不可变的，这意味着它们的代码一旦部署就无法修改。这使得修复部署后发现的故障或漏洞变得困难，并导致了一些备受瞩目的智能合约攻击。

**Gas 费用：** Gas 费用是在与以太坊区块链交互时产生的，包括智能合约的部署和执行。这些费用的成本因网络拥塞和合同复杂性而异，这使得一些消费者不太容易获得这些费用。

**成熟度：** Solidity 是一种新的语言，生态系统仍在发展中。因此，工具、库和最佳实践可能存在局限性。

**灵活性有限：**由于 Solidity 是一种面向合约的语言，它并不适合实现各种应用程序。管理大量数据和进行复杂计算也很困难。

**有限的语言特性：** Solidity 缺少一些在更成熟的语言中常见的语言特性，例如继承、泛型以及集合和映射等复杂的数据结构。

需要注意的是，其中许多批评都与以太坊生态系统的当前状态有关，而不是语言本身，并且生态系统在不断发展和完善。此外，许多批评是主观的，取决于用例、需求和特定的项目上下文。

“朋友们，这就是 Solidity 的初学者指南，它会让您立即像专业人士一样编码！从理解语言的基础知识到其独特组件的基本细节，我们已经涵盖了所有内容。我们探索了 Solidity 的用例及其局限性，并为您提供了一些基本语法和代码示例。

您现在已经掌握了开始在以太坊区块链上创建自己的智能合约的知识。因此，无论您是想推出自己的去中心化应用程序还是创建新的数字资产，区块链世界都在等着您。记住要不断学习和试验，并始终关注 Solidity 世界中令人兴奋的新发展。快乐编码！”

> 原文：https://blog.enitan.dev/a-beginners-guide-to-solidity

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
