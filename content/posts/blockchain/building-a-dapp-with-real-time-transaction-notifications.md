---
title: 构建具有实时交易通知的 dApp
description: null
author: 李留白
weight: 0
date: 2022-10-19T13:04:24.606Z
lastmod: 2022-10-19T13:34:00.018Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210192105021.png
---

在过去的几年里，以太坊上的 dApp 在受欢迎程度和复杂性方面都取得了长足的进步。不幸的是，与 web2 应用程序相比，大多数 dApp 的用户体验仍然不足。

缺少的一个关键部分是事件的实时通知。用户需要立即知道他们的交易何时执行、他们的交易何时失败、他们的拍卖出价何时被接受，或者感兴趣的钱包何时已经加入了一些新的代币。如果没有这些通知，可能会错过交易，忘记关键操作，最终用户最终会放弃您的 dApp。

不幸的是，将这些实时通知构建到您的 dApp 传统上是复杂、耗时且容易出错的。但是现在有了[Alchemy Notify](https://www.alchemy.com/notify)，向您的用户发送实时推送通知以处理重要事件，例如丢失的交易、挖掘的交易、钱包活动，甚至汽油价格的变化，都是简单、简单和可靠的。

在本教程中，我们将看一个示例，说明您的 dApp 如何仅通过几行代码就可以集成 Alchemy Notify 的🔋 功能。

## 概述

1. 示例项目的高级演练

2. 使用 Heroku 构建项目
   1. 克隆[Github 存储库](https://github.com/pileofscraps/alchemy_notify.git)并设置 Heroku
   2. 创建一个[免费的Alchemy帐户](https://alchemy.com/?r=jEzMjQ2OTM4OTY5N)
   3. [‍ Alchemy](https://alchemy.com/?r=jEzMjQ2OTM4OTY5N)通知 API 和注册 Webhook 通知
   4. 插入 Alchemy Webhook Id 和 Auth Key
   5. 部署 Heroku 应用程序！


3. 从头开始构建项目

	1. 创建快速服务器

	2. 创建一个[免费的Alchemy帐户](https://alchemy.com/?r=jEzMjQ2OTM4OTY5N)

	3. [‍Alchemy](https://alchemy.com/?r=jEzMjQ2OTM4OTY5N)通知 API 和注册 Webhook 通知

	4. 插入 Alchemy Webhook Id 和 Auth Key

	5. 创建 dApp 前端

	6. 部署！

## 示例

在我们的示例中，我们将创建一个 dApp，当特定以太坊地址上发生活动时，它会实时通知用户。对于我们的架构，我们将使用[Express](https://expressjs.com/)作为服务器，使用[WebSockets](https://docs.alchemy.com/alchemy/guides/using-websockets)在客户端和服务器之间进行通信，并使用 Alchemy Notify 来监控地址并在该地址有活动时发送推送通知。

> 如果您不想花费真实（主网）以太坊来尝试本教程，您可以使用测试网（如 Rinkeby）遵循相同的流程，这样您就可以在目标地址之间发送交易或从目标地址发送交易以进行测试，而无需支付 gas ⛽

我们的示例 dApp 将执行两个功能：首先，它将注册用户的通知地址，其次，它将在该用户接收或发送交易时向该用户发送通知。

“register a user’s address for notifications”流程如下所示：

1. 用户通过 dApp 连接到他们的钱包
2. 用户点击注册钱包地址进行监控
3. dApp通过WebSockets向服务器发送事件注册地址
4. 服务器调用 Alchemy API 向 Alchemy Notify 注册地址

“send a notification”流程如下所示：

1. 注册地址发生变化 - 他们发送或接收交易
2. Alchemy Notify 使用更改信息调用服务器 webhook
3. 服务器通过 WebSockets 通知客户端地址发生了变化
4. 网站前端显示来自 Websocket 的 JSON 响应，包括所有事务元数据

我们将学习本教程的两个版本：第一个是使用 Heroku 克隆 Github Repo，第二个是从头开始。

## 构建 Heroku 服务的项目

### 1. 设置 Github Repo & Heroku

在本教程中，我们使用 Heroku 来托管服务器和网站；如果您选择使用 heroku，请务必遵循以下所有步骤。（如果您想使用其他提供程序，请参阅“从头开始构建项目”）

#### a) 克隆现有的[Github 存储库](https://github.com/pileofscraps/alchemy_notify.git)

```js
git clone https://github.com/alchemyplatform/Alchemy-Notify-Tutorial

cd alchemy_notify
```

#### b) 安装 Heroku-CLI 并验证/安装依赖项

根据您的操作系统下载[Heroku-CLI](https://devcenter.heroku.com/articles/heroku-cli)

安装后，打开终端并运行heroku login；按照以下命令登录您的 Heroku 帐户。如果您没有 Heroku 帐户，可以注册一个！

运行`node --version`。您必须安装任何大于 10 的 Node 版本。如果您没有它或有旧版本，请安装更新版本的 Node.js。

运行`npm --version`。npm 与 Node 一起安装，因此请检查它是否存在。如果没有，请安装更新版本的 Node：您必须安装任何大于 10 的 Node 版本。如果您没有它或有旧版本，请安装更新版本的 Node.js。

#### c) 启动 Heroku

运行`heroku create`以创建您的 heroku 应用程序。记下终端中弹出的信息，尤其是看起来像 http://xxxxxxxxx.herokuapp.com/ 的 URL 我们将使用它！

### 2. Alchemy 通知 API 和注册 Webhook 通知

首先，让我们看看 Alchemy 的通知是如何工作的。有两种方法可以创建和修改通知：通过[Alchemy Notify 仪表板](https://dashboard.alchemyapi.io/notify)和通过[Alchemy Notify API](https://docs.alchemy.com/alchemy/documentation/enhanced-apis/notify-api)。对于我们的示例，我们将同时使用两者！让我们从查看仪表板开始。

> 如果您还没有，您首先需要[在 Alchemy 上创建一个帐户](https://alchemy.com/?r=jEzMjQ2OTM4OTY5N)。即使是免费版也可以很好地开始使用。

拥有帐户后，转到仪表板并从顶部菜单中选择“通知”。在这里，您将看到可以设置的不同类型的通知：

- Address Activity
- Dropped Transactions
- Mined Transaction
- Gas Price

对于我们的示例，我们将使用[Address Activity](https://docs.alchemy.com/alchemy/guides/using-notify#address-activity)通知，但您应该能够轻松地将任何其他通知替换为您自己的用例。

在仪表板中，您可以创建所有通知，添加要监控的地址，并添加当通知触发时 Alchemy 应与之通信的 webhook URL。Alchemy 将所有相关详细信息发送到此 webhook。您的服务器需要简单地创建 webhook、接收呼叫并根据需要处理信息。

#### a) 通过单击地址活动上的“创建 Webhook”来创建我们的示例通知。

#### b) 输入我们的 webhook URL

- 如果使用 Heroku，请参阅步骤 1 以获取使用 heroku create 生成的 http://xxxxxxxxx.herokuapp.com/ URL
- 如果从头开始，请从您使用的任何服务提供商插入您的自定义 webhook URL。

#### c) 在此处为我们的设置输入一个测试以太坊地址 (0xab5801a7d398351b8be11c439e05c5b3259aec9b)。

（我们将在下一步中通过 API 以编程方式添加我们想要监控的真实的那个地址）。

#### d) 从下拉菜单中选择一个应用程序

确保所选应用位于您要测试的以太坊网络上；如果您在 Rinkeby 上进行测试，请选择一个为其配置的应用程序！

#### e) 点击“创建 Webhook”，我们就完成了！‍

### 3. 插入 Alchemy Webhook Id 和 Auth Key

打开`server.js`文件。更改 server.js 中的第 37 和 43 行以反映您的特定 Alchemy webhook id 和身份验证令牌！

```js
// add an address to a notification in Alchemy

async function addAddress(new_address) {
  console.log("adding address " + new_address);
  const body = { webhook_id: , addresses_to_add: [new_address], addresses_to_remove: [] };
  try {
    fetch('https://dashboard.alchemyapi.io/api/update-webhook-addresses', {
      method: 'PATCH',
      body: JSON.stringify(body),
      headers: { 'Content-Type': 'application/json' },
      headers: { 'X-Alchemy-Token':  }
    })
      .then(res => res.json())
      .then(json => console.log(json));
  }
  catch (err) {
    console.error(err);
  }
}
```

### 4. 部署和测试

```js
git add .                             // to add changes
git commit -m "added Alchemy keys"    // to add a comment
git push heroku master                // to push and deploy your heroku app
```


单击“启用以太坊”以连接到 MetaMask，然后从下拉菜单中选择要连接的钱包。

现在单击“在此地址上启用通知”以注册我们的地址：

我们可以在 Alchemy Dashboard 中确认我们的钱包地址已添加到通知中。

现在，一切就绪，您可以测试您的 dApp！

*🎉 恭喜您部署 dApp！随意编辑您的应用程序，更改其行为，或使前端更漂亮！*

### 5.[结论](https://docs.alchemy.com/alchemy/tutorials/building-a-dapp-with-real-time-transaction-notifications#9-conclusion)

## 从头开始构建项目

在本教程中，我们为允许您运行 Alchemy Notify webhook 的服务器和网站提供了一个通用设置。

### 1.创建Express服务

我们的服务器将是一个简单的 Express node.js 服务器。它将做三件事：

1. 创建一个 WebSocket 服务器与客户端通信
2. 与 Alchemy API 交互以设置/修改通知
3. 创建一个 webhook URL 以接收来自 Alchemy 的实时通知

可以运行此代码，对您的特定本地、AWS 或其他托管服务提供商进行一些自定义，以支持本教程。您需要将 express、path、socket.io 和 node-fetch 安装到您的环境中。

首先，我们需要启动我们的 Express 服务器并为我们的客户端 (index.html) 和我们的 Alchemy webhook 建立路由。这个 Alchemy webhook 允许 Alchemy 向我们的服务器发送实时通知。

### 2. 在你的项目文件夹中创建一个 server.js 文件，并为我们的 webhook 和 web 请求设置适当的路由。



```js
// server.js
// start the express server with the appropriate routes for our webhook and web requests

var app = express()
  .use(express.static(path.join(__dirname, 'public')))
  .use(express.json())
  .post('/alchemyhook', (req, res) => { notificationReceived(req); res.status(200).end() })
  .get('/*', (req, res) => res.sendFile('/index.html'))
  .listen(PORT, () => console.log(`Listening on ${PORT}`))
```



通过上面的设置，我们稍后需要的 webhook URL 是 https://<yoururl.com>/alchemyhook

### 3. 向客户发出通知

如果我们的 webhook 收到通知，我们希望进行任何必要的处理，然后将其发送回客户端。在我们的简单案例中，我们将只使用 WebSockets 向所有客户端发出通知。

```js
// server.js
// notification received from Alchemy from the webhook. Let the clients know.
function notificationReceived(req) {
  console.log("notification received!");
  io.emit('notification', JSON.stringify(req.body));
}
```



### 4.启动WebSocket服务器

让我们启动我们的 WebSockets 服务器。这就是我们与客户 dApp 进行通信的方式。WebSockets 允许我们的客户端 dApp 和我们的 Express 服务器之间进行双向通信。在我们的示例中，我们将使用位于 WebSockets 之上的库[socket.io](http://socket.io/)。

```js
// server.js
// start the websocket server
const io = socketIO(app);
```

### 5. 在 WebSocket 服务器上监听客户端连接和调用

建立 WebSockets 连接后，我们可以监听客户端的连接和断开连接。我们还可以听取客户的特定电话。在我们的示例中，我们希望使用 register address_ 监听想要添加新的以太坊地址以进行监控的客户端。

```js
//server.js
// listen for client connections/calls on the WebSocket server

io.on('connection', (socket) => {
  console.log('Client connected');
  socket.on('disconnect', () => console.log('Client disconnected'));
  socket.on('register address', (msg) => {
    //send address to Alchemy to add to notification
    addAddress(msg);
  });
});
```

### 6. 添加 Alchemy 通知 API 功能

当我们收到添加地址的客户端请求时，我们使用 Alchemy API 将地址注册到我们的 Alchemy 通知中。

（请务必将**<your alchemy token>**替换为您在 Alchemy Dashboard 上找到的身份验证令牌。）_**webhook_id **_是 Alchemy 在我们创建 webhook（在下一步中）时提供的唯一 ID。

```js
//server.js
// add an address to a notification in Alchemy

async function addAddress(new_address) {
  console.log("adding address " + new_address);
  const body = { webhook_id: , addresses_to_add: [new_address], addresses_to_remove: [] };
  try {
    fetch('https://dashboard.alchemyapi.io/api/update-webhook-addresses', {
      method: 'PATCH',
      body: JSON.stringify(body),
      headers: { 'Content-Type': 'application/json' },
      headers: { 'X-Alchemy-Token':  }
    })
      .then(res => res.json())
      .then(json => console.log(json));
  }
  catch (err) {
    console.error(err);
  }
}
```

关于 Alchemy API 的一句话：我们可以在 Alchemy Notify 仪表板中执行的任何操作，也可以通过[Alchemy Notify API](https://docs.alchemy.com/alchemy/documentation/apis/enhanced-apis/notify-api)以编程方式执行。我们可以创建新的通知、修改它们、添加和删除地址等等。例如，这里有一个快速调用来获取我们所有 Alchemy webhook 的列表：

```js
// server.js

async function getWebhooks() {
  try {
    fetch('https://dashboard.alchemyapi.io/api/team-webhooks', {
      method: 'GET',
      headers: { 'Content-Type': 'application/json' },
      headers: { 'X-Alchemy-Token':  }
    })
      .then(res => res.json())
      .then(json => console.log(json));
  }
  catch (err) {
    console.error(err);
  }
}
```

我们的服务器准备好了！这是我们一起创建的整个示例*server.js* 文件：

```js
const express = require('express')
const path = require('path')
const socketIO = require('socket.io');
const PORT = process.env.PORT || 5000
const fetch = require('node-fetch');

// start the express server with the appropriate routes for our webhook and web requests
var app = express()
  .use(express.static(path.join(__dirname, 'public')))
  .use(express.json())
  .post('/alchemyhook', (req, res) => { notificationReceived(req); res.status(200).end() })
  .get('/*', (req, res) => res.sendFile(path.join(__dirname + '/index.html')))
  .listen(PORT, () => console.log(`Listening on ${PORT}`))

// start the websocket server
const io = socketIO(app);

// listen for client connections/calls on the WebSocket server
io.on('connection', (socket) => {
  console.log('Client connected');
  socket.on('disconnect', () => console.log('Client disconnected'));
  socket.on('register address', (msg) => {
    //send address to Alchemy to add to notification
    addAddress(msg);
  });
});

// notification received from Alchemy from the webhook. Let the clients know.
function notificationReceived(req) {
  console.log("notification received!");
  io.emit('notification', JSON.stringify(req.body));
}

// add an address to a notification in Alchemy
async function addAddress(new_address) {
  console.log("adding address " + new_address);
  const body = { webhook_id: , addresses_to_add: [new_address], addresses_to_remove: [] };
  try {
    fetch('https://dashboard.alchemyapi.io/api/update-webhook-addresses', {
      method: 'PATCH',
      body: JSON.stringify(body),
      headers: { 'Content-Type': 'application/json' },
      headers: { 'X-Alchemy-Token':  }
    })
      .then(res => res.json())
      .then(json => console.log(json));
  }
  catch (err) {
    console.error(err);
  }
}
```

webhook id 和 alchemy auth 令牌来自 Alchemy UI，我们在下一步中使用它来创建我们的 webhook！

### 7. 完成Heroku 服务项目的[**第2 步和第 3 步**](https://docs.alchemy.com/alchemy/tutorials/building-a-dapp-with-real-time-transaction-notifications#2-alchemy-notify-api-and-register-webhook-notifications)

### 8. 为你的 dApp 创建前端

最后，我们将构建我们的 dApp。我们的示例客户端非常简单。当然，您需要将此代码适当地集成到您的 dApp 中。

我们的 dApp 是一个简单的 HTML/JavaScript 页面，它做了几件事：

- 连接到我们的 WebSockets 服务器以与服务器通信
- 使用 web3 连接浏览器钱包（如 MetaMask）
- 向服务器发送钱包地址进行监控
- 当服务器收到 Alchemy 通知时从服务器接收通知

注意：您需要两个 JavaScript 库才能运行此代码：socket.io 和 web3。

#### a) 连接到 WebSocket 服务器并监听传入的通知

首先，我们与客户端建立 WebSocket 连接。然后，我们设置一个监听器来接收来自服务器的消息；在这种情况下，我们的地址通知。在本例中，我们将简单地显示通知的内容，但您显然想做一些更有趣的事情：

```js
// connect to WebSocket server and start listening for notifications
let socket = io();
let el;
socket.on('notification', (notificationBody) => {
  console.log("got notification");
  el = document.getElementById('server-notification');
  el.innerHTML = 'Look what just happened!: ' + notificationBody;
});
```

#### b) 向 WebSocket 服务器发送新地址以进行监控

第二段有趣的代码发生在用户单击将他们的以太坊地址添加到受监控地址列表时。当用户单击“在我的地址上启用通知”按钮时，代码会向 WebSockets 发出一个事件，请求注册一个新地址。正如我们在上面看到的，服务器随后接收到此事件并将新地址发送到 Alchemy API 以在 Alchemy 通知中注册，从而关闭我们的循环。

```js
enableNotificationsButton.addEventListener('click', function (e) {
      e.preventDefault();
      console.log("send address");
      if (showAccount.innerHTML) {
        socket.emit('register address', showAccount.innerHTML);
      }
      alert(showAccount.innerHTML + " added to notifications.")
    });
```

这是我们构建的整个示例 index.html 客户端：

```js
// connect to WebSocket server and start listening for notifications
  let socket = io();
  let el;
  socket.on('notification', (notificationBody) => {
    console.log("got notification");
    el = document.getElementById('server-notification');
    el.innerHTML = 'Look what just happened!: ' + notificationBody;
  });




Enable Ethereum
```



## 帐户：

启用此地址的通知

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210192132696.png)

仅此而已！

单击“启用以太坊”以连接到 MetaMask，然后从下拉菜单中选择要连接的钱包。

现在单击“在此地址上启用通知”以注册我们的地址：

我们可以在 Alchemy Dashboard 中确认我们的钱包地址已添加到通知中。

现在，一切就绪，如果我们向我们的地址发送少量测试网 ETH，我们会收到来自客户端的通知，其中包含所有必要的详细信息！请注意，要使用该应用程序，您必须首先单击“启用以太坊”并连接到您想要的 Metamask 地址。然后，您必须单击“启用通知”。然后，webhook 的消息将显示在您的前端！

这是一个简单的示例，但您可以通过多种方式对此进行扩展，以构建对用户实时响应的 dApp。

Fork🍴, build 🏗️ , and design 📝 off this repo!

### 9. 结论

区块链发展迅速，但区块链用户体验仍然令人沮丧，导致用户迅速放弃 dApp。但是，使用 Alchemy Notify，您的用户可以随时了解他们的应用程序使用情况和交易活动并对其充满信心。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
