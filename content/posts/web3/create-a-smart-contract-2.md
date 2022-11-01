---
title: Web3教程：编写您的第一个“Hello, World”智能合约（2）
description: null
author: 李留白
weight: 0
date: 2022-10-29T02:55:56.295Z
lastmod: 2022-10-29T04:12:25.137Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210271827013.png
---

上一篇传送门：[Web3教程：编写您的第一个“Hello, World”智能合约（1）](https://mp.weixin.qq.com/s/qw1GYuDiiXQcfA7GLZwzOQ)

## 目录

1. 什么是智能合约
2. 部署您的第一个智能合约
3. 什么是气体
4. 与您的智能合约交互
5. 智能合约的结构
6. 将您的合同提交给 Etherscan
7. 智能合约安全挑战
8. 将合约集成到前端
9. 其他资源

## 3、什么是气体，它是如何使用的？

以太坊中的 Gas 是指在区块链网络上执行智能合约或交易所需的额外费用。

为什么会存在这种情况？好吧，在运行智能合约时有几个主要限制：

1. 每个部署的交易、智能合约或智能合约的执行，都必须在以太坊区块链上的每个完整节点上运行，以保证有效性。这是非常低效的（尽管新的区块链正在简化这一点）！
2. 因为智能合约是图灵完备的，它们有可能永远执行，锁定区块链上的每个节点。

> 什么是图灵完备性？
>
> 实际上，只要有足够的时间和资源，图灵完备的编程语言就能够解决或表示任何计算问题，无论多么复杂。特别是，这有几个含义： 
> 
> 1. 理论上，任何图灵完备语言都可以用来表示另一种图灵完备语言的逻辑，尽管实现的时间可能长得不合理。
> 2. 图灵完备的程序最终可能会永远循环和执行。事实上，没有通用的方法可以证明这样的程序不会永远运行（也称为“停机问题”）。
> 
> 例如，普通计算器不是图灵完备的，因为它只允许几种类型的计算。但是，计算机或科学计算器是图灵完备的，因为可以在其上执行任何类型的程序。

由于智能合约程序可以永远运行，gas 已成为以太坊中管理区块链程序影响的实用方式！在区块链上进行的每次计算或交易都会产生一些费用。这些费用可以防止昂贵的（或无休止的）合同执行，确保矿工的工作得到公平的补偿，并提供一个公平的市场来优先考虑哪些交易进入区块链。

### 计算天然气成本

对于任何给定的程序，使用的总气体计算为以太坊虚拟机执行的每个操作的气体总和。例如，在智能合约中添加两个数字需要 3 gas，而发送交易需要 21,000 gas。

天然气的总成本是通过将智能合约使用的天然气量乘以**天然气价格**得出的，天然气价格是由您（交易发送者）设置的值。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210291110696.png)

为您的交易设置更高的 gas 价格意味着它更有可能在区块链上得到确认，因为以太坊区块链每秒只能确认大约 15 笔交易。但是，发送者也会花费更多的以太币。

另一个可以设置的重要值是**gas 限制，**或者您愿意在交易中花费的最大 gas 量。

通过将 gas 价格乘以 gas 限制，您将获得允许以太坊在任何特定交易的 gas 费用上花费的最大 Ether 数量。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210291111631.png)



>#### 什么是“gwei”或“wei”，它们与 ETH 有什么关系？
>
>“Wei”是 Ether 的最小单位，其中 10¹⁸ Wei 代表 1 Ether。1 gwei 是 10⁹ wei，每个 Ether 有 10⁹ gwei。

#### 了解气体执行

当进行智能合约调用时，调用（或交易）将尝试使用程序执行时提供的气体。

- 如果调用成功，未使用的gas将返还给发送者。
- 如果调用因 gas 用完而失败，整个交易将恢复，撤消对区块链的所有更改。不会返回任何气体，因为它在计算过程中都已用完。

根据新的 EIP-1559 规范，成功交易的一部分 gas 费用将被烧掉（或从总供应量中移除），其余部分将发送给将您的交易添加到区块链的矿工。

> 什么是 EIP-1559，它如何影响 gas 费用？ 
>
>  在2021年8月5日实施以太坊提案EIP-1559之前，使用一手价gas拍卖的交易被纳入到区块链上。

## 4、与您的智能合约交互

现在我们已经成功地在goerli网络上部署了一个智能合约，让我们测试一下我们的web3技能并与之互动吧

### 第1步：创建一个 interact.js 文件

这是我们要写互动脚本的文件。我们将使用你之前在第一部分安装的Ethers.js库。

