---
title: Web3教程：仅仅一个周末，只用Javascript就可以构建出你自己的DAO（2）
description: null
author: 李留白
weight: 0
date: 2022-11-05T02:04:33.315Z
lastmod: 2022-11-05T02:43:22.994Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211032050628.png
---

### 目录

**🏗 开始**

- 🥑 让我们构建一些很酷的东西。
- 🤔 什么是 DAO？

**💻 为 DAO 设置客户端应用程序**

- 🛠 获取客户端代码 + 获取设置。
- 👜 将“连接到钱包”添加到您的 DAO 仪表板。

**😎 创建会员 NFT**
- 🌈 部署你的 NFT 包。
- 😤 部署 NFT 元数据。
- 😲 让用户铸造你的 NFT。

**💎 创建代币+治理**

- 😈 部署 ERC-20 合约。
- 😇 在 DAO Dashboard 上展示代币持有者。
- 👩‍⚖️ 建库+治理。
- ✅ 让用户对提案进行投票。

**🌟收尾工作**
- 🤠 删除您的管理员权限并处理基本错误。
- 🎉 完成并庆祝。

## 🛠 获取客户端代码 + 获取设置。

### 💎 我们要做什么

我们将为人们构建一个网络应用程序：**连接他们的钱包 → 铸造会员 NFT → 接收代币空投 → 并对提案进行实际投票。**Web 应用程序就是我所说的“DAO 仪表板”。这是我们的新成员可以加入的地方，它允许现有成员查看 DAO 正在做什么。

### 🤯 Replit！

