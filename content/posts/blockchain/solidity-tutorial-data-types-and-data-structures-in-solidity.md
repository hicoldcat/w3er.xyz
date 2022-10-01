---
title: Solidity教程：Solidity 中的数据类型和数据结构
description: null
author: 李留白
weight: 0
date: 2022-10-01T02:34:52.076Z
lastmod: 2022-10-01T02:34:59.186Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221001103439.png
---

Solidity 是一种面向对象的高级编译编程语言，用于[编写智能合约](https://web3.hashnode.com/solidity-tutorial-learn-how-to-build-your-first-smart-contract)，以及在不同的区块链上[构建和部署 Dapp](https://web3.hashnode.com/solidity-tutorial-how-to-build-and-deploy-an-nft-minting-dapp)。

Solidity 与任何其他编程语言一样，有自己的**数据类型**和**数据结构**，但具有不同的语法和应用程序。

本教程将介绍 Solidity 编程语言中一些最常用的数据类型和数据结构。

## Solidity 数据类型的性质

Solidity 本质上是一种静态类型的强类型语言，它在执行源代码之前执行类型检查。

作为一种**静态类型语言**，Solidity 要求程序员在编译代码（编译时）之前声明每个变量的数据类型。

而 Solidity 作为一种**强类型语言**意味着变量的数据类型不能在程序中修改或转换为另一种数据类型。

## Solidity 数据类型

Solidity 和其他编程语言一样，将其数据类型分为两类：**值类型**和**引用类型**。

## Solidity 中的值类型

值类型变量是将数据直接存储在分配给自身的[堆栈内存中的变量。](https://en.wikipedia.org/wiki/Stack_(abstract_data_type))

这些类型是按值传递的，这意味着只要将它们分配给新变量或作为参数提供给函数，它们就会被复制，对新副本所做的任何更改都不会影响原始数据。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/V8P0OvCtl.gif)

### 1.) 整数

Solidity 中的整数数据类型用于存储整数值。整数类型进一步分为**int**和**uint**用于分别声明有**符号**和**无符号**整数类型。

#### i. int/有符号整数

`int`关键字用于声明有符号整数。有符号整数是一种数据类型，可以在智能合约中保存正整数和负整数值。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Signed_Integer_Example{
    int year = 2022; // positive value (✅)
    int temperature = -89; // negative value (✅)
}
```

#### ii. uint/无符号整数

该`uint`关键字用于声明无符号整数。无符号整数是智能合约中只能保存正整数值的数据类型。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Unsigned_Integer_Example{
    uint year = 2022; // positive value (✅)
    uint temperature = -89; // negative value (❌)
}
```

当您尝试将负值分配给无符号数据类型时，您将收到以下`TypeError`消息：

```
TypeError: Type int_const -89 is not implicitly convertible to expected type uint256. Cannot implicitly convert signed literal to an unsigned type.
```

### 2.) 字节

Solidity 中的字节是包含字节序列的固定大小的字节数组。字节数组的长度在字节的前面定义，如`bytes1`to `bytes32`。

该数字相当于字节数组变量可以包含的字符数。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Bytes_Array_Example{
    bytes1 one_character = "a"; // 1 character (✅)
    bytes2 two_characters = "ab"; // 2 characters (✅)
    bytes3 three_characters = "abc"; // 3 characters (✅)
    bytes4 four_characters = "abcd"; // 4 characters (✅)
    bytes5 five_characters = "abcde"; // 5 characters (✅)
    bytes32 thrity_two_characters = "abcdefghijklmnopqrstuvwxyz123456"; // 32 characters (✅)
}
```

当您尝试分配超过固定字节大小的字符数时，如下所示：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Bytes_Array_Example{
    bytes1 one_character = "ab"; // single value (❌)
    bytes1 two_characters = "abc"; // single value (❌)
}
```

您将收到以下`TypeError`消息：

```
TypeError: Type literal_string "abc" is not implicitly convertible to expected type bytes1. Literal is larger than the type.
```

### 3.) 布尔值

Solidity 中的布尔值由`bool`关键字表示，并且与其他所有编程语言一样，Solidity 中的布尔值仅接受两个值：`true`和`false`：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Boolean_Example{
    bool isEthereumMerge = true; // (✅)
    bool currentUserCanMintToken = false; // (✅)

    bool isRaining = "true"; // (❌)
    bool isAdmin = "false"; // (❌)
}
```

当您尝试将非布尔值分配给布尔变量时，您将收到以下`TypeError`消息：

```
TypeError: Type literal_string "true" is not implicitly convertible to the expected type bool.
```

### 4.) 地址

地址是 Solidity 中的一种特殊数据类型，能够接收和发送 Ether。地址数据类型旨在存储以太坊地址，该地址通常以`0x`值开头。

地址大小为 20 个字节，包含 42 个字符。

```
0x0000000000000000000000000000000000000000
```

[地址也是从公钥的](https://www.cs.utexas.edu/users/moore/acl2/manuals/current/manual/index-seo.php/ETHEREUM____PUBLIC-KEY-TO-ADDRESS)[Keccak-256 散列](https://cryptomarketpool.com/keccak256/)生成的不区分大小写的十六进制数字。

当您尝试将字符串分配给地址数据类型时，如下所示：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Address_Example{
    address user_address = 0x0000000000000000000000000000000000000000; // (✅)
    address user_home_address = "Street 2, downtown road"; // (❌)
}
```

您将收到以下`TypeError`消息：

```
TypeError: Type literal_string "Street 2, downtown road" is not implicitly convertible to expected type address.
```

当您尝试将非十六进制数字分配给地址数据类型时，如下所示的八进制数字：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Address_Example{
    address user_address = 0x0000000000000000000000000000000000000000; // (✅)
    address phone_address = 080123456789; // (❌)
}
```

您将收到以下`ParserError`消息：

```
ParserError: Octal numbers not allowed.
```

地址值类型进一步分为两种：

| 功能         | `address` | `address payable` |
| ------------ | --------- | ----------------- |
| 检查地址余额 | ✅         | ✅                 |
| 发送以太币   | ❌         | ✅                 |
| 接收以太币   | ❌         | ✅                 |

**专业提示：**当您希望您的智能合约接收和发送以太币时，请使用`address payable`值类型。当您不希望您的智能合约接收或转移以太币时，请使用纯`address`值类型。

### 5.) 枚举

枚举数据类型，也称为枚举，使开发人员能够创建用户定义的数据类型。用户定义的数据是分配给从零开始的整数常量值的名称。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Enum_Example{
    enum Status {
        Sending, // 0
        Success, // 1
        Failed // 2
    }

    Status status;

    function sendSomething () public {
        status = Status.Sending; // set status to sending
    }
}

```

从上面的代码片段中，我们创建了一个`Status`枚举来保存我们发送内容时的操作状态。然后，我们可以使用枚举将操作的状态更新为枚举中的任何预定义状态`Status`。

## Solidity 中的引用类型（数据结构）

引用类型变量是将数据的位置（内存地址）存储在[堆](https://opendsa-server.cs.vt.edu/OpenDSA/Books/CS2/html/HeapMem.html)内存上的变量，它们不直接共享数据。

对参考数据所做的更改将始终影响原始数据。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/56kIHvRbD.gif)



Solidity 中引用类型的示例包括字符串、结构、数组和映射。

### 1.) 字符串

`string`类型是一个字符序列。Solidity 支持使用单引号`' '`和双引号`" "`的字符串文字。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract String_Example{
        string name = "John Doe";
}
```

### 2.) 结构

`struct`数据类型是一种引用数据类型，可用于创建其他数据类型的结构。结构可以包含值类型和引用类型，包括其他结构，但不能包含其自身的结构。

可以使用以下语法在 Solidity 中创建结构：

```solidity
struct <Struct_Name> {
    <data_type> <variable_name>; 
}

```

data_type可以是string，int，uint，bool，或任何solidity数据类型。结构可以在智能合约之外声明，并导入另一个合约中。

结构的用例如下所示：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Struct_Example{
    // User profile structure
    struct UserProfile {
        string fullName; // string
        bool isOnboarded; // boolean
        uint age; // unsigned integer (no negative)
    }

    // Create a new record using the created structure
    UserProfile _newUserProfile = UserProfile("Ayodele Samuel Adebayo", true, 19);

    // Get the created profile
    function getUserProfile() public view returns (string memory, bool , uint ){
        return (_newUserProfile.fullName, _newUserProfile.isOnboarded, _newUserProfile.age); 
    }
}

```

从上面的代码中，我们为用户配置文件创建了一个结构，期望有一个`fullName`，`isOnboarded`状态，和用户年龄。然后，我们使用这个结构来创建一个新的用户，用一个函数来返回所创建的个人资料的信息。

**要点**：在 Solidity 中使用结构使我们的代码更有条理、可维护、可重用和可读。

### 3.) 数组

数组是具有相同数据类型的变量的集合。它们存储在一个连续的内存位置，每个数组项都有一个唯一的索引。

Solidity 中的数组可以是固定大小或动态大小，每个数组项都可以通过其唯一索引进行搜索。

#### i. 动态数组

可以使用以下语法在 Solidity 中创建动态数组：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Dynamic_Array_Syntax{
    <datatype[]> <variable_name> = <[array_items]>
}
```

下面是一个名字的string动态数组和一个数字的uint动态数组的例子。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Dynamic_Array_Example{
    string[] arrayOfNames = ["Faith", "Becky", "Steve"];
    uint[] arrayOfNumbers = [0, 1, 2, 3, 4, 5];
}
```

#### ii. 固定大小的数组

可以使用以下语法创建固定大小的数组：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Fixed_Size_Array_Syntax{
    <datatype[size]> <variable_name> = <[array_items]>
}

```

下面是 2 个固定大小的`string`名称数组和 1 个固定大小的`uint`动态数字数组的示例：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Fixed_Size_Array_Example{
    string[2] arrayOfNames = ["Faith", "Becky"];
    uint[1] arrayOfNumbers = [0];
}

```

当您尝试超过固定大小限制时：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Fixed_Size_Array_Example{
    string[2] arrayOfNames = ["Faith", "Becky", "Steve"]; // (❌)
    uint[1] arrayOfNumbers = [0, 1, 2]; // (❌)
}

```

您将分别收到以下`TypeError`消息：

```
TypeError: Type string[3] memory is not implicitly convertible to expected type string[2] storage ref.
```

```
TypeError: Type uint8[3] memory is not implicitly convertible to expected type uint256[1] storage ref.
```

### 4.) 映射

Solidity 中的映射是一种键值对数据结构，其功能类似于 Python 中的字典和 JavaScript 中的哈希表或对象。

可以使用以下语法在 Solidity 中创建映射：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Mapping_Syntax{
        mapping (key => value) variable_name;
}
```

其中`key`可以是除引用类型以外的任何数据类型，并且`value`可以是值类型和引用类型。

以下是将用户的钱包地址映射到其余额的示例：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

contract Mapping_Example{
        mapping (address => uint) users_balances;
}

```

从上面的数据结构实现中，我们可以用用户的钱包地址从区块链中检索到用户的加密货币余额，为uint类型。

## 总结

数据类型和数据结构是任何编程语言的基础，也是使用 Solidity 开发高级 Dapp 的构建块。

在本教程中，我们介绍了 Solidity 编程语言中最常用的数据类型和数据结构。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)