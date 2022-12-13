---
title: 建立你自己的以太坊 NFT 集合（4）
description: null
author: 李留白
weight: 0
date: 2022-12-10T02:31:40.366Z
lastmod: 2022-12-13T12:33:30.281Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221210103130.png
draft: true
---

**目录**

```
入门
  我们在建造什么？
铸造你的第一个 NFT
  什么是 NFT
  启动并运行您的本地环境
  在本地铸造 NFT
  创建一个铸造 NFT 的合约
在链上生成 NFT
  什么是链上数据。
  使用静态 SVG 铸造 NFT
  在链上动态生成 SVG NFT
创建 WEB 客户端
  设置 React 应用程序和钱包
  将钱包连接到网络应用程序
  创建一个调用合约和铸造 NFT 的按钮
完成并交付
  Web 应用程序的最后完善
  交付
```

## 创建网络客户端
### 👾设置 React 应用程序和钱包

#### 🤯 Replit

我们将使用[Replit](https://replit.com/~?utm_source=buildspace.so&utm_medium=buildspace_project)这个在线编译软件。

它是一个基于浏览器的IDE(Integrated Drive Electronics)，可以让我们轻松地构建web应用程序，并从网页端部署它们。 这是超级简便的，我们无需建立一个完善的本地环境，也无需编写部署命令，这一切都是现成的。

注意:**你不必使用replit来构建/部署你的网站。如果你想在VSCode本地编译，使用Vercel/Heroku/AWS来进行部署，并且对自己的网页开发技能有信心，那就太棒了。这个[链接](https://github.com/buildspace/buildspace-nft-course-starter?utm_source=buildspace.so&utm_medium=buildspace_project)是我们的基本代码库，您可以克隆下来并在本地编译** 。

如果你决定使用Replit，在继续下一步之前注册一个帐户!

我已经创建了一个基本的react目，你可以在Replit上**folk**它。

**[点击这里](https://replit.com/@adilanchian/nft-starter-project?v=1&utm_source=buildspace.so&utm_medium=buildspace_project)**，在右边你会看到“Fork”按钮**.确认你已经登录等replit账号，然后点击。

您将克隆我的仓库和完整的IDE在您的浏览器中，等待代码加载。一旦它停止加载并显示一些代码，单击顶部的“RUN”，就可以开始了。第一次的初始化可能需要2-4分钟。

***请注意:当你浏览这个项目时，你可能会注意到我们引用的是`.js`文件。在Replit中，如果您要创建任何新的JavaScript文件，鉴于Replit自身性能方面的一些特点，您将需要使用`.jsx`的扩展名*** ！

#### 🦊 设置Metamask钱包

太棒了，我们现在已经有一个可以轻松使用的**并已部署** React 项目。 一切似乎变得那么简单:)。

接下来，我们需要一个Ethereum钱包。可供选择的钱包很多，但是，对于这个项目，我们将使用Metamask。在[这里](https://metamask.io/download.html?utm_source=buildspace.so&utm_medium=buildspace_project)下载浏览器扩展插件和设置您的钱包。即使您已经有了另一个钱包，也可以暂时使用Metamask。

为什么我们需要 Metamask？额！因为我们需要调用区块链上的智能合约的函数。 而且，要做到这一点，我们必须要有一个钱包，来使用我们的Ethereum地址和私钥。

**但是，我们需要一些东西将我们的网站与我们的钱包连接起来，以便我们可以安全地将我们的钱包证书传递给我们的网站。然后，我们的网站可以使用这些凭据调用我们的智能合约。最终，您需要有效的凭证来访问智能合约上的函数**

这就像认证一样，我们需要一些东西来“登录”到区块链，然后使用这些登录凭证从我们的网站发出API请求。

所以，去把它都设置好吧!他们的设置流程是不言自明的:)。

### 💰 将钱包连接到网络应用程序

#### 🌅 使用 window.ethereum()

因此，为了让我们的网站与区块链通信，我们需要以某种方式将我们的钱包连接到它。一旦我们将钱包连接到我们的网站，我们的网站将有权代表我们调用智能合约。 **请记住，这就像在网站中进行身份验证一样。**

回到 Replit 并转到`src`下的 `App.js`文件，这是我们待完成所有工作的地方。

如果我们登录到 Metamask，它会自动将一个名为 `ethereum` 的特殊对象注入到我们的窗口中，该对象具有一些神奇的方法。让我们先检查一下该对象是否存在。

```javascript
import React, { useEffect } from "react";
import './styles/App.css';
import twitterLogo from './assets/twitter-logo.svg';

// Constants
const TWITTER_HANDLE = '_buildspace';
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;
const OPENSEA_LINK = '';
const TOTAL_MINT_COUNT = 50;

const App = () => {
  const checkIfWalletIsConnected = () => {
    /*
    * First make sure we have access to window.ethereum
    */
    const { ethereum } = window;

    if (!ethereum) {
      console.log("Make sure you have metamask!");
      return;
    } else {
      console.log("We have the ethereum object", ethereum);
    }
  }

  // Render Methods
  const renderNotConnectedContainer = () => (
    <button className="cta-button connect-wallet-button">
      Connect to Wallet
    </button>
  );

  /*
  * This runs our function when the page loads.
  */
  useEffect(() => {
    checkIfWalletIsConnected();
  }, [])

  return (
    <div className="App">
      <div className="container">
        <div className="header-container">
          <p className="header gradient-text">My NFT Collection</p>
          <p className="sub-text">
            Each unique. Each beautiful. Discover your NFT today.
          </p>
          {/* Add your render method here */}
          {renderNotConnectedContainer()}
        </div>
        <div className="footer-container">
          <img alt="Twitter Logo" className="twitter-logo" src={twitterLogo} />
          <a
            className="footer-text"
            href={TWITTER_LINK}
            target="_blank"
            rel="noreferrer"
          >{`built on @${TWITTER_HANDLE}`}</a>
        </div>
      </div>
    </div>
  );
};

export default App;
```

#### 🔒 检查我们是否有权访问帐户

因此，当您运行它时，您应该会在网站的控制台中看到“We have the Ethereum object”这一行。 如果您使用的是 Replit，请确保您查看的是项目网站的控制台，而不是 Replit 工作区！ 您可以通过在自己的窗口/选项卡中打开它并启动开发人员工具来访问您网站的控制台。 网址应该看起来像这样 - `https://nft-starter-project.yourUsername.repl.co/`。

**GOOD**

接下来，我们需要检查我们是否有权真正访问用户的钱包。一旦我们可以访问它，我们就可以调用智能合约！

基本上，Metamask 只会将我们的钱包凭证提供给我们授权的网站。 同样，这就像登录一样！ 但是，我们在这里做的是**检查我们是否“登录”。**

查看下面的代码：

```javascript
import React, { useEffect, useState } from "react";
import './styles/App.css';
import twitterLogo from './assets/twitter-logo.svg';

// Constants
const TWITTER_HANDLE = '_buildspace';
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;
const OPENSEA_LINK = '';
const TOTAL_MINT_COUNT = 50;

const App = () => {

  /*
  * Just a state variable we use to store our user's public wallet. Don't forget to import useState.
  */
  const [currentAccount, setCurrentAccount] = useState("");
  
  /*
  * Gotta make sure this is async.
  */
  const checkIfWalletIsConnected = async () => {
    const { ethereum } = window;

    if (!ethereum) {
        console.log("Make sure you have metamask!");
        return;
    } else {
        console.log("We have the ethereum object", ethereum);
    }

    /*
    * Check if we're authorized to access the user's wallet
    */
    const accounts = await ethereum.request({ method: 'eth_accounts' });

    /*
    * User can have multiple authorized accounts, we grab the first one if its there!
    */
    if (accounts.length !== 0) {
      const account = accounts[0];
      console.log("Found an authorized account:", account);
      setCurrentAccount(account);
    } else {
      console.log("No authorized account found");
    }
  }

  // Render Methods
  const renderNotConnectedContainer = () => (
    <button className="cta-button connect-wallet-button">
      Connect to Wallet
    </button>
  );

  useEffect(() => {
    checkIfWalletIsConnected();
  }, [])

  return (
     <div className="App">
      <div className="container">
        <div className="header-container">
          <p className="header gradient-text">My NFT Collection</p>
          <p className="sub-text">
            Each unique. Each beautiful. Discover your NFT today.
          </p>
          {renderNotConnectedContainer()}
        </div>
        <div className="footer-container">
          <img alt="Twitter Logo" className="twitter-logo" src={twitterLogo} />
          <a
            className="footer-text"
            href={TWITTER_LINK}
            target="_blank"
            rel="noreferrer"
          >{`built on @${TWITTER_HANDLE}`}</a>
        </div>
      </div>
    </div>
  );
};

export default App;
```

#### 🛍 构建钱包连接按钮

当您运行上述代码时，console.log应该会输出 `No authorized account found`。为什么?因为我们从未明确地告诉Metamask：*“嘿，Metamask，请让这个网站访问我的钱包”*。

我们需要创建一个 `connectWallet`按钮。在Web3的世界中，连接您的钱包实际上是用户的一个“登录”按钮。

准备好享受最简单的“登录”体验了吗:)？ 一探究竟：

```javascript
import React, { useEffect, useState } from "react";
import './styles/App.css';
import twitterLogo from './assets/twitter-logo.svg';

const TWITTER_HANDLE = '_buildspace';
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;
const OPENSEA_LINK = '';
const TOTAL_MINT_COUNT = 50;

const App = () => {
  const [currentAccount, setCurrentAccount] = useState("");
  
  const checkIfWalletIsConnected = async () => {
    const { ethereum } = window;

    if (!ethereum) {
      console.log("Make sure you have metamask!");
      return;
    } else {
      console.log("We have the ethereum object", ethereum);
    }

    const accounts = await ethereum.request({ method: 'eth_accounts' });

    if (accounts.length !== 0) {
      const account = accounts[0];
      console.log("Found an authorized account:", account);
      setCurrentAccount(account);
    } else {
      console.log("No authorized account found");
    }
  }

  /*
  * Implement your connectWallet method here
  */
  const connectWallet = async () => {
    try {
      const { ethereum } = window;

      if (!ethereum) {
        alert("Get MetaMask!");
        return;
      }

      /*
      * Fancy method to request access to account.
      */
      const accounts = await ethereum.request({ method: "eth_requestAccounts" });

      /*
      * Boom! This should print out public address once we authorize Metamask.
      */
      console.log("Connected", accounts[0]);
      setCurrentAccount(accounts[0]); 
    } catch (error) {
      console.log(error);
    }
  }

  // Render Methods
  const renderNotConnectedContainer = () => (
    <button onClick={connectWallet} className="cta-button connect-wallet-button">
      Connect to Wallet
    </button>
  );

  useEffect(() => {
    checkIfWalletIsConnected();
  }, [])

  /*
  * Added a conditional render! We don't want to show Connect to Wallet if we're already connected :).
  */
  return (
    <div className="App">
      <div className="container">
        <div className="header-container">
          <p className="header gradient-text">My NFT Collection</p>
          <p className="sub-text">
            Each unique. Each beautiful. Discover your NFT today.
          </p>
          {currentAccount === "" ? (
            renderNotConnectedContainer()
          ) : (
            <button onClick={null} className="cta-button connect-wallet-button">
              Mint NFT
            </button>
          )}
        </div>
        <div className="footer-container">
          <img alt="Twitter Logo" className="twitter-logo" src={twitterLogo} />
          <a
            className="footer-text"
            href={TWITTER_LINK}
            target="_blank"
            rel="noreferrer"
          >{`built on @${TWITTER_HANDLE}`}</a>
        </div>
      </div>
    </div>
  );
};

export default App;
```

### ✨ 创建调用合约和铸造 NFT 的按钮

#### 💚 从网站页铸造NFT

真棒。我们已经做到成功构建了我们的网站，部署了我们的合同，并已经连接了我们的钱包。 **现在，我们需要使用从 Metamask 获取的访问凭据，在网页dapp中**调用我们的合约！

所以，请记住，我们的合约具有 `makeAnEpicNFT` 函数，它的功能实际上是铸造 NFT。 我们现在需要从我们的网页dapp中调用此函数，继续在 `connectWallet` 函数下方添加以下函数。

```javascript
const askContractToMintNft = async () => {
  const CONTRACT_ADDRESS = "INSERT_YOUR_DEPLOYED_GOERLI_CONTRACT_ADDRESS";

  try {
    const { ethereum } = window;

    if (ethereum) {
      const provider = new ethers.providers.Web3Provider(ethereum);
      const signer = provider.getSigner();
      const connectedContract = new ethers.Contract(CONTRACT_ADDRESS, myEpicNft.abi, signer);

      console.log("Going to pop wallet now to pay gas...")
      let nftTxn = await connectedContract.makeAnEpicNFT();

      console.log("Mining...please wait.")
      await nftTxn.wait();
      
      console.log(`Mined, see transaction: https://goerli.etherscan.io/tx/${nftTxn.hash}`);

    } else {
      console.log("Ethereum object doesn't exist!");
    }
  } catch (error) {
    console.log(error)
  }
}
```

这会弹出一些错误。不用担心！我们稍后会修复它，让我们逐步完成代码。

```javascript
const provider = new ethers.providers.Web3Provider(ethereum);
const signer = provider.getSigner();
```

`ethers` 是一个帮助我们的前端与我们的合约对话的库。 在码最顶部写入 `import { ethers } from "ethers";`，确保将 `ethers` 库导入。

`Provider` 是我们用来与以太坊节点进行通信的工具。还记得我们是如何使用QuickNode**部署**的吗？在本例中，我们使用Metamask在后台提供的节点来发送/接收自已部署合约的数据。

[这里](https://docs.ethers.io/v5/api/signer/#signers?utm_source=buildspace.so&utm_medium=buildspace_project) 是解释第 2 行签名者是什么的链接。

```javascript
const connectedContract = new ethers.Contract(CONTRACT_ADDRESS, myEpicNft.abi, signer);
```

我们稍后会介绍以上代码。只需知道这行代码实际上是**创建与我们合约的连接**。它需要：合约地址，一个叫做 `abi` 文件，和一个 `signer`。 这是我们始终需要与区块链上的合约进行通信的三个必要因素。

请注意我是如何硬编码 `const CONTRACT_ADDRESS` 的？ **请务必将此变量更改为您最新部署的合约的部署合约地址**。如果您忘记或丢失了它，请不要担心，只需重新部署合约并获得一个新的合约地址 :)。

```javascript
console.log("Going to pop wallet now to pay gas...")
let nftTxn = await connectedContract.makeAnEpicNFT();

