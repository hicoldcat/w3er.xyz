---
title: Web3教程：使用 Solidity 构建 Web3 应用程序（4）
description: null
author: 李留白
weight: 0
date: 2022-11-25T14:01:29.010Z
lastmod: 2022-11-25T14:03:14.461Z
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

### 将我们的钱包连接到我们的网络应用程序

#### 🌅 使用 window.ethereum()

因此，为了让我们的网站与区块链对话，我们需要以某种方式将我们的钱包连接到它。一旦我们将钱包连接到我们的网站，我们的网站将有权代表我们调用智能合约。请记住，这就像在网站上进行身份验证一样。

前往`Replit`，进入`src`下的`App.jsx`，这是我们将进行所有工作的地方。

如果我们登录到 Metamask，它会自动将一个名为`ethereum` 的特殊对象注入到我们的窗口中。让我们先检查一下我们是否有这个对象。

```javascript
import React, { useEffect } from "react";
import "./App.css";

const getEthereumObject = () => window.ethereum;

const App = () => {
  /*
   * The passed callback function will be run when the page loads.
   * More technically, when the App component "mounts".
   */
  useEffect(() => {
    const ethereum = getEthereumObject();
    if (!ethereum) {
      console.log("Make sure you have metamask!");
    } else {
      console.log("We have the ethereum object", ethereum);
    }
  }, []);

  return (
    <div className="mainContainer">
      <div className="dataContainer">
        <div className="header">
          👋 Hey there!
        </div>

        <div className="bio">
          I am Farza and I worked on self-driving cars so that's pretty cool
          right? Connect your Ethereum wallet and wave at me!
        </div>

        <button className="waveButton" onClick={null}>
          Wave at Me
        </button>
      </div>
    </div>
  );
};

export default App;
```

#### 🔒 查看我们是否可以访问用户帐户

因此，当您运行它时，您应该会在网站的控制台中看到“We have the ethereum object”这一行，当您去检查它时。如果您使用的是 Replit，请确保您查看的是项目网站的控制台，而不是 Replit 工作区！您可以通过在自己的窗口/选项卡中打开它并启动开发人员工具来访问您网站的控制台。该 URL 应如下所示 -`https://waveportal-starter-project.yourUsername.repl.co/`

**很好。**

接下来，我们需要实际检查我们是否有权实际访问用户的钱包。一旦我们可以访问它，我们就可以调用我们的智能合约！

基本上，Metamask不会把我们的钱包凭证给我们去的每个网站。它只把它给我们授权的网站。再说一次，这就像登录一样! 但是，我们在这里做的是检查我们是否已经 "登录 "了。

查看下面的代码。

```javascript
import React, { useEffect, useState } from "react";
import "./App.css";

const getEthereumObject = () => window.ethereum;

/*
 * This function returns the first linked account found.
 * If there is no account linked, it will return null.
 */
const findMetaMaskAccount = async () => {
  try {
    const ethereum = getEthereumObject();

    /*
    * First make sure we have access to the Ethereum object.
    */
    if (!ethereum) {
      console.error("Make sure you have Metamask!");
      return null;
    }

    console.log("We have the Ethereum object", ethereum);
    const accounts = await ethereum.request({ method: "eth_accounts" });

    if (accounts.length !== 0) {
      const account = accounts[0];
      console.log("Found an authorized account:", account);
      return account;
    } else {
      console.error("No authorized account found");
      return null;
    }
  } catch (error) {
    console.error(error);
    return null;
  }
};

const App = () => {
  const [currentAccount, setCurrentAccount] = useState("");

  /*
   * The passed callback function will be run when the page loads.
   * More technically, when the App component "mounts".
   */
  useEffect(async () => {
    const account = await findMetaMaskAccount();
    if (account !== null) {
      setCurrentAccount(account);
    }
  }, []);

  return (
    <div className="mainContainer">
      <div className="dataContainer">
        <div className="header">
          👋 Hey there!
        </div>

        <div className="bio">
          I am Farza and I worked on self-driving cars so that's pretty cool
          right? Connect your Ethereum wallet and wave at me!
        </div>

        <button className="waveButton" onClick={null}>
          Wave at Me
        </button>
      </div>
    </div>
  );
};

export default App;
```

