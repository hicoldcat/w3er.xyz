---
title: Web3教程：使用 Solidity 构建 Web3 应用程序（2）
description: null
author: 李留白
weight: 0
date: 2022-11-21T11:19:55.432Z
lastmod: 2022-11-21T13:02:58.555Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211201025786.png
---

## 目录

```text
创建第一个合约
	让你的本地以太坊网络运行起来
	用 Solidity 编写您的第一个智能合约
	在本地编译合约并运行
	将数据存储在我们的智能合约中
	在本地部署，以便我们可以开始构建网站
连接到钱包
	设置一个基本的 React 应用程序，设置 Metamask
	将智能合约部署到真实的测试网
	将我们的钱包连接到我们的网络应用程序
	从我们的网络应用程序调用已部署的智能合约
与合约交互
	将来自用户的消息存储在区块链上
	基金合约，设置奖品，发送用户以太币
优化 UI 并部署
	随机选择获胜者并防止垃圾邮件发送者
	完成并庆祝！
```

### 将数据存储在我们的智能合约中

#### 📦 存储数据！

从这里开始，让我们为我们的合约添加一些花哨的东西。

我们希望能够让某人向我们挥手wave，然后存储该wave。

所以，我们首先需要的是一个他们可以点击向我们wave的功能！

区块链 = 将其视为云提供商，有点像 AWS，但它不为任何人所有。它由想要支持网络的机器的计算能力运行。通常这些人被称为节点，他们运行客户端软件来保持网络运行，同时让我们的数据或交易留在网络上！

智能合约 = 有点像我们服务器的代码，具有人们可以点击的不同功能。

所以，这是我们可以用来“存储” wave 的更新合约。

```solidity
// SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.0;

import "hardhat/console.sol";

contract WavePortal {
    uint256 totalWaves;

    constructor() {
        console.log("Yo yo, I am a contract and I am smart");
    }

    function wave() public {
        totalWaves += 1;
        console.log("%s has waved!", msg.sender);
    }

    function getTotalWaves() public view returns (uint256) {
        console.log("We have %d total waves!", totalWaves);
        return totalWaves;
    }
}
```

搞定！

所以，这就是你在 Solidity 中编写函数的方式。而且，我们还添加了一个自动初始化为 0 的 `totalWaves` 变量。但是，这个变量很特别，因为它被称为“状态变量”，它很酷，因为它永久存储在合约存储中。

我们还在这里使用了一些魔法和 `msg.sender`。这是调用该函数的钱包地址。这太棒了！这就像内置身份验证。我们确切地知道是谁调用了这个函数，因为为了调用智能合约函数，你需要连接一个有效的钱包！

以后我们可以写出只有特定钱包地址才能命中的函数。例如，我们可以改变这个函数，只允许我们的地址发送一个 wave。或者，也许把它放在只有您的朋友可以向您wave的地方！

#### ✅ 更新 run.js 来调用我们的函数

所以，`run.js` 需要改变！

为什么？

那么，我们需要手动调用我们创建的函数。

基本上，当我们将合约部署到区块链时（我们在运行 `waveContractFactory.deploy()` 时会这样做），我们的函数就可以在区块链上被调用，因为我们在函数上使用了那个特殊的 **public** 关键字。

把它想象成一个公共 API 端点:)。

所以现在我们要专门测试这些功能！

```javascript
const main = async () => {
  const [owner, randomPerson] = await hre.ethers.getSigners();
  const waveContractFactory = await hre.ethers.getContractFactory('WavePortal');
  const waveContract = await waveContractFactory.deploy();
  await waveContract.deployed();

  console.log("Contract deployed to:", waveContract.address);
  console.log("Contract deployed by:", owner.address);

  let waveCount;
  waveCount = await waveContract.getTotalWaves();
  
  let waveTxn = await waveContract.wave();
  await waveTxn.wait();

  waveCount = await waveContract.getTotalWaves();
};

const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.log(error);
    process.exit(1);
  }
};

runMain();
```
**VSCode 可能会自动导入 `ethers`。我们不需要导入`ethers`。因此，请确保您没有导入。还记得我们上节课讲的关于 hre 的内容吗？**

#### 🤔它是如何工作的？

```javascript
const [owner, randomPerson] = await hre.ethers.getSigners();
```

为了将某些东西部署到区块链，我们需要有一个钱包地址！ Hardhat在后台神奇地为我们做了这件事，但在这里我抓取了合约所有者的钱包地址，我也抓取了一个随机的钱包地址，并将其命名为“randomPerson”。这会更有意义。

我还补充道：

```javascript
console.log("合约部署者：", owner.address);
```

我这样做只是为了查看部署合约的地址。我很好奇！

我添加的最后一件事是：

```javascript
let waveCount;
waveCount = await waveContract.getTotalWaves();

let waveTxn = await waveContract.wave();
await waveTxn.wait();

waveCount = await waveContract.getTotalWaves();
```

基本上，我们需要手动调用我们的函数！就像我们使用任何普通 API 一样。首先我调用函数来获取总的wave次数。然后，运行wave。最后，我再次抓取 waveCount 以查看它是否发生了变化。

像往常一样运行脚本：

