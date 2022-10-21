---
title: 使用 Polygon 创建 Web3 论坛 (上篇)
description: null
author: 李留白
weight: 0
date: 2022-10-21T13:24:19.496Z
lastmod: 2022-10-21T14:55:03.610Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210212124018.png
---

## 概述

我们很高兴能与Polygon的朋友合作，为您带来这份关于使用[Polygon](https://polygon.technology/)网络建立web3论坛的深度指南。

让我们开始吧 :)

### 目标

对我们来说，web3 最有趣的方面之一是它有助于开发人员围绕数据库构建和共享可组合的行为。

任何人都可以在GitHub上挖掘出一个智能合约，部署它，并在几秒钟内运行他们自己的微服务。

不仅如此，智能合约公开的稳定的API是一张空白的画布，社区中的任何前端开发者都可以在上面构建和共享组件。

本着这种精神，今天我们将建立一个可部署的智能合约和一个配套的React组件，以向您的应用程序添加评论线程。

从理论上讲，这可以开源并作为 npm 包发布，以允许任何开发人员快速将论坛添加到他们的应用程序。

以下是我们要建立的目标：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210212143830.png)

通过在我们的应用程序周围添加像这样的评论线程，我们可以让我们的用户在任何地方就任何事情进行对话

我们认为以下内容将为开发人员带来良好的体验。

```tsx
const App = () => {
  return (
    <Comments topic="my-blog-post" />
  )
}
```

有了这个，我们就可以在任何我们希望让用户谈论某个话题的地方快速放入一个组件！

### 可行性

每当我们建立一个dapp时，我们必须考虑我们试图做的事情在web3生态系统的当前状态下的可行性。

如果我们正在执行大量写入操作，或者我们需要接近实时的网络速度，那么我们可能应该退后一步，思考区块链技术是否适合我们的用例。

重要的是要记住，从区块链中读取数据是免费的，但向其写入数据会产生费用。这意味着用户可以免费查看评论，但写自己的评论却要付费。

在写这篇文章的时候，以太坊仍然是非常的缓慢和昂贵的。（译者注：以太坊已经合并到pos了）如果我们在以太坊主网的基础上建立这个论坛，那么我们将期望用户支付约5美元来提交评论，我们将看到15秒以上的加载时间。

值得庆幸的是，以太坊之上提供了各种扩展解决方案，Polygon可以在这里拯救我们。使用Polygon，我们的用户只需花费约0.002美元就能发表评论，而我们看到的加载时间将接近约2秒!

### 关于Polygon

Polygon的前身是Matic Network，它提供了几种不同的扩展解决方案，允许开发者建立自己的兼容Ethereum的区块链。与以太坊区块链相比，这些解决方案对交易速度、交易成本和安全性都有不同程度的改善。

在这个意义上，把Polygon仅仅看作是一个区块链是不够的，相反，它是一个SDK，用于快速创建根据开发人员需求定制的区块链。然而根据设计，Polygon区块链能够相互沟通并与以太坊通信。

大体上，Polygon支持创建两种类型的链，独立链和安全链。独立链实现了自己的共识层，而安全链则利用了以太坊的共识层。

谈到Polygon与以太坊的通信时，Polygon提供了两种选择，一种是权益证明（PoS）桥，一种是Plasma桥。Plasma桥提供了更高的安全性，然而，随着这种冲击，对代币的一些限制和7天的高额退出期。建议大多数开发者使用PoS桥在Polygon/以太坊链之间传输数据。

虽然Polygon为开发者提供了许多高级功能，但我们只是要使用它的一个公共区块链，即Mumbai Testnet（链ID 80001），我们将把我们共同建立的智能合约部署到这个区块链上。

