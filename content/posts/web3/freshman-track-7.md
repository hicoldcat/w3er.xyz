---
title: Web3系列教程之新手篇---Level 7：用Ethers.js创建一个Ethereum Dapp
description: null
author: 李留白
weight: 0
date: 2022-06-19T16:27:47.998Z
lastmod: 2022-06-26T12:00:53.219Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/freshman.png
---

> 原文：[https://www.learnweb3.io/tracks/freshman](https://www.learnweb3.io/tracks/freshman)<br/>
> 翻译：李留白

本文是[learnweb3](https://www.learnweb3.io/)的新手篇，包括：
- [Level 0：前置基础编程知识](https://hicoldcat.com/posts/web3/freshman-track-0)
- [Level 1：什么是区块链？](https://hicoldcat.com/posts/web3/freshman-track-1)
- [Level 2：什么是Web3？](https://hicoldcat.com/posts/web3/freshman-track-2)
- [Level 3：什么是ETH？](https://hicoldcat.com/posts/web3/freshman-track-3)
- [Level 4：加密钱包？](https://hicoldcat.com/posts/web3/freshman-track-4)
- [Level 5：Remix IDE介绍](https://hicoldcat.com/posts/web3/freshman-track-5)
- [Level 6：Solidity介绍](https://hicoldcat.com/posts/web3/freshman-track-6)
- [Level 7：dApp介绍](https://hicoldcat.com/posts/web3/freshman-track-7)
- [Level 8：加密货币(ERC20)简介](https://hicoldcat.com/posts/web3/freshman-track-8)
- [Level 9：NFT介绍](https://hicoldcat.com/posts/web3/freshman-track-9)


这是一个关于如何创建一个前端、部署一个Solidity智能合约并将它们连接在一起的分步教程。我们将使用[Metamask](https://metamask.io/)、[Remix IDE](https://remix.ethereum.org/)和[Ethers.js](https://github.com/ethers-io/ethers.js/)。

在本教程结束时，你将能够创建一个简单的带有按钮的HTML前端，可以与智能合约功能互动。本教程分3个阶段进行

- 创建一个基本的HTML网页
- 创建一个基本的 Solidity 智能合约
- 使用Ethers.js将网页与智能合约连接起来。

## 更喜欢视频？

如果你想从视频中学习，我们的YouTube上有本教程的录音。请点击下面的截图观看视频，或者继续阅读本教程

[https://www.youtube.com/watch?v=aqxAWLi6UMA](https://www.youtube.com/watch?v=aqxAWLi6UMA)

## 准备工作

1. 下载并安装[MetaMask](https://metamask.io/)
- 从未使用过Metamask？观看[这个解释视频](https://youtu.be/wlm4QcA8c4Q?t=66)
- 对我们来说，重要的部分是`1:06至4:14`
- 单击顶部的以太坊主网。切换到 Ropsten Tesnet 并在您的 Metamask 钱包上获取帐户公共地址的副本。

2.索取一些Ropsten Tesnet Ether到你的Metamask钱包中。
- [申请测试币的水龙头链接](https://faucet.egorfine.com/)
- [解释水龙头及其使用方法的博客](https://blog.b9lab.com/when-we-first-built-our-faucet-we-deployed-it-on-the-morden-testnet-70bfbf4e317e)

3.安装http服务器。使用任何你喜欢的，但我们建议`lite-server`初学者：
- 安装 Node.js（[下载和说明](https://nodejs.org/en/download/)）
- 安装 lite-server（在终端/命令提示符下使用 NPM）：

```
npm install -g lite-server #install lite-server globally
```

## 创建并提供一个简单的网页

第一步是创建一个基本的 HTML 页面。

1.使用在终端中创建一个新文件夹（目录）mkdir <directory name>
2.在代码编辑器（例如 Atom 或 Visual Studio Code）中，打开文件夹
3.创建一个名为的新文件index.html
4.打开 index.html
5.创建 HTML 样板

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>My First dApp</title>
  </head>
  <body></body>
</html>
```
我们将创建一个简单地向区块链读取和写入值的应用程序。我们将需要一个标签、一个输入和按钮。

6. 在 body 标签内，添加一些文本、标签和输入。

```
<body>
  <div>
    <h1>This is my dApp!</h1>
    <p>Here we can set or get the mood:</p>
    <label for="mood">Input Mood:</label> <br />
    <input type="text" id="mood" />
  </div>
</body>
```

7. 在 div 标签内添加一些按钮。

```
<button onclick="getMood()">Get Mood</button>
<button onclick="setMood()">Set Mood</button>
```

可选：在`<head>`标签内，添加一些样式使其看起来更好看

```
<style>
  body {
    text-align: center;
    font-family: Arial, Helvetica, sans-serif;
  }

  div {
    width: 20%;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
  }

  button {
    width: 100%;
    margin: 10px 0px 5px 0px;
  }
</style>
```

8. 通过终端/命令提示符从其中包含的目录中提供网页index.html并运行：

```
lite-server
```

9. 在浏览器中访问http://127.0.0.1:3000/以查看您的页面！

10. 您的前端现已完成！

## 创建一个基本的智能合约

现在是时候创建一个 Solidity 智能合约了。

1. 您可以使用任何您喜欢的编辑器来制作合同。对于本教程，我们推荐在线 IDE [Remix](https://remix.ethereum.org/)

2. 去[Remix](https://remix.ethereum.org/)

3.查看“Solidity Compiler”和“Deploy and Run Transactions”选项卡。如果它们不存在，请在插件管理器中启用它们

4. 在 remix 中创建一个新的 Solidity 文件，命名为mood.sol

5. 写合同

- 指定solidity版本并添加license

```
// SPDX-License-Identifier: MIT
 pragma solidity ^0.8.1;
```

- 定义合同

```
contract MoodDiary{

 }
```

- 在合约内部创建一个名为 mood 的变量

```
 string mood;
```

- 接下来，创建读取和写入函数

```
 //create a function that writes a mood to the smart contract
 function setMood(string memory _mood) public{
     mood = _mood;
 }

 //create a function the reads the mood from the smart contract
 function getMood() public view returns(string memory){
     return mood;
 }
```
就是这样！您的代码应如下所示[https://github.com/LearnWeb3DAO/BasicFrontEndTutorial/blob/master/contracts/mood.sol](https://github.com/LearnWeb3DAO/BasicFrontEndTutorial/blob/master/contracts/mood.sol)

6. 在 Ropsten 测试网上部署合约。

- 确保您的 Metamask 已连接到 Ropsten 测试网。
- 确保选择正确的编译器版本以匹配solidity 合约。（在编译选项卡中）
- 使用“Solidity Compiler”选项卡编译代码。请注意，加载编译器可能需要一些时间
- 在“部署和运行事务”选项卡下部署合约
- 在 Deployed Contracts 部分下，您可以在 Remix Run 选项卡上测试您的函数，以确保您的合约按预期工作！

*一定要在`Injected Web3`环境下通过 Remix 部署到 Ropsten 上，并在 Metamask 中确认部署事务*

制作一个新的临时文件来保存：

- 部署的合约地址
  - 通过 remix 的Run选项卡中已部署的合同下拉列表旁边的复制按钮复制它
- 合约 ABI（[那是什么？](https://solidity.readthedocs.io/en/develop/abi-spec.html)）
  - 通过 remix 的Compile选项卡中的合同下的复制按钮将其复制（也在详细信息中）

## 将您的网页连接到您的智能合约

回到您的本地文本编辑器中`index.html`，将以下代码添加到您的 html 页面：

1. `index.html`在一组新的脚本标签中将 Ethers.js 源代码导入您的页面：

```
<script
  src="https://cdn.ethers.io/lib/ethers-5.2.umd.min.js"
  type="application/javascript"
></script>

<script>
  ////////////////////
  //ADD YOUR CODE HERE
  ////////////////////
</script>
```

2. 在 script 标签中，导入合约 ABI（[那是什么？](https://solidity.readthedocs.io/en/develop/abi-spec.html)）并在我们的供应商区块链上指定合约地址：

```
const MoodContractAddress = "<contract address>";
const MoodContractABI = <contract ABI>
let MoodContract;
let signer;
```

对于合约 ABI，我们希望专门导航到[JSON 部分](https://docs.soliditylang.org/en/develop/abi-spec.html#json)。我们需要以 JSON 格式描述我们的智能合约。

由于我们有两个方法，这应该从一个数组开始，有 2 个对象：

```
const MoodContractABI = [{}, {}]
```

在上面的页面中，每个对象都应具有以下字段：`constant、inputs、name、outputs、payable和。stateMutabilitytype`

对于setMood，我们在下面描述每个字段：

- 名称：`setMood`，不言自明
- 类型：`function`，不言自明
- 输出：应该是`[]`因为这不返回任何东西
- stateMutability：这是`nonpayable`因为这个函数不接受 Ether
- 输入：这是函数的输入数组。数组中的每个对象都应该有`internalType`,`name`和`type`, 这些分别是`string`,`_mood`和`string`

对于`getMood`，我们在下面描述每个字段：

- 名称：`getMood`，不言自明
- 类型：`function`，不言自明
- 输出：这与中的类型相同`inputs`。`setMood`对于`internalType`,`name`和`type`, 这应该分别是`string`, `""`, 和`string`
- stateMutability：这是`view`因为这是一个视图函数
- 输入：这没有参数，所以这应该是`[]`

您的最终结果应如下所示：

```
const MoodContractABI = [
	{
		"inputs": [],
		"name": "getMood",
		"outputs": [
			{
				"internalType": "string",
				"name": "",
				"type": "string"
			}
		],
		"stateMutability": "view",
		"type": "function"
	},
	{
		"inputs": [
			{
				"internalType": "string",
				"name": "_mood",
				"type": "string"
			}
		],
		"name": "setMood",
		"outputs": [],
		"stateMutability": "nonpayable",
		"type": "function"
	}
]
```

3. 接下来，定义一个 ethers 提供者。在我们的例子中，它是 Ropsten：

```
const provider = new ethers.providers.Web3Provider(window.ethereum, "ropsten");
```

4. 请求访问用户的钱包并将签名者连接到您的元掩码帐户（我们使用[0]默认），并使用您的合约地址、ABI 和签名者定义合约对象

```
provider.send("eth_requestAccounts", []).then(() => {
  provider.listAccounts().then((accounts) => {
    signer = provider.getSigner(accounts[0]);
    MoodContract = new ethers.Contract(
      MoodContractAddress,
      MoodContractABI,
      signer
    );
  });
});
```

5. 创建异步函数来调用你的智能合约函数

```
async function getMood() {
  const getMoodPromise = MoodContract.getMood();
  const Mood = await getMoodPromise;
  console.log(Mood);
}

async function setMood() {
  const mood = document.getElementById("mood").value;
  const setMoodPromise = MoodContract.setMood(mood);
  await setMoodPromise;
}
```

6. 将您的功能连接到您的 html 按钮

```
<button onclick="getMood()">Get Mood</button>
<button onclick="setMood()">Set Mood</button>
```

## 测试！

1.你的网络服务器启动了吗？在浏览器中访问http://127.0.0.1:3000/以查看您的页面！
2.通过 Metamask 测试您的功能并根据需要批准交易。注意块时间约为 15 秒......所以请稍等一下以读取区块链的状态
3.通过https://ropsten.etherscan.io/查看您的合约和交易信息
4.在浏览器中打开控制台 ( Ctrl + Shift + i) 以查看按下这些按钮时发生的奇迹

## 完毕！

庆祝！您刚刚制作了一个网页，该网页与互联网上真实的以太坊测试网进行了交互！这不是很多人可以说他们做过的事情！

## 如果您在本教程中遇到问题，可以试用提供的示例应用程序。

```
git clone https://github.com/LearnWeb3DAO/BasicFrontEndTutorial.git
cd BasicFrontEndTutorial
lite-server
```

### 尝试使用以下信息与我们在 Roptsen 测试网上发布的现有合约进行交互：

- 我们在此[交易](https://ropsten.etherscan.io/tx/0x8da093fdc4ae3e1b469dfff97b414a9800c9fdd8c1c897b6b746faf43aa3b7f8)MoodDiary中创建了一个合约实例

- 这是合同（[在 etherscan 上](https://ropsten.etherscan.io/address/0xc5afd2d92750612a9619db2282d9037c58fc22cb)）

  - 我们还向[ropsten.etherscan.io](https://ropsten.etherscan.io/address/0xc5afd2d92750612a9619db2282d9037c58fc22cb#code)验证了我们的源代码，作为您验证合约到底是什么的附加措施，并且 ABI 也可供全世界使用！

- ABI 也在[这个文件中](https://github.com/LearnWeb3DAO/BasicFrontEndTutorial/blob/master/Mood_ABI.json)

*这说明了一个重点：您也可以构建一个 dApp ，而无需自己编写以太坊合约！如果你想使用已经在以太坊上编写的现有合约，你可以！*

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/my.png)