在你的`/scripts`文件夹中（hardhat教程），或者你的主目录中（[Truffle教程](https://docs.alchemy.com/alchemy/tutorials/hello-world-smart-contract#create-and-deploy-your-smart-contract-using-truffle)），创建一个名为`/interact.js`的新文件，添加以下几行代码:

```js
// interact.js

const API_KEY = process.env.API_KEY;
const PRIVATE_KEY = process.env.PRIVATE_KEY;
const CONTRACT_ADDRESS = process.env.CONTRACT_ADDRESS;
```

### 第2步：更新你的.env文件

我们将使用新的环境变量，所以我们需要在我们的`.env`文件中定义它们，并确保`dotenv`模块正在加载这些变量。

我们需要为我们的Alchemy `API_KEY`和部署智能合约的`CONTRACT_ADDRESS`添加一个定义。

你的.env文件应该看起来像这样:

```js
# .env

API_URL = "https://eth-goerli.alchemyapi.io/v2/"
API_KEY = ""
PRIVATE_KEY = ""
CONTRACT_ADDRESS = "0x"
```

### 第3步：获取你的合约ABI

我们的合约ABI（应用二进制接口）是与我们的智能合约互动的接口。你可以在这里[了解更多关于合约ABI的信息](https://docs.alchemy.com/alchemy/guides/eth_getlogs#what-are-ab-is)。Hardhat（和Truffle）自动为我们生成一个ABI，并将其保存在HelloWorld.json文件中。为了使用它，我们需要通过在我们的 `contract-interact.js` 文件中添加以下几行代码来解析其内容。

```js
// interact.js

// For Truffle
const contract = require("./build/contracts/HelloWorld.json");

// For Hardhat
const contract = require("../artifacts/contracts/HelloWorld.sol/HelloWorld.json");
```

如果你想看到ABI，你可以把它打印到你的控制台。

```js
console.log(JSON.stringify(contract.abi));
```

要运行`interactive.js`并看到你的ABI被打印到控制台，请导航到你的终端并运行

#### Hardhat:

```js
npx hardhat run scripts/interact.js
```

#### Truffle:

```js
node interact.js
```

### 第4步：创建你的合同的实例

为了与我们的合同互动，我们需要在我们的代码中创建一个实例。为了用Ethers.js做到这一点，我们将需要使用三个概念。

1. Provider--这是一个节点提供者，让你对区块链进行读写访问。
2. Signer - 这代表一个有能力签署交易的Ethereum账户。
3. Contract - 这是一个Ethers.js对象，代表一个部署在链上的特定合约。

我们将使用上一步的合约ABI来创建我们的合约实例。

```js
// interact.js

// Provider
const alchemyProvider = new ethers.providers.AlchemyProvider(network="goerli", API_KEY);

// Signer
const signer = new ethers.Wallet(PRIVATE_KEY, alchemyProvider);

// Contract
const helloWorldContract = new ethers.Contract(CONTRACT_ADDRESS, contract.abi, signer);
```

你可以在[Ethers.js文档](https://docs.ethers.io/v5/)中阅读更多关于提供者、签名者和合约的信息。

### 第5步：阅读初始信息

还记得我们部署合同时，`initMessage = "Hello world!"`吗？我们现在要读取存储在我们的智能合约中的消息，并将其打印到控制台。

在JavaScript中，我们使用异步函数与网络互动。请看这篇文章，[了解更多关于异步函数的信息](https://blog.bitsrc.io/understanding-asynchronous-javascript-the-event-loop-74cd408419ff)。

使用下面的代码来调用我们智能合约中的`message`函数，并读取初始消息。

```js
// interact.js

// ...

async function main() {
  const message = await helloWorldContract.message();
  console.log("The message is: " + message);
}
main();
```

在终端使用`npx hardhat run scripts/interact.js`运行该文件后，我们应该看到这个响应。

```js
The message is: Hello world!
```

祝贺你! 你刚刚成功地从Ethereum区块链上读取了智能合约数据，好样的!

### 第6步：更新消息

现在，我们不只是读取消息，还可以使用`update`函数来更新保存在我们的智能合约中的消息！非常酷吧？

为了做到这一点，我们可以直接在我们实例化的合约对象上调用更新函数，像这样。

```js
// interact.js

// ...

async function main() {
  const message = await helloWorldContract.message();
  console.log("The message is: " + message);

  console.log("Updating the message...");
  const tx = await helloWorldContract.update("This is the new message.");
  await tx.wait();
}
main();
```



注意，我们在返回的交易对象上调用了`.wait()`。这确保我们的脚本在继续前进之前等待交易在区块链上被挖掘出来。如果你漏掉这一行，你的脚本可能无法在你的合约中看到更新的`message`值。

### 第7步：读取新消息

你应该能够重复步骤5来读取更新的`message`值。花点时间，看看你是否能做出必要的修改来打印出这个新值

如果你需要一个提示，这里是你的`interactive.js`文件在这一点上应该是什么样子。

```js
// interact.js

const API_KEY = process.env.API_KEY;
const PRIVATE_KEY = process.env.PRIVATE_KEY;
const CONTRACT_ADDRESS = process.env.CONTRACT_ADDRESS;

const contract = require("../artifacts/contracts/HelloWorld.sol/HelloWorld.json");

// provider - Alchemy
const alchemyProvider = new ethers.providers.AlchemyProvider(network="goerli", API_KEY);

// signer - you
const signer = new ethers.Wallet(PRIVATE_KEY, alchemyProvider);

// contract instance
const helloWorldContract = new ethers.Contract(CONTRACT_ADDRESS, contract.abi, signer);

async function main() {
    const message = await helloWorldContract.message();
    console.log("The message is: " + message);

    console.log("Updating the message...");
    const tx = await helloWorldContract.update("this is the new message");
    await tx.wait();

    const newMessage = await helloWorldContract.message();
    console.log("The new message is: " + newMessage);
}

main();
```

现在只要运行这个脚本，你就可以看到旧的信息、更新状态和新的信息被打印到你的终端上!

```js
npx hardhat run scripts/interact.js --network goerli
```

```js
The message is: Hello World!
Updating the message...
The new message is: This is the new message.
```

当你运行该脚本时，你可能会注意到`Updating the message...`的步骤在新信息被设置之前需要花费一些时间来加载。

那是由于采矿过程造成的! 如果你对如何跟踪正在挖矿的交易感到好奇，请访问[Alchemy mempool]( https://dashboard.alchemyapi.io/mempool)，查看你的交易状态（是否正在等待、已挖矿或被网络放弃）。如果你的交易被放弃了，查看[Goerli Etherscan](https://goerli.etherscan.io/)并搜索你的交易哈希值也很有帮助。

完成了! 你现在已经部署了一个Ethereum智能合约并与之进行了互动。

## 5、智能合约的结构

以太坊智能合约有许多关键的概念，与web2开发模式不同。

### **Types**

在Solidity中，合约类型是一个结构，围绕一个单一的目的组织一系列相关的功能。

地址类型是Ethereum地址，相当于20个字节，以十六进制形式表示，以前缀0x开头。

大多数其他类型将是你作为一个开发人员所熟悉的--包括布尔、整数、定点数字、字节数组和字词。

### **Data Storage**

在 Solidity 中，参考数据值可以被存储为存储、内存或 Calldata，这取决于存储数据的作用。特别是

- 存储器将数据永久地保存在区块链上，而且非常昂贵。
- 内存值只在智能合约的执行期限内存储，而且使用成本低廉（只需要花费少量的气体）。
- calldata是一个特殊的数据位置，包含函数参数，只适用于外部函数调用参数。

### **Function Modifiers**

函数的存在是为了根据外部交易发起的调用获得/设置信息。特别是，除非由外部交易发起，否则智能合约永远无法运行--它们不会在后台默默地执行。

访问修改器包括:

- **public**:可以被所有函数或调用者访问
- **external**:只能由外部调用者访问，不能由内部函数访问
- **internal**:只能被这个合同或从它派生出来的合同访问。
- **private**:只能由这个合同本身访问

其他修饰语包括:

- **view**:这保证了该函数不会修改合同的数据状态（或存储的数据）。
- **pure**：这保证了该函数既不读取也不修改合同数据的状态。
- **payable**:被声明为可支付的函数和地址可以接受乙醚进入他们的合约。

### **特殊函数和变量**

有一些全局变量和函数将有助于记住你的访问权限！有一些特殊的变量是：

- block.number（uint256）:最近一个区块的编号。
- block.timestamp (uint256):最近一个区块的UNIX时间戳。
- block.gaslimit (uint256):当前区块的气体上限。
- msg.sender（可支付的地址）:触发合同的交易的发件人。
- msg.value (uint256): 与信息一起转移的wei数。

特殊功能包括:

- receive(): 合约只能声明其中一个函数。当合约被发送至以太时，它作为默认的目的地。它不能有参数，不能返回任何东西，而且必须是外部的，应支付的。
- fallback(): 合约只能有一个这样的函数声明。如果对合约的调用与任何函数不匹配，或者没有提供数据且没有声明receive()函数，它将作为后备函数。它不能有参数，不能返回任何东西，而且必须是外部的。

### **Events**

Solidity 事件大致等同于其他编程范式中的日志。它们在合约执行时被发射出来，永久地存储在区块链上，但不能被智能合约修改/读取。

你可以像这样声明和触发一个事件。

```js
event TestEvent (
	uint256 date,
  string value
);

emit TestEvent(block.timestamp,’My first event!”);
```

事件可以通过各种不同的方式被访问。

1. 事件存储在交易的收据中，并可以在那里访问。
2. 你可以用myContract.events.TestEvent([options][, callback])来订阅一个事件。
3. 你可以用myContract.getPastEvents(event[, options][, callback])这样的请求来请求过去的事件。

这应该让您对智能合约的关键组件有了一个大致的了解 。[您可以在这里链接的 Solidity 文档中找到更多细节。](https://docs.soliditylang.org/en/v0.8.0/contracts.html)


> 译者注：下一篇明天到来，保持关注。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)