---
title: Web3系列教程之新手篇---Level 6：Solidity
description: null
author: 李留白
weight: 0
date: 2022-06-19T16:27:47.998Z
lastmod: 2022-06-26T10:48:26.265Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/freshman.png
---

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220626183840.png)

在本模块中，您将了解什么是 Solidity 以及该语言的基本语法。

## 什么是Solidity？

- Solidity 是一种面向对象的高级语言，用于实现智能合约。它旨在针对以太坊虚拟机（EVM）
- 它是静态类型的，支持继承、库和复杂的用户定义类型等特性。

## Solidity开发构建

### 初始化智能合约

```sol
// Define the compiler version you would be using
pragma solidity ^0.8.10;

// Start by creating a contract named HelloWorld
contract HelloWorld {

}
```

### 变量和类型

在Solidity中，有3种类型的变量

- 本地
  - 在函数中声明的，不存储在区块链上。
- 状态
  - 在函数外声明，以维持智能合约的状态
  - 存储在区块链上
- 全局性
  - 提供关于区块链的信息。它们是由Ethereum虚拟机在运行时注入的。
  - 包括诸如交易发送者、区块时间戳、区块哈希等。
  - [全局变量的例子](https://docs.soliditylang.org/en/v0.8.9/units-and-global-variables.html)

变量的范围是由它们被声明的地方定义的，而不是它们的值。将一个局部变量的值设置为全局变量并不能使其成为全局变量，因为它仍然只能在其范围内被访问。

```sol

// Define the compiler version you would be using
pragma solidity ^0.8.10;

// Start by creating a contract named Variables
contract Variables {
    /*
        ******** State variables **********
    */
    /*
    uint stands for unsigned integer, meaning non negative integers
    different sizes are available. Eg
        - uint8   ranges from 0 to 2 ** 8 - 1
        - uint256 ranges from 0 to 2 ** 256 - 1
    `public` means that the variable can be accessed internally
     by the contract and can also be read by the external world
    */
    uint8 public u8 = 10;
    uint public u256 = 600;
    uint public u = 1230; // uint is an alias for uint256

    /*
    Negative numbers are allowed for int types. Eg
    - int256 ranges from -2 ** 255 to 2 ** 255 - 1
    */
    int public i = -123; // int is same as int256

    // address stands for an ethereum address
    address public addr = 0xCA35b7d915458EF540aDe6068dFe2F44E8fa733c;

    // bool stands for boolean
    bool public defaultBoo1 = false;

    // Default values
    // Unassigned variables have a default value in Solidity
    bool public defaultBoo2; // false
    uint public defaultUint; // 0
    int public defaultInt; // 0
    address public defaultAddr; // 0x0000000000000000000000000000000000000000

    function doSomething() public {
        /*
        ******** Local variable **********
        */
        uint ui = 456;

        /*
        ******** Global variables **********
        */

        /*
            block.timestamp tells us whats the timestamp for the current block
            msg.sender tells us which address called the doSomething function
        */
        uint timestamp = block.timestamp; // Current block timestamp
        address sender = msg.sender; // address of the caller
    }
}

```

### 函数, 循环 and If/Else

```sol
// Define the compiler version you would be using
pragma solidity ^0.8.10;

// Start by creating a contract named Conditions
contract Conditions {
    // State variable to store a number
    uint public num;

    /*
        Name of the function is set.
        It takes in a uint and sets the state variable num.
        It is a declared as a public function meaning
        it can be called from within the contract and also externally.
    */
    function set(uint _num) public {
        num = _num;
    }

    /*
        Name of the function is get.
        It returns the value of num.
        It is declared as a view function meaning
        that the function doesnt change the state of any variable.
        view functions in solidity do not require gas.
    */
    function get() public view returns (uint) {
        return num;
    }

    /*
        Name of the function is foo.
        It takes in  uint and returns an uint.
        It compares the value of x using if/else
    */
    function foo(uint x) public returns (uint) {
        if (x < 10) {
            return 0;
        } else if (x < 20) {
            return 1;
        } else {
            return 2;
        }
    }

    /*
        Name of the function is loop.
        It runs a loop till 10
    */
    function loop() public {
        // for loop
        for (uint i = 0; i < 10; i++) {
            if (i == 3) {
                // Skip to next iteration with continue
                continue;
            }
            if (i == 5) {
                // Exit loop with break
                break;
            }
        }
    }


}

```

### 数组、字符串

数组可以有一个编译时的固定大小，也可以有一个动态大小。

```sol

pragma solidity ^0.8.10;

contract Array {

    // Declare a string variable which is public
    string public greet = "Hello World!";
    // Several ways to initialize an array
    // Arrays initialized here are considered state variables that get stored on the blockchain
    // These are called storage variables
    uint[] public arr;
    uint[] public arr2 = [1, 2, 3];
    // Fixed sized array, all elements initialize to 0
    uint[10] public myFixedSizeArr;
    /*
        Name of the function is get
        It gets the value of element stored in an array's index
    */
    function get(uint i) public view returns (uint) {
        return arr[i];
    }

    /*
     Solidity can return the entire array.
     This function gets called with and returns a uint[] memory.
     memory - the value is stored only in memory, and not on the blockchain
              it only exists during the time the function is being executed

     Memory variables and Storage variables can be thought of as similar to RAM vs Hard Disk.
     Memory variables exist temporarily, during function execution, whereas Storage variables
     are persistent across function calls for the lifetime of the contract.
     Here the array is only needed for the duration while the function executes and thus is declared as a memory variable
    */
    function getArr(uint[] memory _arr) public view returns (uint[] memory) {
        return _arr;
    }

     /*
        This function returns string memory.
        The reason memory keyword is added is because string internally works as an array
        Here the string is only needed while the function executes.
    */
    function foo() public returns (string memory) {
        return "C";
    }

    function doStuff(uint i) public {
        // Append to array
        // This will increase the array length by 1.
        arr.push(i);
        // Remove last element from array
        // This will decrease the array length by 1
        arr.pop();
        // get the length of the array
        uint length = arr.length;
        // Delete does not change the array length.
        // It resets the value at index to it's default value,
        // in this case it resets the value at index 1 in arr2 to 0
        uint index = 1;
        delete arr2[index];
        // create array in memory, only fixed size can be created
        uint[] memory a = new uint[](5);
        // create string in memory
        string memory hi = "hi";
    }

 }

```

## 参考

[Solidity by Example](https://solidity-by-example.org/)

## 额外的学习资源

- [Cryptozombies](https://cryptozombies.io/)
- [Solidity by Example](https://solidity-by-example.org/)
- [Solidity docs](https://docs.soliditylang.org/en/v0.8.10/)

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/my.png)