---
title: 如何使用 Web3.js 库构建 Web3 登录功能
description: null
author: 李留白
weight: 0
date: 2022-09-12T14:47:42.366Z
lastmod: 2022-09-12T15:12:33.740Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220912224823.png
---

Web3 身份验证是具有公共数据的应用程序中的一项常见且至关重要的功能。它有助于根据当前登录的用户识别谁在访问该网站以及要传递哪些数据。

## 介绍

多年来，互联网从第一个版本的网络 (web1) 发展而来，该版本需要用户名和密码来进行用户身份验证。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220912225427.png)

从 web1，我们移到了 web 的第二个版本 web2。这是在其他平台上使用社交媒体作为识别和身份验证手段的地方，无需手动填写用户名和密码。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220912225607.png)

web2 的主要问题是人们无法控制自己的个人数据。相反，中心机构（例如政府和您已签约的大多数大公司）以他们认为合适的方式持有和管理其用户的数据。

现在，通常称为 web3 的第三版网络是互联网的去中心化版本，每个用户都可以完全控制自己的个人数据，而前几代互联网用户几乎无法控制。

Web3 身份验证只需要用户连接到他们在 web3 平台上的加密钱包，例如 OpenSea。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220912225637.png)

本文演示了如何使用 Web3.js 库将 web3 登录身份验证添加到您的网站。

## 演示

下面是本文最后我们要搭建的web3登录认证系统的演示视频。

