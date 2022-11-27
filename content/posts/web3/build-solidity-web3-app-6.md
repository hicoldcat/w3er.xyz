---
title: Web3教程：使用 Solidity 构建 Web3 应用程序（6）
description: null
author: 李留白
weight: 0
date: 2022-11-27T12:51:07.069Z
lastmod: 2022-11-27T13:27:33.308Z
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

## 优化 UI 并部署

### 随机选择获胜者并防止垃圾邮件发送者

#### 😈 随机抽取优胜者-

所以现在，我们的代码设置为每次给 waver 0.0001 ETH！我们的合约会很快用完钱，然后快乐就结束了，我们需要向合约中添加更多资金。在本课中，我将向您介绍如何：

1. **随机抽取**一名获胜者。

2. 创建一个**冷却时间**机制，以防止人们向您发送垃圾邮件以试图赢得奖品或惹恼您。 

让我们先做随机获胜者！

因此，在智能合约中生成随机数是众所周知的**难题**。

为什么呢？好吧，想一想随机数是如何正常生成的。当你在程序中正常生成一个随机数时，它会从你的电脑中获取一堆不同的数字作为随机性的来源，比如：风扇的速度、CPU的温度、你在3点52分买电脑后按下 "L "的次数、你的网速，以及大量其他你难以控制的数字。它把所有这些 "随机 "的数字放到一个算法中，生成一个它认为是真正 "随机 "数字的最佳尝试的数字。有意义吗？

在区块链上，几乎没有随机性的来源。合同看到的一切，公众都会看到。正因为如此，有人可以通过查看智能合约，看到它所依赖的随机性数字，然后这个人可以给它提供他们需要的确切数字来赢得游戏。

让我们看看下面的代码:)。

```solidity
// SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.0;

import "hardhat/console.sol";

contract WavePortal {
    uint256 totalWaves;

    /*
     * We will be using this below to help generate a random number
     */
    uint256 private seed;

    event NewWave(address indexed from, uint256 timestamp, string message);

    struct Wave {
        address waver;
        string message;
        uint256 timestamp;
    }

    Wave[] waves;

    constructor() payable {
        console.log("We have been constructed!");
        /*
         * Set the initial seed
         */
        seed = (block.timestamp + block.difficulty) % 100;
    }

    function wave(string memory _message) public {
        totalWaves += 1;
        console.log("%s has waved!", msg.sender);

        waves.push(Wave(msg.sender, _message, block.timestamp));

        /*
         * Generate a new seed for the next user that sends a wave
         */
        seed = (block.difficulty + block.timestamp + seed) % 100;

        console.log("Random # generated: %d", seed);

        /*
         * Give a 50% chance that the user wins the prize.
         */
        if (seed < 50) {
            console.log("%s won!", msg.sender);

            /*
             * The same code we had before to send the prize.
             */
            uint256 prizeAmount = 0.0001 ether;
            require(
                prizeAmount <= address(this).balance,
                "Trying to withdraw more money than the contract has."
            );
            (bool success, ) = (msg.sender).call{value: prizeAmount}("");
            require(success, "Failed to withdraw money from contract.");
        }

        emit NewWave(msg.sender, block.timestamp, _message);
    }

    function getAllWaves() public view returns (Wave[] memory) {
        return waves;
    }

    function getTotalWaves() public view returns (uint256) {
        return totalWaves;
    }
}
```

在这里，我采用 Solidity 给我的两个数字，`block.difficulty`和`block.timestamp`并将它们组合起来创建一个随机数。`block.difficulty`根据区块中的交易告诉矿工该区块的开采难度。由于多种原因，区块变得更难，但是，主要是当区块中有更多交易时它们变得更难（一些矿工更喜欢更容易的区块，但是，这些区块的报酬更少）。`block.timestamp`只是正在处理该块的 Unix 时间戳。

这些是非常随机的。但是，从技术上讲，`block.difficulty`和`block.timestamp`两者都可以由老练的攻击者控制。 

为了增加难度，我创建了一个变量`seed`，每次用户发送新的 wave 时都会发生变化。因此，我结合了所有这三个变量来生成一个新的随机种子。然后我只需`% 100`，以确保将数字降低到 0 - 99 之间的范围内。

而已！然后我就写一个简单的 if 语句，看看种子是否小于等于 50，如果是——那么摇摆者就中奖了！所以，这意味着自从我们写了 之后，挥手者有 50% 的机会获胜`seed < 50`。您可以将其更改为您想要的任何内容:)。我刚刚达到了 50%，因为这样更容易测试！！