console.log("Mining...please wait.")
await nftTxn.wait();

console.log(`Mined, see transaction: https://goerli.etherscan.io/tx/${nftTxn.hash}`);
```

其余代码应该已经有效了。它看起来有点像我们部署的代码:)！ 我们使用 `makeAnEpicNFT` 调用我们的合约，等待它被矿工挖掘，然后链接 Etherscan URL！

最后，我们要在有人单击“Mint NFT”按钮时调用此函数。

```javascript
return (
  {currentAccount === "" 
    ? renderNotConnectedContainer()
    : (
      /** Add askContractToMintNft Action for the onClick event **/
      <button onClick={askContractToMintNft} className="cta-button connect-wallet-button">
        Mint NFT
      </button>
    )
  }
);
```

#### 📂 ABI文件

**在这里我制作了一个小视频来解释 ABI 文件。 请等一下，让我们要复习一些重要的东西。**

https://www.loom.com/share/2d493d687e5e4172ba9d47eeede64a37

所以，当你编译你的智能合约时，编译器会导出一堆你需要的文件，让你与合约交互。您可以在位于Solidity项目根目录下的 `artifacts` 文件夹中找到这些文件。

ABI 文件是我们的dapp需要知道如何与我们的合约进行通信的文件。 [此处](https://docs.soliditylang.org/en/v0.8.14/abi-spec.html?utm_source=buildspace.so&utm_medium=buildspace_project) 可以了解相关信息。

ABI 文件的内容可以在您的 hardhat 项目中的一个神奇的 JSON 文件中找到： `artifacts/contracts/MyEpicNFT.sol/MyEpicNFT.json`

那么，问题就变成了我们如何将这个 JSON 文件放入我们的前端？只需要复制粘贴。

复制 `MyEpicNFT.json` 中的内容，然后转到您的dapp。 您将在 `src` 下创建一个名为 `utils` 的新文件夹。在 `utils` 下创建一个名为 `MyEpicNFT.json` 的文件。所以完整的路径看起来是：`src/utils/MyEpicNFT.json`

将 ABI 文件内容粘贴到我们的新文件中。

现在您已经准备好包含所有的 ABI 内容文件，是时候将它导入到您的 `App.js` 文件中了。应该这样导入：

```javascript
import myEpicNft from './utils/MyEpicNFT.json';
```

所有的代码都已经写完。应该不会再有错误了！你准备好铸造一些 NFT 了！

从这里你需要做的就是点击“Mint NFT”，支付gas（使用你的测试网 ETH），等待交易被开采，然后BOOM！ 您的 NFT 应该立即或在最大 5-15 分钟内在 OpenSea 上显示。

您可能会问自己 gas 是什么。我不打算在这里回答。 但是，您可以在 [此处](https://ethereum.org/en/developers/docs/gas/?utm_source=buildspace.so&utm_medium=buildspace_project) 获取相关信息。

#### 🤩 测试

您现在应该可以直接从您的网站上开始铸造真正的 NFT。 **走吧！！！ 这就是 EPICCCCC。** 这基本上就是所有这些 NFT 铸造网站的工作方式了，而现在你也会了:)。

实际上，我已经在上面链接的 ABI 视频中完成并测试了整个过程。 一定要看一看！当您**更改**您的合约时，我会讨论一些关于该怎么做的重要事情。 因为你的合约是永久的，改变合约需要你重新部署，更新你前端的地址，最后更新前端的 ABI 文件。

#### ✈️ 关于合约重新部署的说明

假设你想改变你的合约。你需要做三件事:

1. 我们需要再次部署它。
2. 我们需要更新我们前端的合约地址。
3. 我们需要更新前端的abi文件。

**人们在更改合约时经常忘记执行这3个步骤。别忘了lol。**

为什么我们需要做这些？这是因为智能合约是不可变的。他们不可更改，它们是永久的。这意味着更改合约需要完全重新部署。这也将**重置**所有变量，因为它将被视为一个全新的合同**这意味着如果我们想更新合约的代码，我们将丢失所有的NFT数据**

> 原文链接：https://buildspace.so/p/mint-nft-collection

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
