---
title: 三大区块链开发环境
description: null
author: 李留白
weight: 0
date: 2022-10-04T02:09:48.635Z
lastmod: 2022-10-04T02:21:24.966Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101002.png
---

> 区块链开发人员用于创建、测试和部署智能合约的三大开发环境

区块链为不同行业带来了巨大的变革，从*安全*领域到*金融*等等。因此，对区块链开发人员的需求有所增加。

**如此多的工具**、**库**和**框架**的可用性提出了一个小挑战，因为知道使用哪个以及何时使用可能会令人困惑。

**本文将探讨区块链开发人员用于创建**、**测试**和**部署**智能合约的三大开发环境。我们还将了解何时以及为何使用每一个。

## 什么是区块链开发环境？

区块链开发环境也称为 IDE（集成开发环境），是一种用于构建、测试和部署智能合约的软件设置。

**区块链 IDE 做了三件事：**

1. 在您的机器上本地编译你的合同。
2. 有效地测试您的合同。
3. 将您的合约部署到网络节点。

**IDE 由以下部分组成：**

- **代码编辑器**：
  代码编辑器用于编写代码，并通过语法突出显示和自动完成等独特功能帮助开发人员。
- **调试器**：
  这会检查开发人员的测试程序是否存在错误。
- **编译器**：
  编译器负责将编写的源代码翻译成机器语言。
- **自动化工具**：
  这些工具可以自动化重复性任务以节省更多时间，例如自动化不同程序的重复测试。

> 一句话，区块链开发环境是一套完整的构建智能合约的工具包。

下面，我们将探讨排名前 3 的区块链 IDE 及其功能：

1. Hardhat
2. Truffle
3. Remix

## 什么是Hardhat？

```js
$ npx hardhat
888    888                      888 888               888
888    888                      888 888               888
888    888                      888 888               888
8888888888  8888b.  888d888 .d88888 88888b.   8888b.  888888
888    888     "88b 888P"  d88" 888 888 "88b     "88b 888
888    888 .d888888 888    888  888 888  888 .d888888 888
888    888 888  888 888    Y88b 888 888  888 888  888 Y88b.
888    888 "Y888888 888     "Y88888 888  888 "Y888888  "Y888

👷 Welcome to Hardhat v2.9.9 👷‍

? What do you want to do? …
❯ Create a JavaScript project
  Create a TypeScript project
  Create an empty hardhat.config.js
  Quit
```

