---
title: ETH 2.0 中的 eWASM
description: null
author: 李留白
weight: 0
date: 2023-04-28T14:30:30.337Z
lastmod: 2023-04-28T15:22:33.969Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230428230130.png
---

## 关于

eWASM 是 ETH 2.0 的一个关键特性，开发者和用户都应该知道。eWASM 是一个新的虚拟机，它将取代当前 ETH 1.0 使用的 EVM（以太坊虚拟机）。eWASM 基于 WebAssembly 标准，旨在提供更好的性能和与其他编程语言的兼容性。这将使开发人员更容易使用更广泛的编程语言在以太坊平台上构建去中心化应用程序。此外，eWASM 将提供更好的性能，使去中心化应用程序能够更快、更高效地运行。总体而言，eWASM 是 ETH 2.0 的一项重要功能，将为开发人员和用户带来显着的好处。

## eWASM 在 eth 2.0 中是如何工作的

它基于 WebAssembly 标准，旨在提供更好的性能和与其他编程语言的兼容性。eWASM 将取代目前 ETH 1.0 使用的 EVM（以太坊虚拟机）。eWASM 的主要优势在于它将提供更好的性能，让去中心化应用程序运行得更快、更高效。此外，eWASM 将使开发人员更容易使用更广泛的编程语言在以太坊平台上构建去中心化应用程序。这将使开发人员更容易访问该平台，并增加在该平台上构建的去中心化应用程序的数量。eWASM 的工作原理是将用 C、C++ 和 Rust 等编程语言编写的智能合约编译成 WebAssembly 字节码，然后可以在以太坊网络上执行。全面的，

## eWASM 和 EVM 的区别

以太坊虚拟机（EVM）是一种轻量级虚拟机，旨在在以太坊网络上运行智能合约，而 eWASM 是 WebAssembly 语言的一个子集，建议在以太坊节点中替代 EVM 以执行智能合约。EVM 是在理论上创建的，而不是实际创建的，因为它似乎并不适合实际应用程序，而 eWASM 旨在提高效率并使程序员能够使用更多语言编写，因为它是 WASM 的根。EVM 经常难以编译大量代码，而 eWASM 被吹捧为比 EVM 更快、更灵活。

## 使用 eWASM 优于 EVM 的优势

1. 性能：与 EVM 相比，eWASM 提供更好的性能，因为它使用 WebAssembly，其设计目的是比 EVM 字节码更快、更高效。WebAssembly 提供接近本机的性能，这可以显着提高以太坊网络的速度和可扩展性。
2. 互操作性：eWASM 提供比 EVM 更好的互操作性，因为它支持多种编程语言，包括 C++、Rust 和 AssemblyScript。这使开发人员能够用他们喜欢的语言编写智能合约，从而提高代码质量和开发人员的工作效率。
3. 安全性：eWASM 提供比 EVM 更好的安全性，因为它包含多个安全功能，例如内存沙箱，它可以将智能合约彼此隔离并防止它们访问彼此的内存。此外，eWASM 提供更好的保护，防止常见的智能合约漏洞，例如重入攻击和整数溢出。
4. 灵活性：eWASM 提供了比 EVM 更好的灵活性，因为它支持动态链接，这使得智能合约可以由多个可以独立更新的模块组成。这可以带来更好的代码组织和更轻松的智能合约维护。
5. 社区支持：eWASM 获得了以太坊社区的大力支持，几个主要的以太坊客户，包括 Geth 和 Parity，已经实施了 eWASM 支持。这意味着开发人员在使用 eWASM 构建智能合约时可以访问范围广泛的工具和资源。

## 关于 eWASM 的未知事实

