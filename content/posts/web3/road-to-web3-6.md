---
title: Web3之路—— 如何建立一个质.押.Dapp
description: null
author: 李留白
weight: 0
date: 2022-08-23T14:19:12.279Z
lastmod: 2022-08-23T14:19:18.527Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814204307.png
---

在本教程中，您将学习如何开发一个具有利息累加、削减和存款/取款功能的简单质.押. DApp。

在过去课程中，我们学习了很多不同的Solidity和Javascript原语，这些原语为我们提供了Web3开发的基本构件。

我们学会了如何从头开始使用Hardhat，建立我们自己的前台，甚至编写Solidity。

虽然所有这些技能对于希望建立坚实基础的开发者来说都是非常有价值的，但也有一些工具可以帮助抽象出一些复杂的环境设置和依赖关系，使构建者可以更容易地进行修补

我们推荐的这些工具之一是[Scaffold-eth](http://scaffoldeth.io/)!

Scaffold-eth的核心是为以太坊上的快速原型开发提供了一个现成的堆栈，使开发者能够获得最先进的工具来快速学习/交付基于以太坊的dApp。

使用Scaffold-eth和Alchemy，你可以轻松地在区块链上合成和部署代码。

在本教程中，我们将使用[SpeedRunEthereum](https://speedrunethereum.com/)   [Challenge #1](https://speedrunethereum.com/challenge/decentralized-staking)的基础代码，并且共同构建一个简单的质.押. dApp。

如果你不熟悉加密货币质.押.，它最好被概括为将持有的加密货币锁定/存入DeFi协议或智能合约以获得利息的过程。

质.押.加密货币已成为许多DeFi协议的基石，并允许开发人员创建复杂的金融衍生产品。

虽然大多数DeFi质.押.合同都非常复杂，但我们将通过一个最基本的合同来学习关键概念。

我们将一起学习以下有关质.押.的构件。

1. 用Scaffold-Eth构建
   - 一起破解前端
   - 打造 Solidity “后端”
2. 将ETH从钱包转移到智能合约，反之亦然
3. 利用Solidity修改器

让我们从了解Scaffold-Eth的工作原理开始吧!

## 1. 下载Scaffold-Eth

在本教程中，我们将使用Scaffold-Eth开发者环境来制作我们的智能合约，并将我们的前端UI放在一起。

> 在开始之前，我想传达几个重要的细节，让大家牢记在心!
>
> Scaffold-Eth在抽象化环境设置和前端依赖方面非常棒，这使它成为一个强大的工具。
>
> 虽然有很多功能是由Scaffold-Eth自动处理的，但当你对整个开发者环境有了更扎实的掌握后，深入到代码中去了解其中一些功能是如何产生的，这一点很重要。

让我们从Fork  [SpeedRunEthereum](https://speedrunethereum.com/)的 [Challenge #1](https://speedrunethereum.com/challenge/decentralized-staking) 的基础代码库开始。

```http
git clone https://github.com/scaffold-eth/scaffold-eth-challenges.git challenge-1-decentralized-staking

cd challenge-1-decentralized-staking

git checkout challenge-1-decentralized-staking

yarn install
```

如果你已经成功跟上，你将能够在你的基础文件目录中出现一个名为`challenge-1-decentralized-staking`的新文件夹。

在运行上述命令后，我们留下了一个充满文件的大文件夹。

即使在继续学习代码之前，我们也应该熟悉Scaffold-Eth中关键文件的存储位置，这样我们就知道应该把重点放在哪里。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220823214629.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220823214643.png)

在本教程中，我们将主要在`Staker.sol`和`App.jsx`上工作。

## 2. 设置你的环境

接下来，你需要为以下三个命令建立三个独立的终端。

启动你的React前端。

```
yarn start
```

启动你的Hardhat后端。

```
yarn chain
```

编译、部署和发布你的package/contracts文件中的所有合同。

```
yarn deploy
```

> 每当你更新你的合同时，运行`yarn deploy --reset`来 "刷新 "Scaffold-Eth中的合同。

很好! 现在你应该可以在http://localhost:3000/，看到这个仓库的UI前台了。

## 3. 熟悉Scaffold-Eth

虽然我知道你很想开始写代码，但还有一些细节需要处理

在我们的默认视图中，我们有两个标签--`Staker UI` & `Debug Contracts`。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220823214655.png)

来吧，在你的前端页面上来回切换，看看不同的功能。

`Staker UI`包含了所有我们将主要与之互动的前端组件。

如果你点击所提供的按钮，你会发现大多数按钮还没有完全连接起来，你会立即遇到错误。

> 看一下`Staker UI`。你会注意到它是如此的简陋的! 这就是我们主要要改变的东西。

由于以太坊上的任何链上互动都需要testnet ETH，所以你需要本地testnet ETH来开始砍价。

首先，抓住你的本地主机钱包地址。

点击右上角的 "复制 "按钮。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220823214707.png)

接下来，前往左下角。你将能够在这里访问本地龙头。

- 要么在打开的字段中复制/粘贴你的地址，要么点击 "钱包 "图标
- 在扩展视图中粘贴您的地址
- 给自己发送一些测试ETH

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220823214732.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220823214739.png)

在为你的本地钱包充值后，你就可以与你的合约进行互动了

第二个标签，`Debug Contracts`，是另一个前端显示，它包含了Scaffold-Eth的一个超级大功能！在这个标签中，你可以看到你的合同。

一旦你`yarn deploy`你的合约，并配置它正确地读取合约数据，它将自动生成一个裸露的UI，允许你与你的合约功能互动。

例如，在下面的例子中，我们可以通过我们的智能合约读取和写入信息，只需放入参数并点击 "发送"。有了Scaffold-Eth，我们不需要只使用CLI命令，而是有一个更直观的原型设计方式。

> 如果你想在`Debug Contracts` 标签页中存储和查看一个变量，请确保将该变量设置为public!

棒极了!

现在我们已经熟悉了Scaffold-Eth，我们可以更深入地研究代码了。

## 4. 潜入Solidity

看一下我们的`Staker.sol`文件，我们发现我们有一个相当空的Solidity文件，里面有一堆注释，说明需要填写的内容。

由于教程偏离了Scaffold-Eth的 Challenge #1，我们可以忽略这些注释，从以下代码开始。