重要的是要在这里看到，如果攻击者真的愿意，他们可以在技术上对您的系统进行游戏。这真的很难。还有其他方法可以在区块链上生成随机数，但 Solidity 本身并不能为我们提供任何可靠的东西，因为它不能！我们的合约可以访问的，所有都是公开的，*从来都不*是真正随机的。

真的，这是区块链的优势之一。但是，对于像我们这里这样的某些应用程序来说可能有点烦人！

无论如何，没有人会攻击我们的小应用程序，但我希望您在构建拥有数百万用户的 dApp 时了解所有这些！

#### 测试一下

让我们确保它能正常工作! 这是我更新的`run.js`。在这种情况下，我只是想确保在挥手的人赢了的情况下，合同的余额会发生变化!

```javascript
const main = async () => {
  const waveContractFactory = await hre.ethers.getContractFactory("WavePortal");
  const waveContract = await waveContractFactory.deploy({
    value: hre.ethers.utils.parseEther("0.1"),
  });
  await waveContract.deployed();
  console.log("Contract addy:", waveContract.address);

  let contractBalance = await hre.ethers.provider.getBalance(
    waveContract.address
  );
  console.log(
    "Contract balance:",
    hre.ethers.utils.formatEther(contractBalance)
  );

  /*
   * Let's try two waves now
   */
  const waveTxn = await waveContract.wave("This is wave #1");
  await waveTxn.wait();

  const waveTxn2 = await waveContract.wave("This is wave #2");
  await waveTxn2.wait();

  contractBalance = await hre.ethers.provider.getBalance(waveContract.address);
  console.log(
    "Contract balance:",
    hre.ethers.utils.formatEther(contractBalance)
  );

  let allWaves = await waveContract.getAllWaves();
  console.log(allWaves);
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

您不会总是有像这样的好教程来指导您如何测试您的代码。由您决定 

1) 您想要测试什么
2)   如何测试它。

在这种情况下，我知道我想确保合约余额仅在生成小于 50 的随机数的情况下减少 0.0001！

所以，当我运行上面的代码时，我得到的是：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221127205432.png)

轰！轰！轰 它是有效的。当 "79 "被生成时，用户没有赢得奖品。但是，当 "23 "被生成时，摇摆人赢了！而且，合同余额正好减少了0.0001。而且，合同余额正好减少了0.0001。很好:)。

#### 防止垃圾邮件发送者的冷却时间

真棒。你现在有办法随机向人们发送ETH了! 现在，在你的网站上添加一个冷却功能可能是有用的，这样人们就不能随便向你发送垃圾波。为什么？好吧，也许你只是不希望他们一直试图通过向你挥手来赢得奖品，一次又一次。或者，也许你不希望只有他们的信息填满你的信息墙。

查看代码。我在添加新行的地方添加了注释。

我使用一种称为[map](https://medium.com/upstate-interactive/mappings-in-solidity-explained-in-under-two-minutes-ecba88aff96e?utm_source=buildspace.so&utm_medium=buildspace_project)的特殊数据结构。

```solidity
// SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.17;

import "hardhat/console.sol";

