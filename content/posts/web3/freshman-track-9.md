---
title: Web3系列教程之新手篇---Level 9：NFT-教程
description: null
author: 李留白
weight: 0
date: 2022-06-19T16:27:47.998Z
lastmod: 2022-06-26T12:51:18.073Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/freshman.png
---

## 喜欢视频？

如果您想从视频中学习，我们的 YouTube 上有本教程的录音。单击下面的屏幕截图观看视频，或继续阅读教程！

[https://www.youtube.com/watch?v=uwnAXAsd428](https://www.youtube.com/watch?v=uwnAXAsd428)

## 先决条件

- 设置Metamask（初学者轨道 - [4 级](https://github.com/LearnWeb3DAO/Crypto-Wallets)）
- 检查您的计算机是否有 Node.js。如果不是从这里[下载](https://nodejs.org/en/download/)

## 构建

### 智能合约

为了构建智能合约，我们将使用Hardhat。Hardhat 是专为全栈开发而设计的以太坊开发环境和框架。简单来说，您可以编写智能合约、部署它们、运行测试和调试代码。

- 要设置安Hardhat项目，请打开终端并执行以下命令
```
mkdir NFT-Tutorial
cd  NFT-Tutorial
npm init --yes
npm install --save-dev hardhat
```
- 在安装 Hardhat 的同一目录中运行：
```
npx hardhat
```
- 选择`Create a basic sample project`
- 按回车键已指定`Hardhat Project root`
- 如果您想添加一个问题，请按 Enter 键`.gitignore`
- 按回车键`Do you want to install this sample project's dependencies with npm (@nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers)?`

现在你有一个Hardhat项目准备好了！

如果您不在 Mac 上，请执行此额外步骤并安装这些库：)

```
npm install --save-dev @nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers
```

## 编写 NFT 合约代码

让我们安装 Open Zeppelin 合约，在终端窗口中执行这个命令

```
npm install @openzeppelin/contracts
```

- 在 contracts 文件夹中，创建一个名为 NFTee.sol 的新 Solidity 文件
- 现在我们将在 NFTee.sol 文件中编写一些代码。我们将导入[Openzeppelin 的 ERC721 Contract](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/ERC721.sol)。ERC721 是创建 NFT 的最常用标准。在新生赛道中，我们只会使用 ERC721。在二年级课程中，您将详细了解 ERC721。所以别担心，如果你不明白一切:)
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// Import the openzepplin contracts
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

// GameItem is  ERC721 signifies that the contract we are creating imports ERC721 and follows ERC721 contract from openzeppelin
contract GameItem is ERC721 {

    constructor() ERC721("GameItem", "ITM") {
        // mint an NFT to yourself
        _mint(msg.sender, 1);
    }
}
```
- 编译合约，打开终端并执行这些命令

```
npx hardhat compile
```

## 配置部署

- 让我们将合约部署到`rinkeby`网络。首先，在文件夹`deploy.js`下创建一个名为的新文件`scripts`

- `deploy.js`现在我们将编写一些代码来在文件中部署合约。
```
// Import ethers from Hardhat package
const { ethers } = require("hardhat");

async function main() {
  /*
A ContractFactory in ethers.js is an abstraction used to deploy new smart contracts,
so nftContract here is a factory for instances of our GameItem contract.
*/
  const nftContract = await ethers.getContractFactory("GameItem");

  // here we deploy the contract
  const deployedNFTContract = await nftContract.deploy();

  // print the address of the deployed contract
  console.log("NFT Contract Address:", deployedNFTContract.address);
}

// Call the main function and catch if there is any error
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });

```

- 现在在文件夹中创建一个`.env`文件`NFT-Tutorial`并添加以下行。使用评论中的说明获取您的 Alchemy API 密钥和 RINKEBY 私钥。确保您获得 rinkeby 私钥的帐户由 Rinkeby Ether 提供资金。您可以在这里获得一些：https ://www.rinkebyfaucet.com/

```

# Go to https://www.alchemyapi.io, sign up, create
# a new App in its dashboard and select the network as Rinkeby, and replace "add-the-alchemy-key-url-here" with its key url
ALCHEMY_API_KEY_URL="add-the-alchemy-key-url-here"

# Replace this private key with your RINKEBY account private key
# To export your private key from Metamask, open Metamask and
# go to Account Details > Export Private Key
# Be aware of NEVER putting real Ether into testing accounts
RINKEBY_PRIVATE_KEY="add-the-rinkeby-private-key-here"

```

您可以将 Alchemy 视为区块链的 AWS EC2。它是一个节点提供者。它通过为我们提供节点来帮助我们与区块链连接，以便我们可以读取和写入区块链。Alchemy 帮助我们将合约部署到 rinkeby。

- 现在我们将安装`dotenv`包以便能够导入 env 文件并在我们的配置中使用它。在您的终端中，执行这些命令。
```
npm install dotenv
```

- 现在打开 hardhat.config.js 文件，我们将在`rinkeby`此处添加网络，以便我们可以将合约部署到 rinkeby。`hardhat.config.js`用下面给出的行替换文件中的所有行

```
require("@nomiclabs/hardhat-waffle");
require("dotenv").config({ path: ".env" });

const ALCHEMY_API_KEY_URL = process.env.ALCHEMY_API_KEY_URL;

const RINKEBY_PRIVATE_KEY = process.env.RINKEBY_PRIVATE_KEY;

module.exports = {
  solidity: "0.8.4",
  networks: {
    rinkeby: {
      url: ALCHEMY_API_KEY_URL,
      accounts: [RINKEBY_PRIVATE_KEY],
    },
  },
};
```

- 要在您的终端类型中部署：

```
    npx hardhat run scripts/deploy.js --network rinkeby
```

- 将打印在终端上的 NFT 合约地址保存在记事本中，您将需要它。


## 在 Etherscan 上验证

- 转到[Rinkeby Etherscan](https://rinkeby.etherscan.io/)并搜索打印的地址。
- 如果`address`在 etherscan 上打开，你已经部署了你的第一个 NFT 🎉
- 通过单击交易哈希转到交易详细信息，检查是否有令牌转移到您的地址

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/my.png)