```solidity
pragma solidity >=0.6.0 <0.7.0;

import "hardhat/console.sol";
import "./ExampleExternalContract.sol";

contract Staker {
  ExampleExternalContract public exampleExternalContract;
  
  constructor(address exampleExternalContractAddress) public {
      exampleExternalContract = ExampleExternalContract(exampleExternalContractAddress);
  }
  
}
```

### 项目参数

在写出我们的智能合约代码之前，让我们来看看我们期望我们的质.押.DApp如何工作

1. 为了简单起见，我们只希望有一个用户与我们的质.押.DApp互动
2. 我们需要能够从Staker合约中存款和取款。
   - 质.押.是一个一次性的行为，这意味着一旦我们质.押.，就不能再重新质.押.。
   - 从合约中取款会移除整个本金余额和任何应计利息
3. Staker合约的利息支付率为每秒钟0.1个ETH，存入的ETH有资格获得利息累积。
4. 合同部署后，Staker合同应该从Hh2时间戳计数器开始。第一个期限应设置为2分钟，第二个期限设置为4分钟
   - 2分钟的期限决定了钉子户能够存入资金的时期。(在t=0分钟和t=2分钟之间，质.押.用户可以存款)
   - 从存入资金到2分钟期限之间发生的所有区块都是有效的应计利息。
   - 在2分钟的提款期限过后，质.押.用户可以提取全部本金余额和任何应计利息，直到4分钟的期限到来。
   - 在额外的2分钟提款窗口过后，用户被阻止提取他们的资金，因为他们已经超时了。
5. 如果质.押.用户还有资金，我们可以调用最后一个函数，将资金 "锁定 "在已经预装在Scaffold-Eth环境中的外部合同中，即ExampleExternalContract.sol。

> 虽然上面列出的质.押.参数可能看起来有点复杂，但许多现实生活中的质.押.dApps都有类似的基元，用户有一个有限的存款和提款期。
>
> 而且，许多DApps将抑制 "非生产性 "资本，这些资本在质.押.期结束后只是闲置在那里。
>
> 有时，DeFi协议甚至可能在等待期结束后吸收未付的存款，这与我们在教程中所说的最后一个参数类似。

### Solidity Mappingss

在我们的智能合约中，我们将需要两个映射来帮助我们存储一些数据。

特别是，我们需要一些东西来跟踪。

1. 有多少ETH被存入合约中
2. 存款发生的时间

我们可以通过以下方式实现这一目标。

```javascript
mapping(address => uint256) public balances; 
mapping(address => uint256) public depositTimestamps;
```

### 公共变量