因此，我们使用`eth_accounts` 这种特殊方法来查看我们是否有权访问用户钱包中的任何帐户。要记住的一件事是，用户的钱包中可能有多个帐户。在这种情况下，我们只获取第一个。

#### 💰建立一个连接钱包按钮

当你运行上面的代码时，打印出来的 console.log 应该是`No authorized account found`. 为什么？好吧，因为我们从未明确告诉 Metamask，“嘿 Metamask，请允许该网站访问我的钱包”。 

我们需要创建一个`connectWallet`按钮。在 Web3 的世界里，连接你的钱包实际上就是你的用户的一个“登录”按钮 :)。一探究竟：

```javascript
import React, { useEffect, useState } from "react";
import "./App.css";

const getEthereumObject = () => window.ethereum;

/*
 * This function returns the first linked account found.
 * If there is no account linked, it will return null.
 */
const findMetaMaskAccount = async () => {
  try {
    const ethereum = getEthereumObject();

    /*
     * First make sure we have access to the Ethereum object.
     */
    if (!ethereum) {
      console.error("Make sure you have Metamask!");
      return null;
    }

    console.log("We have the Ethereum object", ethereum);
    const accounts = await ethereum.request({ method: "eth_accounts" });

    if (accounts.length !== 0) {
      const account = accounts[0];
      console.log("Found an authorized account:", account);
      return account;
    } else {
      console.error("No authorized account found");
      return null;
    }
  } catch (error) {
    console.error(error);
    return null;
  }
};

const App = () => {
  const [currentAccount, setCurrentAccount] = useState("");

  const connectWallet = async () => {
    try {
      const ethereum = getEthereumObject();
      if (!ethereum) {
        alert("Get MetaMask!");
        return;
      }

      const accounts = await ethereum.request({
        method: "eth_requestAccounts",
      });

      console.log("Connected", accounts[0]);
      setCurrentAccount(accounts[0]);
    } catch (error) {
      console.error(error);
    }
  };

  /*
   * This runs our function when the page loads.
   * More technically, when the App component "mounts".
   */
  useEffect(async () => {
    const account = await findMetaMaskAccount();
    if (account !== null) {
      setCurrentAccount(account);
    }
  }, []);

  return (
    <div className="mainContainer">
      <div className="dataContainer">
        <div className="header">
          👋 Hey there!
        </div>

        <div className="bio">
          I am Farza and I worked on self-driving cars so that's pretty cool
          right? Connect your Ethereum wallet and wave at me!
        </div>

        <button className="waveButton" onClick={null}>
          Wave at Me
        </button>

        {/*
         * If there is no currentAccount render this button
         */}
        {!currentAccount && (
          <button className="waveButton" onClick={connectWallet}>
            Connect Wallet
          </button>
        )}
      </div>
    </div>
  );
};

export default App;
```

我们的代码在这里有点长，但是您可以看到我们的`connectWallet`函数有多短。在这种情况下，我使用`eth_requestAccounts`函数是因为我实际上是在要求 Metamask 允许我访问用户的钱包。

在第 90 行，我还添加了一个按钮，以便我们可以调用我们的`connectWallet`函数。你会注意到我只在我们没有currentAccount的时候显示这个按钮。如果我们已经有了currentAccount，那就意味着我们已经可以访问用户钱包中的授权账户。

#### 🌐 连接！

现在，是施展魔法的时候了！看看下面的视频： 

https://www.loom.com/share/1d30b147047141ce8fde590c7673128d

### 从我们的网络应用程序调用已部署的智能合约

#### 📒 通过我们的网站从区块链读取

真棒。我们做到了。我们已经部署了我们的网站。我们已经部署了我们的合约。我们已经连接了我们的钱包。现在我们需要从我们的网站上实际调用我们的合同，使用我们现在从Metamask获得的凭证。

所以，我们的智能合约有这样一个函数，可以检索到waves的总数。

```solidity
function getTotalWaves() public view returns (uint256) {
      console.log("We have %d total waves!", totalWaves);
      return totalWaves;
  }
```