如果你想阅读更多关于Polygon的信息，我们[推荐这篇文章](https://limechain.tech/blog/polygon-blockchain-scaling/)。

## 设置

在我们开始构建之前，让我们先来设置一下吧

首先，请克隆[启动程序 repo ](https://github.com/pointer-gg/comments-with-polygon)并安装所需的依赖项。

```bash
# Clone repo and check out starter branch
git clone https://github.com/pointer-gg/comments-with-polygon.git --branch start

# Change to the newly created directory
cd comments-with-polygon

# Install project dependencies
npm install
```

>  **仔细检查你的分支!**
>
> 在开始本教程之前，请确保你是在start分支上。main分支显示的是完成本教程后你应该拥有的代码的最终状态。

你还需要确保你已经安装了[MetaMask扩展](https://metamask.io/download/)，并且有一个MetaMask账户。如果你愿意，请看我们之前的教程[《设置你的钱包》](https://www.pointer.gg/tutorials/Setting-Up-Your-Wallet)，了解这方面的更多细节。

在这个项目中，我们将使用几个有趣的npm包。

- chakra-ui - 一个组件库，可以快速、轻松地构建应用程序
- react-query - 一个流行的异步状态管理库
- wagmi - 一个React钩子的集合，帮助我们做web3相关的事情
- davatar - 一个用于web3的头像组件

我们将带领你完成每一个项目，并告诉你如何结合使用它们来编写一些很棒的dapp。

## 合约


好了，现在我们已经准备好了，让我们先开始后端，思考一下我们的合约API。

### API设计

我们需要一个数据结构来表示评论，其中应该包括。

- 一个ID
- 评论所属的主题
- 一条消息
- 创建者
- 一个时间戳

我们的用户界面也需要一些不同的东西。

- 获取一个特定主题的评论列表的方法
- 添加新评论的方法
- 接收关于已添加评论的更新的方法

将此转化为一个API，我们将看到如下内容

```solidity
contract Comments {
  // Exposed data structure
  struct Comment {
    uint32 id;
    string topic;
    address creator_address;
    string message;
    uint256 created_at;
  }
       
  // Notify users that a comment was added 
  event CommentAdded(Comment comment);

  // Fetch a list of comments for a topic 
  function getComments(string calldata topic) public view returns(Comment[] memory) {}

  // Persist a new comment
  function addComment(string calldata topic, string calldata message) public {}
}
```

更多关于 solidity 类型的信息：

```
如果这是你第一次看到solidity代码，让我们把它分解一下。

与许多语言一样，solidity 提供了一个关键字 struct，允许用户创建自己的复杂数据类型。我们正在使用 struct 来创建一个带有各种字段的数据类型 Comment。

你可能已经注意到我们在结构中使用了两种不同的整数类型，uint32和uint256。Solidity支持许多有符号和无符号的整数类型，每个类型都有不同的位数，例如int8, int32, int256, uint8, uint32, uint256。因为javascript中的数字类型只能准确地表示最多53位的数字，所以solidity中任何位数高于53位的数字类型在javascript中都会被反序列化为一个特殊的数字类，BigNumber，它被设计用来准确地存储和对极大的数字做算术。通过使注释id成为uint32的类型，我们就不必在javascript代码中处理BigNumber类型，因为javascript的原生数字可以准确地表示一个只有32位的数字。有一点需要注意的是，油费可以根据你选择的数字类型而变化，尽管这并不总是简单地以较少的位数转化为较小的油费。

address是solidity中非常常用的另一种类型，用于存储20字节的区块链地址值，你经常会看到0x1234567891234567891234567891234567891234的形式。

最后，你可能已经注意到我们在接口代码中使用了两个关键字，calldata和memory。在solidity中，有三个关键字指导在执行环境中应该如何存储数值。

storage - 在区块链上无限期地存储值
memory - 只在函数被调用的时间段内存储该值
calldata - 就像memory一样，除了标记为calldata的变量是不可变的，意味着它的值不能被改变（通常用于面向公众的函数参数）。

当考虑gas费用时，calldata是最具成本效益的选择，然后是memory，最后是storage。
```

让我们用[hardhat](https://hardhat.org/getting-started/)建立一个本地Ethereum环境。Hardhat将使我们能够通过在我们的机器上直接运行一个本地以太坊网络来运行、测试和部署solidity合约。

要启动一个新的hardhat项目，请在repo的根目录下运行以下程序。

```bash
npx hardhat
```

这个命令将显示三个提示，你可以如图所示进行回应。

```bash
✔ What do you want to do? · Create a basic sample project
✔ Hardhat project root: · /path/to/cloned/repo # should be populated by default
✔ Do you want to add a .gitignore? (Y/n) · n # we've already added one!
```
这将启动我们的项目，包括 hardhat.config.js、contracts、scripts 和 test等几个文件/目录。

请更新hardhat.config.js的module.exports，以反映以下内容。

```js
module.exports = {
  solidity: "0.8.4",
  networks: {
    hardhat: {
      chainId: 1337,
    },
  },
};
```

这是一个必要的步骤，因为[MetaMask的一个限制](https://hardhat.org/metamask-issue.html#metamask-chainid-issue)，它假定本地链使用的是1337号链ID。

什么是链ID？

```
每个区块链网络都有一个链上的ID来唯一地识别它。链ID在签名过程中使用，被添加到以太坊，以防止跨链的重放攻击。链ID是在EIP-155中引入的。所有链ID的列表可以在chainlist.org上找到。
```

在contracts文件夹中，请将Greeter.sol重命名为Comments.sol，并将其内容替换为我们的脚手架API。

```solidity
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;

import "hardhat/console.sol";

contract Comments {
  // Exposed data structure
  struct Comment {
    uint32 id;
    string topic;
    address creator_address;
    string message;
    uint256 created_at;
  }
       
  // Notify users that a comment was added 
  event CommentAdded(Comment comment);

  // Fetch a list of comments for a topic 
  function getComments(string calldata topic) public view returns(Comment[] memory) {}

  // Persist a new comment
  function addComment(string calldata topic, string calldata message) public {}
}
```

在你的solidity代码上没有看到语法高亮？

```
如果你使用的是Visual Studio Code，而你在solidity代码上没有看到正确的语法高亮，可以尝试安装solidity扩展。
```

### 测试

在我们实现getComments和addComment之前，让我们练习一下测试驱动开发，并编写一个简单的测试来断言他们的预期行为。

在测试文件夹中，将 sample-test.js 改名为 basic.js，并将其内容替换为以下内容。

test/basic.js

```js
const { expect } = require("chai");
const { ethers } = require("hardhat");

describe("Comments", function () {
  it("Should add and fetch successfully", async function () {
    const Comments = await ethers.getContractFactory("Comments");
    const comments = await Comments.deploy();
    await comments.deployed();

    expect(await comments.getComments("my-first-blog-post")).to.be.lengthOf(0);

    const tx1 = await comments.addComment(
      "my-first-blog-post",
      "my first comment"
    );
    await tx1.wait();

    expect(await comments.getComments("my-first-blog-post")).to.be.lengthOf(1);
    expect(await comments.getComments("my-second-blog-post")).to.be.lengthOf(0);

    const tx2 = await comments.addComment(
      "my-second-blog-post",
      "this comment is on a different thread"
    );
    await tx2.wait();

    expect(await comments.getComments("my-first-blog-post")).to.be.lengthOf(1);
    expect(await comments.getComments("my-second-blog-post")).to.be.lengthOf(1);
  });
});
```

这个测试将涵盖我们的用例，即获取和添加评论到不同的主题。

在这个测试中，我们首先获取 "my-first-blog-post "的评论，我们希望不存在任何评论，因为我们还没有给这个主题添加任何评论。下一步自然就是尝试添加评论。

当我们添加评论时，我们等待函数并返回一个事务，但这并不意味着我们的评论被成功添加。然后我们必须等待交易，以确定我们的评论被挖出并坚持到区块链上。

在我们确定它已被mint后，我们再次获取 "我的第一个博客帖子 "的评论列表，我们现在希望有一个评论。

那么，明智的做法是获取不同主题的评论，并确保我们刚刚发布的评论不会以某种方式出现在其他主题上。因此，我们为 "我的第二个博客帖子 "获取评论，希望没有评论。

我们重复一个类似的过程，这次是给'my-second-blog-post'添加评论，并再次确保每个主题的评论数量看起来是合适的。

如果我们现在运行这个测试，它将会失败，因为我们还没有实现我们的两个合约函数，但是一旦我们实现了，我们应该看到它通过了，这将有助于保证我们正确地实现了我们的API!

>  **注意事项**
>
> 为了本教程的目的，我们保持我们的测试简洁，然而在现实世界中，我们应该始终确保我们的合同得到彻底的测试，因为一旦我们部署了它们，它们将是不可改变的。

### 执行

接下来让我们来实现我们的合约功能。

我们需要一个数据结构来存储评论。为此，我们将使用一个映射来存储每个主题及其相关评论的数组。

```solidity
mapping(string => Comment[]) private commentsByTopic;
```

有了这个数据结构，我们可以很简单地实现getComments。

```solidity
mapping(string => Comment[]) private commentsByTopic;
  
function getComments(string calldata topic) public view returns(Comment[] memory) {
   return commentsByTopic[topic];
}
```

鉴于这种数据结构，添加评论也是很自然的事情。

```solidity
mapping(string => Comment[]) private commentsByTopic;

function addComment(string calldata topic, string calldata message) public {
    Comment memory comment = Comment({
        id: idCounter,
        topic: topic,
        creator_address: msg.sender,
        message: message,
        created_at: block.timestamp
    });
    commentsByTopic[topic].push(comment);
    idCounter++;
    emit CommentAdded(comment);
}
```

关于我们的addComment实现，有几件事需要注意。

首先，我们添加了一个idCounter来帮助我们生成序列id。idCounter被存储在链上，所以它的值将在不同的函数调用中被持续保存。在内存中创建一个评论后，我们再把它推送到commentsByTopic映射中。与javascript映射不同，如果我们的commentsByTopic映射还没有意识到一个主题，我们不必担心将其初始化为一个空数组，solidity实际上默认为我们做了这个。如果你想知道msg和block是从哪里来的，它们是自动提供给每个函数的上下文数据位。msg.sender是非常常用的，它保存了认证钱包的地址。最后，我们将发出一个CommentAdded事件，这在以后将对更新我们的用户界面很有帮助。

将这一切联系起来，我们最终的合约实现看起来像下面这样。

```solidity
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;

import "hardhat/console.sol";

contract Comments {
    struct Comment {
        uint32 id;
        string topic;
        address creator_address;
        string message;
        uint created_at;
    }

    uint32 private idCounter;
    mapping(string => Comment[]) private commentsByTopic;
        
    event CommentAdded(Comment comment);

    function getComments(string calldata topic) public view returns(Comment[] memory) {
       return commentsByTopic[topic];
    }

    function addComment(string calldata topic, string calldata message) public {
        Comment memory comment = Comment({
            id: idCounter,
            topic: topic,
            creator_address: msg.sender,
            message: message,
            created_at: block.timestamp
        });
        commentsByTopic[topic].push(comment);
        idCounter++;
        emit CommentAdded(comment);
    }
}
```

现在我们已经实现了我们的合同，让我们运行我们之前写的测试，以确保一切顺利

````bash
npx hardhat test
````

如果我们的测试通过了，那么现在是时候运行一个本地以太坊节点并将我们的合约部署到它上面了。 如果测试失败了，那么请回顾之前的步骤，以确保你没有错过任何东西。

### 部署

让我们进入scripts文件夹，将sample-script.js重命名为deploy-and-seed.js，并用以下部署脚本替换其内容。

scripts/deploy-and-seed.js

```js
// We require the Hardhat Runtime Environment explicitly here. This is optional
// but useful for running the script in a standalone fashion through `node <script>`.
//
// When running the script with `npx hardhat run <script>` you'll find the Hardhat
// Runtime Environment's members available in the global scope.
const hre = require("hardhat");

async function main() {
  // Hardhat always runs the compile task when running scripts with its command
  // line interface.
  //
  // If this script is run directly using `node` you may want to call compile
  // manually to make sure everything is compiled
  // await hre.run('compile');

  // We get the contract to deploy
  const CommentsContract = await hre.ethers.getContractFactory("Comments");
  const contract = await CommentsContract.deploy();

  await contract.deployed();

  const tx1 = await contract.addComment("my-blog-post", "My first comment");
  await tx1.wait();

  const tx2 = await contract.addComment("my-blog-post", "My second comment");
  await tx2.wait();

  console.log("Contract deployed to:", contract.address);
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

顾名思义，这个脚本将我们的Comments合约部署到我们选择的链上，并将为我们填充两个初始评论，这样我们就有一些数据可以立即使用。

让我们让本地的Ethereum节点运行起来。

```bash
npx hardhat node
```

注意这个命令的输出。Hardhat为你设置了几个假钱包用于开发，每个钱包都装了大量的假ETH。每个钱包的地址和私钥都已经输出到终端。我们稍后将使用这些账户中的一个从浏览器连接到我们的本地节点。

```bash
Started HTTP and WebSocket JSON-RPC server at http://127.0.0.1:8545/

Accounts
========

WARNING: These accounts, and their private keys, are publicly known.
Any funds sent to them on Mainnet or any other live network WILL BE LOST.

Account #0: 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266 (10000 ETH)
Private Key: 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

Account #1: 0x70997970c51812dc3a010c7d01b50e0d17dc79c8 (10000 ETH)
Private Key: 0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d
```

运行后，打开一个新终端，让我们将智能合约部署到正在运行的本地节点。

```bash
npx hardhat run --network localhost scripts/deploy-and-seed.js
```

你现在应该看到类似于以下的输出。

```bash
Contract deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3
```

稍后我们会回到这个脚本，并将其部署到Polygon Mumbai Testnet上，但现在我们已经将合同部署到了本地节点上，接下来我们将进入前端UI开发工作。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
