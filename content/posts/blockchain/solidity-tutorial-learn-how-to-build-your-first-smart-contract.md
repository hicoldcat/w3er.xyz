---
title: Solidity教程——学习如何构建您的第一个智能合约
description: null
author: 李留白
weight: 0
date: 2022-09-13T12:49:06.353Z
lastmod: 2022-09-13T13:27:52.683Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913205035.png
---

本 Solidity 教程将指导您如何编写您的第一个智能合约。

## 先决条件

在开始学习本 Solidity 教程之前，您需要对函数、变量等编程概念有基本的了解，并了解 IDE 是什么。

## 什么是智能合约？

智能合约是仅在满足特定条件时才在区块链上部署和执行的功能，无需任何第三方参与。

由于智能合约本质上是不可变的和分布式的，因此它们在编写和部署后无法修改或更新。此外，分布式的意义在于任何人都可以检查和查看区块链上的智能合约状态和交易历史。

## 如何构建智能合约？

尽管 Solidity 是使用最广泛的官方智能合约语言，但智能合约也可以用多种编程语言编写，包括 Javascript、Rust、Go 和 Yul。

## 什么是Solidity？

Solidity 是一种面向对象的高级编译编程语言，用于编写智能合约。对于有 JavaScript 知识的人来说，Solidity 更容易，因为它在语法上类似于 JavaScript。

## Solidity 语法

以下是一个简单的 Solidity 智能合约示例：

```solidity
// 1. SPDX-License-Identifier: MIT

// 2. Solidity Version
pragma solidity ^ 0.8.13;

// 3. Contract
contract My_Smart_Contract {

    // 4. Contract state declaration
    string public myName;

    // 5. Constructor to initialize value to declared states
    constructor() {
        myName = "Samuel";
    }

    // 6. Contract function
    function showMyName() public view returns (string memory) {
        return myName;
    }
}

```

### 1. Solidity 智能合约许可证

鼓励每个开发人员在其 Solidity 源文件的顶部添加机器可读许可证，如下所示：

```solidity
// SPDX-License-Identifier: MIT
```

MIT 许可证类似于您在[GitHub 上](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/licensing-a-repository)找到的许可证。如果您不想在 Solidity 源文件上指定许可证，则可以添加`UNLICENSED`值，但这不应留空。