让我们从我们的网站调用这个函数:)。

继续把这个函数写在我们的`connectWallet()`函数下面。

```javascript
const wave = async () => {
    try {
      const { ethereum } = window;

      if (ethereum) {
        const provider = new ethers.providers.Web3Provider(ethereum);
        const signer = provider.getSigner();
        const wavePortalContract = new ethers.Contract(contractAddress, contractABI, signer);

        let count = await wavePortalContract.getTotalWaves();
        console.log("Retrieved total wave count...", count.toNumber());
      } else {
        console.log("Ethereum object doesn't exist!");
      }
    } catch (error) {
      console.log(error);
    }
}
```

这里快速解释一下：

```javascript
const provider = new ethers.providers.Web3Provider(ethereum);
const signer = provider.getSigner();
```

`ethers`是一个帮助我们的前端与我们的合约对话的库。请务必在顶部使用`import { ethers } from "ethers";`.

“Provider”是我们用来与Ethereum节点实际对话的。还记得我们是如何使用QuickNode来部署的吗？那么在这种情况下，我们使用Metamask在后台提供的节点，从我们部署的合约中发送/接收数据。

[这里](https://docs.ethers.io/v5/api/signer/#signers?utm_source=buildspace.so&utm_medium=buildspace_project)一个链接，解释了第 2 行的签名者是什么。

通过`onClick`将prop 从`null`更新为`wave` ，将此功能连接到我们的 wave按钮：

```html
<button className="waveButton" onClick={wave}>
    Wave at Me
</button>
```

真棒。

所以，现在这段代码**中断了**。在我们的 replit shell 中它会说：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221125221757.png)

我们需要这两个变量！！

那么，你有合约地址——对吗？还记得当你部署你的合同时我告诉你保存地址吗？这就是它的要求！

但是，什么是 ABI？很早之前我提到过当你编译一个合约时，它会在`artifacts`为你创建一堆文件. ABI就是这些文件中的一个。

#### 🏠 设置你的合约地址

还记得你在Goerli Testnet上部署你的合同吗？那次部署的输出包括你的智能合约地址，它看起来应该是这样的：

```
Deploying contracts with the account: 0xF79A3bb8d5b93686c4068E2A97eAeC5fE4843E7D
Account balance: 3198297774605223721
WavePortal address: 0x957fe7381be45A31967F1EcfAc6Ff001D8AF8D6c
```

你需要在你的React应用程序中访问这个地址。这很简单，在你的`App.js`文件中创建一个名为`contractAddress`的新属性，并将其值设置为在控制台中打印出来的`WavePortal`地址。

```javascript
import React, { useEffect, useState } from "react";
import { ethers } from "ethers";
import "./App.css";

const App = () => {
  const [currentAccount, setCurrentAccount] = useState("");
  /**
   * Create a variable here that holds the contract address after you deploy!
   */
  const contractAddress = "0x957fe7381be45A31967F1EcfAc6Ff001D8AF8D6c";
```

#### 🛠 获取 ABI 文件内容

回顾一下，我们已经走了一半的路了！让我们回到我们的智能合约文件夹。

当你编译你的智能合约时，编译器会吐出一堆让你与合约交互所需的文件。您可以在位于 Solidity 项目根目录的`artifacts`文件夹中找到这些文件。

