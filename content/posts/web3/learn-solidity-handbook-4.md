---
title: 学习 Solidity——智能合约开发手册(4)
description: null
author: 李留白
weight: 0
date: 2022-12-19T14:15:29.151Z
lastmod: 2022-12-21T08:06:36.999Z
tags: []
categories:
  - 区块链
  - WEB3.0
  - Solidity
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221219221551.png
draft: true
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