根据上面列出的[准则](https://docs.alchemy.com/docs/how-to-build-a-staking-dapp#project-parameters)，我们还需要一些不同的变量。

```solidity
uint256 public constant rewardRatePerSecond = 0.1 ether; 
uint256 public withdrawalDeadline = block.timestamp + 120 seconds; 
uint256 public claimDeadline = block.timestamp + 240 seconds; 
uint256 public currentBlock = 0;
```

奖励率设定了质.押.本金的ETH的发放利率。

提款和索赔的最后期限帮助我们设定质.押.机制开始/结束的最后期限。

最后，我们有一个变量，用来保存当前区块。

> 我们使用block.timestamp + XXX seconds来创建最后期限，正好是我们的合约启动后的XXX秒。作为一种计时机制，这肯定有点 "天真"；你能想出更好的方法来实现这一点，例如，它更具有通用性？

### 事件

尽管我们不会将事件推送到我们的前端，但我们仍然应该确保我们在合同的关键部分发出事件，以确保我们保持最佳的编程实践。

```javascript
event Stake(address indexed sender, uint256 amount); 
event Received(address, uint); 
event Execute(address indexed sender, uint256 amount);
```

现在我们已经锁定了关键参数/变量，我们可以继续制作我们将在教程中使用的具体函数。

### 只读的时间函数

正如项目参数中所述，许多不同的质.押.DApp的功能都受制于 "时间锁"，在特定的时间点启用/禁止某些行动。

在这里，我们有两个不同的功能来管理提款窗口的开始和结束。

```javascript
  function withdrawalTimeLeft() public view returns (uint256 withdrawalTimeLeft) {
    if( block.timestamp >= withdrawalDeadline) {
      return (0);
    } else {
      return (withdrawalDeadline - block.timestamp);
    }
  }

  function claimPeriodLeft() public view returns (uint256 claimPeriodLeft) {
    if( block.timestamp >= claimDeadline) {
      return (0);
    } else {
      return (claimDeadline - block.timestamp);
    }
  }
```

这两个函数在设计上其实都非常熟悉。

它们都有一个标准的if -> else语句。

条件只是检查当前时间是否大于或小于[公共变量部分](https://docs.alchemy.com/docs/how-to-build-a-staking-dapp#public-variables)规定的最后期限。

如果当前时间大于预先安排的最后期限，我们就知道最后期限已过，并返回0以表示 "状态变化 "已经发生。

否则，我们只是返回在最后期限到来之前的剩余时间。

### 修改器

对于一个更深入的修改器的例子，请看 [Solidity By Example](https://solidity-by-example.org/function-modifier)。

简而言之，Solidity 修改器是可以在函数调用之前和/或之后运行的代码片段。

虽然它们有许多不同的用途，但最常见和最基本的用例之一是在特定条件未完全满足的情况下限制对某些功能的访问。

在本教程中，我们将精确地使用修改器来帮助对关键功能进行把关，这些功能决定了我们的入股、提款和返还功能。

下面是我们使用的三个修改器。

```javascript
  modifier withdrawalDeadlineReached( bool requireReached ) {
    uint256 timeRemaining = withdrawalTimeLeft();
    if( requireReached ) {
      require(timeRemaining == 0, "Withdrawal period is not reached yet");
    } else {
      require(timeRemaining > 0, "Withdrawal period has been reached");
    }
    _;
  }

  modifier claimDeadlineReached( bool requireReached ) {
    uint256 timeRemaining = claimPeriodLeft();
    if( requireReached ) {
      require(timeRemaining == 0, "Claim deadline is not reached yet");
    } else {
      require(timeRemaining > 0, "Claim deadline has been reached");
    }
    _;
  }

  modifier notCompleted() {
    bool completed = exampleExternalContract.completed();
    require(!completed, "Stake already completed!");
    _;
  }
```

修改器 `withdrawalDeadlineReached(bool requireReached) `&` claimDeadlineReached(bool requireReached) `都接受一个布尔参数，并检查以确保其各自的最后期限为真或假。

修改器`notCompleted()`的操作方式类似，但实际上它的性质更复杂一点，尽管它包含的代码行数更少。

它实际上是从Staker外部的合同中调用一个函数` completed()`，并检查它的返回值是真还是假，以确认该标志是否被切换。

现在，让我们在接下来的几个函数上实现我们刚刚创建的修改器，用闸门限制访问。

### 存款/质.押.功能

在我们的入金函数中，我们使用先前创建的修改器，将`withdrawingDeadlineReached()`中的参数设置为false，将`claimDeadlineReached()`设置为false，因为我们不希望这两个期限已经过去。

```javascript
  // Stake function for a user to stake ETH in our contract
  
  function stake() public payable withdrawalDeadlineReached(false) claimDeadlineReached(false) {
    balances[msg.sender] = balances[msg.sender] + msg.value;
    depositTimestamps[msg.sender] = block.timestamp;
    emit Stake(msg.sender, msg.value);
  }
```

该函数的其余部分在一个典型的 "存款 "场景中是相当标准的，我们的余额映射被更新以包括送入的资金。

我们还用存款的当前时间来设置我们的存款时间戳，这样我们就可以在以后的利息计算中访问这个存储值。

### 提款功能

在我们的取款函数中，我们再次使用先前创建的修改器，但这次我们希望`drawalDeadlineReached()`为真， `claimDeadlineReached()`为假。

这组修改器/参数意味着我们处于提款窗口的最佳位置，因为提款时间到了，不会有任何处罚，而且我们还能得到利息。

```solidity
  /*
  Withdraw function for a user to remove their staked ETH inclusive
  of both the principle balance and any accrued interest
  */
  
  function withdraw() public withdrawalDeadlineReached(true) claimDeadlineReached(false) notCompleted{
    require(balances[msg.sender] > 0, "You have no balance to withdraw!");
    uint256 individualBalance = balances[msg.sender];
    uint256 indBalanceRewards = individualBalance + ((block.timestamp-depositTimestamps[msg.sender])*rewardRatePerBlock);
    balances[msg.sender] = 0;

    // Transfer all ETH via call! (not transfer) cc: https://solidity-by-example.org/sending-ether
    (bool sent, bytes memory data) = msg.sender.call{value: indBalanceRewards}("");
    require(sent, "RIP; withdrawal failed :( ");
  }
```

该函数的其余部分做了几个重要步骤。

1. 它检查以确保试图提取ETH的人实际上有一个非零的股份。
2. 它通过计算从存款到取款的区块数，并乘以我们的利息常数，来计算欠下的ETH的利息金额。
3. 它将用户的余额质.押.ETH设为0，这样就不会发生重复计算。
4. 它将ETH从智能合约转移回用户的钱包。

### 执行返还功能

在这里，我们希望 `claimDeadlineReached() `为真，因为非生产性资金的返还只能在4分钟后发生。

同样地，我们希望`notCompleted`为真，因为这个DApp只设计为单一用途。

```solidity
  /*
  Allows any user to repatriate "unproductive" funds that are left in the staking contract
  past the defined withdrawal period
  */
  
  function execute() public claimDeadlineReached(true) notCompleted {
    uint256 contractBalance = address(this).balance;
    exampleExternalContract.complete{value: address(this).balance}();
  }
```

其余的功能。

1. 抓取Staker合约中的ETH的当前余额
2. 将ETH发送到repo的exampleExternalContract中。

如果你到目前为止一直跟着Solidity走，你的Staker.sol应该是下面这个样子。

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.4;

import "hardhat/console.sol";
import "./ExampleExternalContract.sol";

contract Staker {

  ExampleExternalContract public exampleExternalContract;

  mapping(address => uint256) public balances;
  mapping(address => uint256) public depositTimestamps;

  uint256 public constant rewardRatePerSecond = 0.1 ether;
  uint256 public withdrawalDeadline = block.timestamp + 120 seconds;
  uint256 public claimDeadline = block.timestamp + 240 seconds;
  uint256 public currentBlock = 0;

  // Events
  event Stake(address indexed sender, uint256 amount);
  event Received(address, uint);
  event Execute(address indexed sender, uint256 amount);

  // Modifiers
  /*
  Checks if the withdrawal period has been reached or not
  */
  modifier withdrawalDeadlineReached( bool requireReached ) {
    uint256 timeRemaining = withdrawalTimeLeft();
    if( requireReached ) {
      require(timeRemaining == 0, "Withdrawal period is not reached yet");
    } else {
      require(timeRemaining > 0, "Withdrawal period has been reached");
    }
    _;
  }

  /*
  Checks if the claim period has ended or not
  */
  modifier claimDeadlineReached( bool requireReached ) {
    uint256 timeRemaining = claimPeriodLeft();
    if( requireReached ) {
      require(timeRemaining == 0, "Claim deadline is not reached yet");
    } else {
      require(timeRemaining > 0, "Claim deadline has been reached");
    }
    _;
  }

  /*
  Requires that the contract only be completed once!
  */
  modifier notCompleted() {
    bool completed = exampleExternalContract.completed();
    require(!completed, "Stake already completed!");
    _;
  }

  constructor(address exampleExternalContractAddress){
      exampleExternalContract = ExampleExternalContract(exampleExternalContractAddress);
  }

  // Stake function for a user to stake ETH in our contract
  function stake() public payable withdrawalDeadlineReached(false) claimDeadlineReached(false){
    balances[msg.sender] = balances[msg.sender] + msg.value;
    depositTimestamps[msg.sender] = block.timestamp;
    emit Stake(msg.sender, msg.value);
  }

  /*
  Withdraw function for a user to remove their staked ETH inclusive
  of both principal and any accrued interest
  */
  function withdraw() public withdrawalDeadlineReached(true) claimDeadlineReached(false) notCompleted{
    require(balances[msg.sender] > 0, "You have no balance to withdraw!");
    uint256 individualBalance = balances[msg.sender];
    uint256 indBalanceRewards = individualBalance + ((block.timestamp-depositTimestamps[msg.sender])*rewardRatePerBlock);
    balances[msg.sender] = 0;

    // Transfer all ETH via call! (not transfer) cc: https://solidity-by-example.org/sending-ether
    (bool sent, bytes memory data) = msg.sender.call{value: indBalanceRewards}("");
    require(sent, "RIP; withdrawal failed :( ");
  }

  /*
  Allows any user to repatriate "unproductive" funds that are left in the staking contract
  past the defined withdrawal period
  */
  function execute() public claimDeadlineReached(true) notCompleted {
    uint256 contractBalance = address(this).balance;
    exampleExternalContract.complete{value: address(this).balance}();
  }

  /*
  READ-ONLY function to calculate the time remaining before the minimum staking period has passed
  */
  function withdrawalTimeLeft() public view returns (uint256 withdrawalTimeLeft) {
    if( block.timestamp >= withdrawalDeadline) {
      return (0);
    } else {
      return (withdrawalDeadline - block.timestamp);
    }
  }

  /*
  READ-ONLY function to calculate the time remaining before the minimum staking period has passed
  */
  function claimPeriodLeft() public view returns (uint256 claimPeriodLeft) {
    if( block.timestamp >= claimDeadline) {
      return (0);
    } else {
      return (claimDeadline - block.timestamp);
    }
  }

  /*
  Time to "kill-time" on our local testnet
  */
  function killTime() public {
    currentBlock = block.timestamp;
  }

  /*
  \Function for our smart contract to receive ETH
  cc: https://docs.soliditylang.org/en/latest/contracts.html#receive-ether-function
  */
  receive() external payable {
      emit Received(msg.sender, msg.value);
  }

}

```

## 5. 进入前台

太棒了! 我们刚刚经历了一堆Solidity。当涉及到前端显示时，Scaffold-Eth试图让事情变得简单而美好。它包含了很多不同的反应组件，为用户提供了低代码的解决方案，以实现令人敬畏的UI！我鼓励你玩玩这些组件。我鼓励你玩玩不同的组件，但与此同时，我们将在spartan方面学习更多。

看看我们的App.jsx文件，特别是在[链接573附近的代码块](https://github.com/scaffold-eth/scaffold-eth/blob/challenge-1-decentralized-staking/packages/react-app/src/App.jsx#L573)，我们看到一个代码块，用于从我们的Solidity合约中捕获发射的事件，并将其显示为一个列表。

有效地，它允许我们记录从我们的智能合约发射的不同行动，解析存储的信息，然后直观地允许dApp用户查看他们的链上历史。

虽然我们将通过在Solidity合约中发射事件来实践良好的编程标准，但这次，为了简单起见，我们将在前端忽略事件，所以让我们完全删除这个代码块。

如果你看一下你的Staker UI标签，你会发现事件框已经被抹去了。

### 前台编辑

在大多数情况下，前台的许多代码将保持与默认的相同 在前面的步骤中，我们已经删除了事件反应组件。

我们的最终目标是要有一个漂亮的、简单的用户界面，看起来像下面这样。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220823214950.png)

请注意，默认的前台缺少一些默认 repo 的视觉元素。

### 每秒奖励率

为了构建这部分内容，我们在Staker合同块下直接插入以下代码。

```javascript
  <div style={{ padding: 8, marginTop: 16 }}>
    <div>Reward Rate Per Second:</div>
    <Balance balance={rewardRatePerSecond} fontSize={64} /> ETH
  </div>
```

在这里，我们利用了Scaffold-Eth的平衡反应组件来帮助格式化!

### 最后期限的UI元素

为了构建截止日期视觉组件，我们使用了以下2段代码。

```javascript
  // ** keep track of a variable from the contract in the local React state:
  const claimPeriodLeft = useContractReader(readContracts, "Staker", "claimPeriodLeft");
  console.log("⏳ Claim Period Left:", claimPeriodLeft);

  const withdrawalTimeLeft = useContractReader(readContracts, "Staker", "withdrawalTimeLeft");
  console.log("⏳ Withdrawal Time Left:", withdrawalTimeLeft);
```



```html
<div style={{ padding: 8, marginTop: 16, fontWeight: "bold" }}>
  <div>Claim Period Left:</div>
  {claimPeriodLeft && humanizeDuration(claimPeriodLeft.toNumber() * 1000)}
</div>

<div style={{ padding: 8, marginTop: 16, fontWeight: "bold"}}>
  <div>Withdrawal Period Left:</div>
  {withdrawalTimeLeft && humanizeDuration(withdrawalTimeLeft.toNumber() * 1000)}
</div>
```



虽然第二个代码片断很熟悉，与我们已经看到的相似，但第一个代码块有点独特。不是说我们调用 `claimPeriodLeft `和` withdrawalTimeLeft `来访问前端的存储变量值。

然而，我们实际上必须先从智能合约中读取这些值本身。第一个代码片断处理了这个逻辑!

### 杂项:对其他现有的UI组件的编辑

现在你已经看到了2个不同的前台UI组件的例子，你能想出如何对前台进行其余的修改，使其看起来像上面提供的样本吗！？

你应该只需要在这里调整一些参数，所以不要想得太多了

### 对用户界面的自由发挥

虽然Scaffold-Eth有很多默认组件，使用户能够简单地利用 "低代码 "解决方案，但它也使用户能够访问更大的前端库。

默认情况下，它有一个与Ant Design react components（https://ant.design/components/overview/）的挂钩，允许任何人从那里提取组件

在我们的前端样本中，我们实际上看到了 "线 "来划分每个大块的视觉组件。

通过探索Ant Design中的不同选项，可以重现这些线条

> 如果你想得到提示，请看一下Ant Dividers!
>
> 不要忘记，我们需要在App.jsx文件的顶部导入我们计划使用的组件
>
> 
>`从 "antd "导入 { Alert, Button, Col, Menu, Row, List, Divider }。`

如果你已经跟上了前端的代码，你的App.jsx应该看起来像下面这样。

```react
import WalletConnectProvider from "@walletconnect/web3-provider";
//import Torus from "@toruslabs/torus-embed"
import WalletLink from "walletlink";
import { Alert, Button, Col, Menu, Row, List, Divider } from "antd";
import "antd/dist/antd.css";
import React, { useCallback, useEffect, useState } from "react";
import { BrowserRouter, Link, Route, Switch } from "react-router-dom";
import Web3Modal from "web3modal";
import "./App.css";
import { Account, Address, Balance, Contract, Faucet, GasGauge, Header, Ramp, ThemeSwitch } from "./components";
import { INFURA_ID, NETWORK, NETWORKS } from "./constants";
import { Transactor } from "./helpers";
import {
  useBalance,
  useContractLoader,
  useContractReader,
  useGasPrice,
  useOnBlock,
  useUserProviderAndSigner,
} from "eth-hooks";
import { useEventListener } from "eth-hooks/events/useEventListener";
import { useExchangeEthPrice } from "eth-hooks/dapps/dex";
// import Hints from "./Hints";
import { ExampleUI, Hints, Subgraph } from "./views";

import { useContractConfig } from "./hooks";
import Portis from "@portis/web3";
import Fortmatic from "fortmatic";
import Authereum from "authereum";
import humanizeDuration from "humanize-duration";

const { ethers } = require("ethers");
/*
    Welcome to 🏗 scaffold-eth !

    Code:
    https://github.com/austintgriffith/scaffold-eth

    Support:
    https://t.me/joinchat/KByvmRe5wkR-8F_zz6AjpA
    or DM @austingriffith on Twitter or Telegram

    You should get your own Infura.io ID and put it in `constants.js`
    (this is your connection to the main Ethereum network for ENS etc.)


    🌏 EXTERNAL CONTRACTS:
    You can also bring in contract artifacts in `constants.js`
    (and then use the `useExternalContractLoader()` hook!)
*/

/// 📡 What chain are your contracts deployed to?
const targetNetwork = NETWORKS.localhost; // <------- select your target frontend network (localhost, rinkeby, xdai, mainnet)

// 😬 Sorry for all the console logging
const DEBUG = true;
const NETWORKCHECK = true;

// 🛰 providers
if (DEBUG) console.log("📡 Connecting to Mainnet Ethereum");
// const mainnetProvider = getDefaultProvider("mainnet", { infura: INFURA_ID, etherscan: ETHERSCAN_KEY, quorum: 1 });
// const mainnetProvider = new InfuraProvider("mainnet",INFURA_ID);
//
// attempt to connect to our own scaffold eth rpc and if that fails fall back to infura...
// Using StaticJsonRpcProvider as the chainId won't change see https://github.com/ethers-io/ethers.js/issues/901
const scaffoldEthProvider = navigator.onLine
  ? new ethers.providers.StaticJsonRpcProvider("https://rpc.scaffoldeth.io:48544")
  : null;
const poktMainnetProvider = navigator.onLine
  ? new ethers.providers.StaticJsonRpcProvider(
      "https://eth-mainnet.gateway.pokt.network/v1/lb/611156b4a585a20035148406",
    )
  : null;
const mainnetInfura = navigator.onLine
  ? new ethers.providers.StaticJsonRpcProvider("https://mainnet.infura.io/v3/" + INFURA_ID)
  : null;
// ( ⚠️ Getting "failed to meet quorum" errors? Check your INFURA_ID

// 🏠 Your local provider is usually pointed at your local blockchain
const localProviderUrl = targetNetwork.rpcUrl;
// as you deploy to other networks you can set REACT_APP_PROVIDER=https://dai.poa.network in packages/react-app/.env
const localProviderUrlFromEnv = process.env.REACT_APP_PROVIDER ? process.env.REACT_APP_PROVIDER : localProviderUrl;
if (DEBUG) console.log("🏠 Connecting to provider:", localProviderUrlFromEnv);
const localProvider = new ethers.providers.StaticJsonRpcProvider(localProviderUrlFromEnv);

// 🔭 block explorer URL
const blockExplorer = targetNetwork.blockExplorer;

// Coinbase walletLink init
const walletLink = new WalletLink({
  appName: "coinbase",
});

// WalletLink provider
const walletLinkProvider = walletLink.makeWeb3Provider(`https://mainnet.infura.io/v3/${INFURA_ID}`, 1);

// Portis ID: 6255fb2b-58c8-433b-a2c9-62098c05ddc9
/*
  Web3 modal helps us "connect" external wallets:
*/
const web3Modal = new Web3Modal({
  network: "mainnet", // Optional. If using WalletConnect on xDai, change network to "xdai" and add RPC info below for xDai chain.
  cacheProvider: true, // optional
  theme: "light", // optional. Change to "dark" for a dark theme.
  providerOptions: {
    walletconnect: {
      package: WalletConnectProvider, // required
      options: {
        bridge: "https://polygon.bridge.walletconnect.org",
        infuraId: INFURA_ID,
        rpc: {
          1: `https://mainnet.infura.io/v3/${INFURA_ID}`, // mainnet // For more WalletConnect providers: https://docs.walletconnect.org/quick-start/dapps/web3-provider#required
          42: `https://kovan.infura.io/v3/${INFURA_ID}`,
          100: "https://dai.poa.network", // xDai
        },
      },
    },
    portis: {
      display: {
        logo: "https://user-images.githubusercontent.com/9419140/128913641-d025bc0c-e059-42de-a57b-422f196867ce.png",
        name: "Portis",
        description: "Connect to Portis App",
      },
      package: Portis,
      options: {
        id: "6255fb2b-58c8-433b-a2c9-62098c05ddc9",
      },
    },
    fortmatic: {
      package: Fortmatic, // required
      options: {
        key: "pk_live_5A7C91B2FC585A17", // required
      },
    },
    // torus: {
    //   package: Torus,
    //   options: {
    //     networkParams: {
    //       host: "https://localhost:8545", // optional
    //       chainId: 1337, // optional
    //       networkId: 1337 // optional
    //     },
    //     config: {
    //       buildEnv: "development" // optional
    //     },
    //   },
    // },
    "custom-walletlink": {
      display: {
        logo: "https://play-lh.googleusercontent.com/PjoJoG27miSglVBXoXrxBSLveV6e3EeBPpNY55aiUUBM9Q1RCETKCOqdOkX2ZydqVf0",
        name: "Coinbase",
        description: "Connect to Coinbase Wallet (not Coinbase App)",
      },
      package: walletLinkProvider,
      connector: async (provider, _options) => {
        await provider.enable();
        return provider;
      },
    },
    authereum: {
      package: Authereum, // required
    },
  },
});

function App(props) {
  const mainnetProvider =
    poktMainnetProvider && poktMainnetProvider._isProvider
      ? poktMainnetProvider
      : scaffoldEthProvider && scaffoldEthProvider._network
      ? scaffoldEthProvider
      : mainnetInfura;

  const [injectedProvider, setInjectedProvider] = useState();
  const [address, setAddress] = useState();

  const logoutOfWeb3Modal = async () => {
    await web3Modal.clearCachedProvider();
    if (injectedProvider && injectedProvider.provider && typeof injectedProvider.provider.disconnect == "function") {
      await injectedProvider.provider.disconnect();
    }
    setTimeout(() => {
      window.location.reload();
    }, 1);
  };

  /* 💵 This hook will get the price of ETH from 🦄 Uniswap: */
  const price = useExchangeEthPrice(targetNetwork, mainnetProvider);

  /* 🔥 This hook will get the price of Gas from ⛽️ EtherGasStation */
  const gasPrice = useGasPrice(targetNetwork, "fast");
  // Use your injected provider from 🦊 Metamask or if you don't have it then instantly generate a 🔥 burner wallet.
  const userProviderAndSigner = useUserProviderAndSigner(injectedProvider, localProvider);
  const userSigner = userProviderAndSigner.signer;

  useEffect(() => {
    async function getAddress() {
      if (userSigner) {
        const newAddress = await userSigner.getAddress();
        setAddress(newAddress);
      }
    }
    getAddress();
  }, [userSigner]);

  // You can warn the user if you would like them to be on a specific network
  const localChainId = localProvider && localProvider._network && localProvider._network.chainId;
  const selectedChainId =
    userSigner && userSigner.provider && userSigner.provider._network && userSigner.provider._network.chainId;

  // For more hooks, check out 🔗eth-hooks at: https://www.npmjs.com/package/eth-hooks

  // The transactor wraps transactions and provides notificiations
  const tx = Transactor(userSigner, gasPrice);

  // Faucet Tx can be used to send funds from the faucet
  const faucetTx = Transactor(localProvider, gasPrice);

  // 🏗 scaffold-eth is full of handy hooks like this one to get your balance:
  const yourLocalBalance = useBalance(localProvider, address);

  // Just plug in different 🛰 providers to get your balance on different chains:
  const yourMainnetBalance = useBalance(mainnetProvider, address);

  const contractConfig = useContractConfig();

  // Load in your local 📝 contract and read a value from it:
  const readContracts = useContractLoader(localProvider, contractConfig);

  // If you want to make 🔐 write transactions to your contracts, use the userSigner:
  const writeContracts = useContractLoader(userSigner, contractConfig, localChainId);

  // EXTERNAL CONTRACT EXAMPLE:
  //
  // If you want to bring in the mainnet DAI contract it would look like:
  const mainnetContracts = useContractLoader(mainnetProvider, contractConfig);

  // If you want to call a function on a new block
  useOnBlock(mainnetProvider, () => {
    console.log(`⛓ A new mainnet block is here: ${mainnetProvider._lastBlockNumber}`);
  });

  // Then read your DAI balance like:
  const myMainnetDAIBalance = useContractReader(mainnetContracts, "DAI", "balanceOf", [
    "0x34aA3F359A9D614239015126635CE7732c18fDF3",
  ]);

  //keep track of contract balance to know how much has been staked total:
  const stakerContractBalance = useBalance(
    localProvider,
    readContracts && readContracts.Staker ? readContracts.Staker.address : null,
  );
  if (DEBUG) console.log("💵 stakerContractBalance", stakerContractBalance);

  const rewardRatePerSecond = useContractReader(readContracts, "Staker", "rewardRatePerSecond");
  console.log("💵 Reward Rate:", rewardRatePerSecond);

  // ** keep track of a variable from the contract in the local React state:
  const balanceStaked = useContractReader(readContracts, "Staker", "balances", [address]);
  console.log("💸 balanceStaked:", balanceStaked);

  // ** 📟 Listen for broadcast events
  const stakeEvents = useEventListener(readContracts, "Staker", "Stake", localProvider, 1);
  console.log("📟 stake events:", stakeEvents);

  const receiveEvents = useEventListener(readContracts, "Staker", "Received", localProvider, 1);
  console.log("📟 receive events:", receiveEvents);

  // ** keep track of a variable from the contract in the local React state:
  const claimPeriodLeft = useContractReader(readContracts, "Staker", "claimPeriodLeft");
  console.log("⏳ Claim Period Left:", claimPeriodLeft);

  const withdrawalTimeLeft = useContractReader(readContracts, "Staker", "withdrawalTimeLeft");
  console.log("⏳ Withdrawal Time Left:", withdrawalTimeLeft);


  // ** Listen for when the contract has been 'completed'
  const complete = useContractReader(readContracts, "ExampleExternalContract", "completed");
  console.log("✅ complete:", complete);

  const exampleExternalContractBalance = useBalance(
    localProvider,
    readContracts && readContracts.ExampleExternalContract ? readContracts.ExampleExternalContract.address : null,
  );
  if (DEBUG) console.log("💵 exampleExternalContractBalance", exampleExternalContractBalance);

  let completeDisplay = "";
  if (complete) {
    completeDisplay = (
      <div style={{padding: 64, backgroundColor: "#eeffef", fontWeight: "bold", color: "rgba(0, 0, 0, 0.85)" }} >
        -- 💀 Staking App Fund Repatriation Executed 🪦 --
        <Balance balance={exampleExternalContractBalance} fontSize={32} /> ETH locked!
      </div>
    );
  }

  /*
  const addressFromENS = useResolveName(mainnetProvider, "austingriffith.eth");
  console.log("🏷 Resolved austingriffith.eth as:", addressFromENS)
  */

  //
  // 🧫 DEBUG 👨🏻‍🔬
  //
  useEffect(() => {
    if (
      DEBUG &&
      mainnetProvider &&
      address &&
      selectedChainId &&
      yourLocalBalance &&
      yourMainnetBalance &&
      readContracts &&
      writeContracts &&
      mainnetContracts
    ) {
      console.log("_____________________________________ 🏗 scaffold-eth _____________________________________");
      console.log("🌎 mainnetProvider", mainnetProvider);
      console.log("🏠 localChainId", localChainId);
      console.log("👩‍💼 selected address:", address);
      console.log("🕵🏻‍♂️ selectedChainId:", selectedChainId);
      console.log("💵 yourLocalBalance", yourLocalBalance ? ethers.utils.formatEther(yourLocalBalance) : "...");
      console.log("💵 yourMainnetBalance", yourMainnetBalance ? ethers.utils.formatEther(yourMainnetBalance) : "...");
      console.log("📝 readContracts", readContracts);
      console.log("🌍 DAI contract on mainnet:", mainnetContracts);
      console.log("💵 yourMainnetDAIBalance", myMainnetDAIBalance);
      console.log("🔐 writeContracts", writeContracts);
    }
  }, [
    mainnetProvider,
    address,
    selectedChainId,
    yourLocalBalance,
    yourMainnetBalance,
    readContracts,
    writeContracts,
    mainnetContracts,
  ]);

  let networkDisplay = "";
  if (NETWORKCHECK && localChainId && selectedChainId && localChainId !== selectedChainId) {
    const networkSelected = NETWORK(selectedChainId);
    const networkLocal = NETWORK(localChainId);
    if (selectedChainId === 1337 && localChainId === 31337) {
      networkDisplay = (
        <div style={{ zIndex: 2, position: "absolute", right: 0, top: 60, padding: 16 }}>
          <Alert
            message="⚠️ Wrong Network ID"
            description={
              <div>
                You have <b>chain id 1337</b> for localhost and you need to change it to <b>31337</b> to work with
                HardHat.
                <div>(MetaMask -&gt; Settings -&gt; Networks -&gt; Chain ID -&gt; 31337)</div>
              </div>
            }
            type="error"
            closable={false}
          />
        </div>
      );
    } else {
      networkDisplay = (
        <div style={{ zIndex: 2, position: "absolute", right: 0, top: 60, padding: 16 }}>
          <Alert
            message="⚠️ Wrong Network"
            description={
              <div>
                You have <b>{networkSelected && networkSelected.name}</b> selected and you need to be on{" "}
                <Button
                  onClick={async () => {
                    const ethereum = window.ethereum;
                    const data = [
                      {
                        chainId: "0x" + targetNetwork.chainId.toString(16),
                        chainName: targetNetwork.name,
                        nativeCurrency: targetNetwork.nativeCurrency,
                        rpcUrls: [targetNetwork.rpcUrl],
                        blockExplorerUrls: [targetNetwork.blockExplorer],
                      },
                    ];
                    console.log("data", data);

                    let switchTx;
                    // https://docs.metamask.io/guide/rpc-api.html#other-rpc-methods
                    try {
                      switchTx = await ethereum.request({
                        method: "wallet_switchEthereumChain",
                        params: [{ chainId: data[0].chainId }],
                      });
                    } catch (switchError) {
                      // not checking specific error code, because maybe we're not using MetaMask
                      try {
                        switchTx = await ethereum.request({
                          method: "wallet_addEthereumChain",
                          params: data,
                        });
                      } catch (addError) {
                        // handle "add" error
                      }
                    }

                    if (switchTx) {
                      console.log(switchTx);
                    }
                  }}
                >
                  <b>{networkLocal && networkLocal.name}</b>
                </Button>
              </div>
            }
            type="error"
            closable={false}
          />
        </div>
      );
    }
  } else {
    networkDisplay = (
      <div style={{ zIndex: -1, position: "absolute", right: 154, top: 28, padding: 16, color: targetNetwork.color }}>
        {targetNetwork.name}
      </div>
    );
  }

  const loadWeb3Modal = useCallback(async () => {
    const provider = await web3Modal.connect();
    setInjectedProvider(new ethers.providers.Web3Provider(provider));

    provider.on("chainChanged", chainId => {
      console.log(`chain changed to ${chainId}! updating providers`);
      setInjectedProvider(new ethers.providers.Web3Provider(provider));
    });

    provider.on("accountsChanged", () => {
      console.log(`account changed!`);
      setInjectedProvider(new ethers.providers.Web3Provider(provider));
    });

    // Subscribe to session disconnection
    provider.on("disconnect", (code, reason) => {
      console.log(code, reason);
      logoutOfWeb3Modal();
    });
  }, [setInjectedProvider]);

  useEffect(() => {
    if (web3Modal.cachedProvider) {
      loadWeb3Modal();
    }
  }, [loadWeb3Modal]);

  const [route, setRoute] = useState();
  useEffect(() => {
    setRoute(window.location.pathname);
  }, [setRoute]);

  let faucetHint = "";
  const faucetAvailable = localProvider && localProvider.connection && targetNetwork.name.indexOf("local") !== -1;

  const [faucetClicked, setFaucetClicked] = useState(false);
  if (
    !faucetClicked &&
    localProvider &&
    localProvider._network &&
    localProvider._network.chainId === 31337 &&
    yourLocalBalance &&
    ethers.utils.formatEther(yourLocalBalance) <= 0
  ) {
    faucetHint = (
      <div style={{ padding: 16 }}>
        <Button
          type="primary"
          onClick={() => {
            faucetTx({
              to: address,
              value: ethers.utils.parseEther("0.01"),
            });
            setFaucetClicked(true);
          }}
        >
          💰 Grab funds from the faucet ⛽️
        </Button>
      </div>
    );
  }

  return (
    <div className="App">
      {/* ✏️ Edit the header and change the title to your project name */}
      <Header />
      {networkDisplay}
      <BrowserRouter>
        <Menu style={{ textAlign: "center" }} selectedKeys={[route]} mode="horizontal">
          <Menu.Item key="/">
            <Link
              onClick={() => {
                setRoute("/");
              }}
              to="/"
            >
              Staker UI
            </Link>
          </Menu.Item>
          <Menu.Item key="/contracts">
            <Link
              onClick={() => {
                setRoute("/contracts");
              }}
              to="/contracts"
            >
              Debug Contracts
            </Link>
          </Menu.Item>
        </Menu>

        <Switch>
          <Route exact path="/">
            {completeDisplay}

            <div style={{ padding: 8, marginTop: 16 }}>
              <div>Staker Contract:</div>
              <Address value={readContracts && readContracts.Staker && readContracts.Staker.address} />
            </div>

            <Divider />

            <div style={{ padding: 8, marginTop: 16 }}>
              <div>Reward Rate Per Second:</div>
              <Balance balance={rewardRatePerSecond} fontSize={64} /> ETH
            </div>

            <Divider />

            <div style={{ padding: 8, marginTop: 16, fontWeight: "bold" }}>
              <div>Claim Period Left:</div>
              {claimPeriodLeft && humanizeDuration(claimPeriodLeft.toNumber() * 1000)}
            </div>

            <div style={{ padding: 8, marginTop: 16, fontWeight: "bold"}}>
              <div>Withdrawal Period Left:</div>
              {withdrawalTimeLeft && humanizeDuration(withdrawalTimeLeft.toNumber() * 1000)}
            </div>

            <Divider />

            <div style={{ padding: 8, fontWeight: "bold"}}>
              <div>Total Available ETH in Contract:</div>
              <Balance balance={stakerContractBalance} fontSize={64} />
            </div>

            <Divider />

            <div style={{ padding: 8,fontWeight: "bold" }}>
              <div>ETH Locked 🔒 in Staker Contract:</div>
              <Balance balance={balanceStaked} fontSize={64} />
            </div>

            <div style={{ padding: 8 }}>
              <Button
                type={"default"}
                onClick={() => {
                  tx(writeContracts.Staker.execute());
                }}
              >
                📡 Execute!
              </Button>
            </div>

            <div style={{ padding: 8 }}>
              <Button
                type={"default"}
                onClick={() => {
                  tx(writeContracts.Staker.withdraw());
                }}
              >
                🏧 Withdraw
              </Button>
            </div>

            <div style={{ padding: 8 }}>
              <Button
                type={balanceStaked ? "success" : "primary"}
                onClick={() => {
                  tx(writeContracts.Staker.stake({ value: ethers.utils.parseEther("0.5") }));
                }}
              >
                🥩 Stake 0.5 ether!
              </Button>
            </div>

            {/*
                🎛 this scaffolding is full of commonly used components
                this <Contract/> component will automatically parse your ABI
                and give you a form to interact with it locally
            */}

            {/* uncomment for a second contract:
            <Contract
              name="SecondContract"
              signer={userProvider.getSigner()}
              provider={localProvider}
              address={address}
              blockExplorer={blockExplorer}
              contractConfig={contractConfig}
            />
            */}
          </Route>
          <Route path="/contracts">
            <Contract
              name="Staker"
              signer={userSigner}
              provider={localProvider}
              address={address}
              blockExplorer={blockExplorer}
              contractConfig={contractConfig}
            />
            <Contract
              name="ExampleExternalContract"
              signer={userSigner}
              provider={localProvider}
              address={address}
              blockExplorer={blockExplorer}
              contractConfig={contractConfig}
            />
          </Route>
        </Switch>
      </BrowserRouter>

      <ThemeSwitch />

      {/* 👨‍💼 Your account is in the top right with a wallet at connect options */}
      <div style={{ position: "fixed", textAlign: "right", right: 0, top: 0, padding: 10 }}>
        <Account
          address={address}
          localProvider={localProvider}
          userSigner={userSigner}
          mainnetProvider={mainnetProvider}
          price={price}
          web3Modal={web3Modal}
          loadWeb3Modal={loadWeb3Modal}
          logoutOfWeb3Modal={logoutOfWeb3Modal}
          blockExplorer={blockExplorer}
        />
        {faucetHint}
      </div>

      <div style={{ marginTop: 32, opacity: 0.5 }}>
        {/* Add your address here */}
        Created by <Address value={"Your...address"} ensProvider={mainnetProvider} fontSize={16} />
      </div>

      <div style={{ marginTop: 32, opacity: 0.5 }}>
        <a target="_blank" style={{ padding: 32, color: "#000" }} href="https://github.com/scaffold-eth/scaffold-eth">
          🍴 Fork me!
        </a>
      </div>

      {/* 🗺 Extra UI like gas price, eth price, faucet, and support: */}
      <div style={{ position: "fixed", textAlign: "left", left: 0, bottom: 20, padding: 10 }}>
        <Row align="middle" gutter={[4, 4]}>
          <Col span={8}>
            <Ramp price={price} address={address} networks={NETWORKS} />
          </Col>

          <Col span={8} style={{ textAlign: "center", opacity: 0.8 }}>
            <GasGauge gasPrice={gasPrice} />
          </Col>
          <Col span={8} style={{ textAlign: "center", opacity: 1 }}>
            <Button
              onClick={() => {
                window.open("https://t.me/joinchat/KByvmRe5wkR-8F_zz6AjpA");
              }}
              size="large"
              shape="round"
            >
              <span style={{ marginRight: 8 }} role="img" aria-label="support">
                💬
              </span>
              Support
            </Button>
          </Col>
        </Row>

        <Row align="middle" gutter={[4, 4]}>
          <Col span={24}>
            {
              /*  if the local provider has a signer, let's show the faucet:  */
              faucetAvailable ? (
                <Faucet localProvider={localProvider} price={price} ensProvider={mainnetProvider} />
              ) : (
                ""
              )
            }
          </Col>
        </Row>
      </div>
    </div>
  );
}

export default App;

```



真棒！我们已经做了很多工作。

在开发者环境、Solidity和前端代码方面，我们已经一起完成了很多新的组件。

验证您的dApp的功能是否符合预期!

1. dApp是否具有单次使用押金的功能？
2. 提款/资金返还条件是否得到尊重？继续点击`yarn deploy --reset`几次，检查每个时间窗口。

## 挑战时间!

好了，现在到了最精彩的部分。我将留给你一些扩展挑战，让你自己去尝试，看看你是否完全理解了你在这里学到的东西!

1. 更新`Staker.sol`合约中的利息机制，这样你就可以根据存款和提款之间的区块获得 "非线性 "的ETH数额

> 我建议实现一个基本的指数函数!

2. 允许用户向智能合约存入任意数量的ETH，而不仅仅是0.5ETH。

3. 不要使用vanilla `ExampleExternalContract`合约，在Staker.sol中实现一个函数，允许你取回锁定在`ExampleExternalContract`中的ETH，并将其重新存入Staker合约中。

- 请确保只有 "白名单 "中的一个地址可以调用这个新函数，以控制其使用。
- 确保你创建了逻辑/删除了现有的代码，以确保用户能够一次又一次地与Staker合同互动 我们希望能够反复地从`Staker` ->` ExampleExternalContract`进行ping-pong。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
