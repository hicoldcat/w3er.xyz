---
title: 使用 Infura 的 Polygon WebSocket 构建更好的 dapp
description: null
author: 李留白
weight: 0
date: 2023-06-26T14:40:37.549Z
lastmod: 2023-06-26T14:49:25.055Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230626224030.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230626224030.png)

Web3 dapp 通常又慢又笨重。但**以太坊 L2**上的**WebSocket**可以通过将实时、双向、客户端-服务器通信引入 web3 世界来帮助解决这个问题。第 3 方产品（例如 Infura 的 Polygon 上的 WebSockets）可以帮助您创建动态、低延迟的下一代 dapp。

在本文中，我们将详细介绍 WebSocket——它们是什么、它们与传统 HTTP 的比较，以及它们最耀眼的一些用例。最后，我们将编写一个脚本，使用 Infura 的[Polygon Websocket](https://www.infura.io/networks/ethereum/polygon)来获取与我们的加密钱包相关的所有待处理交易的实时更新。

## WebSocket 与 HTTP

大多数网络都是由**HTTP 请求**驱动的，但 HTTP 是一种无状态协议，仅支持单向通信并且是非持久性的。它们通常涉及客户端（应用程序或浏览器）向服务器发送请求，然后服务器发送响应作为回报。

这些请求足以支持大多数网络功能。例如，显示页面最新内容、发表评论、汇款等。

另一方面，WebSockets 在客户端和服务器之间建立持久连接，与轮流进行通信的 HTTP 不同，WebSockets 使代理能够双向实时对话**。**

正如您所猜测的，WebSockets 是 Web 应用程序的首选技术，例如在线多人游戏、Figma 和 Miro 等协作工具、股票市场和实时流媒体应用程序以及推送通知。

然而，WebSockets 在 web3 和区块链领域的使用已经滞后。但他们是需要的！将 WebSocket 与以太坊 L2 的可扩展性相结合，您可以构建受益于即时更新和连续数据流的 Web3 应用程序。

以下是 WebSocket 优于传统 HTTP 的一些用例：

1. 使用实时更新来记录特定智能合约铸造的数字资产/NFT 的数量。
2. 当每个新区块添加到网络时跟踪天然气价格。
3. 跟踪特定钱包的代币余额。
4. 通过跟踪市场交易、订单和最佳出价 (BBO) 来监控 DeFi dapp 市场。

## Infura 的 Polygon WebSocket

那么如何使用 WebSocket 呢？让我们看看 Infura 的 Polygon WebSocket。

在当前版本中，支持两种类型的请求：

- **通过 WebSocket 的状态订阅**(wscat -c wss://)。这些允许您提交 eth_subscribe 请求，该请求创建双向实时连接，可以在客户端和服务器之间推送数据。您可以侦听特定事件，例如特定日志、待处理事务和新头。
- **基于 HTTP (https://) 的无状态 WebSocket**。这些允许您使用 HTTP 连接订阅事件 - 但您只能得到单个响应，因为它不是持久连接。

## 创建实时钱包交易跟踪器

那么让我们开始编写一个示例吧！我们将使用 Infura 的 Polygon WebSocket 支持来创建一个脚本来监控钱包上的实时交易。

### 第 1 步：创建 Infura API 密钥

为了访问 Polygon WebSocket 端点，我们需要[创建一个免费的 Infura 帐户](https://app.infura.io/register)。创建帐户后，您将被重定向到 Infura 主仪表板。单击右上角的**“创建新 API 密钥”**。

对于网络，选择默认的 Web3 API。您可以将密钥命名为任何您想要的名称。

![img](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230626224407.png)

创建密钥后，您将被重定向到项目的仪表板。

在 **“端点”** 选项卡中，向下导航直至找到“Polygon”，然后单击 **“激活附加组件”**。

![img](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230626224439.png)

您将被重定向到计划和计费页面。初学者层（核心层）对于本演练来说已经足够了。

在底部的**网络附加组件**部分中，选择**Polygon PoS**。与上面的核心层一样，它是免费的。您可能需要提供信用卡详细信息（尽管我们不会向您收费）才能完成此请求。

签出后，导航回项目的 Endpoints 部分，您应该会看到 Websockets 选项卡。现在，这将包含 Polygon 主网和测试网（称为Mumbai）的 WSS URL。记下Mumbai WSS URL。它的格式应该是**wss://****[Polygon-mumbai.infura.io/ws/v3](http://polygon-mumbai.infura.io/ws/v3/) <-您的** **API 密钥->**。

接下来，导航回**HTTPS**选项卡并记下 Mumbai HTTP URL。

### 第 2 步：创建 MetaMask 钱包

现在让我们创建一个可以跟踪其交易的钱包。

有很多可用的钱包，但我们还是选择最流行的 MetaMask。您可以[将 MetaMask 钱包](https://metamask.io/download/)作为免费的浏览器扩展安装。

安装后，MetaMask 将引导您完成一系列步骤来设置您的钱包。在此过程中，您将获得一个秘密恢复短语。**将其存放在安全的地方**（最好是数字和物理位置），因为如果您丢失了它，您将无法找回钱包。

创建钱包后，导航到扩展窗口的右上角，然后单击网络下拉列表。从这里，切换到显示测试网络。

从下拉列表中选择“Polygon Mumbai”。这是我们将在本教程的其余部分处理的网络。

![img](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230626224519.png)

如果这是您第一次使用Mumbai网络，您将看到 0 MATIC 余额。不用担心; 本教程不需要将加密货币添加到您的钱包中。

### 第 3 步：安装Python和PIP

[我们将使用 Python 3 构建跟踪器。在此处](https://www.python.org/downloads/)下载并安装本地计算机操作系统所需的软件。

完成后，通过获取 Python 和 pip 的版本号来检查它们是否已正确安装。

```text
python3 –version
pip3 –version
```

### 第 4 步：创建 Python 项目

在我们的计算机上安装了 Python 和 pip 后，让我们创建项目存储库并安装必要的包，即*web3*和*websockets*。

```text
mkdir polygon-ws && cd polygon-ws
pip3 install web3 websockets
touch main.py
```

在您喜欢的代码编辑器（例如 VS Code）中打开存储库。您应该看到一个名为 **[main.py](http://main.py/)** 的文件。

### 第 5 步：编写跟踪器脚本

在该`main.py`文件中，添加以下代码：

```python
import asyncio
import json
import requests
from web3 import Web3
from websockets import connect

# Instantiate HTTP and WSS URLs
WSS_URL = '< INFURA WSS MUMBAI URL >'
HTTP_URL = '< INFURA HTTP MUMBAI URL >'

# Define a HTTP web3 provider
web3 = Web3(Web3.HTTPProvider(HTTP_URL))

# Wallet address you want to track transactions of
wallet = '< METAMASK WALLET ADDRESS >'

async def get_event():
    async with connect(WSS_URL) as ws:

        # Create a websocket connection
        await ws.send('{"jsonrpc": "2.0", "id": 1, "method": "eth_subscribe", "params": ["newPendingTransactions"]}')
        subscription_response = await ws.recv()
        print("Created a new websocket connection:")
        print(subscription_response)

        while True:
            try:
                # Check for all pending transactions
                message = await asyncio.wait_for(ws.recv(), timeout=15)
                response = json.loads(message)
                txHash = response['params']['result']

                # Monitor transactions to a specific address
                tx = web3.eth.get_transaction(txHash)
                if tx["to"] == wallet or tx["from"] == wallet:
                    print("Pending transaction found with the following details:")
                    print("Txn Hash:", txHash)
                pass
            except:
                pass

if __name__ == "__main__":
    loop = asyncio.get_event_loop()
    while True:
        loop.run_until_complete(get_event())
```

这是非常简单明了的代码。但让我们看一下重要的部分：

- 我们使用 Mumbai HTTP URL 定义一个 web3 提供商。该提供程序将用于获取有关我们感兴趣的交易（即涉及我们的钱包的交易）的更多信息。
- 我们使用Mumbai WSS URL 创建 WebSocket 连接，并订阅 Polygon Mumbai 网络上的所有待处理事务。
- 我们检索每笔交易的往返信息，如果涉及我们的钱包，我们会打印交易的详细信息。

其余的都是样板代码，我们实际上不需要了解其细节。

### 第 6 步：跟踪待处理交易

我们都准备好了！让我们使用以下命令运行此脚本：


```text
python3 main.py
```

如果一切顺利，您应该看到如下所示的输出：

```text
Created a new websocket connection:
{"jsonrpc":"2.0","id":1,"result":"0xc68a169a0da44750b0f505a77f7bfd99"}
```

在后台，我们的脚本使用 WebSockets 来跟踪所有待处理的事务。现在让我们使用钱包发起一笔交易，看看我们的脚本是否收到通知。

我们可以通过两种方式做到这一点：

1. 通过水龙头将免费的 MATIC 存入您的钱包。
2. 将 MATIC 发送到另一个钱包。

[对于前者，您可以使用此处](https://mumbaifaucet.com/)提供的水龙头。

一旦触发上述任何交易，几秒钟后，您应该会看到脚本显示交易的详细信息……这意味着它有效！

```text
Pending transaction found with the following details:
Txn Hash: 0x2e83edfba2fbbc8fc2753ab4675f10d204ff3b740ec47cb6c3a9b6f6dd4ebe95
```

[为了进行完整性检查，我们可以在Mumbai Polygonscan](https://mumbai.polygonscan.com/)上搜索此哈希。

![img](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230626224655.png)

## 结论

在 Polygon 上使用 WebSocket 可以提供传统 HTTP API 请求无法比拟的 dapp 用户体验。我们才刚刚开始看到这种实时双向通信所支持的用例。使用 Infura 的 WebSocket 进行尝试，看看您可以构建什么！

祝你有美好的一天！

> 原文：https://johnjvester.hashnode.dev/building-better-dapps-with-infuras-polygon-websocket