[Hardhat](https://hardhat.org/)是一个以太坊开发环境。您可以利用其各种组件来编辑、编译、调试和部署您的***[dApp](https://web3.hashnode.com/web3.hashnode.com/glossary/what-are-dapps)\***和***[智能合约](https://web3.hashnode.com/glossary/what-are-smart-contracts)\***。它包括内置的自动化流程，以帮助开发人员简化复杂的程序。

Hardhat 拥有大量**插件**，并允许自定义、灵活性和可扩展性，让开发人员可以完全控制他们的项目。

此外，它具有内置的本地以太坊**网络节点**，可让您在计算机上本地调试代码。

### 特征

- Hardhat 有大量的插件，可以轻松定制。
- Hardhat 使用 `Ether.js`其默认的 web3 库。
- Hardhat 可以运行该 `console.log`功能进行调试。
- Hardhat 提供原生`Typescript`支持，并且还有一个**Vscode**扩展，为 Vscode 编辑器添加了可靠的支持。
- Hardhat 带有一个内置的本地以太坊网络，称为**Hardhat Network**，用于在本地机器上运行和部署智能合约。

### 谁使用Hardhat？

**1.** [Chainlink](https://chain.link/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101309.png)

基于以太坊的区块链预言机网络，Chainlink 是一个去中心化的网络，可以更轻松地将防篡改数据从链下来源转移到链上智能合约。

**2.** [Uniswap](https://uniswap.org/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101351.png)

众所周知，Uniswap 是领先的加密货币交易所之一，是一个在以太坊区块链上运行的开源和去中心化平台。它使用一种不同类型的交易模型，称为自动流动性协议。

**3.** [Aave](https://aave.com/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101409.png)

Aave 是一个运行在以太坊区块链上的智能合约系统。它使资产能够通过运行其软件的分布式计算机网络进行管理。它是一个去中心化的借贷平台，消除了中间商，让用户可以借出、借入加密资产并赚取利息。

**4.** [SushiSwap](https://sushi.com/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101431.png)

SushiSwap 是最初从 Uniswap 分叉出来的去中心化交易所，它利用流动性池中的智能合约（流动性池是用户通过锁定其加密资产成为流动性提供者的地方）来处理加密货币交易。

**5.** [ENS](https://ens.domains/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101445.png)

以太坊名称服务基本上是域名服务，但具有以太坊区块链提供的超能力。拥有域的规则由智能合约运行，您可以使用唯一名称创建子域。

### 为什么要使用Hardhat？

- 与 Truffle 相比，Hardhat 更灵活。
- Hardhat 允许同时使用 Ether.js 和 web3js。
- Hardhat 具有良好的调试能力。
- Hardhat 会在调试时为您提供代码中发生的堆栈跟踪。
- 大多数大型项目都在迁移到Hardhat。

### 文档和资源

Hardhat的官方[文档](https://hardhat.org/docs)非常简洁明了。它具有突出 IDE 不同功能的不同部分，并使用代码片段和分步大纲对其进行了深入解释。突出显示的功能是；

- Hardhat跑步者
- Hardhat网络
- 用于 Vscode 的Hardhat
- Hardhat柴火柴人
- Hardhat网络助手

官方 Hardhat 教程是另一个有用的工具；它提供了有关如何设置环境、创建智能合约并将其部署到Hardhat和以太坊网络的说明。该教程对初学者很友好，并且有外部链接可以帮助新手了解如何使用 IDE。

## 什么是Truffle？

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101559.png)

[Truffle](https://trufflesuite.com/)是一个开发环境和测试框架，用于创建、测试和部署智能合约到以太坊测试网络或主网络。它利用 node.js 包管理并提供 CLI（命令行界面）来管理和创建智能合约。

您可以使用 Truffle 提供的名为**Ganache**的私有区块链环境私下部署您的智能合约。根据您的喜好，您可以使用可用的 Ganache 区块链的两个版本之一：**UI**（用户界面）或**CLI**（命令行界面）。

### 特征

- Truffle 使用 web3.js 作为其默认的 Web 库。
- Hardhat 和 Truffle 都支持**分叉**功能（在不影响原始软件的情况下复制软件并进行实验）。
- 它带有一组名为**Drizzle**的前端库，用于为 dApp 构建前端。
- 它支持使用**Filecoin**和**Tezos**进行开发，它还具有 Vscode 的扩展。

### 谁在使用Truffle ？

**1.** [Amazon](https://www.amazon.com/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101638.png)

每个人都知道亚马逊，一家专注于数字流媒体、云计算和人工智能的跨国美国科技公司。

**2.** [Microsoft](https://www.microsoft.com/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101652.png)

他们也不需要介绍，您很可能正在从他们的产品中阅读这篇文章。微软是一家美国跨国公司，生产个人电脑、电子产品、软件产品和其他相关服务。

**3.** [Consensys](https://consensys.net/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101711.png)

Consensys 是一家区块链软件公司，参与了 Metamask 和 Infura 等流行的区块链项目。

**4.** [J.P. Morgan](https://www.jpmorgan.com/DE/en/about-us)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101732.png)

JP Morgan 是一家美国跨国投资银行和金融服务控股公司，为全球 100 多个国家的最重要的组织、政府和机构提供解决方案。

**5.** [ShapeShift](https://shapeshift.com/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101748.png)

ShapeShift 是一个私人加密货币交易平台，允许用户赚取、购买和交易加密货币资产

### 为什么要使用Truffle？

- Truffle 是一个更加静态的开发环境；它提供较少的定制灵活性；大多数想要开始工作的开发人员更愿意适应这个框架。
- Ganache UI 对初学者非常友好。
- Truffle 是最古老、最流行的框架。
- Truffle 拥有庞大的插件生态系统。

### 文档和资源

Truffle 有一个易于理解的[文档](https://trufflesuite.com/docs)，通过该文档，您将学习如何使用 IDE 及其原生区块链的不同功能。它还有关于如何将其与不同区块链集成的不同指南，例如 Tezos、Filecoin 和 Hyperledger Fabric (EVM)。

由于 Truffle 已经存在了一段时间，围绕它构建了一个生态系统，可以轻松找到教程、文章和其他有用的资源，以帮助理解如何使用 IDE。

## 什么是Remix ？

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101848.png)

[Remix](https://remix.ethereum.org/)是一个开源的 Web 和桌面集成开发环境，用于开发基于以太坊的应用程序。它是一个在线工具，可让您直接从浏览器编写、编译和部署 Solidity 智能合约。

它具有丰富的插件集和易于使用的图形用户界面 (GUI)，并且不需要任何类型的设置。由于其显着的特点，它通常被称为学习如何使用以太坊网络的**游乐场。**

重要的是要注意，Remix IDE 是称为**Remix 项目**的子项目，这通常可以互换使用。其他子项目是*Remix Plugin Engine*和*Remix Libraries*。

### 特征

- 它有一个独特的功能，允许您直接在浏览器中编写代码、编译代码和部署智能合约。
- 它有一组插件，允许您扩展 IDE 并为您的项目添加自定义功能，这些插件可在图标选项卡中访问。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101912.png)

- 它有一个 CLI，可用于侦听事务、检查事务详细信息和调试。

### 谁在使用Remix？

**1.** [Ethereum.org](https://ethereum.org/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101939.png)

[Ethereum.org](http://ethereum.org/)是为以太坊社区创建的平台，成员在这里进行自我教育并获取有关以太坊的最新消息。它基本上是一个获得以太坊所有东西的地方。

**2.** [OpenSea](https://opensea.io/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004101953.png)

OpenSea 也被称为世界上最大和第一个 NFT 市场，是一个去中心化的市场，允许用户创建和销售不可替代的代币。

**3.** [Moonbeam Network](https://moonbeam.network/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221004102019.png)

Moonbeam 是基于 Polkadot 区块链的加密代币。Polkadot 的用户可以构建自己的平行链（连接到 Polkadot 主中继链的区块链），所以 Moonbeam 是连接到 Polkadot 主中继链的平行链。Moonbeam 以这样一种方式运行，即使它在 Polkadot 上运行，它也可以连接到以太坊区块链并与之通信。

### 为什么要使用Remix？

- 对于那些开始以太坊开发的人来说，它是最简单的开发工具。
- 它有一个易于使用的 GUI。
- 在构建较小的项目时，它是一个更好的工具。
- 当您想尝试新的 Solidity 功能时，它是一个更好的工具。

### 文档和资源

remix[文档](https://remix-ide.readthedocs.io/en/latest/#)是一个易于浏览的资源，它为初学者提供了使用和熟悉 IDE 布局的良好介绍。涵盖的主题包括：

- 默认模块
- Solidity 模块
- 单元测试
- 外部集成
- 了解Remix

## 总结

如果你刚开始，我建议你从 Remix 开始，然后当你想从事需要可扩展性和可扩展性的大型项目时，你应该使用 Hardhat 或 Truffle。话虽如此，有一个流行的说法是这样的；

> 一个坏工人总是责怪他的工具。-[非洲谚语](https://www.advance-africa.com/a-bad-workman-always-blames-his-tools.html#:~:text=The meaning of this proverb,never complete a job successfully.)

在这种情况下也是如此，我试图启发您使用哪种工具以及每种工具相对于其他工具的优势。但是，如果您不学会高效地做事，这将毫无意义。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