contract WavePortal {
    uint256 totalWaves;
    uint256 private seed;

    event NewWave(address indexed from, uint256 timestamp, string message);

    struct Wave {
        address waver;
        string message;
        uint256 timestamp;
    }

    Wave[] waves;

    /*
     * This is an address => uint mapping, meaning I can associate an address with a number!
     * In this case, I'll be storing the address with the last time the user waved at us.
     */
    mapping(address => uint256) public lastWavedAt;

    constructor() payable {
        console.log("We have been constructed!");
        /*
         * Set the initial seed
         */
        seed = (block.timestamp + block.difficulty) % 100;
    }

    function wave(string memory _message) public {
        /*
         * We need to make sure the current timestamp is at least 15-minutes bigger than the last timestamp we stored
         */
        require(
            lastWavedAt[msg.sender] + 15 minutes < block.timestamp,
            "Wait 15m"
        );

        /*
         * Update the current timestamp we have for the user
         */
        lastWavedAt[msg.sender] = block.timestamp;

        totalWaves += 1;
        console.log("%s has waved!", msg.sender);

        waves.push(Wave(msg.sender, _message, block.timestamp));

        /*
         * Generate a new seed for the next user that sends a wave
         */
        seed = (block.difficulty + block.timestamp + seed) % 100;

        if (seed <= 50) {
            console.log("%s won!", msg.sender);

            uint256 prizeAmount = 0.0001 ether;
            require(
                prizeAmount <= address(this).balance,
                "Trying to withdraw more money than they contract has."
            );
            (bool success, ) = (msg.sender).call{value: prizeAmount}("");
            require(success, "Failed to withdraw money from contract.");
        }

        emit NewWave(msg.sender, block.timestamp, _message);
    }

    function getAllWaves() public view returns (Wave[] memory) {
        return waves;
    }

    function getTotalWaves() public view returns (uint256) {
        return totalWaves;
    }
}
```

试着运行`npx hardhat run scripts/run.js`，如果您现在尝试连续挥手两次而不等待 15 分钟，请尝试运行并查看您收到的错误消息:)。

砰！这就是你建立冷却时间的方式！

### 完成并庆祝！

#### 🎨 完成你的 UI，让它成为你自己的

您已经掌握了所有核心功能！现在，如果您还没有，是时候真正把它变成您自己的了。更改 CSS、文本，添加一些有趣的 YouTube 嵌入，添加您自己的简介，等等。让东西看起来很酷 :)。

**如果你愿意，花 30 分钟在这上面！！我强烈推荐它！**

顺便说一句，在我们进行测试时——您可能希望将合约的冷却时间计时器更改为 30 秒，而不是像这样的 15 分钟：

```
require(lastWavedAt[msg.sender] + 30 seconds < block.timestamp, "Must wait 30 seconds before waving again.");
```

为什么？好吧，当您测试每 15 分钟只能挥动一次时，这可能会很烦人！ 

所以，我把我的改为 30 秒！

当您部署 **最终**合约时，可以将其设置为您想要的任何值！

#### ⛽️ 设置气体限制

当您现在尝试“挥手”时，您可能会注意到有时会出现类似于“out of gas"”的错误。为什么？

好吧，基本上 Metamask 会尝试估计一笔交易将使用多少 gas。但是，有时它是错误的！在这种情况下，由于我们涉及一些随机性，因此变得更加困难。因此，如果合约发送奖品，那么挥手者需要支付更多的气体，因为我们正在运行**更多的**代码。

估计 gas 是一个难题，一个简单的解决方法（这样我们的用户不会在交易失败时生气）设置一个限制。

在 `App.js `上，我把发送wave的那一行改成了

```solidity
wavePortalContract.wave(message, { gasLimit: 300000 })
```

这样做的目的是让用户支付 300,000 的固定气体量。如果他们在交易中没有用完，他们将自动获得退款。

因此，如果一笔交易花费了 250,000 gas，那么 *在*交易完成后，用户未使用的剩余 50,000 gas 将被退还:)。

#### 🔍 验证交易

当您的合约已经部署并且您正在使用您的 UI 和您的钱包对其进行测试时，一开始可能很难确定您的钱包账户是否已成功获得奖品。您的账户将用完一定数量的 gas，并可能获得 ETH 奖励。那么你如何验证你的合约是否按预期工作呢？

为了验证，您可以在[Goerli Etherscan](https://goerli.etherscan.io/?utm_source=buildspace.so&utm_medium=buildspace_project)上打开您的合约地址并查看已发生的交易。你会发现这里有各种有用的信息，包括被调用的方法，在这个例子中是Wave。如果你点击进入一个Wave交易，你会注意到在To属性中，它将确定合同地址被调用。如果用户赢得了奖金，你会注意到在该领域，合同已经从合同地址转移了0.0001 ETH到你的账户地址。

请注意，交易的价值仍然是0 ETH，因为用户从未支付任何费用来启动wave。从智能合约内部转移ETH被称为 "内部交易"，你可以通过切换Etherscan上的标签来查看它们。

#### 🎤 事件

还记得我们在智能合约中如何使用下面那条神奇的线吗？我告诉过你要谷歌一下Solidity中的事件是如何工作的。如果你还没有的话，请现在就去做吧！

```solidity
emit NewWave(msg.sender, block.timestamp, _message);
```

在基本层面上，事件是我们的智能合约发出的消息，我们可以在客户端实时捕获这些消息。

假设我正在浏览您的网站，我只是打开它。当我这样做时，你的另一个朋友杰里米向你挥手。现在，我看到 Jeremy 的挥手的唯一方式是刷新我的页面。这似乎很糟糕。如果我知道合同已更新并让我的 UI 神奇地更新，那不是很酷吗？

即使是现在，当我们自己提交一条消息，然后我们必须等待它被挖掘然后刷新页面才能看到所有更新的消息列表时，这还是有点烦人，对吧？让我们解决这个问题。

请看我的代码，我在`App.js`中更新了`getAllWaves`：

```javascript
const getAllWaves = async () => {
  const { ethereum } = window;

  try {
    if (ethereum) {
      const provider = new ethers.providers.Web3Provider(ethereum);
      const signer = provider.getSigner();
      const wavePortalContract = new ethers.Contract(contractAddress, contractABI, signer);
      const waves = await wavePortalContract.getAllWaves();

      const wavesCleaned = waves.map(wave => {
        return {
          address: wave.waver,
          timestamp: new Date(wave.timestamp * 1000),
          message: wave.message,
        };
      });

      setAllWaves(wavesCleaned);
    } else {
      console.log("Ethereum object doesn't exist!");
    }
  } catch (error) {
    console.log(error);
  }
};