```bash
npx hardhat run scripts/run.js
```

这是我的输出：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211212100730.png)

非常棒，嗯:)？

您还可以看到wave的钱包地址等于部署合约的地址。我对自己wave！

所以我们：

1.调用了wave函数。

2.更改了状态变量。

3.读取变量的新值。

这几乎是大多数智能合约的基础。读取函数。编写函数。并改变状态变量。我们正在构建需要继续开发的史诗般的 WavePortal。

很快，我们将能够从我们将要处理的 React 应用程序中调用这些函数:)。


#### 🤝 测试其他用户

所以，我们可能希望我们以外的其他人向我们发送wave挥手，对吗？要是能发个wave就太无聊了！！我们想让我们的网站 **有很多人**！

看一下这个。我在函数底部添加了几行。我不打算解释太多（但请在#general-chill-chat 中提问）。基本上这就是我们如何模拟其他人点击我们的功能:)。更改代码并运行后，请密切注意终端中的钱包地址。

```javascript
const main = async () => {
  const [owner, randomPerson] = await hre.ethers.getSigners();
  const waveContractFactory = await hre.ethers.getContractFactory('WavePortal');
  const waveContract = await waveContractFactory.deploy();
  await waveContract.deployed();

  console.log('Contract deployed to:', waveContract.address);
  console.log('Contract deployed by:', owner.address);

  let waveCount;
  waveCount = await waveContract.getTotalWaves();

  let waveTxn = await waveContract.wave();
  await waveTxn.wait();

  waveCount = await waveContract.getTotalWaves();

  waveTxn = await waveContract.connect(randomPerson).wave();
  await waveTxn.wait();

  waveCount = await waveContract.getTotalWaves();
};

const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.log(error);
    process.exit(1);
  }
};

runMain();
```

添加到此代码块的最新项目是：

```javascript
waveTxn = await waveContract.connect(randomPerson).wave();
await waveTxn.wait();

waveCount = await waveContract.getTotalWaves();
```

### 在本地部署，以便我们可以开始构建网站

#### 👀 编写脚本并在本地部署

*“等等，我不是已经部署到本地网络了吗？？”*

嗯，一部分。

请记住，当您运行 `scripts/run.js` 时，它实际上是

1. 创建一个新的本地以太坊网络。

2. 正在部署您的合约。

3. 然后，当脚本结束时，Hardhat 将自动**销毁**该本地网络。

我们需要一种方法来保留本地网络。为什么？好吧，想想本地服务器。你想让它保持，这样你就可以继续和它对话！例如，如果您有一个自己提供 API 的本地服务器，您希望保持该本地服务器处于活动状态，以便您可以在您的网站上工作并对其进行测试。

我们将在这里做同样的事情。

前往您的终端并创建一个**新窗口**。在这个窗口中，cd 回到你的“my-wave-portal project”。然后，在这里继续运行本地节点

```bash
npx hardhat node
```

搞定。

您刚刚启动了一个**保持活动**的本地以太坊网络。而且，正如您所看到的，Hardhat 为我们提供了 20 个帐户，并给了每个账户 10000 ETH，我们现在很富有！哇！有史以来最好的项目。（本地的测试币）

所以现在，这只是一个空的区块链。没有块！

我们想创建一个新块并在其上获取我们的智能合约！开始吧。

在 `scripts` 文件夹下，创建一个名为 `deploy.js` 的文件。这是代码。看起来与 `run.js` 非常相似。

```javascript
const main = async () => {
  const [deployer] = await hre.ethers.getSigners();
  const accountBalance = await deployer.getBalance();

  console.log('Deploying contracts with account: ', deployer.address);
  console.log('Account balance: ', accountBalance.toString());

  const Token = await hre.ethers.getContractFactory('WavePortal');
  const portal = await Token.deploy();
  await portal.deployed();

  console.log('WavePortal address: ', portal.address);
};

const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.error(error);
    process.exit(1);
  }
};

runMain();
```

### 🎉 部署

现在我们要在本地部署的命令是：

```bash
npx hardhat run scripts/deploy.js --network localhost
```

**您需要确保使用和** `my-wave-portal` **不同的目录执行此操作（简言之，使用单独的窗口来跟踪本地节点的消息）。我们不想弄乱保持我们本地以太坊网络活跃的终端窗口。**

好的，所以一旦我运行，这就是我得到的：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211212100876.png)

太棒了。

我们部署了合约，我们也在区块链上有它的地址！我们的网站将需要它，以便知道在区块链上的何处查找合约。 （想象一下，如果必须在整个区块链中搜索我们的合约。那将是......糟糕！）。

在本地以太坊网络保持活跃的终端窗口中，您会看到一些新东西！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211212101850.png)

有趣的。但是……什么是 gas？块 #1 是什么意思？ “交易”旁边的大段代码是什么？我想让你尝试谷歌这些东西。在 #general-chill-chat 中提问:)。

好的！你已经到了本节的结尾。查看 [此链接](https://gist.github.com/adilanchian/9f745fdfa9186047e7a779c02f4bffb7) 以确保您的代码步入正轨！


```
未完待续...

原文地址：https://buildspace.so/p/build-solidity-web3-app
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)