1. eWASM 基于 WebAssembly 标准，该标准由包括谷歌、微软和 Mozilla 在内的技术公司联盟开发。这意味着 eWASM 建立在一个成熟且广泛使用的标准之上，这有助于促进互操作性和采用。
2. eWASM 的开发由著名的以太坊开发人员 Lane Rettig 领导，他还参与过其他以太坊项目，包括伊斯坦布尔硬分叉和以太坊 Cat Herders 社区。
3. eWASM 于 2016 年首次被提出作为 EVM 的替代品，此后一直在开发中。eWASM 团队与其他以太坊开发人员和利益相关者密切合作，以确保从 EVM 到 eWASM 的过渡尽可能顺利。
4. eWASM 旨在与现有的以太坊基础设施兼容，包括智能合约开发工具和库。这意味着开发人员在使用 eWASM 构建智能合约时可以继续使用他们喜欢的开发工具。
5. eWASM 仍处于开发的早期阶段，许多细节仍在制定中。然而，eWASM 团队致力于为智能合约开发打造一个稳定可靠的平台，他们正与以太坊社区密切合作，以确保 eWASM 满足开发者和用户的需求。

## eWASM 如何改进智能合约的执行？

首先，它比 EVM 更高效，这意味着它可以使大部分预编译变得冗余。

其次，eWASM 旨在使程序员能够使用更多语言编写，因为它是 WASM 的根源，而这对于 EVM 是不可能的。

第三，eWASM 比 EVM 更快，这意味着它可以极大地提高交易吞吐量。

第四，eWASM 比 EVM 更安全，因为它是标准化的。

最后，eWASM 可以在任何地方执行并受益于 WASM 生态系统，这意味着它可以支持更多语言，而不仅仅是 Solidity。

总之，eWASM 通过比 EVM 更高效、灵活、快速、安全和支持更多语言来改进智能合约执行。

## 如何使用 eWASM 编写智能合约

使用 eWASM 编写智能合约，不需要在 eWASM 中编写合约。相反，您可以使用 Solidity 编写它，它将被转换为 EVM 字节码，然后是 eWASM [1](https://ethereum.stackexchange.com/questions/45044/how-to-start-writing-a-smart-contract-in-ewasm)。Ethereum WebAssembly 是使用 WebAssembly [2](https://ewasm.readthedocs.io/)的确定性子集对以太坊智能合约执行层进行重新设计的建议。使用 WebAssembly 作为智能合约的格式可以获得多种好处，包括智能合约接近本机的执行速度、使用许多传统编程语言（如 C、C++ 和 Rust）开发智能合约的可能性，以及访问庞大的开发者社区和围绕 WebAssembly 工具链。网上有教程解释了如何在 eWASM 上编译和部署 ERC20 合约[3](https://blog.secondstate.io/post/20191025-compile-and-deploy-an-erc20-contract-on-ewasm/) .

## 在 Solidity 中创建简单智能合约的步骤

1. 打开 Solidity 编辑器，例如 Remix。
2. 编写合约功能和数据的代码。例如，您可以编写一个简单的合约来存储和检索字符串值。
3. 编译代码以检查错误。
4. 通过指定合约的地址和气体限制将合约部署到以太坊区块链。
5. 通过调用其函数并验证结果来测试合约。

```solidity
pragma solidity ^0.8.0;

contract SimpleStorage {
    string value;

    function set(string memory _value) public {
        value = _value;
    }

    function get() public view returns (string memory) {
        return value;
    }
}
```

该合约有两个功能：`set`和`get`。函数`set`设置变量`value`的值，而`get`函数检索变量`value`的值。

## 开启区块链开发之旅的推荐书籍

- [以太坊圣经：关于以太坊你需要知道的一切](https://amzn.to/3VbOjaT)
- [Solidity 中的以太坊智能合约开发](https://amzn.to/425C4yR)
- [掌握以太坊构建智能合约和 DAPP](https://amzn.to/3V9erCZ)
- [以太坊傻瓜书](https://amzn.to/3VbGUbt)

> 原文：https://blog.metaalgo.in/ewasm-in-eth-20

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)