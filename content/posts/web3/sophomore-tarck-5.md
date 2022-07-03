---
title: Web3系列教程之入门篇---5. 以太坊虚拟机（EVM）
description: null
author: 李留白
weight: 0
date: 2022-07-03T13:49:40.864Z
lastmod: 2022-07-03T13:54:11.202Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703215340.png
---

以太坊网络的存在仅仅是为了保持状态机（即以太坊区块链）的单一、连续、不间断和不可变的操作。它是所有以太坊账户、智能合约和数据所在的环境。在任何给定的区块中，以太坊都有一个也是唯一一个全球公认的“状态”。以太坊虚拟机 (EVM) 定义了从块到块计算新的有效状态的规则。

## 先决条件

理解 EVM 需要对[字节](https://en.wikipedia.org/wiki/Byte)、[内存](https://en.wikipedia.org/wiki/Computer_memory)和[堆栈](https://en.wikipedia.org/wiki/Stack_(abstract_data_type))有一些基本的了解。

熟悉一些密码学（如[散列函数](https://en.wikipedia.org/wiki/Cryptographic_hash_function)）也可能会有所帮助。

## 以太坊作为状态机

像比特币这样的区块链通常被描述为“分布式账本”，它使用密码学的基本工具来实现去中心化货币的存在。

加密货币的行为可以像“正常”货币一样，因为规则规定了人们可以做什么和不可以做什么来修改这个分类帐。例如，一个比特币地址不能花费比它之前收到的更多的比特币。这些规则支持在比特币以及类似的其他区块链上发生的所有交易。

虽然以太坊也有其原生加密货币以太币，但它还支持我们已经看到的更强大的功能——智能合约。对于这个更复杂的功能，我们需要一个比“分布式账本”更强大的类比。

以太坊可以描述为分布式[状态机](https://en.wikipedia.org/wiki/Finite-state_machine)，而不是分布式账本。状态机本质上是可以响应某些输入而从一种状态变为另一种状态的任何机器。

简单的状态机是一种投币式[旋转门](https://i.imgur.com/Uh7m6jN.png)，常见于地铁或火车站，用于防止人们进入，除非他们使用硬币支付或有票。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703213837.png)

旋转门的初始状态是锁定的。在锁定状态下，如果你继续推动它，它会保持锁定状态。如果您插入硬币，它会进入解锁状态。如果您继续插入硬币，它会保持解锁状态。一旦您进入解锁状态（有人通过），它就会再次锁定。

对于以太坊来说，状态要复杂得多。它使用包含区块链所有状态的大型数据结构来描述。状态如何从一个块到另一个块的变化的具体规则由 EVM 定义。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703213858.png)

## 以太坊状态转换

在高层次上，EVM 的行为类似于数学状态转换函数。给定当前状态和一组新的有效交易，它会产生一个新状态。输出是确定性的，这意味着对于相同的输入，它总是会产生相同的输出。

```
Y(S, T) = S'
```

给定旧的有效状态`S`和一组新的有效交易`T`，状态转换函数`Y`产生新的有效状态`S`。

以太坊中的状态存储为一个非常大的数据结构，称为`Merkle Patricia Trie`。您不需要确切了解它的结构，但如果您愿意，可以阅读给定的链接。

## EVM层

EVM 作为以太坊软件堆栈中的一个层存在。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703214107.png)

以太坊节点包含 EVM 的实现，然后 EVM 可以在其上执行 EVM 代码。EVM 代码是编译后的可以执行的智能合约字节码。

## EVM 代码生成

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703214135.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703214151.png)

## EVM 指令（操作码）

EVM 本身就像一个[堆栈机器](https://en.wikipedia.org/wiki/Stack_machine)，堆栈上的最大深度为 1024 个项目。堆栈中的每一项都是一个 256 位（32 字节）的字。

在执行期间，EVM 维护一个临时内存，作为一个 32 字节寻址的字节数组，它不会在事务之间持续存在。执行新事务时清除瞬态内存。

然而，智能合约确实在区块链中维护自己的状态。该状态也被建模为[Merkle Patricia Trie](https://eth.wiki/en/fundamentals/patricia-tree)。这通常被称为事务执行期间的 EVM存储。

EVM 具有允许其执行[EVM Opcodes](https://ethereum.org/en/developers/docs/evm/opcodes/)的逻辑，这些操作码在堆栈上执行标准操作，例如、 、 、XOR等ADD。EVMAND还实现了许多区块链特定的堆栈操作，例如和。SUBMULBALANCEBLOCKHASH

当智能合约被编译成字节码（以十六进制表示）时，它会编译成 EVM 操作码。这些操作码是在 EVM 上执行的。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703214431.png)

## EVM 实现

EVM 的所有实现都必须遵守[以太坊黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf)中描述的规范。在以太坊的历史上，EVM 经历了多次修订，现在存在多种编程语言的 EVM 实现。

所有以太坊客户端都包含一个 EVM 实现。除此之外，还有多个独立的实现。

## Ethereum 客户端 (with EVM)

- [Geth](https://geth.ethereum.org/) | Programming Language = Go
- [OpenEthereum](https://github.com/openethereum/openethereum) | Programming Language = Rust
- [Nethermind](https://nethermind.io/) | Programming Language = C# (.NET)
- [Besu](https://consensys.net/quorum/developers/) | Programming Language = Java
- [Erigon](https://github.com/ledgerwatch/erigon) | Programming Language = Go

## 独立的EVM实现

- [Py-EVM ](https://github.com/ethereum/py-evm)| Programming Language = Python
- [evmone](https://github.com/ethereum/evmone) | Programming Language = C++
- [ethereumjs-evm](https://github.com/ethereumjs/ethereumjs-monorepo) | Programming Language = Javascript
- [Enclave EVM](https://github.com/microsoft/eevm) | Programming Language = C++

## 资料

以下是推荐的，但可选择的阅读/观看内容，以了解更多关于EVM的信息。

- [Ethereum EVM: Illustrated](https://takenobu-hs.github.io/downloads/ethereum_evm_illustrated.pdf)
- [EVM Opcodes](https://www.ethervm.io/)
- [Ethereum Yellowpaper](https://ethereum.github.io/yellowpaper/paper.pdf)
- [Understanding EVM](https://www.youtube.com/watch?v=RxL_1AfV7N4)
- [Merkle Patricia Trie](https://www.youtube.com/watch?v=OxofT39TJgg)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