[https://youtu.be/Glvg3uX7reE](https://youtu.be/Glvg3uX7reE)

## 先决条件

在我们继续本指南之前，您应该对 JavaScript 有基本的了解并安装基于以太坊的钱包（见**第 2 步**）。

## 什么是 Web3.js？

根据以太坊基金会的说法，Web3.js 是一个库集合，允许您使用 HTTP、IPC 或 WebSocket 与本地或远程以太坊节点进行交互。

Web3.js 可用于应用程序的前端或后端，以从区块链读取数据、进行交易并将智能合约部署到区块链。[您可以在此处](https://web3.hashnode.com/what-is-web3js-an-introduction-into-the-web3js-libraries)阅读 Web3.js 库的介绍。

## 第 1 步 - 安装 Web3.js 库

将 Web3.js 添加到项目中的最快方法是在项目 HTML 文件中包含 Web3.js CDN。

您可以从[此处](https://cdnjs.com/libraries/web3)的 CDNJS 获取它，或者将下面的脚本标签复制到您的 HTML 文件中。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/web3/1.7.1/web3.min.js" integrity="sha512-GKw4QT/RccGJIwQxY3MhyiQ5pHrhQ8SuKFEafV+WcpOvtz7iYFQuQGFCvmGlHLctJTe8KrWU1FqvF7VOkEAJtw==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
```

将 Web3.js 库安装到前端或后端应用程序的另一种方法是使用包管理器`yarn add web3`或`npm install web3`. 这些命令将在您的应用程序中安装 Web3.js 库。

我们将在本 web3 js 教程中使用 Web3.js CDN。

在您的项目目录中，创建一个新`index.html`文件并粘贴以下 HTML 代码：

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <!-- Web3.js LIBRARY -->
    <script
      src="https://cdnjs.cloudflare.com/ajax/libs/web3/1.7.1/web3.min.js"
      integrity="sha512-GKw4QT/RccGJIwQxY3MhyiQ5pHrhQ8SuKFEafV+WcpOvtz7iYFQuQGFCvmGlHLctJTe8KrWU1FqvF7VOkEAJtw=="
      crossorigin="anonymous"
      referrerpolicy="no-referrer"
    ></script>
    <title>Web3 Login</title>
  </head>
  <body></body>
</html>

```

我们已经成功地将 Web3.js 库添加到我们的项目中。

## 第 2 步 - 设置以太坊钱包

为了让用户从我们的应用程序连接到他们的以太坊帐户，他们必须在他们的设备上设置一个基于以太坊的钱包。

以太坊钱包将充当银行应用程序，允许您访问您的银行账户。以太坊钱包可以是物理硬件钱包、桌面钱包、移动钱包应用程序或 Web 浏览器钱包扩展（如 Metamask）的形式。

[在此处](https://web3.hashnode.com/what-is-a-web3-wallet)阅读有关钱包的更多信息。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220912225747.png)

在这个 web3 js 教程中，我们将使用 Metamask，它允许我们通过 Web 浏览器与我们的帐户进行交互。按照此[链接](https://metamask.io/download/)在您的浏览器上安装 Metamask 钱包。

## 第 3 步 - 我们将构建什么 - 思考过程

我们将构建一个登录认证系统，用户可以使用他们的以太坊钱包登录我们的应用程序。

我们的应用程序将具有以下内容：

1. 用户可以连接到他们的钱包的登录部分。
2. 显示登录用户的钱包地址和他们的以太坊账户余额的仪表板部分。

如果没有登录用户，则默认显示登录部分，而一旦用户连接了他们的以太坊帐户，将显示仪表板部分。我们将使用 CSS 显示属性和 JavaScript 切换这两个部分。

## 第 4 步 - 构建登录和仪表板界面

现在我们已经安装了 Web3.js 和以太坊钱包，让我们构建用户可以连接到他们的钱包的界面和一个仪表板，他们将在登录后被重定向。

使用以下代码行更新您的`index.html`文件：

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />

    <!-- Web3.js LIBRARY -->
    <script
      src="https://cdnjs.cloudflare.com/ajax/libs/web3/1.7.1/web3.min.js"
      integrity="sha512-GKw4QT/RccGJIwQxY3MhyiQ5pHrhQ8SuKFEafV+WcpOvtz7iYFQuQGFCvmGlHLctJTe8KrWU1FqvF7VOkEAJtw=="
      crossorigin="anonymous"
      referrerpolicy="no-referrer"
    ></script>

    <!-- CSS STYLES -->
    <link rel="stylesheet" href="index.css" />
    <title>Web3 Login</title>
  </head>
  <body>

    <!-- LOGIN SECTION -->
    <section class="login-section">
      <button class="login-btn">🔓 Log in with Web3</button>
      <span class="instruction">
        Ensure to have an Ethereum based wallet installed i.e MetaMask
      </span>
    </section>

    <!-- DASHBOARD SECTION -->
    <section class="dashboard-section">
      <h2 class="wallet-status">Wallet Connected! 🤝</h2>
      <h3 class="wallet-address-heading">
        ETH Wallet Address:
        <span class="wallet-address"></span>
      </h3>
      <h3 class="wallet-balance-heading">
       ETH Balance:
        <span class="wallet-balance"></span>
      </h3>
      <button class="logout-btn">🔐 Log out</button>
    </section>
  </body>

  <!-- SCRIPT -->
  <script src="./index.js"></script>
</html>

```

接下来，创建一个新`index.js`文件，这是我们稍后将编写功能的地方。

最后，创建一个新`index.css`文件并粘贴以下 CSS 代码行：

```css
/* index.css */
* {
  margin: 0;
  box-sizing: border-box;
}

body {
  background-color: #182e48;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

/* LOGIN SECTION */

.login-section {
  display: flex;
  flex-direction: column;
}

.login-btn {
  background: #21bf96;
  color: #fff;
  padding: 13px 35px;
  font-size: 24px;
  border: none;
  font-weight: 600;
  cursor: pointer;
}

.instruction {
  text-align: center;
  color: #21bf96;
  color: #feba35;
  margin: 1rem 0;
}

/* DASHBOARD SECTION */

.dashboard-section {
  display: none;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.wallet-status {
  font-size: 54px;
  color: #21bf96;
  letter-spacing: 1.5px;
}

.wallet-address-heading,
.wallet-balance-heading {
  color: white;
  letter-spacing: 1.5px;
  margin-top: 1rem;
  text-align: center;
}

.wallet-balance,
.wallet-address {
  color: #feba35;
  letter-spacing: normal;
  display: block;
  margin-top: 1rem;
  background: #000;
  padding: 8px;
  border-radius: 19px;
}

.logout-btn {
  color: white;
  background: #cc0000;
  padding: 13px 35px;
  font-size: 24px;
  border: none;
  font-weight: 600;
  cursor: pointer;
  margin-top: 40px;
}

```

当您`index.html`在浏览器中打开文件时（我使用的是[liveserver](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)），您应该有以下界面。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220912225850.png)

由于没有登录用户，因此默认显示登录部分。我们还提醒用户，他们必须在其设备上安装以太坊钱包（请参阅**第 2 步**中的原因）。

将以下代码复制并粘贴到您的`index.css`文件中以隐藏登录部分并显示仪表板部分：

```css
/* REMOVE AFTER TESTING */
.login-section {
  display: none;
}

.dashboard-section {
  display: flex;
}
```

您的仪表板应如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220912225946.png)

这是一个简单的仪表板，显示连接用户的钱包地址和他们的以太坊钱包余额。

我们将在下一步中继续实现登录功能，而不是手动更新 CSS 显示属性，在此我们将使用 JavaScript 来处理登录和仪表板部分之间的切换（基于应用程序的身份验证状态） .

> 确保删除测试 CSS 样式

## 第 5 步 - 检查用户是否安装了以太坊钱包

我们希望确保用户在其浏览器上安装了以太坊钱包。我们还希望在页面加载后立即提示未安装钱包的用户。

我们将遵循以下思考过程：

1. 创建一个全局范围`userWalletAddress`变量，这是我们将存储用户钱包地址的地方。
2. 在页面加载时，检查用户是否安装了以太坊钱包。
3. 如果用户安装了钱包，则创建一个新的 web3 实例。
4. 否则会提示用户安装钱包。
5. 然后我们会检查浏览器中是否已经存在用户的钱包地址`localStorage`，并将其更新为`userWalletAddress`变量。
6. 最后，我们将调用该`showUserDashboard`函数。

上面的思考过程可以翻译成以下几行代码：

将以下代码复制并粘贴到您的`index.js`文件中：

```js
// 1. Create global userWalletAddress variable
window.userWalletAddress = null;

// 2. when the browser is ready
window.onload = async (event) => {

  // 2.1 check if ethereum extension is installed
  if (window.ethereum) {

    // 3. create web3 instance
    window.web3 = new Web3(window.ethereum);

  } else {

    // 4. prompt user to install Metamask
    alert("Please install MetaMask or any Ethereum Extension Wallet");
  }

  // 5. check if user is already logged in and update the global userWalletAddress variable
  window.userWalletAddress = window.localStorage.getItem("userWalletAddress");

  // 6. show the user dashboard
  showUserDashboard();
};

```

我们将使用以下测试用例来测试我们的实现：

**案例 1 - 未安装以太坊钱包的用户**

我们将使用一个隐身窗口来测试当**没有**安装以太坊钱包的用户尝试使用我们的应用程序时会发生什么。

启动浏览器隐身窗口并访问项目 URL：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/eqCBnqYTr.gif)

系统会提示您此消息：**请安装 MetaMask 或任何以太坊扩展钱包。**

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220912230113.png)

案例1通过✅

**案例 2 - 安装了以太坊钱包的用户**

在安装了以太坊钱包的普通浏览器窗口中启动您的项目，因此现在您不应该收到提示消息。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/w4cTpxlAY.gif)

案例2通过✅

但是`showUserDashboard`第 6 步中的函数还没有定义，我们稍后会创建它。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220912230204.png)

## 第 6 步 - 添加 Web3 登录功能

对于登录功能，我们的主要兴趣是用户的钱包地址，我们将从以太坊钱包中用户选择的账户中获取该地址。我们只对选定的以太坊钱包地址感兴趣，因为用户可以在他们的钱包上拥有多个以太坊账户。

我们将按照下面的思路来实现我们的以太坊登录功能：

1. 首先，创建一个异步`loginWithEth`函数并检查是否启用了 web3 实例。
2. 如果启用了 web3 实例，我们将使用该`window.ethereum`方法触发 Metamask 钱包，供用户选择以太坊帐户。
3. 选择帐户后，我们将`userWalletAddress`使用用户选择的钱包地址更新全局变量。
4. 接下来，我们将选择的帐户存储在`localStorage`.
5. 然后，我们将用户重定向到他们的仪表板。
6. 最后，我们将`loginWithEth`使用 click 事件侦听器将该函数绑定到登录按钮。

上面的思考过程可以翻译成以下几行代码：

`index.js`使用以下代码更新您的文件：

```js
// 1. Web3 login function
const loginWithEth = async () => {
  // 1.1 check if there is global window.web3 instance
  if (window.web3) {
    try {
      // 2. get the user's ethereum account - prompts metamask to login
      const selectedAccount = await window.ethereum
        .request({
          method: "eth_requestAccounts",
        })
        .then((accounts) => accounts[0])
        .catch(() => {
          // 2.1 if the user cancels the login prompt
          throw Error("Please select an account");
        });

      // 3. set the global userWalletAddress variable to selected account
      window.userWalletAddress = selectedAccount;

      // 4. store the user's wallet address in local storage
      window.localStorage.setItem("userWalletAddress", selectedAccount);

      // 5. show the user dashboard
      showUserDashboard();

    } catch (error) {
      alert(error);
    }
  } else {
    alert("wallet not found");
  }
};

// 6. when the user clicks the login button run the loginWithEth function
document.querySelector(".login-btn").addEventListener("click", loginWithEth);
```

*在生产就绪的应用程序中，您可能希望将用户的以太坊钱包地址存储在您的数据库中，以作为用户的唯一标识符。*

在我们继续测试我们的实现之前，让我们`showUserDashboard`在下一步中创建函数。

## 第 7 步 - 处理重定向

在这一步中，我们将在登录部分和用户仪表板部分之间实现重定向（切换）。

为了处理重定向，我们将检查用户是否连接到他们的钱包地址。如果它们未连接，我们将显示登录部分，一旦连接，我们会将用户重定向到仪表板部分。

我们还想更新网页的页面标题以向用户显示网站的状态。

使用以下代码行更新您的`index.js`文件：

```js
// function to show the user dashboard
const showUserDashboard = async () => {

  // if the user is not logged in - userWalletAddress is null
  if (!window.userWalletAddress) {

    // change the page title
    document.title = "Web3 Login";

    // show the login section
    document.querySelector(".login-section").style.display = "flex";

    // hide the user dashboard section
    document.querySelector(".dashboard-section").style.display = "none";

    // return from the function
    return false;
  }

  // change the page title
  document.title = "Web3 Dashboard 🤝";

  // hide the login section
  document.querySelector(".login-section").style.display = "none";

  // show the dashboard section
  document.querySelector(".dashboard-section").style.display = "flex";

  // show the user's wallet address
  // showUserWalletAddress();

  // get the user's wallet balance
  // getWalletBalance();
};
```

如果您在应用该`showUserDashboard`功能后刷新应用程序，您应该能够连接到您的 Metamask 钱包并被重定向到仪表板部分。也看看标题栏！🤝

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/fxyAPha-N.gif)

在下一步中，我们将创建`showUserWalletAddress`在仪表板上显示用户钱包地址的函数。

## 第 8 步 - 显示用户以太坊钱包地址

在这一步中，我们将创建`showUserWalletAddress`负责在仪表板上显示用户钱包地址的函数。已连接用户的钱包地址已在`userWalletAddress`全局变量中可用。

取消注释并使用以下代码`showUserWalletAddress();`更新您的：`index.js`

```js
// show the user's wallet address from the global userWalletAddress variable
const showUserWalletAddress = () => {
  const walletAddressEl = document.querySelector(".wallet-address");
  walletAddressEl.innerHTML = window.userWalletAddress;
};
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220912230334.png)

现在，刷新页面后，您的以太坊钱包地址将显示在您的仪表板上。

## 第 9 步 - 显示用户以太坊余额

在这一步中，我们将创建`getWalletBalance()`函数。此功能将获取用户的余额并将其显示在仪表板上。

我们将使用该`window.web3.eth.getBalance(ethWalletAddress);`方法来查询连接用户的以太坊账户余额。

取消注释该`getWalletBalance();`函数并将下面的代码应用到您的`index.js`文件中。

```js
// get the user's wallet balance
const getWalletBalance = async () => {
  // check if there is global userWalletAddress variable
  if (!window.userWalletAddress) {
    return false;
  }

  // get the user's wallet balance
  const balance = await window.web3.eth.getBalance(window.userWalletAddress);

  // convert the balance to ether
  document.querySelector(".wallet-balance").innerHTML = web3.utils.fromWei(
    balance,
    "ether"
  );
};
```

从上面的`getWalletBalance`函数中，我们正在检查是否有连接的用户，因为需要以太坊钱包地址来获取以太坊账户余额。

然后，我们通过将连接的钱包地址从我们的`window.userWalletAddress`全局变量作为参数传递给该`getBalance`方法来查询用户余额。

最后，我们将返回的“Wei”余额转换为“ether”并将其显示在仪表板上。

> 以太（Eth）的最小单位是“Wei”。该`fromWei`方法是 web3 中的一种实用方法，可将任何“Wei”值转换为以太币。

刷新网页后，应该会显示您的以太坊余额。

如下所示，虽然我的余额为零🤧

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220912230402.png)

## 第 10 步 - 添加 Web3 注销功能

本 web3 教程的最后一步是在我们的应用程序中实现注销功能。我们如何从 web3 应用程序中注销？这并不复杂，我们所要做的就是将全局`window.userWalletAddress`变量设置为`null`并`userWalletAddress`从浏览器中删除`localStorage`。

此过程也类似于 web2 [JWT 令牌](https://jwt.io/introduction)注销。

以下代码行将处理 web3 注销功能：

```js
// web3 logout function
const logout = () => {
  // set the global userWalletAddress variable to null
  window.userWalletAddress = null;

  // remove the user's wallet address from local storage
  window.localStorage.removeItem("userWalletAddress");

  // show the user dashboard
  showUserDashboard();
};

// when the user clicks the logout button run the logout function
document.querySelector(".logout-btn").addEventListener("click", logout);
```

就是这样！要测试注销功能，请单击“注销”按钮。您应该被重定向到登录部分，如下所示。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/hgWpTD79W.gif)

恭喜🎉🎉🎉

您已经成功地学习了如何将 Web3.js 登录添加到您的应用程序。这是我们最终的应用程序演示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/EWkN7pJkS.gif)

如果您在此处遇到任何问题，请查看[完整的源代码](https://github.com/unclebay143/web3-authentication)。

## 结论

在本教程中，我们使用 Web3.js 库创建了一个登录身份验证系统，允许用户使用他们的以太坊钱包连接到您的网站。您学习了如何获取用户的以太坊账户余额，将其转换为以太 (Eth) 并将其显示给用户。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
