---
title: Web3教程：使用 Solidity 构建 Web3 应用程序（3）
description: null
author: 李留白
weight: 0
date: 2022-11-23T12:59:59.525Z
lastmod: 2022-11-23T13:23:46.162Z
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

## 连接到钱包

### 设置一个基本的 React 应用程序，设置 Metamask

#### 💻 设置客户端

是时候开始在我们的网站上工作了！我们的合约非常简单，但是，让我们尽快了解我们的前端如何与我们的合约交互！

因此，有大约 100 种不同的方法来设置和部署基本的 React 项目。我将在 10 分钟内向您展示如何做到这一点，到最后，您实际上将拥有一个完全部署了自己的域和所有内容的 React 应用程序。

#### 🤯 复制

**注意：你不一定要用replit来构建和部署你的网站。如果你想使用 create-react-app + Vercel/Heroku/AWS——那太棒了。[这是一个指向您可以在本地克隆和处理的基本存储库的链接](https://github.com/buildspace/waveportal-starter-project?utm_source=buildspace.so&utm_medium=buildspace_project)。**

我们将使用[Replit](https://replit.com/~?utm_source=buildspace.so&utm_medium=buildspace_project)！ 它是一个基于浏览器的集成开发环境，让我们可以轻松地构建网络应用，并在浏览器中部署它们。它是超级合法的。我们不需要建立一个完整的本地环境，也不需要写命令进行部署，而是直接给我们提供。

在继续之前先在 Replit 上注册一个帐户。

我已经创建了一个基本的 React 项目，您可以在 Replit 上进行分叉**。**只需点击[此处](https://replit.com/@adilanchian/waveportal-starter-project?v=1&utm_source=buildspace.so&utm_medium=buildspace_project)，在右侧附近您会看到“fork”按钮。确保您已登录，然后单击此按钮。您将在浏览器中神奇地克隆我的存储库和完整的 IDE 以使用代码。一旦它停止加载并显示一些代码，请单击顶部的“运行”按钮。第一次这可能需要2-3 分钟。通常来说，Replit正在启动你的项目，并将其部署到一个实际的域。

**请注意：在您完成此项目时，您可能会注意到我们正在引用`.js`文件。在 Replit 中，如果您要创建任何新的 JavaScript 文件，则需要改用`.jsx`扩展名！Replit 有一些性能奇特，需要您使用`.jsx`文件扩展名:)。**

我制作了一个快速视频，介绍了如何在 Replit、部署和进入暗模式时编辑代码。在下面查看：

https://www.loom.com/share/8e8f47eacf6d448eb5d25b6908021035

#### 🦊 元掩码

太棒了，我们有了一个部署好的React项目，我们可以轻松地工作。这很简单:)。。

接下来我们需要一个Ethereum钱包。有一堆这样的钱包，但是，对于这个项目，我们将使用Metamask。[下载浏览器扩展程序并在此处](https://metamask.io/download.html?utm_source=buildspace.so&utm_medium=buildspace_project)设置您的钱包。即使您已经有其他钱包提供商，现在也只需使用 Metamask。

为什么我们需要 Metamask？出色地。我们需要能够调用位于区块链上的智能合约的功能。而且，要做到这一点，我们需要有一个钱包，里面有我们的以太坊地址和私钥。

**但是，我们需要一些东西将我们的网站与我们的钱包连接起来，以便我们可以安全地将我们的钱包凭据传递到我们的网站，这样我们的网站就可以使用这些凭据来调用我们的智能合约。您需要有有效的凭据才能访问智能合约上的功能。**

这几乎就像身份验证。我们需要一些东西来“登录”到区块链，然后使用这些登录凭据从我们的网站发出 API 请求。

所以，继续设置吧！他们的设置流程是不言自明的:)。

### 将智能合约部署到真实的测试网

#### 📤 设置部署到区块链

继续并关闭运行本地区块链网络的终端，就是你运行`npx hardhat node`的地方。我们将不再需要它；)。我主要只是想向您展示如何在本地部署。

现在我们要做的是真正的交易，将其部署到实际的区块链上。

