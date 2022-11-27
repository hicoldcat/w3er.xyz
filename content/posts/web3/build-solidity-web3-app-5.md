---
title: Web3教程：使用 Solidity 构建 Web3 应用程序（5）
description: null
author: 李留白
weight: 0
date: 2022-11-27T12:15:38.062Z
lastmod: 2022-11-27T12:16:03.896Z
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

## 与合约互动

### 将来自用户的消息存储在区块链上

#### 📦 使用结构将消息存储在数组中

所以，我们现在有了一个可以与区块链对话的成熟网络应用程序！

现在，如果你还记得的话，我们希望我们的最终应用程序成为人们可以向我们挥手并向我们发送消息的地方。我们还想显示我们收到的所有过去的 waves/消息。这就是我们将在本课中做的事情！

所以在课程结束时我们想要：

1. 让用户与他们的wave一起提交信息。

2. 将该数据以某种方式保存在区块链上。

3. 在我们的网站上显示该数据，这样任何人都可以看到所有向我们挥手的人和他们的信息。

查看我更新的智能合约代码。我在这里添加了很多注释，以帮助您了解发生了什么变化:)。

```solidity
// SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.17;

import "hardhat/console.sol";

contract WavePortal {
    uint256 totalWaves;

    /*
     * A little magic, Google what events are in Solidity!
     */
    event NewWave(address indexed from, uint256 timestamp, string message);

    /*
     * I created a struct here named Wave.
     * A struct is basically a custom datatype where we can customize what we want to hold inside it.
     */
    struct Wave {
        address waver; // The address of the user who waved.
        string message; // The message the user sent.
        uint256 timestamp; // The timestamp when the user waved.
    }

    /*
     * I declare a variable waves that lets me store an array of structs.
     * This is what lets me hold all the waves anyone ever sends to me!
     */
    Wave[] waves;

    constructor() {
        console.log("I AM SMART CONTRACT. POG.");
    }

    /*
     * You'll notice I changed the wave function a little here as well and
     * now it requires a string called _message. This is the message our user
     * sends us from the frontend!
     */
    function wave(string memory _message) public {
        totalWaves += 1;
        console.log("%s waved w/ message %s", msg.sender, _message);

        /*
         * This is where I actually store the wave data in the array.
         */
        waves.push(Wave(msg.sender, _message, block.timestamp));

        /*
         * I added some fanciness here, Google it and try to figure out what it is!
         * Let me know what you learn in #general-chill-chat
         */
        emit NewWave(msg.sender, block.timestamp, _message);
    }

    /*
     * I added a function getAllWaves which will return the struct array, waves, to us.
     * This will make it easy to retrieve the waves from our website!
     */
    function getAllWaves() public view returns (Wave[] memory) {
        return waves;
    }

    function getTotalWaves() public view returns (uint256) {
        // Optional: Add this line if you want to see the contract print the value!
        // We'll also print it over in run.js as well.
        console.log("We have %d total waves!", totalWaves);
        return totalWaves;
    }
}
```

#### 🧐 测试一下

每当我们改变我们的合同，我们要改变run.js来测试我们增加的新功能。这样我们才能知道它是如何工作的。这是我的合同现在的样子。

这是我更新的`run.js`。 