ABI 文件是我们的网络应用程序需要知道如何与我们的合约进行通信的文件。[在这里](https://docs.soliditylang.org/en/v0.8.14/abi-spec.html?utm_source=buildspace.so&utm_medium=buildspace_project)阅读。

ABI 文件的内容可以在您的 hardhat 项目中的一个JSON 文件中找到：

```
artifacts/contracts/WavePortal.sol/WavePortal.json
```

那么，问题就变成了我们如何将这个 JSON 文件放入我们的前端？对于这个项目，我们要做一些好的老的 "copy pasta"！

复制您的`WavePortal.json`中的内容，然后前往您的网络应用程序。您你将在`src`下建立一个名为`utils`的新文件夹。在`utils`下创建一个名为`WavePortal.json`的文件。因此，完整的路径将看起来像：

```
src/utils/WavePortal.json
```

将整个 JSON 文件粘贴到那里！

现在您已经准备好包含所有 ABI 内容的文件，是时候将其导入您的`App.js`文件并创建对它的引用了。就在您导入`App.css`的位置下，继续导入您的 JSON 文件并创建对 abi 内容的引用：

```javascript
import React, { useEffect, useState } from "react";
import { ethers } from "ethers";
import "./App.css";
import abi from "./utils/WavePortal.json";

const App = () => {
  const [currentAccount, setCurrentAccount] = useState("");

  const contractAddress = "0x957fe7381be45A31967F1EcfAc6Ff001D8AF8D6c";
  /**
   * Create a variable here that references the abi content!
   */
  const contractABI = abi.abi;
```

让我们看看你实际上在哪里使用了这个 ABI 内容：

```javascript
const wave = async () => {
    try {
      const { ethereum } = window;

      if (ethereum) {
        const provider = new ethers.providers.Web3Provider(ethereum);
        const signer = provider.getSigner();

        /*
        * You're using contractABI here
        */
        const wavePortalContract = new ethers.Contract(contractAddress, contractABI, signer);

        let count = await wavePortalContract.getTotalWaves();
        console.log("Retrieved total wave count...", count.toNumber());
      } else {
        console.log("Ethereum object doesn't exist!");
      }
    } catch (error) {
      console.log(error);
    }
  }
```

添加该文件并单击“Wave”按钮后——**您将通过 Web 客户端正式从区块链上的合同中读取数据**。

#### 📝 写入数据

将数据写入合约的代码与读取数据并无太大区别。主要区别在于，当我们要将新数据写入合约时，我们需要通知矿工以便可以挖掘交易。当我们读取数据时，我们不需要这样做。读取是“免费”的，因为我们所做的只是从区块链中读取，**我们不会改变它。**

这是wave的代码：

```javascript
const wave = async () => {
    try {
      const { ethereum } = window;

      if (ethereum) {
        const provider = new ethers.providers.Web3Provider(ethereum);
        const signer = provider.getSigner();
        const wavePortalContract = new ethers.Contract(contractAddress, contractABI, signer);

        let count = await wavePortalContract.getTotalWaves();
        console.log("Retrieved total wave count...", count.toNumber());

        /*
        * Execute the actual wave from your smart contract
        */
        const waveTxn = await wavePortalContract.wave();
        console.log("Mining...", waveTxn.hash);

        await waveTxn.wait();
        console.log("Mined -- ", waveTxn.hash);

        count = await wavePortalContract.getTotalWaves();
        console.log("Retrieved total wave count...", count.toNumber());
      } else {
        console.log("Ethereum object doesn't exist!");
      }
    } catch (error) {
      console.log(error);
    }
  }
```

很简单，对吧:)？

这里最棒的是，当交易被挖出时，你实际上可以打印出交易哈希值，将其复制/粘贴到[Etherscan](https://goerli.etherscan.io/?utm_source=buildspace.so&utm_medium=buildspace_project)，并实时查看它的处理过程:)。

当我们运行它时，您会看到总wave数增加了 1。您还会看到 Metamask 向我们弹出并要求我们支付“gas”，我们用我们的假 $ 支付的。[这里](https://ethereum.org/en/developers/docs/gas/?utm_source=buildspace.so&utm_medium=buildspace_project)有一篇很棒的文章。试着弄清楚什么是气体 :)。

## 🎉成功

**NICEEEEEEE :)。**

我们现在有一个可以读取和写入数据到区块链的实际客户端。从这里，你可以做任何你想做的事。你已经掌握了基础知识。您可以构建去中心化版本的 Twitter。你可以为人们发布他们最喜欢的表情包建立一种方式，并允许人们用 ETH 给发布最佳表情包的人“打赏”。你可以建立一个去中心化的投票系统，一个国家可以用它在一切公开透明的选举中投票。

可能性真的是无穷无尽的。

```
未完待续...

原文地址：https://buildspace.so/p/build-solidity-web3-app
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)