[您可以在此处查看](https://spdx.org/licenses/)SPDX 支持的 Solidity 许可证的完整列表。

### 2. Solidity Pragma

一个 pragma 指令指示 Solidity 编译器运行智能合约的版本。

下面的 pragma 指令显示智能合约是为 Solidity 版本 0.8.13 编写的。^符号表示 Solidity 程序不应与低于 0.8.0 的版本或以 0.9.0 开头的版本一起使用。

```solidity
pragma solidity ^ 0.8.13;
```

pragma 指令始终位于源文件的本地，这意味着您必须将其添加到所有源文件中。

### 3. Solidity 合约

合约是部署在区块链上指定地址的状态和功能的集合。

```solidity
contract My_Smart_Contract {}
```

### 4. Solidity 中的变量

Solidity 是一种静态类型的编程语言，这意味着 Solidity 程序中的状态和局部变量必须由程序员在编译智能合约之前声明。

下面是在 Solidity 中声明变量的示例：

```solidity
string public myName;
```

定义的变量初始化如下：

```solidity
myName = "Samuel";
```

上面的变量可以像这样声明和初始化：

```solidity
string myName = "Samuel";
```

Solidity 中有 3 种主要类型的变量：局部变量、状态变量和全局变量。

#### 1.局部变量

这些是在solidity 函数中声明的变量，它们不存储在区块链上。

#### 2.状态变量

状态变量是在solidity 函数之外声明的变量，它们永久存储在区块链上。

#### 3. 全局变量

Solidity 全局变量是其他函数可以访问的变量。它们保存有关区块链及其交易属性的信息。

### 5. Solidity 构造函数

在 Solidity 中，构造函数是一个特殊的关键字，用于创建一个可选函数，用于初始化智能合约中的状态变量。

```solidity
constructor() {
     myName = "Samuel";
}
```

智能合约只能有一个构造函数，并且只有在编译智能合约后才会执行。

### 6. Solidity 函数

在编程中，函数是执行任务的代码块。它们是封装在单个对象中的代码组件。

该`function`关键字用于在 Solidity 中创建函数，类似于在 JavaScript 中创建函数的方式。

```solidity
function showMyName() public view returns (string memory) {}
```

Solidity 函数分解：

- `public`关键字表示该函数可以被其他合约访问。
- `view` 关键字表示该函数在区块链上是只读的，它不会改变区块链上的数据。
- `returns`关键字表示函数返回的数据类型。
- `string`关键字指定返回值的数据类型。
- `memory`关键字意味着函数的变量将在函数被调用时存储在一个临时位置。

### 7. Solidity 字符串连接

连接通常是将一个字符串连接到另一个字符串末尾的过程。串联在任何编程语言中都是一个非常重要的概念。

在 Solidity 中连接一个字符串与使用流行的`+`符号连接两个或多个字符串有很大不同。

在 Solidity 中，我们将使用一种调用方法`abi`来连接两个或多个字符串。这`abi`是*Application Binary Interface*的缩写形式，它允许我们将参数编码或解码为 ABI。

```solidity

string a = "A ";
string b = "B ";
string c = "C ";

string(abi.encodePacked(a, b, c));
```

上面的代码将给出以下输出：

```solidity
A B C
```

[您可以从此处](https://www.quicknode.com/guides/solidity/what-is-an-abi)阅读有关应用程序二进制接口的更多信息。

## 构建我们的第一个智能合约

现在我们已经掌握了 Solidity 的基础知识，我们将继续使用 Solidity 编写我们的第一个智能合约。

### 第 1 步 - Solidity IDE - Remix

运行 Solidity 智能合约的最快方法是使用在线 Solidity IDE，如 Remix（推荐）。

Remix IDE 是一个功能强大的开源 Solidity IDE，它允许我们直接从 Web 浏览器快速编写、编译和部署智能合约。

访问[remix.ethereum.org](https://remix.ethereum.org/)在您的浏览器上启动 Remix IDE。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913210028.png)

### 第 2 步 - 创建 Solidity 源文件

接下来，找到“文件资源管理器”部分下的 contracts 文件夹，并在其中创建一个新`Hello_World.sol`文件，如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913210041.png)

### 第 3 步 - 编写智能合约

在这一步中，我们将编写一个`Hello_World`智能合约，将宠物的信息存储在区块链上，并返回以下语句：

> “Hello World！我叫**Kitty**，今年**2**岁，我的主人叫**John Doe**。

宠物的姓名、年龄和主人的信息将使用允许用户输入宠物信息的设置功能动态化。

将以下代码复制并粘贴到您的`Hello_World.sol`文件中：

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^ 0.8.13;

contract Hello_World {
    string public greetingPrefix = "Hello World! ";
    string public petName;
    string public age;
    string public owner;

    constructor() {
        petName = "Kitty";
        age = "2";
        owner = "John Doe";
    }

    function setPetName(string memory newPetName) public {
        petName = newPetName;
    }

    function setAge(string memory newAge) public {
        age = newAge;
    }

    function setOwner(string memory newOwner) public {
        owner = newOwner;
    }

    function greet() public view returns (string memory){
        return string(abi.encodePacked(greetingPrefix, "My name is ", petName, " I'm ", age, " years old and my owner's name is ", owner));
    }
}
```

在上面的代码中，我们将智能合约的状态变量（`petName`, `age`, 和`owner `）声明为字符串。然后我们在构造函数中为状态变量设置一个初始值。

初始调用该`greet()`函数时未设置宠物名称、年龄和所有者，将返回构造函数中的初始宠物详细信息。

接下来，`setPetName`、`setAge`和`setOwner`充当我们合约的设置器函数，它将分别接收和设置名称、年龄和所有者状态变量。

最后，该`greet()`函数将返回一个连接的字符串，与状态变量中当前提供的宠物详细信息组成一个句子。

> Solidity 的最佳实践是将您的智能合约命名为与源文件相同的名称。

### 第 4 步 - 编译智能合约

Remix IDE 允许我们直接从浏览器编译我们的 Solidity 智能合约。

- 确保将源文件保存为`ctrl + s`.
- 如果您在 pragma 指令上注意到这样的红标：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913210415.png)

这意味着我们的源代码中没有将 Remix IDE 编译器设置为指定的 Solidity 版本。

要解决此问题，请单击 Solidity 编译器图标并选择您用于智能合约的 Solidity 版本：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913210426.png)

最后，保存您的源文件`ctrl + s`并单击编译按钮。您的 Solidity 编译器图标应变为绿色，如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913210437.png)

### 第 5 步 - 部署智能合约

是时候部署我们的智能合约了。单击侧边栏中的“部署并运行事务”按钮。

首先，选择一个 JavaScript 虚拟机环境（我们将在本 Solidity 教程中使用 JavaScript London VM）。

[您可以在此处](https://remix-ide.readthedocs.io/en/latest/run.html#run-setup)阅读有关 Remix IDE 虚拟机环境的更多信息。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913210448.png)

接下来，保留其他默认选项，然后单击“部署”按钮：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913210500.png)

如果部署成功，您将在**Deployed Contracts**部分看到我们的智能合约名称，该部分位于“deploy”按钮下方：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913210510.png)

Remix IDE 为我们提供了与智能合约交互的接口。

展开智能合约卡片可以看到我们的智能合约设置器函数，旁边有输入框，还有一个“getter 函数”按钮来显示我们的状态变量。

Remix IDE 会为所有状态变量自动生成`getter`函数。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913210537.png)

### 第 6 步 - 与智能合约交互

我们与智能合约的第一次交互将是检查该`greet()`函数是否会返回我们的默认宠物详细信息。

点击“问候”按钮：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913210554.png)

> 你好世界！我的名字是**Kitty**，我**2**岁，我的主人的名字是**John Doe**

如上所示，该`greet`函数按预期返回了我们智能合约的初始状态变量，同时还正确地将**问候**句占位符替换为状态变量。

接下来，单击所有“getter function”按钮。每个 getter 函数都应该从它们各自的状态变量中返回值，如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913211026.png)

在这一步中，我们将测试我们的`setAge`功能：

- 在`setAge`输入框中填写新宠物的年龄。
- 接下来，单击`setAge`按钮运行该功能。
- 然后，单击`age`getter 按钮（它应该返回新的年龄）。
- 最后，点击`greet`功能按钮。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913211039.png)

接下来，我们将测试我们的`setOwner`功能：

- 在`setOwner`输入框中填写宠物的主人。
- 接下来，单击`setOwner`按钮运行该功能。
- 然后，单击`owner`getter 按钮（它应该返回新的所有者名称）。
- 最后，点击`greet`功能按钮。

宠物的年龄应更新为您输入的新宠物的年龄：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913211051.png)

最后，我们将测试我们的`setPetName`功能：

- 在`setPetName`输入框中填写宠物的名字。
- 接下来，单击`setPetName`按钮运行该功能。
- 然后，单击`petName`getter 按钮（它应该返回新的宠物名称）。
- 最后，点击`greet`功能按钮。

宠物名称应更新为您输入的新宠物名称：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913211118.png)

恭喜🎉🎉🎉

我们的 Solidity 智能合约按预期运行。您可以继续使用不同的宠物名称、年龄和所有者姓名来测试智能合约。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220913211136.png)

## 你成功通过这个 Solidity 教程了！👏

恭喜您完成本 Solidity 教程！我们已经学习了如何使用 Remix IDE 来编写、部署我们的第一个智能合约并与之交互。

如果你想打入 web3 行业，学习 Solidity 是有利的。Solidity 是一种编译语言，您可以直接从计算机浏览器上的 Remix IDE 运行它。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
