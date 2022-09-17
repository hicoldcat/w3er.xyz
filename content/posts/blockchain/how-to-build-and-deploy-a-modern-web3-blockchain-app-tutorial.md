---
title: 手把手分步教程——构建和部署一个有趣和现代的搭讪词收集Web3区块链dApp
description: null
author: 李留白
weight: 0
date: 2022-09-17T01:13:33.804Z
lastmod: 2022-09-17T01:57:56.894Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220917091345.png
---

在本 Web3 教程中，我们将构建一个功能齐全的区块链web3应用程序。我们将从构建区块链应用程序的基础开始，最后，我们将上线我们的web3应用程序供所有人使用。

## 我们要建造什么？

我们将构建一个名为[🧀 Pick Up Lines的去](https://www.pickuplines.art/)**中心化应用程序（dApp）**。顾名思义，我们的用户将能够发送一些好的老式搭讪台词，并有机会赢得以太坊作为奖励

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/zarlX1XZp.gif)

## 先决条件

- **React**初级到中级知识
- 对**Solidity**智能合约有一定了解
- **对区块链编程**的基本了解

## 我们需要的工具

工作变成了使用正确的工具，对吧？幸运的是，web3 有很多工具可以用来实现臭名昭著的**WAGMI** 🧘

1. Visual Studio Code 或任何文本编辑器
2. 用于以太坊开发的安全帽
3. Metamask 作为加密钱包
4. Vercel 和 Alchemy 作为托管平台

## 让我们开始！

现在，我们对最终的应用程序和我们将要使用的工具有了一些了解，让我们开始编写代码吧！

首先，我们将编写区块链应用程序的**智能合约**。然后，我们将构建我们的**React 应用程序**，最后，将这两个东西连接起来以拥有一个成熟的 web3 应用程序。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/sCg_8VCxs.gif)

## 用 Solidity 编写我们的智能合约：

### 1. 运行我们的本地以太坊网络

我们需要启动一个本地以太坊网络。本地以太坊网络是专门在您的本地机器上运行的区块链网络。我们将在构建应用程序时使用它进行测试，因为它为我们提供了所有区块链功能，而无需使用*真正的*加密货币。

在这个 web3 教程中，我们将使用[Hardhat](https://hardhat.org/)。由于我们需要在启动之前在本地测试我们的区块链应用程序，因此我们将使用假 ETH 和假测试帐户通过 Hardhat 测试我们的智能合约。最重要的是，它将有助于在我们的本地区块链上编译我们的智能合约。

现在，前往终端并移动到您想要的目录。在那里，运行以下命令：

```shell
mkdir pickup-lines
cd pickup-lines
npm init -y
npm install --save-dev hardhat

```

接下来，让我们运行一个示例项目：

```shell
/*To create our Hardhat project*/
npx hardhat
```

运行项目：

```shell
/*To compile our contracts*/
npx hardhat compile
```

测试项目：

```shell
/*To run our tests*/
npx hardhat test

```

> 上面的代码设置了一个准系统 Hardhat 项目。没有插件，它允许您创建自己的任务、编译您的 Solidity 代码并运行您的测试。基本上，您正在本地环境中创建区块链网络

你会看到类似这样的东西：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220917092720.png)

### 2. 创建我们的智能合约

现在，让我们在`Contracts`目录下创建一个`PickupLines.sol`文件。

> 我们需要遵循严格的文件夹结构。这非常重要，因为我们是在 Hardhat 之上构建的，我们的`/contracts` , `/scripts`和`/test` 的默认路径是预定义的。不遵循此结构将导致我们的Hardhat任务失败。要小心！

```solidity
/*Use a license depending on your project.*/
// SPDX-License-Identifier: UNLICENSED

/*Code is written for Solidity version 0.4.16, or a newer version*/
pragma solidity ^0.8.0;

/*Built-in Hardhat interactive JavaScript console*/
import "hardhat/console.sol";

/*Main Solidity Contract*/
contract PickupLines {
   /*Constructor function for our contract*/
    constructor() {
        console.log("I am the Cheesy PickUp Lines' smart contract.");
    }
}
```

### 3. 在本地运行我们的合约

现在，让我们创建一个脚本来运行我们刚刚构建的智能合约。这将使我们能够在我们的本地区块链上对其进行测试。

进入`scripts`文件夹并创建一个`run.js`文件. 在`run.js`文件中，输入以下代码：