```javascript
const main = async () => {
  const waveContractFactory = await hre.ethers.getContractFactory("WavePortal");
  const waveContract = await waveContractFactory.deploy();
  await waveContract.deployed();
  console.log("Contract addy:", waveContract.address);

  let waveCount;
  waveCount = await waveContract.getTotalWaves();
  console.log(waveCount.toNumber());

  /**
   * Let's send a few waves!
   */
  let waveTxn = await waveContract.wave("A message!");
  await waveTxn.wait(); // Wait for the transaction to be mined

  const [_, randomPerson] = await hre.ethers.getSigners();
  waveTxn = await waveContract.connect(randomPerson).wave("Another message!");
  await waveTxn.wait(); // Wait for the transaction to be mined

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

这是我在终端使用`npx hardhat run scripts/run.js`运行时得到的东西。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221127202325.png)

非常棒，对吧:)？ 

数组看起来有点吓人，但我们可以看到单词 `waver`, `message`和 `timestamp` 旁边的数据!!它正确地存储了我们的消息`"A message"`和`"Another message"`:)。

注意：“timestamp”作为“BigNumber”类型返回给我们。稍后我们将学习如何使用它，但只要知道这里没有任何问题！

看起来一切正常，让我们转到我们的**前端**，这样我们就可以在我们的网站上看到我们所有的 waves 了！

#### ✈️重新部署

所以，现在我们已经更新了我们的合约，我们需要做一些事情：

1. 我们需要重新部署一下。

2. 我们需要在前端更新合约地址。

3. 我们需要更新前端的 abi 文件。 

**人们在更改合同时经常忘记执行这 3 个步骤。别忘了哈哈。**

为什么我们需要做这一切？好吧，这是因为智能合约是 **不可变的。**他们无法改变。它们是永久性的。这意味着更改合同需要完全重新部署。这也将**重置**所有变量，因为它会被视为一个全新的合同。**这意味着如果我们想更新合约的代码，我们将丢失所有 wave 数据。**

**奖励**：在#general-chill-chat 中，有人可以在这里告诉我一些解决方案吗？我们还可以在哪里存储我们的 wave 数据，我们可以在哪里更新我们的合约代码并保留我们的原始数据？这里有很多解决方案，让我知道您找到了什么！

所以你现在需要做的是：

1. 使用`npx hardhat run scripts/deploy.js --network goerli`再次部署

2. 把`App.js`中的`contractAddress`改成我们在终端中从上面的步骤中得到的新的合同地址，就像我们之前第一次部署时那样

3. 像以前一样从`artifacts`中获取更新的 abi 文件，然后像以前一样将其复制粘贴到 Replit 中。如果您忘记了如何执行此操作，请务必在重温该课程

**同样——每次更改合同代码时都需要这样做。**

#### 🔌 与我们的客户联系起来

所以，这是我添加到App.js的新函数:

```javascript
const [currentAccount, setCurrentAccount] = useState("");
  /*
   * All state property to store all waves
   */
  const [allWaves, setAllWaves] = useState([]);
  const contractAddress = "0xd5f08a0ae197482FA808cE84E00E97d940dBD26E";
  
   /*
   * Create a variable here that references the abi content!
   */
  const contractABI = abi.abi;

  /*
   * Create a method that gets all waves from your contract
   */
  const getAllWaves = async () => {
    try {
      const { ethereum } = window;
      if (ethereum) {
        const provider = new ethers.providers.Web3Provider(ethereum);
        const signer = provider.getSigner();
        const wavePortalContract = new ethers.Contract(contractAddress, contractABI, signer);

        /*
         * Call the getAllWaves method from your Smart Contract
         */
        const waves = await wavePortalContract.getAllWaves();


        /*
         * We only need address, timestamp, and message in our UI so let's
         * pick those out
         */
        let wavesCleaned = [];
        waves.forEach(wave => {
          wavesCleaned.push({
            address: wave.waver,
            timestamp: new Date(wave.timestamp * 1000),
            message: wave.message
          });
        });

        /*
         * Store our data in React State
         */
        setAllWaves(wavesCleaned);
      } else {
        console.log("Ethereum object doesn't exist!")
      }
    } catch (error) {
      console.log(error);
    }
  }
```

非常简单，与我们之前处理的工作非常相似，我们如何连接到提供者、获取签名者和连接到合同！我在这里做了一个小魔术，遍历了我们所有的 waves 并将它们很好地保存在一个数组中，我们可以稍后使用。如果您遇到问题，请随时访问 console.log`waves`以查看您得到了什么。

但是，我们在哪里调用这个全新的`getAllWaves()`功能呢？好吧——当我们确定用户有一个连接的钱包和一个授权账户时，我们想调用它，因为我们需要一个授权账户来调用它！提示：你必须在`checkIfWalletIsConnected()`某个地方调用这个函数. 我让你自己去想吧。记住，当我们确定我们有一个连接的+授权的账户时，我们要调用这个函数。

我做的最后一件事是更新我们的 HTML 代码以呈现数据供我们查看！

```javascript
return (
    <div className="mainContainer">
      <div className="dataContainer">
        <div className="header">
          👋 Hey there!
        </div>

        <div className="bio">
          I am farza and I worked on self-driving cars so that's pretty cool right? Connect your Ethereum wallet and wave at me!
        </div>

        <button className="waveButton" onClick={wave}>
          Wave at Me
        </button>

        {!currentAccount && (
          <button className="waveButton" onClick={connectWallet}>
            Connect Wallet
          </button>
        )}

        {allWaves.map((wave, index) => {
          return (
            <div key={index} style={{ backgroundColor: "OldLace", marginTop: "16px", padding: "8px" }}>
              <div>Address: {wave.address}</div>
              <div>Time: {wave.timestamp.toString()}</div>
              <div>Message: {wave.message}</div>
            </div>)
        })}
      </div>
    </div>
  );
