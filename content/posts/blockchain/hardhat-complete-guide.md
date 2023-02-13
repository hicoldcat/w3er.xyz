---
title: Hardhat：完整指南
description: null
author: 李留白
weight: 0
date: 2023-02-13T12:55:03.395Z
lastmod: 2023-02-13T13:20:09.612Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230213205818.png
---

Hardhat框架是专门为以太坊智能合约开发设计的开发环境。它提供了一套工具和实用程序，可以更轻松地在以太坊网络上编译、测试和部署智能合约。借助 Hardhat，开发人员可以使用以太坊最流行的编程语言 Solidity 编写智能合约，并利用自动合约测试、合约调试等高级功能，以及与 VSCode 和 Truffle 等流行开发工具的集成。

除了其核心功能外，Hardhat 还提供了一系列插件，可进一步扩展其功能。这些插件涵盖了广泛的用例，从安全和审计到工具和集成。例如，Hardhat Security 插件提供了一个高级安全分析工具，可以检测智能合约中的常见漏洞，而 Hardhat Network 插件使开发人员能够创建自定义测试环境来模拟真实世界的网络条件。Hardhat Etherscan 插件等其他插件提供与流行的以太坊区块链浏览器的轻松集成，使开发人员能够轻松验证已部署的智能合约并与之交互。凭借其广泛的插件系统，Hardhat可以进行定制，以满足任何以太坊开发项目的需求，使其成为所有技能水平的开发者的绝佳选择。

## Hardhat的好处

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230213205944.png)

### 集成

Hardhat框架对Ethereum智能合约开发的好处很多。一个主要的好处是它与其他流行的Ethereum开发工具集成。Hardhat 旨在与 Truffle 和 Remix 等工具无缝协作，使开发人员能够利用每个工具的最佳功能。Hardhat 还与 VSCode 和 Vim 等流行的代码编辑器集成，使其能够轻松编写、测试和部署智能合约而无需离开编辑器。

### 测试

使用 Hardhat 的另一个主要好处是其先进的测试能力。Hardhat 带有一个内置的测试框架，使开发人员能够为其智能合约编写全面的测试套件。这些测试可以在本地或远程网络上运行，确保智能合约在部署到以太坊网络之前经过全面测试。Hardhat 还支持自动化测试，这可以节省开发人员的时间并提高其智能合约的整体质量。

### 自动化

Hardhat 还为自定义任务和工作流提供广泛的支持。开发者可以使用 Hardhat 任务系统自动执行重复性任务，例如编译合约、运行测试以及将合约部署到以太坊网络。Hardhat 还支持自定义脚本和工作流，可用于创建满足项目特定需求的复杂开发环境和工作流。这种灵活性使开发人员能够更高效地工作并适应不断变化的项目需求。

### 安全性

最后，Hardhat 框架最重要的好处之一是它对安全性的关注。Hardhat 旨在确保智能合约安全可靠，具有自动安全分析和广泛审计功能等功能。例如，Hardhat Security 插件可以分析智能合约并检测常见的安全漏洞，而 Hardhat Network 插件使开发人员能够模拟真实世界的网络状况并确保他们的智能合约按预期执行。这些功能有助于确保使用 Hardhat 开发的智能合约安全可靠，这对于处理敏感数据或金融交易的项目至关重要。

## Hardhat的核心功能

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230213210026.png)

1. **测试**：Hardhat 提供了一个强大的测试框架，可以轻松地为您的智能合约编写和运行测试。该测试框架建立在流行的 Mocha 测试库之上，包括诸如合约部署和交易模拟等常见任务的测试助手、确定性合约测试和气体消耗报告等功能。
2. **部署**：Hardhat 可以轻松地将您的智能合约部署到不同的以太坊网络。该框架包括对流行的以太坊网络（如以太坊主网、Rinkeby 和 Kovan）的内置支持，并且可以轻松配置以与自定义网络一起使用。Hardhat 还包括一个强大的部署脚本系统，允许您使用 JavaScript 编写自定义部署脚本。
3. **调试**：Hardhat 包含一个内置调试器，可以轻松调试您的智能合约。调试器可用于逐步执行合约、检查变量和合约状态，以及诊断合约代码的问题。该调试器与流行的代码编辑器（如 Visual Studio Code）集成，并支持使用 Solidity 和 Vyper 编程语言进行调试。

## Hardhat的实施

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230213210047.png)

### 安装和初始化