我们将使用这个叫做 [Replit](https://replit.com/~?utm_source=buildspace.so&utm_medium=buildspace_project)的东西！它是一个基于浏览器的 IDE，可让我们轻松构建 Web 应用程序并从浏览器中部署它们。这是很合法的。无需设置完整的本地环境并编写命令进行部署，这一切都交给了我们。

在继续之前在 Replit 上注册一个帐户。

我已经创建了一个基本的反应项目，你可以 在**Replit** 上创建它。 **只要去 [这里](https://replit.com/@thirdweb/buildspace-dao-starter-v3?utm_source=buildspace.so&utm_medium=buildspace_project)，在右边你会看到“fork”按钮。** 确保您已登录，然后单击此按钮。

您将在浏览器中克隆我的 repo 和完整的 IDE 以使用代码。一旦它停止加载并显示一些代码。单击顶部的“运行”，一切顺利。

这是我在过去的项目中制作的关于 Replit 的视频：

https://www.loom.com/share/8e8f47eacf6d448eb5d25b6908021035

### 👩‍💻 想在本地工作？获取代码

如果您不想使用 Replit，则不必这样做。

首先前往[此处](https://github.com/buildspace/buildspace-dao-starter?utm_source=buildspace.so&utm_medium=buildspace_project) ，您可以在此处找到起始代码库代码。从这里你要确保你按下页面右上角的“Fork”按钮：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211051009378.png)

很好! 当你分叉这个 repo 时，你实际上是在创建一个与之相同的副本，该副本存在于你的 Github 档案下。所以现在你有了自己版本的代码，你可以随心所欲地编辑。

这里的最后一步是在你的本地机器上实际获得你新分叉的 repo。点击 "代码 "按钮并复制该链接!

到你的终端，然后`cd`到你的项目所在的任何目录。例如，我喜欢把我的项目克隆到我的`Developer`文件夹。这要看你喜欢什么了! 一旦你找到了位置，只需通过运行来克隆 repo。

```plaintext
git clone YOUR_FORKED_LINK
cd buildspace-dao-starter
```

就这样了! 从那里开始，继续运行：

```plaintext
npm install
```

然后:

```plaintext
npm start
```

### 🦊 获取 Metamask

接下来我们需要一个以太坊钱包。有很多这样的，但是，对于这个项目，我们将使用 Metamask。[下载浏览器扩展并在此处](https://metamask.io/download.html?utm_source=buildspace.so&utm_medium=buildspace_project)设置您的钱包 。即使您已经有另一个钱包提供商，现在也只需使用 Metamask。

但是为什么我们需要 Metamask？

出色地。我们需要能够调用位于区块链上的智能合约上的函数。而且，要做到这一点，我们需要有一个包含我们的以太坊地址和私钥的钱包。

这几乎就像身份验证一样。我们需要一些东西来“登录”到区块链，然后使用这些登录凭据从我们的网站发出 API 请求。

因此，为了让我们的网站与区块链对话，我们需要以某种方式将我们的钱包连接到它。一旦我们将钱包连接到我们的网站，我们的网站将有权代表我们调用智能合约。 **请记住，这就像对网站进行身份验证一样。**

所以，继续设置吧！他们的设置流程非常不言自明。

设置好钱包后，请务必将网络切换到“ **Goerli** ”，这是我们将使用的测试网络。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211051023816.png)

## 🤑 得到一些假 $

那里有一些测试网，我们将使用一个名为“Goerli”的测试网，由以太坊基金会运营。

为了部署到 Goerli，我们需要假 ETH。为什么？因为如果您要部署到实际的以太坊主网，您将使用真钱！因此，测试网复制了主网的工作方式，唯一的区别是不涉及真金白银。

为了获得假 ETH，我们必须向网络索取一些。**这个假的 ETH 只能在这个特定的测试网上工作。**你可以通过水龙头为 Goerli 获取一些假的以太坊。你只需要找到一个有效的大声笑。

对于 MyCrypto，您需要连接您的钱包，创建一个帐户，然后再次单击同一链接以请求资金。对于Goerli官方水龙头，如果您登录您的Alchemy帐户，您应该可以获得2倍的金额。

您有几个水龙头可供选择：

| 姓名      | 关联                                                         | 数量 | 时间   |
| --------- | ------------------------------------------------------------ | ---- | ------ |
| mycrypto  | [https://app.mycrypto.com/faucet](https://app.mycrypto.com/faucet?utm_source=buildspace.so&utm_medium=buildspace_project) | 0.01 | 没有   |
| goerli    | [https://goerlifaucet.com/](https://goerlifaucet.com/?utm_source=buildspace.so&utm_medium=buildspace_project) | 0.25 | 24小时 |
| chainlink | [https://faucets.chain.link/goerli](https://faucets.chain.link/goerli?utm_source=buildspace.so&utm_medium=buildspace_project) | 0.1  | 没有   |

[您可以在此处](https://metamask.zendesk.com/hc/en-us/articles/360015289512-How-to-copy-your-MetaMask-account-public-address-?utm_source=buildspace.so&utm_medium=buildspace_project)找到您的公共地址。

一旦你的交易被挖掘出来，你的钱包里就会有一些假的 ETH。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211051024149.png)

## 👜 将“连接到钱包”添加到您的 DAO 仪表板。

### ⛓ 指定你的链和钱包类型

因此，为了让我们的网站与区块链交互，我们需要以某种方式将我们的钱包连接到它。一旦我们将钱包连接到我们的网站，我们的网站将有权代表我们调用智能合约。 **请记住，这就像对网站进行身份验证一样。**

您过去可能已经构建了“连接到钱包”按钮！这一次，我们将使用thirdweb 的前端SDK，这让它变得非常简单。

转到您的 React 应用程序的`src/index.js`并添加以下代码：

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';
import './index.css';
import App from './App';

// Import thirdweb provider and Goerli ChainId
import { ThirdwebProvider } from '@thirdweb-dev/react';
import { ChainId } from '@thirdweb-dev/sdk';

// This is the chainId your dApp will work on.
const activeChainId = ChainId.Goerli;

// Wrap your app with the thirdweb provider
const container = document.getElementById('root');
const root = createRoot(container);
root.render(
  <React.StrictMode>
    <ThirdwebProvider desiredChainId={activeChainId}>
      <App />
    </ThirdwebProvider>
  </React.StrictMode>,
);
```

很简单。我们正在导入thirdweb，然后指定我们正在处理的链的`chainId`，即Goerli！。

然后我们用 `<ThirdwebProvider>`包装所有内容，该provider保存用户经过身份验证的钱包数据（如果他们之前已连接到我们的网站）并将其传递给`App`.

*注意：如果你以前在 dapps 上工作过，或者如果你以前连接过它，请确保你在 Metamask 上断开你的钱包与[https://localhost:3000](https://localhost:3000/?utm_source=buildspace.so&utm_medium=buildspace_project)的连接。*

### 🌟 添加连接到钱包

如果您前往您的网络应用程序，您会看到一个空白的紫色页面。让我们添加一些基本的副本 + 一个按钮，让用户连接他们的钱包。

前往`App.jsx`。添加以下代码。

```jsx
import { useAddress, ConnectWallet } from '@thirdweb-dev/react';

const App = () => {
  // Use the hooks thirdweb give us.
  const address = useAddress();
  console.log("👋 Address:", address);

  // This is the case where the user hasn't connected their wallet
  // to your web app. Let them call connectWallet.
  if (!address) {
    return (
      <div className="landing">
        <h1>Welcome to NarutoDAO</h1>
        <div className="btn-hero">
          <ConnectWallet />
        </div>
      </div>
    );
  }

  // This is the case where we have the user's address
  // which means they've connected their wallet to our site!
  return (
    <div className="landing">
      <h1>👀 wallet connected, now what!</h1>
    </div>);
}

export default App;
```

相当简单！顺便说一句 -如果您在本地运行，请确保您的 Web 应用程序正在使用`npm start`运行。

现在，当您转到您的网络应用程序并单击连接到钱包时，您会看到它弹出 Metamask！授权您的钱包后，您将看到以下屏幕：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211051037278.png)

很好。如果你去你的控制台，你会看到它打印出了你的公共地址。如果您在此处刷新页面，您会看到我们的钱包连接仍然还存在。

如果您过去已经建立了与钱包的连接，您会注意到使用 thirdweb 的客户端 SDK 变得更加容易，因为它可以为您处理常见的边缘情况（例如，在变量中维护用户钱包的状态）。

顺便说一句 - 我在这里`<h1>Welcome to NarutoDAO</h1>`，请把它变成你自己的。不要模仿我！这是你自己的 DAO！

*注意：如果您想测试用户没有连接钱包的情况，也可以随时[断开您的网站与 Metamask 的连接。](https://metamask.zendesk.com/hc/en-us/articles/360059535551-Disconnect-wallet-from-Dapp?utm_source=buildspace.so&utm_medium=buildspace_project)*

> Github源码：https://github.com/buildspace/buildspace-projects/tree/main/JS_DAO/
>
> 原文链接：https://buildspace.so/p/build-dao-with-javascript

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
