---
title: Web3系列教程之入门篇---6. Solidity高级指南
description: null
author: 李留白
weight: 0
date: 2022-07-10T05:13:20.431Z
lastmod: 2022-07-10T06:37:22.854Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703215340.png
---

在新手课程中，我们查看了一些基本的 Solidity 语法。我们涵盖了变量、数据类型、函数、循环、条件流和数组。

然而，Solidity 还有一些东西，这些东西对于入门及以后的编码任务很重要。在本教程中，我们将介绍一些更重要的 Solidity 话题。

## 喜欢视频？

如果您想从视频中学习，YouTube 上有本教程的录音。它分为两部分，并且有时间戳。单击下面的屏幕截图观看视频，或继续阅读教程！

### 第1部分

![https://www.youtube.com/watch?v=Z5P3rKBRmEM](https://www.youtube.com/watch?v=Z5P3rKBRmEM)

### 第2部分

![https://www.youtube.com/watch?v=ILY3fIbwjk0](https://www.youtube.com/watch?v=ILY3fIbwjk0)

## 索引

- Mappings
- Enums
- Structs
- View and Pure Functions
- Function Modifiers
- Events
- Constructors
- Inheritance
- Transferring ETH
- Calling external contracts
- Import statements
- Solidity Libraries

## Mappings

Soliity 中的映射与其他编程语言中的散列映射或字典类似。它们用于以键-值对的形式存储数据。

使用语法` mapping (keyType => valueType)`创建映射

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract Mapping {
    // Mapping from address to uint
    mapping(address => uint) public myMap;
    
    function get(address _addr) public view returns (uint) {
        // Mapping always returns a value.
        // If the value was never set, it will return the default value.
        // The default value for uint is 0
        return myMap[_addr];
    }

    function set(address _addr, uint _i) public {
        // Update the value at this address
        myMap[_addr] = _i;
    }

    function remove(address _addr) public {
        // Reset the value to the default value.
        delete myMap[_addr];
    }
}
```

我们还可以创建嵌套映射，其中关键字指向第二个嵌套映射。为此，我们将 valueType 设置为映射本身。

```
contract NestedMappings {
    // Mapping from address => (mapping from uint to bool)
    mapping(address => mapping(uint => bool)) public nestedMap;
    
    function get(address _addr1, uint _i) public view returns (bool) {
        // You can get values from a nested mapping
        // even when it is not initialized
        // The default value for a bool type is false
        return nestedMap[_addr1][_i];
    }

    function set(
        address _addr1,
        uint _i,
        bool _boo
    ) public {
        nestedMap[_addr1][_i] = _boo;
    }

    function remove(address _addr1, uint _i) public {
        delete nestedMap[_addr1][_i];
    }
}
```

## Enums

Enum 这个词代表 Enumable。它们是用户定义的类型，包含一组常量(称为成员)的可读名称。它们通常用于限制变量只具有少数几个预定义值中的一个。因为它们只是人类可读常数的抽象，实际上，它们在内部表示为 unint。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract Enum {
    // Enum representing different possible shipping states
    enum Status {
        Pending,
        Shipped,
        Accepted,
        Rejected,
        Canceled
    }
    
    // Declare a variable of the type Status
    // This can only contain one of the predefined values
    Status public status;
    
    // Since enums are internally represented by uints
    // This function will always return a uint
    // Pending = 0
    // Shipped = 1
    // Accepted = 2
    // Rejected = 3
    // Canceled = 4
    // Value higher than 4 cannot be returned
    function get() public view returns (Status) {
        return status;
    }
    
    // Pass a uint for input to update the value
    function set(Status _status) public {
        status = _status;
    }
    
    // Update value to a specific enum members
    function cancel() public {
        status = Status.Canceled; // Will set status = 4
    }
}
```

## Structs

结构的概念存在于许多高级编程语言中。它们用于定义将相关数据组合在一起的自己的数据类型。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract TodoList {
    // Declare a struct which groups together two data types
    struct TodoItem {
        string text;
        bool completed;
    }
    
    // Create an array of TodoItem structs
    TodoItem[] public todos;
    
    function createTodo(string memory _text) public {
        // There are multiple ways to initialize structs
        
        // Method 1 - Call it like a function
        todos.push(TodoItem(_text, false));
        
        // Method 2 - Explicitly set its keys
        todos.push(TodoItem({ text: _text, completed: false }));
        
        // Method 3 - Initialize an empty struct, then set individual properties
        TodoItem memory todo;
        todo.text = _text;
        todo.completed = false;
        todos.push(todo);
    }
    
    // Update a struct value
    function update(uint _index, string memory _text) public {
        todos[_index].text = _text;
    }
    
    // Update completed
    function toggleCompleted(uint _index) public {
        todos[_index].completed = !todos[_index].completed;
    }
}
```

## View and Pure Functions

您可能已经注意到，我们正在编写的一些函数在函数头中指定了一个`view`或`pure`关键字。这些是指示函数特定行为的特殊关键字。

Getter 函数(那些返回值的函数)可以声明为 view 或 Pure。

- View: 不更改任何状态值的函数
- Pure: 不更改任何状态值，但也不读取任何状态值的函数

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract ViewAndPure {
    // Declare a state variable
    uint public x = 1;
    
    // Promise not to modify the state (but can read state)
    function addToX(uint y) public view returns (uint) {
        return x + y;
    }
    
    // Promise not to modify or read from state
    function add(uint i, uint j) public pure returns (uint) {
        return i + j;
    }
}
```

## Function Modifiers

修饰符是可以在函数调用之前和/或之后运行的代码。它们通常用于限制对某些函数的访问、验证输入参数、防止某些类型的攻击等。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract Modifiers {
    address public owner;
    
    constructor() {
        // Set the contract deployer as the owner of the contract
        owner = msg.sender;
    }
    
    // Create a modifier that only allows a function to be called by the owner
    modifier onlyOwner() {
        require(msg.sender == owner, "You are not the owner");
        
        // Underscore is a special character used inside modifiers
        // Which tells Solidity to execute the function the modifier is used on
        // at this point
        // Therefore, this modifier will first perform the above check
        // Then run the rest of the code
        _;
    }
    
    // Create a function and apply the onlyOwner modifier on it
    function changeOwner(address _newOwner) public onlyOwner {
        // We will only reach this point if the modifier succeeds with its checks
        // So the caller of this transaction must be the current owner
        owner = _newOwner;
    }
}
```

## Events

事件允许合约在以太坊区块链上执行日志记录。例如，一个给定合约的日志可以在以后被解析，以便在前台界面上执行更新。它们通常被用来让前端界面监听特定的事件并更新用户界面，或者被用作一种廉价的存储形式。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract Events {
    // Declare an event which logs an address and a string
    event TestCalled(address sender, string message);
    
    function test() public {
        // Log an event 
        emit TestCalled(msg.sender, "Someone called test()!");
    }
}
```

## Constructors

构造函数是一个可选的函数，在合同首次部署时执行。你也可以向构造函数传递参数。

P.S.--如果你还记得，我们实际上在新手课程的加密货币和NFT教程中使用了构造函数

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract X {
    string public name;
    
    // You will need to provide a string argument when deploying the contract
    constructor(string memory _name) {
        // This will be set immediately when the contract is deployed
        name = _name;
    }
}
```

## Inheritance

继承是一个合约可以继承另一个合约的属性和方法的程序。Solidity 支持多重继承。合约可以通过使用 `is` 关键字来继承其他合约。

注意：我们实际上也在入门教程加密货币和NFT教程中做了继承--我们分别从`ERC20`和`ERC721`合约中继承。

一个父合同如果有一个可以被子合同重写的函数，必须被声明为一个`virtual`函数。

要重写父函数的子协定必须使用`override`关键字。

如果父合约以相同的名称共享方法或属性，则继承的顺序很重要。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

/* Graph of inheritance
    A
   / \
  B   C
 /   /
D   E

*/

contract A {
    // Declare a virtual function foo() which can be overridden by children
    function foo() public pure virtual returns (string memory) {
        return "A";
    }
}

contract B is A {
    // Override A.foo();
    // But also allow this function to be overridden by further children
    // So we specify both keywords - virtual and override
    function foo() public pure virtual override returns (string memory) {
        return "B";
    }
}

contract C is A {
    // Similar to contract B above
    function foo() public pure virtual override returns (string memory) {
        return "C";
    }
}

// When inheriting from multiple contracts, if a function is defined multiple times, the right-most parent contract's function is used.
contract D is B, C {
    // D.foo() returns "C"
    // since C is the right-most parent with function foo();
    // override (B,C) means we want to override a method that exists in two parents
    function foo() public pure override (B, C) returns (string memory) {
        // super is a special keyword that is used to call functions
        // in the parent contract
        return super.foo();
    }
}

contract E is C, B {
    // E.foo() returns "B"
    // since B is the right-most parent with function foo();
    function foo() public pure override (C, B) returns (string memory) {
        return super.foo();
    }
}
```

## Transferring ETH


有三种方法可以将 ETH 从合同转移到其他地址。然而，其中两个方法在最新版本中不再被 Soliity 推荐，因此我们将跳过这些方法。

目前，从合约中转移ETH的推荐方式是使用`call`函数。调用函数返回一个表示转移成功或失败的bool。

### 如何在一个常规的以太帐户地址接收以太

如果将 ETH 转移到一个常规帐户(如 Metamask 地址) ，您不需要做任何特殊的事情，因为所有这样的帐户都可以自动接受 ETH 转移。

### 如何在合约中接收以太

但是，如果你写的合同希望能够直接接收ETH转账，你必须至少具备以下功能之一

- `receive() external payable`
- `fallback() external payable`

如果` msg.data` 为空值，则调用` receive()` ，否则使用 `falback ()`。

> `msg.data` 是指定任意数据和事务的一种方法，通常不需要手动使用。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract ReceiveEther {
    /*
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
    */

    // Function to receive Ether. msg.data must be empty
    receive() external payable {}

    // Fallback function is called when msg.data is not empty
    fallback() external payable {}

    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}

contract SendEther {
    function sendEth(address payable _to) public payable {
        // Just forward the ETH received in this payable function
        // to the given address
        uint amountToSend = msg.value;
        // call returns a bool value specifying success or failure
        (bool success, bytes memory data) = _to.call{value: msg.value}("");
        require(success == true, "Failed to send ETH");
    }
}
```

## Calling External Contracts

合约可以通过调用其他合约的实例上的函数来调用其他合约，比如`A.foo(x, y, z)`。要做到这一点，你必须有一个A的接口，告诉你的合约存在哪些函数。Solidity 中的接口表现得像头文件，它的作用类似于我们从前端调用合约时使用的 ABI。这允许合约知道如何对函数参数和返回值进行编码和解码，以便调用外部合约。

注意: 您使用的接口不需要扩展。也就是说，它们不一定包含外部合约中存在的所有函数——只包含您可能在某个时刻调用的那些函数。

假设有一个外部 `ERC20`合约，并且我们有兴趣调用 `balanceOf `函数来检查合约中给定地址的余额。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

interface MinimalERC20 {
    // Just include the functions we are interested in
    // in the interface
    function balanceOf(address account) external view returns (uint256);
}

contract MyContract {
    MinimalERC20 externalContract;
    
    constructor(address _externalContract) {
        // Initialize a MinimalERC20 contract instance
        externalContract = MinimalERC20(_externalContract);
    }
    
    function mustHaveSomeBalance() public {
        // Require that the caller of this transaction has a non-zero
        // balance of tokens in the external ERC20 contract
        uint balance = externalContract.balanceOf(msg.sender);
        require(balance > 0, "You don't own any tokens of external contract");
    }
}
```

## Import Statements

为了保持代码的可读性，您可以将 Soliity 代码分割到多个文件中。可靠性允许导入本地和外部文件。

### Local Imports

假设我们有这样的文件夹结构:

```
├── Import.sol
└── Foo.sol
```

`Foo.sol` 在哪

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

contract Foo {
    string public name = "Foo";
}
```

我们可以导入 `Foo` 并像这样在 `Import. sol `中使用它

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

// import Foo.sol from current directory
import "./Foo.sol";

contract Import {
    // Initialize Foo.sol
    Foo public foo = new Foo();

    // Test Foo.sol by getting it's name.
    function getFooName() public view returns (string memory) {
        return foo.name();
    }
}
```

注意: 在使用 Hardhat 时，我们还可以通过 npm 将合约作为节点模块安装，然后从 `node_ module` 文件夹导入合约。这些也算作本地导入，因为从技术上讲，当您安装一个包时，您正在将合同下载到您的本地计算机。

### External Imports

您还可以通过复制 URL 从 Github 导入。我们在加密货币和 NFT 教程的新手课程中这样做过。

```
// https://github.com/owner/repo/blob/branch/path/to/Contract.sol
import "https://github.com/owner/repo/blob/branch/path/to/Contract.sol";

// Example import ERC20.sol from openzeppelin-contract repo
// https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol";

```

## Solidity Libraries

Libraries 类似于 Solidy 中的合同，但有一些局限性。库不能包含任何状态变量，也不能传输 ETH。


通常，库用于向合约中添加助手函数。一个非常常用的库在实体世界是安全数学-它确保数学运算不会导致一个整数下溢或溢出。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

library SafeMath {
    function add(uint x, uint y) internal pure returns (uint) {
        uint z = x + y;
        // If z overflowed, throw an error
        require(z >= x, "uint overflow");
        return z;
    }
}

contract TestSafeMath {
    function testAdd(uint x, uint y) public pure returns (uint) {
        return SafeMath.add(x, y);
    }
}
```

祝贺你们走到这一步: D

> 原文：[https://www.learnweb3.io/tracks/sophomore/advanced-solidity](https://www.learnweb3.io/tracks/sophomore/advanced-solidity)


![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