1. 安装 Node.js：Hardhat 是基于 Node.js 构建的，因此第一步是在本地计算机上安装 Node.js。您可以从官方网站**[nodejs.org](https://nodejs.org/)**下载并安装最新版本的 Node.js。
2. 安装 Hardhat：安装 Node.js 后，您可以通过在终端中运行以下命令，使用节点包管理器 (npm) 安装 Hardhat：

```bash
npm install --save-dev hardhat
```

此命令将 Hardhat 安装为项目中的开发依赖项。

初始化 Hardhat 项目：安装 Hardhat 后，您可以通过在终端中运行以下命令来创建新的 Hardhat 项目：

```bash
npx hardhat init
```

该命令在一个名为`my-hardhat-project`的新目录下创建一个新的Hardhat项目。你可以用你选择的名字替换`my-hardhat-project`。

探索项目：初始化完成后，您可以通过在终端中运行以下命令来探索项目目录：

```bash
cd my-hardhat-project
ls
```

这会将您的目录更改为新的项目目录并列出该目录的内容。

您的文件夹结构如下所示：

```plaintext
my-hardhat-project/
├── contracts/
│   ├── .gitkeep
├── test/
│   ├── sample-test.js
├── scripts/
│   ├── sample-script.js
├── artifacts/
├── cache/
├── node_modules/
├── .gitignore
├── hardhat.config.js
├── package.json
└── README.md
```

就是这样！您现在已经在本地计算机上安装并初始化了 Hardhat。您可以使用 Hardhat 开始构建您的去中心化应用程序。

### 配置

```apache
/**
 * @type import('hardhat/config').HardhatUserConfig
 */
module.exports = {
  solidity: {
    version: "0.8.0",
  },
  networks: {
    hardhat: {},
    localhost: {
      url: "http://localhost:8545",
    },
  },
};
```

确保你的配置文件看起来像这样。在 solidity version 中，写下你正在使用的 solidity 版本。

### 智能合约

在合同文件夹中，删除现有合同并创建一个新的**.sol**文件。在文件中，编写以下智能合约。

```solidity
pragma solidity ^0.8.0;

contract BasicDemo {
    uint256 private value;

    function setValue(uint256 _newValue) public {
        value = _newValue;
    }

    function getValue() public view returns (uint256) {
        return value;
    }
}
```

这个智能合约称为 BasicDemo，包括两个功能：`setValue`和`getValue`。`setValue`函数要一个单一的参数`_newValue` ，用于更新`value`变量。`getValue`函数是一个`view`函数，这意味着它不会修改合约的状态，并且可以在不产生任何气体成本的情况下被调用。

要使用此合约，需要使用 Hardhat 或 Remix 等工具将其部署到以太坊网络。部署后，可以调用`setValue`函数来设置变量的值，也可以调用`getValue`函数来检索当前值。这个智能合约非常简单，但它演示了 Solidity 智能合约的基本结构和语法。

### 部署

在脚本文件夹中，删除现有文件并创建一个新的**deploy.js**文件。将以下内容写入其中。

```javascript
const { ethers } = require("hardhat");

async function main() {
  const BasicDemo = await ethers.getContractFactory("BasicDemo");
  const basicDemo = await BasicDemo.deploy();
  await basicDemo.deployed();
  console.log("BasicDemo contract deployed to:", basicDemo.address);
}

main()
  .then(() => process.exit(0))
  .catch(error => {
    console.error(error);
    process.exit(1);
});
```

该脚本首先从 Hardhat 库中导入`ethers`对象。然后它定义了一个`async`调用的`main`函数，该函数使用对象`ethers`的`getContractFactory`方法来检索`BasicDemo`智能合约的工厂。然后它调用工厂的`deploy`方法，将合约部署到本地网络。一旦合约被部署，该脚本会将其地址记录到控制台。

要运行此脚本，请将其保存到在 Hardhat 项目的根目录中命名的`deploy.js`文件，然后运行以下命令：

```bash
npx hardhat run deploy.js --network localhost
```

这会将 BasicDemo 合约部署到在[`localhost`](http://localhost/) 上运行的本地 Hardhat 网络。如果部署成功，您应该会在控制台中看到合约地址。

### 测试

在test文件夹中，删除已有的文件，新建一个**01_test.js**文件，写入如下内容

```javascript
const { ethers } = require("hardhat");
const { expect } = require("chai");

describe("BasicDemo", function () {
  let basicDemo;

  beforeEach(async function () {
    const BasicDemo = await ethers.getContractFactory("BasicDemo");
    basicDemo = await BasicDemo.deploy();
    await basicDemo.deployed();
  });

  it("should set the value", async function () {
    const newValue = 42;
    await basicDemo.setValue(newValue);
    const value = await basicDemo.getValue();
    expect(value).to.equal(newValue);
  });

  it("should get the default value", async function () {
    const defaultValue = 0;
    const value = await basicDemo.getValue();
    expect(value).to.equal(defaultValue);
  });
});
```

该脚本使用 Chai 断言库为 BasicDemo 智能合约定义了一个测试套件。它包括两个测试：一个用于设置合约的值，另一个用于检索默认值。

`describe`函数定义测试套件并使用`beforeEach`函数在每次测试前部署 BasicDemo 合约的新实例。这确保了每个测试都使用合约的新实例执行。

第一个测试使用`it`函数定义一个测试用例，将合约的值设置为 42，然后使用`getValue`函数检索它。它使用 Chai `expect`函数断言检索到的值等于预期值 42。

第二个测试类似，但它只是检索合约的默认值（即 0）并断言它等于预期值 0。

要运行此脚本，请将其保存到以您的 Hardhat 项目`test`目录下命名的`basicDemo.test.js`文件中，然后运行以下命令：

```apache
npx hardhat test
```

这将执行`basicDemo.test.js`文件中的测试并将结果报告到控制台。如果测试通过，您应该会看到一条消息，表明所有测试都已通过。

## 结论

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230213210400.png)

总之，Hardhat 是一个强大的开发框架，它提供了广泛的工具和功能，用于在以太坊区块链上构建、测试和部署智能合约。其测试和部署框架使开发和部署智能合约变得容易，而其内置的调试器允许高效的合约调试。此外，Hardhat 通过其插件实现的灵活性和可扩展性使其成为需要自定义其开发过程的开发人员的热门选择。总的来说，对于希望在以太坊网络上构建去中心化应用程序的开发人员来说，Hardhat 是一个强烈推荐的框架。

原文：https://moayaan.hashnode.dev/hardhat-complete-guide

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)