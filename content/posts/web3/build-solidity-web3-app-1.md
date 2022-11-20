---
title: Web3教程：使用 Solidity 构建 Web3 应用程序（1）
description: null
author: 李留白
weight: 0
date: 2022-11-20T02:22:26.417Z
lastmod: 2022-11-20T03:02:47.668Z
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

## 创建第一个合约

### 让你的本地以太坊网络运行起来

#### ✅ 设置你的环境以开始使用区块链

首先，我们需要让我们的本地以太坊网络正常工作。这是我们编译和测试智能合约代码的方式！您知道如何启动本地环境来处理它吗？这里也一样！

现在，你所需要知道的是，智能合约是一段生活在区块链上的代码。区块链是一个公共场所，任何人都可以安全地读取和写入数据，并收取费用。想想看，它有点像AWS或Heroku，只是没有人真正拥有它！

所以在这种情况下，我们希望人们对我们👋。这里的大背景是：

1.**我们要写一个智能合约。**该合同包含有关如何处理 👋 的所有逻辑。这就像您的服务器代码。

2.**我们的智能合约将被部署到区块链上。**这样，世界上任何人都可以访问和运行我们的智能合约（如果我们允许他们这样做）。所以，非常像一个服务器 :)。

3.**我们将建立一个客户网站**，让人们可以轻松地与我们在区块链上的智能合约进行交互。

我将根据需要深入解释某些事情（例如挖矿如何工作，智能合约如何编译和运行等），*但现在让我们只专注于让东西运行*。

如果您在这里有任何问题，只需在 Discord 中的  `#section-1-help`留言. 

#### ✨Hardhat的魔力

1. 我们将经常使用名为 Hardhat 的工具。这将使我们能够轻松启动本地以太坊网络，并为我们提供假测试 ETH 和假测试账户。请记住，它就像一个本地服务器，除了“服务器”是区块链。

2. 快速编译智能合约并在我们本地的区块链上进行测试。

首先，您需要获取 Node/NPM。如果你没有它，请到[这里来](https://hardhat.org/tutorial/setting-up-the-environment.html?utm_source=buildspace.so&utm_medium=buildspace_project)。

我们建议使用当前的 LTS Node.js 版本运行 Hardhat，否则您可能会遇到一些问题！[您可以在此处](https://nodejs.org/en/about/releases/?utm_source=buildspace.so&utm_medium=buildspace_project)找到当前版本。**确保你的 NodeJs 版本是正确的，否则你会遇到问题！**我们现在推荐版本 16。

接下来，让我们前往终端（Git Bash 将无法运行）。继续并 cd 到你想要工作的目录。一旦你在那里运行这些命令：

```bash
mkdir my-wave-portal
cd my-wave-portal
npm init -y
npm install --save-dev hardhat@latest
```

#### 👏 开始示例项目

太棒了，现在我们应该有Hardhat了。让我们开始一个示例项目。

运行：

```bash
npx hardhat
```

*注意：如果你安装了 yarn 和 npm，你可能会得到诸如`npm ERR! could not determine executable to run`. 在这种情况下，你可以这样做：运行`yarn add hardhat`。*

选择**创建 JavaScript 项目**选项，对一切都选Yes。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211201029238.png)

示例项目将要求您安装 hardhat-waffle 和 hardhat-ethers。这些是我们稍后会用到的其他好东西:)。

继续安装这些其他依赖项，以防它没有自动执行。

```bash
npm install --save-dev chai @nomiclabs/hardhat-ethers ethers @nomicfoundation/hardhat-toolbox @nomicfoundation/hardhat-chai-matchers
```

你的`hardhat.config.js`应该看起来像这样。

```javascript
require("@nomicfoundation/hardhat-toolbox");

// This is a sample Hardhat task. To learn how to create your own go to
// https://hardhat.org/guides/create-task.html
task("accounts", "Prints the list of accounts", async (taskArgs, hre) => {
    const accounts = await hre.ethers.getSigners();

    for (const account of accounts) {
        console.log(account.address);
    }
});

// You need to export an object to set up your config
// Go to https://hardhat.org/config/ to learn more

/**
 * @type import('hardhat/config').HardhatUserConfig
 */
module.exports = {
    solidity: "0.8.17",
};
```

最后，运行`npx hardhat node`，这应该会打印出一堆如下所示的帐户：