继续并在[此处](https://www.quicknode.com/?utm_source=buildspace&utm_campaign=generic&utm_content=sign-up&utm_medium=buildspace)使用 QuickNode 创建一个帐户。

很抱歉让您创建了这么多帐户，但是，这个生态系统很复杂，我们希望利用那里的优秀工具。QuickNode 所做的是它为我们提供了一种部署到真正的以太坊区块链的简单方法。

#### 💳交易

所以，当我们想在以太坊区块链上执行一个动作时，我们称之为*交易*。例如，向某人发送 ETH 就是一笔交易。在我们的合约中做一些更新变量的事情也被认为是一个交易。

因此，当我们调用`wave`并且它调用`totalWaves += 1`时，这就是一笔交易！ **部署智能合约也是一种交易。**

请记住，区块链没有所有者。它只是世界各地由拥有区块链副本的**矿工**运行的一堆计算机。

当我们部署我们的合约时，我们需要告诉**所有这些**矿工，“嘿，这是一个新的智能合约，请将我的智能合约添加到区块链中，然后也告诉其他人”。

这就是[QuickNode](https://www.quicknode.com/?utm_source=buildspace&utm_campaign=generic&utm_content=sign-up&utm_medium=buildspace)的用武之地。

QuickNode 本质上帮助我们广播我们的合约创建交易，以便矿工尽快获取它。一旦交易被挖掘，它就会作为合法交易广播到区块链。从那里，每个人都更新他们的区块链副本。

这是复杂的。而且，如果您不完全理解它，请不要担心。当您编写更多代码并实际构建此应用程序时，它自然会更有意义。 

[因此，请在此处注册一个 QuickNode 帐户](https://www.quicknode.com/?utm_source=buildspace&utm_campaign=generic&utm_content=sign-up&utm_medium=buildspace)。

查看下面的视频，了解如何获取测试网的 API 密钥！

https://www.loom.com/share/c079028c612340e8b7439d0d2103a313

#### 🕸️ 测试网

直到最后我们才打算部署到“以太坊主网”。为什么？因为它需要花费真金白银，不能乱来! ！我们将从“测试网”开始，它是“主网”的克隆版，但它使用假 $，因此我们可以尽可能多地测试东西。但是，重要的是要知道测试网是由真实的矿工运行并模仿现实世界的场景。

这太棒了，因为我们可以在实际场景中测试我们的应用程序，我们实际上将要：

1. 广播我们的交易

2. 等待被真矿工发掘

3. 等待它被开采

4. 等待它被广播回区块链，告诉所有其他矿工更新他们的副本

因此，您将在接下来的几节课中完成所有这些工作 :)。

#### 🤑 得到一些假 $

外面有一些测试网，我们将使用的一个叫做“Goerli”，它由以太坊基金会运行。

为了部署到 Goerli，我们需要假以太币。为什么？因为如果你要部署到实际的以太坊主网上，你会用到真钱！因此，测试网复制了主网的工作方式，唯一的区别是不涉及真钱。

为了获得假的 ETH，我们必须向网络索要一些。**这个假的 ETH 只能在这个特定的测试网上工作。**你可以通过水龙头为Goerli获取一些假的ETH。在使用水龙头之前，请确保您的 MetaMask 钱包设置为“Goerli 测试网络”。

对于 MyCrypto，您需要连接您的钱包，创建一个帐户，然后再次单击相同的链接以请求资金。对于官方 Goerli 水龙头，您应该登录您的 Alchemy 帐户以获得 2 倍的金额。

| 姓名            | 关联                                                         | 数量 | 时间     |
| --------------- | ------------------------------------------------------------ | ---- | -------- |
| Chainlink       | [https://faucets.chain.link/goerli](https://faucets.chain.link/goerli?utm_source=buildspace.so&utm_medium=buildspace_project) | 0.1  | 没有任何 |
| Official Goerli | [https://goerlifaucet.com](https://goerlifaucet.com/?utm_source=buildspace.so&utm_medium=buildspace_project) | 0.25 | 24小时   |
| MyCrypto        | [https://app.mycrypto.com/faucet](https://app.mycrypto.com/faucet?utm_source=buildspace.so&utm_medium=buildspace_project) | 0.01 | 没有任何 |

#### 📈 部署到 Goerli 测试网

我们需要更改我们的`hardhat.config.js`文件。您可以在智能合约项目的根目录中找到它。

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
    networks: {
      goerli: {
        url: "YOUR_QUICKNODE_API_URL",
        accounts: ["YOUR_PRIVATE_GOERLI_ACCOUNT_KEY"]
      },
    },
};
```

**注意：不要将此文件提交到 GITHUB。它有你的私钥。你会被黑客攻击和抢劫的。这个私钥和你的主网私钥是一样的。**

**如果上传到 Github 或通常使用 git 版本控制，最好保护自己不要将密钥上传到你不想要的地方。首先，最好的方法是不上传你的hardhat配置文件，把它添加到.gitignore。**

另一种保护自己和保持`hardhat.config.js`安全的方法是使用 dotenv。安装它：

```bash
npm install --save dotenv
```

现在我们可以更新 hardhat.config.js 以使用 dotenv：

```javascript
require("@nomicfoundation/hardhat-toolbox");
// Import and configure dotenv
require("dotenv").config();

module.exports = {
  solidity: "0.8.17",
  networks: {
    goerli: {
      // This value will be replaced on runtime
      url: process.env.STAGING_QUICKNODE_KEY,
      accounts: [process.env.PRIVATE_KEY],
    },
    mainnet: {
      url: process.env.PROD_QUICKNODE_KEY,
      accounts: [process.env.PRIVATE_KEY],
    },
  },
};
```

在根项目文件夹中，创建一个`.env`文件并添加您的秘钥。它应该是这样的：

```javascript
STAGING_QUICKNODE_KEY=REPLACE_WITH_ACTUAL_QUICKNODE_URL     // Goerli Quicknode
PROD_QUICKNODE_KEY=BLAHBLAH                                 // Mainnet Quicknode
PRIVATE_KEY=BLAHBLAH
```

最后，添加`.env`到你的`.gitignore`文件中，这样 Git 就会忽略它，这样你的秘钥就不会离开你的电脑！如果您对此感到困惑，只需观看有关它的 YouTube 视频，这很简单！

接下来，从 QuickNode 仪表板获取您的 API URL 并将其粘贴。然后，您将需要您的 **私人**Goerli 密钥（而不是您的公共地址！），您可以从 metamask 中获取并将其也粘贴到那里。

**注意：访问您的私钥可以通过打开MetaMask来完成，将网络更改为“Goerli Test Network”，然后单击三个点并选择“Account Details”>“Export Private Key”**

为什么需要使用私钥？因为为了执行像部署合约这样的交易，你需要“登录”到区块链。而且，您的用户名是您的公共地址，您的密码是您的私钥。这有点像登录 AWS 或 GCP 进行部署。

一旦您完成了配置设置，我们就可以使用我们之前编写的部署脚本进行部署。

从`my-wave-portal` 的根目录运行此命令。请注意，我们所做的只是将其从 `localhost`更改为`goerli`。

```bash
npx hardhat run scripts/deploy.js --network goerli
```

#### ❤️ 部署完成！ 

这是我的输出：

```bash
Deploying contracts with the account: 0xF79A3bb8d5b93686c4068E2A97eAeC5fE4843E7D
Account balance: 3198297774605223721
WavePortal address: 0xd5f08a0ae197482FA808cE84E00E97d940dBD26E
```

复制最后一行已部署合约的地址并将其保存在某处。别弄丢了！稍后您将需要它作为前端:)。你的会和我的不一样。

**你刚刚部署了你的合同。**

实际上，您可以获取该地址，然后[在此处](https://goerli.etherscan.io/?utm_source=buildspace.so&utm_medium=buildspace_project)将其粘贴到 Etherscan中。Etherscan 是一个向我们展示区块链状态并帮助我们查看交易位置的地方。你应该在这里看到你的交易:)。可能需要一分钟才能出现！

例如，[这是](https://goerli.etherscan.io/address/0x957fe7381be45A31967F1EcfAc6Ff001D8AF8D6c?utm_source=buildspace.so&utm_medium=buildspace_project)我的！


```
未完待续...

原文地址：https://buildspace.so/p/build-solidity-web3-app
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)