```

基本上，我只是通过`allWaves`，为每个波浪创建新的 div，然后在屏幕上显示该数据。

#### 🙀啊！`wave()`被破坏了

所以，在`App.js`中，我们的`wave()`功能不再起作用了！如果我们尝试挥动它，它会给我们一个错误，因为它期望现在发送一条消息！现在，您可以通过对消息进行硬编码来解决此问题，例如：

```
const waveTxn = await wavePortalContract.wave("this is a message")
```

我会把这个留给你：弄清楚如何添加一个文本框，让用户添加他们自己的自定义消息，他们可以发送到 wave 函数 :)。

目标是什么？你想让你的用户能够使用一个他们可以输入的文本框给你发送一条自定义信息！这就是你的目标。或者，也许你想让他们向你发送一个备忘录的链接？或者一个Spotify链接？这由你决定！

### 基金合约，设置奖品，发送用户以太币

#### 💸 将 ETH 发送给向你wave的人

现在我们要做的是向向我们招手的人发送一些ETH! 例如，也许你想让人们有1%的机会通过向你挥手赢得5美元。或者，你想让每个向你挥手的人得到0.01美元的ETH，因为他们向你挥手。

您甚至可以在其中手动将 ETH 发送给您最喜欢的人的消息。也许他们给你发了一首很棒的歌！！

**轻松将 ETH 发送给用户是智能合约的核心部分，也是智能合约最酷的部分之一**，所以，让我们开始吧！

首先，我们只是要给每个向我们招手的人0.0001个ETH。也就是0.31美元:)。而这一切都发生在testnet上，所以，这是假的$!

查看我在 `WavePortal.sol`上更新的`wave`功能。

```solidity
function wave(string memory _message) public {
    totalWaves += 1;
    console.log("%s has waved!", msg.sender);

    waves.push(Wave(msg.sender, _message, block.timestamp));

    emit NewWave(msg.sender, block.timestamp, _message);

    uint256 prizeAmount = 0.0001 ether;
    require(
        prizeAmount <= address(this).balance,
        "Trying to withdraw more money than the contract has."
    );
    (bool success, ) = (msg.sender).call{value: prizeAmount}("");
    require(success, "Failed to withdraw money from contract.");
}
```

这太棒了。

用我刚刚发起的奖金数额`prizeAmount`。Solidity 实际上让我们使用关键字`ether`，以我们可以很容易地表示货币金额。很方便:)!

我们也有一些新的关键字。您会看到`require`，它基本上会检查某些条件是否为真。如果不正确，它将退出函数并取消交易。这就像一个花哨的 if 语句！

在这种情况下，它正在检查 是否`prizeAmount <= address(this).balance`。这里`address(this).balance`是**合约本身的余额。**

为什么？**好吧，为了让我们将 ETH 发送给某人，我们的合约需要有 ETH。**

这是如何工作的，当我们第一次部署合约时，我们“资助”它 :)。到目前为止，我们**从未**资助过我们的合同！！它的价值一直是 0 ETH。这意味着我们的合约无法向人们发送 ETH，因为它**根本没有**！我们将在下一节介绍资金！

最酷的是

```solidity
require(prizeAmount <= address(this).balance, "Trying to withdraw more money than the contract has.");
```

是它让我们确定*合约的余额*大于*奖金数额，*如果是，我们可以继续发放奖金！如果不是`require`这样，交易就会终止，并且会说，“哟，这个合同甚至不能给你发奖金！”。 

`(msg.sender).call{value: prizeAmount}("")`是我们汇款的神奇线路 :)。语法有点奇怪！注意我们是如何通过它`prizeAmount`的！

`require(success`是我们知道交易成功的地方:)。如果不是，它会将交易标记为错误并说`"Failed to withdraw money from contract."`.

非常棒，对吧:)？

#### 🏦 为合约注资，这样我们就可以发送 ETH！

我们现在已经设置了发送 ETH 的代码。好的！现在我们需要真正确保我们的合约有资金，否则，我们没有 ETH 可以发送！

我们将首先在`run.js`. 请记住，`run.js`这就像我们的测试场，我们希望在部署之前确保我们的合约核心功能正常工作。同时 调试合约代码和前端代码**真的很难**，所以，我们把它分开了！

让我们前往`run.js`并进行一些更改以确保一切正常。这是我的更新`run.js`。

```javascript
const main = async () => {
  const waveContractFactory = await hre.ethers.getContractFactory("WavePortal");
  const waveContract = await waveContractFactory.deploy({
    value: hre.ethers.utils.parseEther("0.1"),
  });
  await waveContract.deployed();
  console.log("Contract addy:", waveContract.address);

  /*
   * Get Contract balance
   */
  let contractBalance = await hre.ethers.provider.getBalance(
    waveContract.address
  );
  console.log(
    "Contract balance:",
    hre.ethers.utils.formatEther(contractBalance)
  );

  /*
   * Send Wave
   */
  let waveTxn = await waveContract.wave("A message!");
  await waveTxn.wait();

  /*
   * Get Contract balance to see what happened!
   */
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

神奇的是`hre.ethers.utils.parseEther("0.1"),`。这就是我说的，“去部署我的合约并用 0.1 ETH 为其提供资金”。这将从我的钱包中取出 ETH，并用它为合约提供资金。 **就是这样**。

然后我执行`hre.ethers.utils.formatEther(contractBalance)`测试一下我的合约是否真的有 0.1 的余额。我使用ethers在这里给我的一个叫做getBalance的函数，并把我的合约地址传给它。

但是，我们还想看看当我们调用时`wave`是否从合约中正确删除了 0.0001 ETH！！这就是为什么我在调用后再次打印余额`wave`。

当我们运行

```bash
npx hardhat run scripts/run.js
```

你会看到我们遇到了一些错误！

它会说类似的话

```bash
Error: non-payable constructor cannot override value
```

这就是说，我们的合同现在不允许付钱给别人！这是个快速的解决方案，我们需要将关键字`payable`添加到我们的`WavePortal.sol`构造函数中。一探究竟：

```solidity
constructor() payable {
  console.log("We have been constructed!");
}
```

现在，当我运行

```bash
npx hardhat run scripts/run.js
```

这就是我得到的：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221127204710.png)

我们刚刚从合约中发送了一些 ETH，大获成功！而且，我们知道我们成功了，因为合约余额从 0.1 减少了 0.0001 ETH 到 0.0999！

#### ✈️更新部署脚本以资助合约

我们需要对`deploy.js`做小小的更新：

```javascript
const main = async () => {
  const waveContractFactory = await hre.ethers.getContractFactory("WavePortal");
  const waveContract = await waveContractFactory.deploy({
    value: hre.ethers.utils.parseEther("0.001"),
  });

  await waveContract.deployed();

  console.log("WavePortal address: ", waveContract.address);
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

我所做的就是像这样为合约注资 0.001 ETH：

```javascript
const waveContract = await waveContractFactory.deploy({
    value: hre.ethers.utils.parseEther("0.001"),
});
```

我喜欢先用少量 ETH 部署到测试网进行测试！

 而且我还添加了`await waveContract.deployed()`，以便我很容易知道它何时部署！

简单的！

让我们用同样的老方法来部署我们的合约吧

```bash
npx hardhat run scripts/deploy.js --network goerli
```

现在，当您转到[Etherscan](https://goerli.etherscan.io/?utm_source=buildspace.so&utm_medium=buildspace_project)并粘贴您的合约地址时，您会看到您的合约现在有 0.001 ETH 的价值！成功！

**请记住使用新的合约地址和\*新的 ABI 文件更新您的前端。否则，它将被破坏**。

测试你的wave 函数并确保它仍然有效！

## 🎁总结

使用实际的ETH来为你的合约提供燃料是有道理的吧？查看[此链接](https://gist.github.com/adilanchian/236fe9f3a56b73751060800cae3a780d?utm_source=buildspace.so&utm_medium=buildspace_project)以查看本节中编写的所有代码！


```
未完待续...

原文地址：https://buildspace.so/p/build-solidity-web3-app
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)