```
0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266
0x70997970C51812dc3A010C7d01b50e0d17dc79C8
0x3C44CdDdB6a900fa2b585dd299e03d12FA4293BC
0x90F79bf6EB2c4f870365E785982E1f101E93b906
0x15d34AAf54267DB7D7c367839AAf71A00a2C6A65
0x9965507D1a55bcC2695C58ba16FB37d819B0A4dc
0x976EA74026E726554dB657fA54763abd0C3a0aa9
0x14dC79964da2C08b23698B3D3cc7Ca32193d9955
0x23618e81E3f5cdF7f54C3d65f7FBc0aBf5B21E8f
0xa0Ee7A142d267C1f36714E4a8F75612F20a79720
0xBcd4042DE499D14e55001CcbB24a551F3b954096
0x71bE63f3384f5fb98995898A86B02Fb2426c5788
0xFABB0ac9d68B0B445fB7357272Ff202C5651694a
0x1CBd3b2770909D4e10f157cABC84C7264073C9Ec
0xdF3e18d64BC6A983f673Ab319CCaE4f1a57C7097
0xcd3B766CCDd6AE721141F452C550Ca635964ce71
0x2546BcD3c84621e976D8185a91A922aE77ECEc30
0xbDA5747bFD65F08deb54cb465eB87D40e51B197E
0xdD2FD4581271e230360230F9337D5c0430Bf44C0
0x8626f6940E2eb28930eFb4CeF49B2d1F2C9C1199
```

这些是 Hardhat 为我们生成的以太坊地址，用于模拟区块链上的真实用户。在项目后期，当我们想模拟用户👋在我们身边时，这将对我们有很大的帮助！

#### 🌟 运行它

为确保一切正常，请运行：

```bash
 npx hardhat compile
```

然后运行：

```bash
npx hardhat test
```

你应该看到这样的东西：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211201030863.png)

让我们做一个小小的清理。

现在就去用你喜欢的代码编辑器打开这个项目的代码。我最喜欢VSCode! 我们要删除所有为我们生成的蹩脚的启动代码。我们不需要任何这些东西。我们是专业人员;)！

继续，删除`test` 下的`Lock.js`文件。另外，删除`scripts` 下`deploy.js`。然后，删除`contracts`下`Lock.sol`。不要删除实际的文件夹！

### 用 Solidity 编写您的第一个智能合约

#### 👩‍💻 写个合约吧

太棒了，我们做到了。

我们将直接进入我们的项目。

让我们构建一个智能合约，让我们向合约发送👋 并跟踪总波数。这将很有用，因为在您的网站上，您可能想要跟踪这个 #! 请随意更改它以适合您的用例。

在`contracts`目录下创建一个名为`WavePortal.sol`的文件。使用 Hardhat 时文件结构非常重要，所以，请注意！

我们将从每个合同开始的结构开始。

```solidity
// SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.17;

import "hardhat/console.sol";

contract WavePortal {
    constructor() {
        console.log("Yo yo, I am a contract and I am smart");
    }
}
```

注意：您可能需要在[此处](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity&utm_source=buildspace.so&utm_medium=buildspace_project)下载 VS Code Solidity 扩展以轻松突出显示语法。

```solidity
// SPDX-License-Identifier: UNLICENSED
```

只是一个花哨的注释。它被称为“SPDX 许可证标识符”，请随意谷歌它是什么:)。

```solidity
pragma solidity ^0.8.17;
```

这是我们希望合约使用的 Solidity 编译器的版本。它基本上是说“运行时，我只想使用 0.8.17 版本的 Solidity 编译器，不要再低了。注意，确保编译器版本与`hardhat.config.js`中是相同的.

```solidity
import "hardhat/console.sol";
```

Hardhat 给了我们一些魔法，可以在我们的合约中做一些控制台日志。调试智能合约实际上具有挑战性，但这是Hardhat给我们的好东西之一，使开发更容易。

```solidity
contract WavePortal {
    constructor() {
        console.log("Yo yo, I am a contract and I am smart");
    }
}
```

所以，智能合约有点像其他语言中的`class` ，如果你见过的话！一旦我们第一次初始化这个合约，构造函数就会运行并打印出那一行。请让那一行写上你想写的东西:)!

在下一课中，我们将运行它，看看会得到什么！

### 在本地编译合约并运行

#### 🔥 模仿区块链环境进行测试

你已经做到了。你已经写了一个智能合约。你是冠军！

现在我们实际上需要