```js
/*The `main` function to run contract locally for an instance.*/
const main = async () => {
  /*Helper function to get the contract `PickupLines`*/
  const contracts = await hre.ethers.getContractFactory("PickupLines"); 

  /*Deploying the contract for an 'instance'*/
  const contract = await contracts.deploy();
  await contract.deployed();

  /*Address of the deployed contract.*/
  console.log("Contract deployed to:", contract.address);
};

/*A try-catch block for our `main` function*/
const runMain = async () => {
  try {
    await main();
    process.exit(0); // exit Node process without error
  } catch (error) {
    console.log(error);
    process.exit(1); // exit Node process while indicating 'Uncaught Fatal Exception' error
  }
};

/*Running the `runMain` function.*/
runMain();

```

让我们运行我们刚刚从终端创建的`run.js`文件：

```js
/*To run the `run.js` file.*/
npx hardhat run scripts/run.js
```

您可以看到我们在`constructor()`方法中输入的`console.log`信息。在那里，你也会看到合约地址

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220917093226.png)

### 4. 完成智能合约逻辑

现在，让我们的合同更丰富一点。

我们希望能够让某人向我们发送搭讪，然后将该搭讪存储在区块链中。所以，我们需要的第一件事是一个函数，这样任何人都可以给我们发送搭讪。

在`Contracts` 文件夹下的`PickupLines.sol`文件中，输入以下代码：

```solidity
contract PickUpLines {
    /*Solidity event, that fires when a new line is submitted.*/
    event NewPickUpLine(address indexed from, uint256 timestamp, string line);

    /*Data members*/
    uint256 private seed; /*Seed data*/
    uint256 totalLines; /*Total lines data*/
    mapping(address => bool) hasWrote; /*Map of all addresses with a line submitted*/

    /*A composite data member for a pick up line*/
    struct PickUpLine {
        address writer;
        string line;
        uint256 timestamp;
    }
    /*Array of all pick up lines submitted.*/
    PickUpLine[] pickuplines;

    constructor() payable {
      console.log("I am the Cheesy PickUp Lines' smart contract!");
    }

    /*Function for adding a new line to the contract.*/
    function newLine(string memory _line) public {

        /*Adding a new Pickup Line to our blockchain.*/
        totalLines += 1;
        pickuplines.push(PickUpLine(msg.sender, _line, block.timestamp));
        hasWrote[msg.sender] = true;
        emit NewPickUpLine(msg.sender, block.timestamp, _line);
    }

    /*Function to get all the lines submitted to the contract.*/
    function getTotalLines() public view returns (uint256) {
        console.log("We have %s total PickUpLines.", totalLines);
        return totalLines;
    }
}
```

所以，这就是用 Solidity 编写函数的方式。

我们还添加了一个自动初始化为 0 的变量`totalLines`。这个变量之所以特殊，是因为它被称为*状态变量*，它是一个特殊的变量，因为它永久存储在我们的合约存储中。

### 5. 部署智能合约

现在，我们将从本地区块链升级到全球可访问的区块链。

请按照以下 4 个步骤操作：

#### 1. 让我们在`scripts`文件夹内创建一个名为的`deploy.js`文件。在我们刚刚创建的`deploy.js`文件中输入下面给出的代码。

```js
   /*The `main` function to deploy contract locally*/
   const main = async () => {
   /*Getting deployer's address.*/
  const [deployer] = await hre.ethers.getSigners(); 

  /*Getting deployer's ETH balance*/
  const accountBalance = await deployer.getBalance();

  /*Logging the Deployer's address and the balance.*/
  console.log("Deploying contracts with account: ", deployer.address);
  console.log("Account balance: ", accountBalance.toString());

  /*Deploying the contract.*/
  const contracts = await hre.ethers.getContractFactory("PickupLines");
  const contract = await contracts.deploy();
  await contract.deployed();

  /*Logging the address of the deployed contract.*/
  console.log("PickupLines address: ", contract.address);
};

/*A try-catch block for our `main` function*/
const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.log(error);
    process.exit(1);
  }
};

/*Running the `runMain` function.*/
runMain();

```

#### 2. 我们将使用一个名为**Alchemy**的平台。[在此注册](https://www.alchemy.com/)。

我们将使用 Alchemy 在测试网上部署我们的*合约*。这是因为如果我们使用*以太坊主网*，那么我们应用程序上的每个**操作/交易**都将具有真正的货币价值。在我们的应用程序完全开发供公众使用之前，我们不想这样做。目前，我们只是在测试我们的应用程序。