/**
 * Listen in for emitter events!
 */
useEffect(() => {
  let wavePortalContract;

  const onNewWave = (from, timestamp, message) => {
    console.log("NewWave", from, timestamp, message);
    setAllWaves(prevState => [
      ...prevState,
      {
        address: from,
        timestamp: new Date(timestamp * 1000),
        message: message,
      },
    ]);
  };

  if (window.ethereum) {
    const provider = new ethers.providers.Web3Provider(window.ethereum);
    const signer = provider.getSigner();

    wavePortalContract = new ethers.Contract(contractAddress, contractABI, signer);
    wavePortalContract.on("NewWave", onNewWave);
  }

  return () => {
    if (wavePortalContract) {
      wavePortalContract.off("NewWave", onNewWave);
    }
  };
}, []);
```

在最底部，您会看到我添加的神奇代码:)。在这里，当我的合约抛出`NewWave`事件时，我实际上可以“监听”。就像一个 webhook :)。很酷，对吧？

我还可以访问该事件的数据，例如`message`和`from`。在这里，我在收到此事件时执行`setAllWaves`此操作，这意味着当我们收到该事件时，用户的消息将自动附加到我的`allWaves`数组中，并且我们的 UI 将更新！

这是很强大的。它可以让我们创建实时更新的网络应用:)。想想看，如果你在区块链上做一个类似于Uber或Twitter的东西，实时更新的网络应用就变得非常重要了。

我希望你能解决这个问题并构建你想要的任何东西 :)。

#### 🙉 关于 github 的注释

**如果上传到 Github，请不要将带有私钥的 hardhat 配置文件上传到你的 repo。你会被抢走的。**

我为此使用 dotenv。

```bash
npm install --save dotenv
```

你的 hardhat.config.js 文件看起来像这样：

```javascript
require("@nomiclabs/hardhat-waffle");
require("dotenv").config();

module.exports = {
  solidity: "0.8.0",
  networks: {
    goerli: {
      url: process.env.STAGING_QUICKNODE_KEY,
      accounts: [process.env.PRIVATE_KEY],
    },
    mainnet: {
      chainId: 1,
      url: process.env.PROD_QUICKNODE_KEY,
      accounts: [process.env.PRIVATE_KEY],
    },
  },
};
```

你的 .env 文件看起来像这样：

```
STAGING_QUICKNODE_KEY=BLAHBLAH
PROD_QUICKNODE_KEY=BLAHBLAH
PRIVATE_KEY=BLAHBLAH
```

 请务必在您的 .gitignore 中包含 .env。

#### 🎉完成

你已经做到了。您已经部署了一个智能合约，并且您已经编写了一个与之对话的 Web 应用程序。随着我们走向去中心化网络应用程序变得更加普遍的现实，这两项技能将进一步改变世界。 

我希望这是对 web3 的一个有趣的介绍，我希望你继续你的旅程。

我会在 Discord 中向大家发布有关新项目的消息 :)。

## 🎁总结

你做到了。全场掌声👏！想查看我们为本节编写的所有代码吗？单击[此链接](https://gist.github.com/adilanchian/93fbd2e06b3b5d3acb99b5723cebd925?utm_source=buildspace.so&utm_medium=buildspace_project)查看全部！

```
原文地址：https://buildspace.so/p/build-solidity-web3-app
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)

