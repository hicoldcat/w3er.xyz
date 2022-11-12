---
title: 使用 Node.js 为 IPFS 内容创建 HTTP 代理
description: null
author: 李留白
weight: 0
date: 2022-11-12T01:23:11.303Z
lastmod: 2022-11-12T01:49:05.069Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211120929605.png
---

在本文中，我们将演示如何创建 Node.js 服务器以通过 clearnet 访问 IPFS 内容。在我们解释如何做到这一点之前，让我们先定义一下关键术语，并创建一些上下文来解释为什么这很重要。

## 什么是clearnet？

clearnet 本质上是可公开访问的互联网。它是指使用所有浏览器支持的标准协议在公共网络上消费的所有 Web 内容，例如 HTTP、[WebSocket（用于实时通信）](https://blog.logrocket.com/websockets-tutorial-how-to-go-real-time-with-node-and-react-8e4693fbf843/)和 WebRTC（用于大多数音频和视频流平台）。

这些协议通常需要中央服务器和服务提供商才能工作，这有可能成为审查的载体，特别是在对言论自由和隐私有限制的国家。

### clearnet之外

尽管并非所有浏览器都支持它们，但还有许多其他非标准网络协议。示例包括：

- **Onion routing**：一种通过网络实现匿名通信的技术，特别是在 Tor 项目中实现
- **Gemini**：一种通信协议，力求成为 Gopher 和 HTTP 的替代品。有许多 Gemini 客户端可用
- **星际文件系统（IPFS）**：一种用于共享数据的协议和点对点网络。流行的基于 Chromium 的 Brave 浏览器支持 IPFS

## IPFS 解释

与 BitTorrent 类似，IPFS 是一种用于在分布式文件系统中存储和共享数据的协议。然而，关键区别在于 IPFS 旨在创建一个单一的全球网络。在 IPFS 中，为托管的单个文件分配了一个内容 ID (CID)，用于访问它。

为了说明区别，在 HTTP 中，我们使用类似`https://www.google.com` 的 URI ，然后将其发送到 DNS 服务器。服务器识别资源的 IP 地址并为资源提供服务。但是，如果该主机已关闭或无法访问，则该资源就……消失了。

在 IPFS 中，我们使用类似`ipfs://xjd9809bnuiue900sdfnuiwerwwer` 的 CID ，通过搜索网络中所有参与者托管的分布式哈希表 (DHT) 来识别它。不是识别资源的单个提供者，而是识别拥有资源副本的所有主机，并且我们从所有主机下载资源的一部分。

与任何互联网协议一样，IPFS 的设计有利有弊。

### IPFS 优点

- 将内容添加到 DHT 后，只要有一个主机连接到 IPFS 网络并带有文件的副本，就可以在网络上访问该文件
- 随着时间的推移，文件的可访问性会增加，因为每个访问该文件的用户都会成为另一个主机来检索该文件
- 没有外部实体可以单独删除已添加到网络的文件

### IPFS 缺点

- 当您最初将内容添加到 IPFS 时，您是唯一的主机。因此，在一开始，可访问性就受到限制
- 您不能更改文件，因此如果您需要修改文件，则必须添加具有新 CID 的文件的新副本并提醒其他用户转到新 CID
- 目前并非所有浏览器都支持

## IPFS 入门

让我们安装 IPFS CLI 并向 IPFS 添加一些内容。在我们进行时，您可以[参考 IPFS 文档。](https://docs.ipfs.io/install/ipfs-desktop/)

首先，安装 IPFS。对于 Linux 和 Mac，您可以使用`brew install ipfs`在 Homebrew 安装它。完成后，请务必阅读输出以获取重要信息；对于 Linux，它应该为您提供运行守护程序的确切路径。

安装、运行`ipfs init`后，再次确保阅读输出以了解一些重要细节——您可能希望将其保存在安全的地方。输出应该共享一个 cat 命令以查看文档；运行该命令以确认一切都已设置。

使用`ipfs init` 启动守护程序。要将文件添加到 IPFS，只需使用`ipfs add FILENAME`命令，您就会收到 CID。就这么简单！

### 消费 IPFS 内容

要使用 IPFS 内容，只需在命令行中运行`ipfs cat /ipfs/CONTENTID`以检索内容。

要通过浏览器使用 IPFS 内容，您需要使用[Brave](https://brave.com/)，它允许您查看来自 IPFS 的内容。在浏览器中，使用`ipfs://CONTENTID`此模式访问内容。例如，这里有一个指向“Hello, World!”的链接。我发布到 IPFS 的文本文件：`ipfs://QmWATWQ7fVPP2EFGu71UkfnqhYXDYH566qy47CnJDgvs8u`

## 通过 HTTP 提供 IPFS 内容

IPFS 非常酷，但大多数日常互联网用户不想下载替代浏览器或使用 CLI 工具来浏览网络上的资源。因此，也许您想通过创建代理应用程序来获取主流浏览器中的内容，从而将您的 IPFS 内容交付给 clearnet 用户。让我们用 Node.js 来做这件事。

> **注意**，这个演示应用程序必须在同时运行 IPFS 服务器的机器上运行。这意味着您需要使用虚拟专用服务器 (VPS) 进行部署，您可以在其中运行与 Node.js 应用程序并行的 IPFS 服务器。

### 设置应用程序

首先创建一个新文件夹（您可以为其命名`ipfs-proxy`或类似名称），然后将目录更改为该文件夹。通过运行`touch server.js`创建一个`server.js`文件，然后使用 `npm init -y`启动一个新的 Node.js 项目。最后，通过运行`npm install fastify ipfs-http-client`安装 Fastify（尽管[任何 Web 框架都可以](https://blog.logrocket.com/comparing-top-node-js-frameworks-frontend-developers/)）和 ipfs-http-client 。

### 让Node服务器运行

将以下代码添加到您的`server.js`文件中：

```js
// import fastify
const fastify = require("fastify");

// create the server object
const server = fastify({ logger: true });

// example route
server.get("/", async (request, reply) => {
  return { message: "The Server is Working" };
});

// start server function
const start = async () => {
  await server.listen(3000).catch((err) => {
    fastify.log.error(err);
    process.exit(1);
  });
  console.log("Listening on port 3000")
};

// turn server on
start();
```

运行`node server.js`文件，在浏览器中转到`localhost:3000`，并确保你看到该信息。在终端中用`Control+C`关闭服务器。

### 使用ipfs-http-client

我们需要将我们的server.js更新为下面的代码，但首先让我们走过我们正在做的事情。

首先，我们将 ipfs-http-client 库导入为`ipfs`. 然后我们创建一个连接到`localhost:5001`的客户端，这是我们的本地 IPFS 服务器应该运行的默认端口。

我们使用客户端的`get`方法来获取数据CID。该函数返回一个被`AsyncGenerator` 包裹的`Uint8Array`（有趣！）的字节。

我们使用`for await`循环遍历生成器，然后使用文本编码器将字节数组解码为字符串。在循环之后，生成的文本中有几个空字符，所以我们使用`string.replace`将他们删除. 最后，我们将结果字符串作为 JSON 发回。

```javascript
// import fastify
const fastify = require("fastify");
// import ipfs-http-client
const ipfs = require("ipfs-http-client")

// connect to local ipfs node
const client = ipfs.create()

// create the server object
const server = fastify({ logger: true });

// example route
server.get("/", async (request, reply) => {

  // fetch the content from ipfs
  const result = await client.get("QmWATWQ7fVPP2EFGu71UkfnqhYXDYH566qy47CnJDgvs8u")

  // create a string to append contents to
  let contents = ""


  // loop over incoming data
  for await(const item of result){
      // turn string buffer to string and append to contents
      contents += new TextDecoder().decode(item)
  }

  // remove null characters
  contents = contents.replace(/\0/g, "")

  // return results as a json
  return { message: contents };
});

// start server function
const start = async () => {
  await server.listen(3000).catch((err) => {
    fastify.log.error(err);
    process.exit(1);
  });
  console.log("Listening on port 3000")
};

// turn server on
start();
```

请注意，结果并不是世界上最友好的。产生的字符串包括CID和一个时间戳；换句话说，附加信息与我的 "Hello, World!"文件的内容一起。如果这是一个HTML文件，我可能要做一些额外的清理工作来删除所有非HTML的内容，但至少我们得到了内容。

因此，从理论上讲，我们可以将这个应用程序托管在一个也安装了IPFS的VPS上，在clearnet/HTTP上的用户可以看到我们的内容，而无需下载一个特殊的浏览器或命令行工具。。

## 结论

IPFS提出了一些非常有趣的想法，即互联网如何能够更加分散和自由，但它确实以牺牲一些便利性为代价。然而，通过巧妙地使用Node.js，我们可以开始为大众弥补这一差距。

>原文：https://blog.logrocket.com/using-node-js-http-proxy-ipfs-content/
>翻译：李留白

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
