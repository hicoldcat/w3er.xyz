---
title: 如何使用 Solidity 构建 CRUD 应用程序
description: null
author: 李留白
weight: 0
date: 2022-09-22T14:10:20.739Z
lastmod: 2022-09-22T14:36:04.660Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922221033.png
---

你有没有想过如何创建你的区块链应用程序？谈到以太坊，它从[智能合约](https://web3.hashnode.com/glossary/what-are-smart-contracts)开始。

在本文中，我们将学习如何在以太坊上构建一个简单的智能合约，并使用 Truffle 框架对其进行测试。我们的智能合约将执行必要的创建、读取、更新和删除 (CRUD) 操作。

我们将专注于用 Solidity 语言编写的智能合约。我们将使用 Truffle Suite 部署以太坊区块链的本地版本，并使用以太坊虚拟机 (EVM) 编译智能合约。

## 先决条件

对于本教程，需要以下软件和软件包：

- Node 及其包管理器`npm`. 我们运行命令`node -v`&&`npm -v`来验证我们已经安装了它们，或者从[这里](https://nodejs.org/en/)安装它们。
- 或者，我们可以使用另一个包管理器[Yarn](https://yarnpkg.com/)
- 以太坊区块链，智能合约编译器
- 用于通信的 JavaScript 库，[Web3.js](https://web3js.readthedocs.io/en/v1.5.2/getting-started.html#adding-web3)

## 什么是智能合约？

简而言之，智能合约是一段控制某些数字资产的代码。它定义了转移资产和罚款的规则，就像传统合同一样。最好的是，它会根据预先编码的条件自动执行这些转移和惩罚，而无需中介。

## 什么是Solidity ？

Solidity 是在以太坊上编写智能合约的最著名的语言之一。它专为智能合约编程而设计。它在语法上类似于 javascript。

## 什么是Truffle Suite？

以太坊是一个允许应用程序在其上运行的区块链。代码以智能合约的形式用 Solidity 语言编写。为了编译这些合约，我们需要一个以太坊编译器，它将智能合约转换为机器可读的代码。

Truffle Suite 是一组专门为以太坊上的区块链开发而设计的工具。该套件包括三个软件：

- [Truffle](https://trufflesuite.com/truffle/)，一个智能合约开发框架
- [Ganache](https://trufflesuite.com/ganache/)，它使您能够在本地网络上设置个人以太坊区块链以进行测试和开发
- [Drizzle](https://trufflesuite.com/drizzle/)，用于创建 DApp 用户界面，包括一系列现成的组件

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922221507.png)

### 安装Ganache 

智能合约在以太坊区块链上运行，因此我们需要一个用于部署和测试。我们也可以在实时链上部署，但这会花费我们以太币作为汽油费。所以让我们建立一个本地链并在那里进行测试。当您确定代码并准备好分发您的应用程序时，您可以将其部署在实时链上。

Ganache 是安装在我们计算机上并在 localhost 上运行的本地链。从[Truffle Suite 网站](https://trufflesuite.com/ganache/)下载 Ganache 。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922221529.png)

> 我们可以看到 Ganache 提供了十个账户，每个账户都有 100 ETH。这些是假以太币，所以不要太兴奋。此外，该链在 7545 端口的 127.0.0.1 上运行。我们将使用这些账户在这条链上部署我们的智能合约。以太币将帮助我们支付汽油费。

### 安装Truffle

Truffle 为智能合约提供编译器。我们需要它来将 Solidity 代码转换为可以部署在 Ganache 区块链上的机器可读代码。

使用以下命令安装 Truffle：

```shell
 $ npm install truffle -g
```

或者

```shell
 $ yarn add truffle
```

## 创建智能合约

要创建智能合约，我们首先需要创建一个项目目录，我们将在其中保存所有 Solidity 文件。让我们创建一个`crud-app`文件夹并使用`cd crud-app`命令 进入到目录。

现在，我们的项目是空的。要使用它，我们需要一些样板代码。例如，如果我们希望在 React 中创建 UI，我们需要安装 React。

Truffle 已经提供了一些名为[box](https://www.trufflesuite.com/boxes)的包。这些包是不同框架的捆绑包，例如**Truffle**、**Ganache**、**React**、**Web3**和**Redux**，其中有一个供 Vue.js 开发人员使用。他们共同完成了从客户端 UI 到区块链智能合约的端到端应用程序开发。

本文将使用[Truffle 提供的 React box](https://trufflesuite.com/boxes/react/)。

### 安装 React box

要安装 React box，请运行以下命令：

```
yarn truffle unbox react
```

这将安装 Web3.js、React、Ganache CLI、Truffle 和 Ethereum。

对于本教程，我们不会关注 React 或基于浏览器的 UI。相反，我们将创建智能合约并仅使用终端处理它们。

我们项目的目录结构将如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922222107.png)

这里`client`是一个 React 项目文件夹，我们可以在其中创建应用程序的 UI。其中的一个文件夹`client/src/contracts`以 JSON 格式保存已编译的智能合约。这些文件是在我们编译智能合约时生成的。它们包含`ABI`、`bytecode`和其他信息。

```json
{
  "contractName": "Migrations",
  "abi": [
    {
      "inputs": [],
      "name": "last_completed_migration",
      "outputs": [
        {
          "internalType": "uint256",
          "name": "",
          "type": "uint256"
        }
      ],
      "stateMutability": "view",
      "type": "function",
      "constant": true
    },
    {
      "inputs": [],
      "name": "owner",
      "outputs": [
        {
          "internalType": "address",
          "name": "",
          "type": "address"
        }
      ],
      "stateMutability": "view",
      "type": "function",
      "constant": true
    },
    {
      "inputs": [
        {
          "internalType": "uint256",
          "name": "completed",
          "type": "uint256"
        }
      ],
      "name": "setCompleted",
      "outputs": [],
      "stateMutability": "nonpayable",
      "type": "function"
    }
  ],
  "metadata": "{\"compiler\":{\"version\":\"0.8.11+commit.d7f03943\"},\"language\":\"Solidity\",\"output\":{\"abi\":[{\"inputs\":[],\"name\":\"last_completed_migration\",\"outputs\":[{\"internalType\":\"uint256\",\"name\":\"\",\"type\":\"uint256\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[],\"name\":\"owner\",\"outputs\":[{\"internalType\":\"address\",\"name\":\"\",\"type\":\"address\"}],\"stateMutability\":\"view\",\"type\":\"function\"},{\"inputs\":[{\"internalType\":\"uint256\",\"name\":\"completed\",\"type\":\"uint256\"}],\"name\":\"setCompleted\",\"outputs\":[],\"stateMutability\":\"nonpayable\",\"type\":\"function\"}],\"devdoc\":{\"kind\":\"dev\",\"methods\":{},\"version\":1},\"userdoc\":{\"kind\":\"user\",\"methods\":{},\"version\":1}},\"settings\":{\"compilationTarget\":{\"project:/contracts/Migrations.sol\":\"Migrations\"},\"evmVersion\":\"london\",\"libraries\":{},\"metadata\":{\"bytecodeHash\":\"ipfs\"},\"optimizer\":{\"enabled\":false,\"runs\":200},\"remappings\":[]},\"sources\":{\"project:/contracts/Migrations.sol\":{\"keccak256\":\"0x7eaedbb1a3e4e0f585d9063393872f88ded247ca3c3c3c8492ea18e7629a6411\",\"license\":\"MIT\",\"urls\":[\"bzz-raw://4a3eb571cee910095df65a06a1c1d3f89187c72a3c184ef87a7538d9aa39ad07\",\"dweb:/ipfs/QmdqR3vrSSGR49qFGZr49Mb39z7dgD6tSzEDoaqtM31o61\"]}},\"version\":1}",
  "bytecode": "0x6080604052336000806101000a81548173ffffffffffffffffffffffffffffffffffffffff021916908373ffffffffffffffffffffffffffffffffffffffff16021790555034801561005057600080fd5b50610327806100606000396000f3fe608060405234801561001057600080fd5b50600436106100415760003560e01c8063445df0ac146100465780638da5cb5b14610064578063fdacd57614610082575b600080fd5b61004e61009e565b60405161005b9190610179565b60405180910390f35b61006c6100a4565b60405161007991906101d5565b60405180910390f35b61009c60048036038101906100979190610221565b6100c8565b005b60015481565b60008054906101000a900473ffffffffffffffffffffffffffffffffffffffff1681565b60008054906101000a900473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff1614610156576040517f08c379a000000000000000000000000000000000000000000000000000000000815260040161014d906102d1565b60405180910390fd5b8060018190555050565b6000819050919050565b61017381610160565b82525050565b600060208201905061018e600083018461016a565b92915050565b600073ffffffffffffffffffffffffffffffffffffffff82169050919050565b60006101bf82610194565b9050919050565b6101cf816101b4565b82525050565b60006020820190506101ea60008301846101c6565b92915050565b600080fd5b6101fe81610160565b811461020957600080fd5b50565b60008135905061021b816101f5565b92915050565b600060208284031215610237576102366101f0565b5b60006102458482850161020c565b91505092915050565b600082825260208201905092915050565b7f546869732066756e6374696f6e206973207265737472696374656420746f207460008201527f686520636f6e74726163742773206f776e657200000000000000000000000000602082015250565b60006102bb60338361024e565b91506102c68261025f565b604082019050919050565b600060208201905081810360008301526102ea816102ae565b905091905056fea2646970667358221220353cb1298ecaaf65fe00ddfd9e11ec1e26a6b97a78dc65de1604cb8b8a399ab364736f6c634300080b0033",
  "deployedBytecode": "0x608060405234801561001057600080fd5b50600436106100415760003560e01c8063445df0ac146100465780638da5cb5b14610064578063fdacd57614610082575b600080fd5b61004e61009e565b60405161005b9190610179565b60405180910390f35b61006c6100a4565b60405161007991906101d5565b60405180910390f35b61009c60048036038101906100979190610221565b6100c8565b005b60015481565b60008054906101000a900473ffffffffffffffffffffffffffffffffffffffff1681565b60008054906101000a900473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff1614610156576040517f08c379a000000000000000000000000000000000000000000000000000000000815260040161014d906102d1565b60405180910390fd5b8060018190555050565b6000819050919050565b61017381610160565b82525050565b600060208201905061018e600083018461016a565b92915050565b600073ffffffffffffffffffffffffffffffffffffffff82169050919050565b60006101bf82610194565b9050919050565b6101cf816101b4565b82525050565b60006020820190506101ea60008301846101c6565b92915050565b600080fd5b6101fe81610160565b811461020957600080fd5b50565b60008135905061021b816101f5565b92915050565b600060208284031215610237576102366101f0565b5b60006102458482850161020c565b91505092915050565b600082825260208201905092915050565b7f546869732066756e6374696f6e206973207265737472696374656420746f207460008201527f686520636f6e74726163742773206f776e657200000000000000000000000000602082015250565b60006102bb60338361024e565b91506102c68261025f565b604082019050919050565b600060208201905081810360008301526102ea816102ae565b905091905056fea2646970667358221220353cb1298ecaaf65fe00ddfd9e11ec1e26a6b97a78dc65de1604cb8b8a399ab364736f6c634300080b0033",
  "immutableReferences": {},
  "generatedSources": [],
  "deployedGeneratedSources": [
    {
      "ast": {
        "nodeType": "YulBlock",
        "src": "0:3176:2",
        "statements": [
          {
            "body": {
              "nodeType": "YulBlock",
              "src": "52:32:2",
              "statements": [
                {
                  "nodeType": "YulAssignment",
                  "src": "62:16:2",
                  "value": {
                    "name": "value",
                    "nodeType": "YulIdentifier",
                    "src": "73:5:2"
    }
```

如上所示，`client/src/contracts`设置为保存已编译的代码。开发网络设置在端口 8545。这是这个盒子运行 Ganache 的端口。

如果您查看我们安装 Ganache 的顶部，您可以看到它在 7545 端口上运行，但是这个在 8545 上运行，因为我们安装的 Ganache 已经使用了 7545。如果您愿意，您可以将此端口更改为 7545，Truffle 将使用 Ganache 和我们安装的帐户，而不是框提供的帐户。我保持在8545。

## 使用 CRUD 操作构建智能合约

现在是时候编写一些代码了。我们将进行 CRUD 操作并管理技术列表。

我们的应用程序将显示不同技术的列表。您可以添加、更新和删除技术。

如果您以前开发过应用程序，我们将遵循您无疑熟悉的程序：

- 创建一个数组来保存技术的名称
- 创建一个将新值推送到数组的函数
- 创建一个函数以更改给定索引处的值
- 创建一个函数来删除一个值
- 创建一个返回数组的函数

现在让我们用 Solidity 编写代码。

在目录中创建一个新文件`contracts`并调用它`Techs.sol`。我们将通过指明我们支持的许可证和solidity 版本来开始文件：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity >=0.4.22 <0.9.0;

```

接下来，声明我们要编写所有代码的合约范围：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity >=0.4.22 <0.9.0;

contract Techs{

}
```

创建一个数组来保存技术堆栈：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity >=0.4.22 <0.9.0;

contract Techs{
    string [] myTechs;
}

```

这是一个带有 private 修饰符的字符串数组，这意味着它不能在合约之外访问；因此，我们不能直接更改该值。

接下来，创建一个函数来添加一个新值：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity >=0.4.22 <0.9.0;

contract Techs{
    string [] myTechs;

    function addTech(string memory techName) public {
      myTechs.push(techName);
  }
}
```

在上面的代码片段中，我们创建了一个名为 `addTech`的函数，它接受一个字符串`techName`作为参数。函数是public的，因此 UI 或终端可以调用它。在函数体中，我们简单地将值推送到数组中。

更新值：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity >=0.4.22 <0.9.0;

contract Techs{
    string [] myTechs;

   function addTech(string memory techName) public {
       myTechs.push(techName);
   }

  function updateTech(uint techIndex, string memory newTechName) public returns (bool) {
      if(myTechs.length > techIndex){
          myTechs[techIndex] = newTechName;
          return true;
      }
      return false;
  }
}
```

```solidity
// SPDX-License-Identifier: MIT
pragma solidity >=0.4.22 <0.9.0;

contract Techs{
    string [] myTechs;

   function addTech(string memory techName) public {
       myTechs.push(techName);
   }

  function updateTech(uint techIndex, string memory newTechName) public returns (bool) {
      if(myTechs.length > techIndex){
          myTechs[techIndex] = newTechName;
          return true;
      }
      return false;
  }
}
```

`updateTech`接受两个参数，`techIndex`和`newTechName`，并返回一个布尔值。它的工作原理是这样的：如果索引超出数组的范围，则返回 false。否则，它会在提供的索引处使用新提供的水果名称更改数组的值并返回`true`。

下一步是创建删除函数：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity >=0.4.22 <0.9.0;

contract Techs{
    string [] myTechs;

    function addTech(string memory techName) public {
      myTechs.push(techName);
  }

  function updateTech(uint techIndex, string memory newTechName) public returns (bool) {
      if(myTechs.length > techIndex){
          myTechs[techIndex] = newTechName;
          return true;
      }
      return false;
  }

  function deleteTech(uint techIndex) public returns (bool) {
      if(myTechs.length > techIndex){
          for(uint i=techIndex; i < myTechs.length-1; i++){
              myTechs[i] = myTechs[i+1];
          }

          myTechs.pop();

          return true;
      }
      return false;
  }
}
```

在这里，我们检查索引超出范围的条件，然后通过将值替换为提供的索引中的下一个值来更新数组。这样，提供的索引的值就会丢失。最终，我们弹出最后一个值并返回`true`。

最后一步是返回数组。读取数组的所有值：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity >=0.4.22 <0.9.0;

contract Techs{
    string [] myTechs;

    function addTech(string memory techName) public {
      myTechs.push(techName);
  }

  function updateTech(uint techIndex, string memory newTechName) public returns (bool) {
      if(myTechs.length > techIndex){
          myTechs[techIndex] = newTechName;
          return true;
      }
      return false;
  }

  function deleteTech(uint techIndex) public returns (bool) {
      if(myTechs.length > techIndex){
          for(uint i=techIndex; i < myTechs.length-1; i++){
              myTechs[i] = myTechs[i+1];
          }

          myTechs.pop();

          return true;
      }
      return false;
  }

  function getTechs() public view returns (string[] memory) {
      return myTechs;
  }
}

```

## 使用 Truffle 编译智能合约

现在我们已经完成了智能合约的编码，是时候使用 Truffle 编译它了。但首先，我们需要创建一个迁移文件以向 Truffle 指示我们要将其迁移到链上。

如果我们检查`migration`文件夹，我们会找到一个 JavaScript 文件：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922223010.png)

该文件以数字开头，因此我们的第二个文件将以 2 开头，依此类推。代码几乎是标准的。因为`1_initial_migrations`是：

```js
const Migrations = artifacts.require("Migrations");

module.exports = function (deployer) {
  deployer.deploy(Migrations);
};
```

让我们添加我们的迁移文件`2_techs_contracts`：

```js
const TechStack = artifacts.require("./Techs.sol");

module.exports = function (deployer) {
  deployer.deploy(TechStack);
};
```

我们已准备好编译和迁移我们的技术合同。移动到终端并运行以下命令：

```
> truffle develop
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922223123.png)

此命令将启动 truffle 控制台。还会显示链网、账户、助记词等信息。

Ganache 默认提供十个账户。它们对你来说会有所不同。请不要在实时链上使用任何这些私钥，因为它们对本文的所有访问者都是可见的，这意味着任何人都可以访问这些帐户。

现在，让我们使用以下命令编译合约：

```
> compile
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922223140.png)

我们可以检查构建/合同是否创建了`Techs.json`文件。

我们可以使用以下命令将编译后的文件迁移到链中：

```
> migrate
```

> 这会将所有三个智能合约迁移到链上。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922223217.png)



![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922223223.png)



![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922223228.png)

最后，我们的应用程序在以太坊链上。我们花费了 0.001778438 ETH 的汽油费，交易发生在第一个账户。默认情况下，它始终采用第一个帐户。我们现在可以执行各种操作。

## 获取技术列表

我们可以使用 Web3.js 来响应和写入各种值。让我们首先将合约的实例存储在一个变量中：

```node
let instance = await Techs.deployed()
```

我们使用`await`是因为区块链中的所有内容都是异步的并返回一个承诺。

现在使用这个实例来获取数组：

```node
> let techs = instance.getTechs()
undefined
> techs
[]
```

这将返回一个空数组，因为我们的`techs`数组当前没有值。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922223321.png)

## 将技术添加到列表中

让我们添加一些技术：

```
> let result = await instance.addTech("JavaScript")
undefined
```

`result`将持有交易信息。此操作将值添加到数组并因此更改数据。因此，它被记录为交易。请记住，所有读取操作都是免费的，但任何导致区块链变化的操作都需要支付 gas 费。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922223405.png)

现在我们可以再次读取数组来检查内容：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922223417.png)

让我们在列表中添加更多技术：

```
await instance.addTech("React");
await instance.addTech("Nextjs");
await instance.addTech("Web3.js");
await instance.addTech("Solidity");
```

> 请记住，所有这些操作都会花费你以太币。您可以通过在合同中创建一个函数来一次接受多个水果值来节省费用。

现在读取数组：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922223439.png)

## 更新技术名称

我们可以在上图中看到“React.js”被写成“React”。`updateTech()`让我们使用函数来纠正它。它将接受索引和新值。指数为 1。

```
> await instance.updateTech(1, "React.js")
```

现在让我们读取数组：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922223506.png)

> *拼写已成功修复。*

## 删除技术名称

最后一个操作是删除一个值：

```
> await instance.deleteTech(5)
```

读取值：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220922223533.png)

“Svelte”项目已从列表中删除。

## 结论

创建智能合约并在区块链上进行部署既有趣又强大。它提供了传统编程的新视角。您可以使用这些技术创建各种应用程序，例如在线投票、数字银行、钱包、拍卖等。

在本教程中，我们演示了如何构建一个可靠的 CRUD 应用程序并将其部署在本地链上。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