1. 编译它。
2. 将其部署到我们的本地区块链。
3. 一旦它在那里，console.log 就会运行 :)。

我们需要这样做，因为在现实世界中，智能合约存在于区块链上。而且，我们希望我们的网站和智能合约能够被真实的人使用，这样他们就可以👋 在我们这里或做任何你想让他们做的事！

所以，即使我们在本地工作时，我们也想模仿这种环境。从技术上讲，我们可以直接编译和运行Solidity代码，但Solidity的神奇之处在于它可以与区块链和Ethereum钱包互动（我们将在下一课中看到更多）。所以，最好现在就把这个敲出来。

我们只是要编写一个自定义脚本来为我们处理这 3 个步骤。

让我们开始吧！

### 📝 构建一个脚本来运行我们的合约

进入`scripts`目录并创建一个名为`run.js`的文件。

所以，为了测试智能合约，我们必须做很多正确的事情。比如：编译、部署，然后执行。

我们的脚本将使我们非常容易地快速迭代我们的合约 :)。

所以，这就是`run.js`将要拥有的：

```javascript
const main = async () => {
  const waveContractFactory = await hre.ethers.getContractFactory("WavePortal");
  const waveContract = await waveContractFactory.deploy();
  await waveContract.deployed();
  console.log("Contract deployed to:", waveContract.address);
};

const runMain = async () => {
  try {
    await main();
    process.exit(0); // exit Node process without error
  } catch (error) {
    console.log(error);
    process.exit(1); // exit Node process while indicating 'Uncaught Fatal Exception' error
  }
  // Read more about Node exit ('process.exit(num)') status codes here: https://stackoverflow.com/a/47163396/7974948
};

runMain();
```

真棒。

#### 🤔 效果如何？

再次在这里逐行进行。

```javascript
const waveContractFactory = await hre.ethers.getContractFactory("WavePortal");
```

这实际上将编译我们的合约并生成我们在`artifacts`目录下使用我们的合约所需的必要文件。运行后去检查它:)。

```javascript
const waveContract = await waveContractFactory.deploy();
```

这很花哨:)。 

这里发生的事情是Hardhat将为我们创建一个本地Ethereum网络，但只是为了这个合约。然后，在脚本完成后，它将销毁这个本地网络。所以，每次你运行合约时，它都是一个新的区块链。这有什么意义呢？这有点像每次刷新你的本地服务器，所以你总是从一个干净的板块开始，这使得它很容易调试错误。。

```javascript
await waveContract.deployed();
```

我们将等到我们的合约正式部署到我们的本地区块链！我们的`constructor`函数在实际部署时运行。

```javascript
console.log("Contract deployed to:", waveContract.address);
```

最后，一旦它被部署，`waveContract.address`基本上会给我们已部署合约的地址。这个地址是我们如何在区块链上实际找到我们的合约。在实际的区块链上有数以百万计的合约。所以，这个地址让我们很容易就能找到我们感兴趣的合约！这一点在后面会更重要。一旦我们部署到真正的Ethereum网络上，这将是更重要的。。

让我们运行吧！

```bash
npx hardhat run scripts/run.js
```

您应该从合约中看到您的`console.log`运行，然后您还应该看到打印出的合约地址！！！这是我得到的：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211201044435.png)

#### 🎩 Hardhat和 HRE

在这些代码块中，您会经常注意到我们使用`hre.ethers`, 但`hre`从未在任何地方导入？这是什么魔术？

直接从 Hardhat 文档本身，您会注意到这一点：

> Hardhat 运行时环境，简称 HRE，是一个包含 Hardhat 在运行任务、测试或脚本时公开的所有功能的对象。实际上，Hardhat 就是 HRE。

那么这意味着什么呢？好吧，每次你运行一个以`npx hardhat`开头的终端命令时，你都会得到这个`HRE`对象，它是使用你的代码中`hardhat.config.js`指定的配置建立的！这意味着你永远不必实际操作Hardhat。这意味着你将永远不必在你的文件中进行某种导入，比如:

```
const hre = require("hardhat")
```

你会在我们的代码中看到很多`hre`，但从未导入任何地方！查看这个很棒的[Hardhat 文档](https://hardhat.org/advanced/hardhat-runtime-environment.html?utm_source=buildspace.so&utm_medium=buildspace_project)以了解更多信息！

```
未完待续...

原文地址：https://buildspace.so/p/build-solidity-web3-app
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)

