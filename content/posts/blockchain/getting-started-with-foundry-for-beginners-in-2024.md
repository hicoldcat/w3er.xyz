---
title: 2024 年初学者入门 Foundry
description: null
author: 李留白
weight: 0
date: 2024-01-07T09:27:07.051Z
lastmod: 2024-01-07T09:31:10.825Z
tags: []
categories:
    - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107172715.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107172715.png)

>原文：https://learnhubafrica.hashnode.dev/getting-started-with-foundry-for-beginners-in-2024

作为一名区块链开发人员，从 Remix 升级到 Foundry 标志着你的通行权，显示出你对技术的奉献精神。Foundry提供了与主网紧密匹配的本地以太坊开发环境。这使得像专业人士一样轻松测试和部署 Solidity 合约。

Foundry 相对于 Remix 的主要优势包括：

- 像 Ganache 这样的本地区块链模拟器的行为就像真正的主网一样
- 自动化测试框架
- 可编写脚本的部署
- 内置安全分析
- 与 VSCode 集成

掌握 Foundry 将提高您的开发技能，并为您提供一个令人印象深刻的环境来向潜在雇主或客户展示。现在，让我们开始吧！

## 安装 Foundry 的先决条件

第一步是通过安装来准备系统：

- [VSCode](https://code.visualstudio.com/download) - 用于编写 Solidity 合约的代码编辑器
- [Foundry](https://book.getfoundry.sh/getting-started/installation#using-foundryup) - 用于本地区块链模拟和部署
- [Ganache](https://trufflesuite.com/ganache/) - 本地区块链模拟器

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107172758.png)

安装 Vscode 后，找到上面的终端按钮并单击它以启动新终端；设置终端后，运行代码以设置下载并安装 Foundry。

```bash
// Install Foundry 
curl -L https://foundry.paradigm.xyz | bash
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107172822.png)

有时，Foundry 安装脚本会超时。如果您遇到这些问题，请先尝试通过 VPN 服务进行连接。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107172834.png)

我第一次安装时超时，所以我安装了[ProtonVPN](https://protonvpn.com/download)，之后安装变得无缝。

安装核心工具后，启动新终端并输入：

```
foundryup
```

这会将 Foundry 更新到最新版本。交叉检查以查看是否安装了以下内容。

```bash
anvil --version 
forge --version 
cast --version
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107172850.png)

安装后，让我们创建一个文件夹，然后使用我们的终端导航到该文件夹，但如果您更喜欢使用 GUI，请继续。

## 创建您的第一个铸造项目

要初始化新的 Foundry 项目，请创建一个项目目录并使用 Forge 进行设置。

```bash
cd Desktop
mkdir my_foundry_project
cd my_foundry_project  
forge init
```

这会安装 Solidity 编译器等依赖项，并创建样板文件（例如`script`、`src`、 ）`test`以开始编码。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107172902.png)

您可以删除在上述文件中找到的模板 counter.sol 文件，我们将创建自己的模板文件。

## 连接本地区块链

Ganache 和 Anvil 是连接本地区块链进行开发的两个不错的选择。

Ganache 提供自动生成的 RPC URL 和帐户来模拟主网。整合 Ganache：

要设置 Ganache，请单击链接[并](https://trufflesuite.com/ganache/)下载安装程序，运行它，您应该会看到类似这样的内容。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107172913.png)

单击快速启动以打开模拟区块链。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107172920.png)

让我们做一些测试和甘纳许来测试我们的智能合约。如果您没有任何可使用的智能合约，请单击[链接](https://github.com/Scofield-Idehen/web_finder/blob/master/src/SimpleStorage.sol)并复制 simpletorage.sol。确保将该文件粘贴到 src/ 文件夹中。

```solidity
// I'm a comment!
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.18;

contract SimpleStorage {
    uint256 myFavoriteNumber;

    struct Person {
        uint256 favoriteNumber;
        string name;
    }
    // uint256[] public anArray;
    Person[] public listOfPeople;

    mapping(string => uint256) public nameToFavoriteNumber;

    function store(uint256 _favoriteNumber) public virtual {
        myFavoriteNumber = _favoriteNumber;
    }

    function retrieve() public view returns (uint256) {
        return myFavoriteNumber;
    }

    function addPerson(string memory _name, uint256 _favoriteNumber) public {
        listOfPeople.push(Person(_favoriteNumber, _name));
        nameToFavoriteNumber[_name] = _favoriteNumber;
    }
}
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107172933.png)

在终端上，运行 forgecompile 来编译您的代码，您将立即看到一个新文件夹出现，`out,`该文件夹包含我们刚刚发送的交易的字节代码。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107172941.png)

要使用Ganache，我们必须建立一个网络或RPC来模拟交易，我们可以使用Metamask；如果您还没有 Metatmask，请使用本[指南在此处下载并设置帐户。](https://learn.metamask.io/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107172949.png)

转到设置，然后网络并填充以下内容

**网络名称：**[**Scofield_LocalHost**](https://code.visualstudio.com/download)

**新的 RPC URL：** [HTTP://127.0.0.1:7545](https://code.visualstudio.com/download)

**链号：1337**

**货币符号：ETH**

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107172959.png)

点击保存，然后在Metamask上添加水龙头，导入其中一个`private keys`从ganache到metamask，你就会有`100ETH`

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107173008.png)

接下来，运行. 这将提示您输入您的私钥，我们将从 Ganache 页面获取该私钥。`forge create SimpleStorage --rpc-url` [`http://127.0.0.1:7545`](https://code.visualstudio.com/download) `--interactive`

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107173016.png)

单击显示密钥以打开私钥并复制它。**请不要对真实账户执行此操作**，但由于我们正在使用测试账户，所以我们没问题。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107173046.png)
粘贴私钥，粘贴时不会显示任何内容，只需单击 Enter 即可。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240107173055.png)

我们的合约已经部署到ganache模拟区块链上，我们可以看到部署者、部署到的位置以及交易哈希。