通过测试网，我们将能够享受区块链的所有功能，尽管使用的是*假*加密货币。[你可以在这里](https://rinkebyfaucet.com/)得到一些假的**ETH**.

#### 3. 这是部署的最后一部分。通过输入以下代码对`hardhat.config.js`文件进行更改：

```js
 //Find YOUR_ALCHEMY_API_URL in the alchemy dashboard.
require("@nomiclabs/hardhat-waffle");

module.exports = {
  solidity: "0.8.0",
  networks: {
    rinkeby: {
      url: "YOUR_ALCHEMY_API_URL",
      accounts: ["YOUR_WALLET_ACCOUNT_KEY"]
    },
  },
};

```

> **注意：访问您的私钥可以通过打开[MetaMask](https://www.metamask.io/)，将网络更改为“Rinkeby 测试网络”，然后单击三个点并选择“帐户详细信息”>“导出私钥”来完成。**

#### 4. 现在，我们要部署合约。我们可以通过移动到终端并运行以下命令来做到这一点：

```shell
npx hardhat run scripts/deploy.js --network rinkeby
```

它奏效了吗？激动。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220917093753.png)

我们部署了合约，并且我们在区块链上也有它的地址。请记下某处的地址，因为我们的网站将需要此地址，以便它知道在区块链上查找您的合约的位置。

## 在 React 中构建 Web 应用程序

### 1. 使用 Metamask 设置一个基本的 React 应用程序

是时候开始开发我们的网络应用了。我们的合同很简单。现在，让我们弄清楚前端应用程序如何与我们的智能合约进行交互。

> **注意：我们已经为您构建了一个入门工具包！这是存储库的[链接。](https://github.com/niiischall/pickup-lines-starter-kit)您可以克隆存储库并开始工作。**
>
> 这篇博文的目的是让您习惯区块链开发。在本节中，我们不会深入探讨前端开发。

### 2. 将钱包连接到我们的应用程序

接下来，我们需要一个以太坊钱包。有很多可用的，但对于这个项目，我们将使用**Metamask**。[下载其浏览器扩展并在此处](https://metamask.io/)设置您的钱包。

在`src`文件夹内的`App.tsx`文件中，输入以下代码：

```tsx
import React, { useEffect, useState }  from "react";
import { ethers } from "ethers";
import './App.css';

/*This will be present on the terminal once you deploy the contract.*/
const contractAddress = '0x52BB......';
/*Copy-Paste the ABI file from the contract folder into this repository.*/
import abi from "./utils/PickupLines.json";

export default function App() {
  /*State variable to store the account connected with the wallet.*/
  const [currentAccount, setCurrentAccount] = useState("");

  /*Function to check if the wallet is connected to the app.*/
  const checkIfWalletIsConnected = async () => {
    try {
      const { ethereum } = window;

      if (!ethereum) {
        console.log("Make sure you have metamask!");
        return;
      } else {
        console.log("We have the ethereum object", ethereum);
      }

      const accounts = await ethereum.request({ method: "eth_accounts" });

      if (accounts.length !== 0) {
        const account = accounts[0];
        console.log("Found an authorized account:", account);
        setCurrentAccount(account);
      } else {
        console.log("No authorized account found")
      }
    } catch (error) {
      console.log(error);
    }
  }

  /*Function to connect the wallet*/
  const connectWallet = async () => {
    try {
      const { ethereum } = window;
      if (!ethereum) {
        alert("Get MetaMask!");
        return;
      }
      const accounts = await ethereum.request({ method: "eth_requestAccounts" });
      console.log("Connected", accounts[0]);
      setCurrentAccount(accounts[0]);
    } catch (error) {
      console.log(error)
    }
  }

 /*React hook to check for wallet connection when the app is mounted.*/
  useEffect(() => {
    checkIfWalletIsConnected();
  }, []);

  return (
    <div className="mainContainer">
      <div className="dataContainer">
        <div className="header">
        🧀 Hey there!
        </div>
        <div className="bio">
        <span>Welcome to Pick-Up Lines!</span>
        <button className="button" onClick={null}>
          Shoot Line
        </button>

        {/*If there is no current account render this button*/}
        {!currentAccount && (
          <button className="button" onClick={connectWallet}>
            Connect Wallet
          </button>
        )
      </div>
    </div>
    </div>
  );
}

```

### 3. 从我们的应用程序调用智能合约

我们现在有一个前端应用程序。我们已经部署了我们的合约。我们已经连接了我们的钱包。现在让我们使用我们可以从 Metamask 访问的凭据从前端调用我们的合约。

在`App.tsx`文件内的`App`组件中添加以下`pickup`函数：

```tsx
const pickup = async () => {
    try {
      const { ethereum } = window;

      if (ethereum) {
        const provider = new ethers.providers.Web3Provider(ethereum);
        const signer = provider.getSigner();
        const contract = new ethers.Contract(contractAddress, contractABI, signer);

        /*Get the count of all lines before adding a new line*/
        let count = await contract.getTotalLines();
        console.log("Retrieved total lines...", count.toNumber());

        /*Execute the actual pickup lines from your smart contract*/
        const contractTxn = await contract.wave();
        console.log("Mining...", contractTxn.hash);

        await contractTxn.wait();
        console.log("Mined -- ", contractTxn.hash);

        /*Get the count of all lines after adding a new line*/
        count = await contract.getTotalLines();
        console.log("Retrieved total lines count...", count.toNumber());
      } else {
        console.log("Ethereum object doesn't exist!");
      }
    } catch (error) {
      console.log(error);
    }
}

```

现在要调用该函数，让我们在`App.tsx`文件中创建一个按钮。添加以下代码：

```tsx
<button className="button" onClick={pickup}>
    Send a line
</button>

```

当你运行它时，你会看到总行数增加了 1。你还会看到 Metamask 弹出我们并要求我们支付我们使用假加密支付的“gas”。

## 向幸运用户发送一些以太坊

### 1. 设置奖品并选择用户发送以太坊

所以现在，我们的代码设置为每次存储随机提取搭讪。让我们通过在`newLine`函数中添加奖励算法来使其更有趣。修改`PickupLines.sol`文件中的`newLine`函数：

```tsx
function newLine(string memory _line) public {
  if (hasWrote[msg.sender]) {
    revert(
      "It seems you've posted a line already. We don't do repeats when it comes to picking up lines!"
     );
   }
  /*Adding a new Pickup Line to our blockchain.*/
  totalLines += 1;
  pickuplines.push(PickUpLine(msg.sender, _line, block.timestamp));
  hasWrote[msg.sender] = true;
  emit NewPickUpLine(msg.sender, block.timestamp, _line);

 /*Reward 10% of the senders by creating a random seed number.*/
 seed = (block.difficulty + block.timestamp + seed) % 100;
 if (seed <= 10) {
  uint256 prizeAmount = 0.0001 ether;
  require(
    prizeAmount <= address(this).balance,
    "The contract has insufficient ETH balance."
  );
  (bool success, ) = (msg.sender).call{value: prizeAmount}("");
  require(success, "Failed to withdraw ETH from the contract");
  }
}

```

在这里，算法需要一个随机数。我们采用 Solidity 给我们的两个数字`block.difficulty`和`block.timestamp`，并将它们组合起来创建一个随机数。

为了使这个随机数更加随机，我们将创建一个`seed`变量，每次用户发送新行时该变量都会发生变化。我们结合所有这三个变量来生成一个新的随机种子。

现在，我们需要该值在 0-99 的范围内，为了实现这一点，我们将通过应用 seed 来使用模运算符（%）`% 100`。

## 最后的步骤

### 1. 防止垃圾邮件发送者

现在，您可以随机选择要奖励的人。向您的网站添加条件很有用，这样人们就不会只是向您发送垃圾邮件。

为什么？好吧，也许您只是不希望他们通过向您发送多条搭讪来一遍又一遍地试图赢得奖励。或者，也许您不希望他们的消息填满您的消息墙。修改`PickupLines.sol`文件里面的`newLine`函数：

```solidity
contract PickUpLines {
    mapping(address => bool) hasWrote;

    struct PickUpLine {
        address writer;
        string line;
        uint256 timestamp;
    }
    PickUpLine[] pickuplines;

   /*Adding a new Pickup Line to our blockchain.*/
    function newLine(string memory _line) public {

       /*Condition to check for repetitive submission.*/
        if (hasWrote[msg.sender]) {
            revert(
                "It seems you've posted a line already. We don't do repeats when it comes to picking up lines!"
            );
        }

        hasWrote[msg.sender] = true;
    }
}

```

### 2. 完成

恭喜！您已经完成了所有核心功能。

现在，是时候把它变成你自己的了。更改 CSS、文本、添加一些媒体嵌入、添加更多功能等等。让东西看起来很酷:)。

## 结论

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/eR5c14rAW.gif)

这个区块链应用程序总是可以想到多项改进/功能。随意尝试代码以提高您的技能。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)