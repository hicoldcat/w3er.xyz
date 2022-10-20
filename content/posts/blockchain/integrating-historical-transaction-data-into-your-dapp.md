---
title: 将历史交易数据集成到您的 dApp
description: null
author: 李留白
weight: 0
date: 2022-10-20T10:55:25.136Z
lastmod: 2022-10-20T11:17:42.219Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210201855173.png
---

在过去的几年里，以太坊上的 dApp 在复杂性、深度和广度上都在增长。但是其中一个缺失的部分是对历史区块信息的高效查询。使用标准的以太坊 JSON-RPC 方法，开发人员要么需要维护包含大量区块链历史记录的集中式数据库，要么需要在很长一段时间内重复查询区块，以便废弃特定地址的整个交易历史记录。这两个选项使钱包提供商等用户提供信息的过程变得复杂，例如特定用户在区块链上的交互历史。如果无法轻松访问这些信息，开发人员必须依赖昂贵、缓慢的方法来限制其应用程序的功能集。

虽然在 dApp 中构建历史查询传统上是复杂、耗时且容易出错的，但[Alchemy Transfers API](https://www.web3.university/alchemy/documentation/enhanced-apis/transfers-api)允许开发人员在简单的交易中查询历史钱包活动、代币转账和其他账户驱动的交易。  

在本教程中，我们将看一个示例，说明您的 dApp 如何通过几行代码集成 Alchemy Transfers API 的强大功能。

## 示例

对于我们预先打包的示例，我们将创建一个 dApp 仪表板，用于跟踪特定地址的交易活动，在后端处理它，然后将其实时推送到前端。

### **问题陈述：** 🐕

Akita Inu (AKITA) 和 Shiba Inu (SHIBA) 的创建者选择向 Vitalik Buterin 赠送大量以狗为主题的代币，而不是将烧毁的代币发送到 0xdead，这是大多数代币燃烧的建议。然而，Vitalk 并没有保留 meme 代币，而是选择将代币批量出售给 Uniswap，将它们换成 ETH，并将 ETH 收益和其他无法出售给慈善机构的代币都捐赠了。查看[这篇文章](https://www.theblockcrypto.com/post/104676/vitalik-buterin-donates-more-than-60m-to-charity-after-selling-meme-tokens-including-shiba-inu)了解更多信息。

这笔意外之财的幸运接受者之一是[Gitcoin](https://gitcoin.co/)社区multisig帐户（multisig代表多重签名，它是一种特定类型的数字签名，可以让两个或多个用户登录以访问/控制一个单个钱包地址）。由于 AKITA 代币转让的市场估值约为 4.5 亿美元，没有一个市场能够吸收代币的单次销售，如果多重签名试图这样做，AKITA 的价格将会暴跌。因此，Gitcoin 社区决定实施一个代币“救援”流程，每卖出 1 个 AKITA 到公开市场，就会烧掉 13 个 AKITA 代币。  

我们的示例仪表板通过跟踪 Gitcoin 多重签名中持有的 AKITA 代币总数以及作为救援合同的一部分已被烧毁的代币总数来跟踪 AKITA 及其救援过程的故事。  

我们创建的仪表板执行两个功能。刷新页面或用户点击后，webapp 会向 Alchemy 发出请求，查询 Gitcoin 多重签名地址和[“救援”智能合约](https://etherscan.io/address/0x0B71C0E0F03e8546e682c0107f9c771D190A0F1e#code)。收到响应后，仪表板会解析 JSON 对象并对其进行处理。最终，网站前端显示已处理的项目。

## 选项 1：使用 Heroku 构建 dApp

### 1. 设置 Github Repo & Heroku

#### a) 克隆现有的[Github 存储库](https://github.com/pileofscraps/alchemy_notify.git)

导航到您的命令行并键入：

```js
git clone https://github.com/alchemyplatform/Alchemy-Transfers-Tutorial

cd Alchemy-Transfers-Tutorial
```

#### b) 安装 Heroku-CLI 并验证/安装依赖项

在本教程中，我们使用 Heroku 来托管服务器和网站；如果您选择使用 Heroku，请务必遵循以下所有步骤。如果您想使用其他提供程序，请参阅选项 2：从头开始构建项目

- 根据您的操作系统下载[Heroku-CLI 。](https://devcenter.heroku.com/articles/heroku-cli#download-and-install)确保根据您使用的计算机环境下载正确的版本！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210201859854.png)

- 安装后，导航到您刚刚 git 克隆的文件并在命令行中运行以下命令以登录到您的 Heroku 帐户。

```js
heroku login
```

按照命令登录到您的 Heroku 帐户。如果您没有 Heroku 帐户，可以免费[注册一个](https://www.heroku.com/)！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210201900610.png)

- 让我们确认您已下载正确版本的 Node.js。在你的命令行运行：

```js
node --version
```

运行命令后，您将看到版本号出现，或者您将收到一条错误消息，告诉您没有安装 Node。  

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210201900201.png)

请注意，Heroku 要求用户安装任何大于 10 的 Node 版本。如果您没有它或有旧版本，[请安装更新版本的 Node](https://nodejs.org/en/download/)。

- 最后，让我们确认我们也正确安装了 npm。

再次在命令行中，运行以下命令：

```js
npm --version
```

npm 与 Node 一起安装，因此请检查它是否存在。如果没有，[请安装更新版本的 Node ](https://nodejs.org/en/download/)**。**

#### c) 启动 Heroku

现在我们已经确认 Heroku 具有运行所需的所有依赖项，让我们通过运行以下命令来创建我们的 Heroku 应用程序：

```js
heroku create
```

然后，您应该会看到类似这样的弹出窗口：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210201901271.png)

确保记下弹出的 URL **http://xxxxxxxxx.herokuapp.com/。**我们将使用它，因为它是我们示例仪表板的 URL！

有关设置要为 Heroku 配置的环境的更详细说明，请查看[官方 Heroku 文档](https://devcenter.heroku.com/articles/getting-started-with-nodejs?singlepage=true)。

### 2. 创建一个[免费的Alchemy帐户](https://alchemy.com/?r=jEzMjQ2OTM4OTY5N)

如果您还没有，您首先需要[在 Alchemy 上创建一个帐户。](https://alchemy.com/?r=jEzMjQ2OTM4OTY5N)免费版本可以很好地开始使用！

### 3. 集成 Alchemy Transfers API

拥有帐户后，您现在可以使用`alchemy_getAssetTransfers`允许您查询转账的方法。

对于我们的 Akita Token Rescue 仪表板，这是我们使用的特定请求：

```js
{
  "jsonrpc": "2.0",
  "id": 0,
  "method": "alchemy_getAssetTransfers",
  "params": [
      {
      "fromBlock": "0xC30965",
      "toBlock": "latest",
      "fromAddress": "0xde21F729137C5Af1b01d73aF1dC21eFfa2B8a0d6",
      "toAddress": "0xDead000000000000000000000000000000000d06",
      "contractAddresses": ["0x3301Ee63Fb29F863f2333Bd4466acb46CD8323E6"],
      "category": ["external","token"]
    }
  ]
}
```

`fromBlock`:`0xC30965`是部署合约 ( `0x3301Ee63Fb29F863f2333Bd4466acb46CD8323E6`) 的区块，我们不需要查看任何更早的区块，不会有任何活动。

`toBlock`:`latest`最近的区块，我们想要所有的交易活动

`fromAddress`:`0xde21F729137C5Af1b01d73aF1dC21eFfa2B8a0d6`是 Gitcoin 多重签名地址

`toAddress`:`0xDead000000000000000000000000000000000d06`是用于销毁代币的地址

`contractAddresses`:`0x3301Ee63Fb29F863f2333Bd4466acb46CD8323E6`存储秋田救援逻辑的合约

`category`:是`["external","token"]`我们要监控的传输事件类型

###  4. 插入 Alchemy API 密钥

导航到 main.py 以查找您的 API 密钥的使用位置。请注意，要获取 Alchemy API 密钥，您需要在 Alchemy 仪表板中[创建一个应用程序。](https://www.youtube.com/watch?v=tfggWxfG9o0)

> **注意：**对于本教程，我们需要使用指向以太坊主网的应用程序，因为我们的合约部署在主网上。

我们的 Heroku 应用程序配置为接受 Alcehmy API 密钥作为环境变量，以鼓励安全的最佳实践。创建帐户后获得 API 密钥后，在之前运行 heroku create 的同一文件目录中运行以下命令，以在 Heroku 本身中设置环境变量。

```js
heroku config:set KEY=""
```

> **注意：**当您从仪表板复制密钥时，您应该得到一个完整的 URL，如下所示：[https ://eth-mainnet.alchemyapi.io/v2/kXtBl52Cr0hNbOn0rI2up7lhUiGk_2eS](https://eth-mainnet.alchemyapi.io/v2/kXtAc2qCG7HnbON0fI4ho3NHUiWj_2cS)

> 您的密钥只是 URL 中的最后一部分：[kXtBl52Cr0hNbOn0rI2up7lhUiGk_2eS](https://eth-mainnet.alchemyapi.io/v2/kXtAc2qCG7HnbON0fI4ho3NHUiWj_2cS)

您已设置 API 密钥！要确认它已正确配置，您可以使用以下命令查看 Heroku 上的环境变量：`heroku config`

您的 Heroku 环境变量应如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210201901768.png)

### 5. 部署 Heroku 应用！

现在，我们进入了最后的步骤！确认您已导航到 Heroku 项目所在的文件。在那里，运行以下命令以将更改保存在 Git 上并部署应用程序。

```js
git add .                             // to add changes
git commit -m "added Alchemy keys"    // to add a comment
git push heroku master                // to push and deploy your heroku app
```

这样，我们将所有更改推送到 Heroku，现在可以实时查看我们的仪表板。在您的项目已推送到的 URL 处打开 Heroku 应用程序。  

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210201901450.png)

请注意，刷新页面后，我们会看到仪表板的数据已更新。同样，如果我们单击“刷新数据”按钮，我们会发现如果有人与救援合约交互，显示的余额也会发生变化。

现在，一切就绪，您可以测试您的 dApp！  

🎉恭喜您部署 dApp！随意编辑您的应用程序，将目标地址指向其他有趣的合约/公众人物，或使前端更漂亮！

## 选项 2：从头开始构建 dApp

在本教程中，我们为 Python webapp 提供了一个通用设置，允许您查询 Alchemy Transfers API、处理 JSON 响应，然后将数据推送到前端。

### 1-2. 完成上面Heroku 项目的第2 步和第 3 步

### 3. 使用您的 Alchemy API 密钥配置您的应用程序

为了遵循安全的最佳实践，我们鼓励您将 API 密钥存储在`.env`文件中。获取我们从上一步生成的密钥，创建并保存`.env`为以下内容：

> 提示：如果您还没有安装 dotenv 包，则需要安装。请按照[以下步骤](https://www.web3.university/alchemy/tutorials/sending-txs#5-install-dotenv)了解更多信息。

```js
KEY=""
```

恭喜！您已设置 API 密钥！  

### 4. 创建后端处理脚本

在本教程中，我们使用 Python / React 来发送我们的 API 请求、解码 JSON 响应并对其进行处理。

#### a) 安装必要的依赖项

我们的教程主要是用 Python 构建的，我们使用[Flask](https://flask.palletsprojects.com/en/2.0.x/)来为我们的应用程序提供动力。确保您的环境中有以下依赖项以进行跟进。

安装必要依赖项的一种简单方法是创建一个`requirements.txt`文件，其中包含以下项目。

```js
Click==7.0
Flask==1.1.1
flask-ldap3-login==0.9.16
Flask-Login==0.4.1
Flask-WTF==0.14.2
gunicorn==19.9.0
itsdangerous==1.1.0
Jinja2==2.10.1
ldap3==2.6.1
MarkupSafe==1.1.1
pyasn1==0.4.7
pyasn1-modules==0.2.6
requests==2.26.0
Werkzeug==0.16.0
WTForms==2.2.1
web3==5.20.0
```

然后，运行以下命令来安装软件包：

```js
pip install requirements.txt
```

#### b) 创建一个名为 main.py 的文件

这是我们的后端处理脚本所在的地方。我们还将添加一些安装并在顶部定义我们的 Alchemy 键变量：

```js
from flask import Flask, jsonify, render_template, request
from forms import DataTriggerForm
import os
import json
from web3 import Web3
import requests

ALCHEMY_KEY = os.environ.get('KEY')
```

#### c) 定义 Web3 函数

在我们的教程中，我们总共有两个不同的原生 Web3 调用。特别是，我们称`get_block`和`balanceOf`。

要调用`get_block`以获取当前块号，我们使用将以下函数添加到`main.py`：

```js
w3 = Web3(Web3.HTTPProvider('https://eth-mainnet.alchemyapi.io/v2/'+ALCHEMY_KEY))

def get_block_num():
    return str(w3.eth.get_block('latest')['number'])
```

要调用`balanceOf`以获取 ERC20 代币的当前余额，我们需要在我们通常在 Web3 中调用的代码之上添加一段额外的代码来获取地址的以太坊余额。为此，我们将在`main.py`中添加以下函数：

```js
w3 = Web3(Web3.HTTPProvider('https://eth-mainnet.alchemyapi.io/v2/'+ALCHEMY_KEY))

# includes the standard ERC20 ABI info
ERC20_ABI = json.loads('[{"constant":true,"inputs":[],"name":"name","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_spender","type":"address"},{"name":"_value","type":"uint256"}],"name":"approve","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"totalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_from","type":"address"},{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"transferFrom","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"decimals","outputs":[{"name":"","type":"uint8"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"_owner","type":"address"}],"name":"balanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"symbol","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"transfer","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_owner","type":"address"},{"name":"_spender","type":"address"}],"name":"allowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_from","type":"address"},{"indexed":true,"name":"_to","type":"address"},{"indexed":false,"name":"_value","type":"uint256"}],"name":"Transfer","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_owner","type":"address"},{"indexed":true,"name":"_spender","type":"address"},{"indexed":false,"name":"_value","type":"uint256"}],"name":"Approval","type":"event"}]')  # noqa: 501

akita = w3.eth.contract(address=AKITA_ADDRESS, abi=ERC20_ABI)

def get_gtc_akita_bal():
    return str(akita.functions.balanceOf(GITCOIN_ADDRESS).call())
```

请注意，我们在此代码片段中包含 ERC20 ABI，以便正确读取 Gitcoin 多重签名钱包的 AKITA 余额。合约应用二进制接口 ( [**ABI**](https://www.web3.university/alchemy/guides/eth_getlogs#what-are-ab-is) ) 是与以太坊生态系统中的合约交互的标准方式，我们使用 ERC20 ABI 与 ERC20 合约（例如 AKITA 合约）进行交互。

#### d) 定义 Alchemy 传递函数

在这里，我们使用 Alchemy 特定的方法`alchemy_getAssetTransfers`来获取 Gitcoin 合约燃烧的 AKITA 代币总数。

```js
def get_total_burn(): total_burn = requests.post('https://eth-mainnet.alchemyapi.io/v2/'+ALCHEMY_KEY, json={"jsonrpc": "2.0","id": 0,"method": "alchemy_getAssetTransfers","params": [{"fromBlock": "0xC30965","toBlock": "latest","fromAddress": "0xde21F729137C5Af1b01d73aF1dC21eFfa2B8a0d6","toAddress": "0xDead000000000000000000000000000000000d06","contractAddresses": ["0x3301Ee63Fb29F863f2333Bd4466acb46CD8323E6"],"category": ["external","token"]}]})

    json_response = total_burn.json()
    transfer_nums = len(json_response['result']['transfers'])

    burned = 0
    for i in range(transfer_nums):
        burned = burned + json_response['result']['transfers'][i]['value']
    return burned
```

要了解参数细分，请阅读上面的[更多内容](https://docs.alchemy.com/docs/integrating-historical-transaction-data-into-your-dapp#3-integrate-alchemy-transfers-api)。

> 注意：我们使用导入的 Python 包 json 解析我们的 JSON 响应，以帮助我们轻松地对响应进行排序并过滤所需字段。要更好地了解此响应的原始内容，请尝试使用[Alchemy Composer 应用程序](https://composer.alchemyapi.io/?composer_state={"chain"%3A0%2C"network"%3A0%2C"methodName"%3A"eth_getBlockByNumber"%2C"paramValues"%3A["latest"%2Cfalse]})或尝试从 Postman 执行 Alchemy API 调用。

#### e) 配置 Flask 路由

为了确保来自前端按钮的“刷新数据”命令触发上面定义的 3 个函数的执行，我们需要配置我们的烧瓶来处理这个逻辑！将以下内容添加到您的`main.py`文件中：

```js
app = Flask(__name__)
SECRET_KEY = os.urandom(32)
app.config['SECRET_KEY'] = SECRET_KEY

@app.route('/', methods=['GET', 'POST'])
def refresh():
    #num1 = None
    block_num = get_block_num()
    balance = get_gtc_akita_bal()
    total_burn = get_total_burn()

    form = DataTriggerForm()

    if request.method == 'POST':
        #num1 = form.num1.data
        block_num = get_block_num()
        balance = get_gtc_akita_bal()
        total_burn = get_total_burn()

    return render_template('index.html', form=form, bal=balance, block_num=block_num, total_burn=total_burn)
```

每当触发“刷新数据”按钮时，我们都会向 Alchemy API 发出请求，这反过来又会为我们提供更新的数据。请注意，第 7-10 行和第 14-18 行是重复的，以确保我们的网站在我们首次加载时刷新其数据，当我们访问它时，每当有人刷新站点时，或者当用户单击“刷新数据”按钮时。

一旦我们能够从 3 个函数中的每一个中获取最新的值，我们就可以将这些数据传递给我们的`render_template`函数，该函数将新信息推送到前端。

我们的脚本准备好了！这是我们一起创建的`main.py`整个示例：

```js
from flask import Flask, jsonify, render_template, request
from forms import DataTriggerForm
import os
import json
from web3 import Web3
import requests

ALCHEMY_KEY = os.environ.get('KEY')
w3 = Web3(Web3.HTTPProvider('https://eth-mainnet.alchemyapi.io/v2/'+ALCHEMY_KEY))

# includes the standard ERC20 ABI info
ERC20_ABI = json.loads('[{"constant":true,"inputs":[],"name":"name","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_spender","type":"address"},{"name":"_value","type":"uint256"}],"name":"approve","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"totalSupply","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_from","type":"address"},{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"transferFrom","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[],"name":"decimals","outputs":[{"name":"","type":"uint8"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[{"name":"_owner","type":"address"}],"name":"balanceOf","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":true,"inputs":[],"name":"symbol","outputs":[{"name":"","type":"string"}],"payable":false,"stateMutability":"view","type":"function"},{"constant":false,"inputs":[{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"transfer","outputs":[{"name":"","type":"bool"}],"payable":false,"stateMutability":"nonpayable","type":"function"},{"constant":true,"inputs":[{"name":"_owner","type":"address"},{"name":"_spender","type":"address"}],"name":"allowance","outputs":[{"name":"","type":"uint256"}],"payable":false,"stateMutability":"view","type":"function"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_from","type":"address"},{"indexed":true,"name":"_to","type":"address"},{"indexed":false,"name":"_value","type":"uint256"}],"name":"Transfer","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"name":"_owner","type":"address"},{"indexed":true,"name":"_spender","type":"address"},{"indexed":false,"name":"_value","type":"uint256"}],"name":"Approval","type":"event"}]')  # noqa: 501

# configures web3 to point towards the AKITA token address
AKITA_ADDRESS = '0x3301Ee63Fb29F863f2333Bd4466acb46CD8323E6'
GITCOIN_ADDRESS = '0xde21F729137C5Af1b01d73aF1dC21eFfa2B8a0d6'
akita = w3.eth.contract(address=AKITA_ADDRESS, abi=ERC20_ABI)

app = Flask(__name__)
SECRET_KEY = os.urandom(32)
app.config['SECRET_KEY'] = SECRET_KEY


def get_block_num():
    return str(w3.eth.get_block('latest')['number'])

def get_gtc_akita_bal():
    return str(akita.functions.balanceOf(GITCOIN_ADDRESS).call())

def get_total_burn():
    total_burn = requests.post('https://eth-mainnet.alchemyapi.io/v2/'+ALCHEMY_KEY, json={"jsonrpc": "2.0","id": 0,"method": "alchemy_getAssetTransfers","params": [{"fromBlock": "0xC30965","toBlock": "latest","fromAddress": "0xde21F729137C5Af1b01d73aF1dC21eFfa2B8a0d6","toAddress": "0xDead000000000000000000000000000000000d06","contractAddresses": ["0x3301Ee63Fb29F863f2333Bd4466acb46CD8323E6"],"category": ["external","token"]}]})

    json_response = total_burn.json()
    transfer_nums = len(json_response['result']['transfers'])

    burned = 0
    for i in range(transfer_nums):
        burned = burned + json_response['result']['transfers'][i]['value']
    return burned

@app.route('/', methods=['GET', 'POST'])
def refresh():
    #num1 = None
    block_num = get_block_num()
    balance = get_gtc_akita_bal()
    total_burn = get_total_burn()

    form = DataTriggerForm()

    if request.method == 'POST':
        #num1 = form.num1.data
        block_num = get_block_num()
        balance = get_gtc_akita_bal()
        total_burn = get_total_burn()

    return render_template('index.html', form=form, bal=balance, block_num=block_num, total_burn=total_burn)


if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```

请注意，我们之前的 Python 文件中的一个导入语句引用了名为forms.py 的文件中的 _DataTriggerForm 。这段代码会影响出现在我们前端按钮上的文本，并包含来自 Flask 的默认表单。

下面包括 _DataTriggerForm _ 的代码，它位于 我们的 forms.py 文件中。

```js
from flask_wtf import FlaskForm
from wtforms import SubmitField, IntegerField


class DataTriggerForm(FlaskForm):
    submit = SubmitField('Refresh Data!')
```

### 5. 创建 dApp 仪表板前端

准备好 Python 脚本后，我们现在将构建仪表板。我们的示例客户端非常简单。当然，您需要将此代码适当地集成到您的 dApp 中，并使用不同的目标地址和不同的图形！

我们的仪表板是一个简单的 HTML 页面，它显示由 Python 脚本在后台处理的所有信息。

#### a) 创建你的 index.html**文件**

这是我们将存储所有前端代码的文件。

#### b) 创建“刷新数据”按钮

```js
<form action="/" method="post">
    <div class="form-group">
        {{ form.submit }}
    </div>
</form>
```

这段代码“发布”了我们之前创建的 Python 脚本的触发器。这让我们知道用户何时单击“刷新数据”按钮并执行 Python 代码。

#### c) 创建我们的 Python 脚本可以将数据推送到的 UI 元素

```js
<div data-gb-custom-block data-tag="if">

<p>Block Number: {{ block_num }}</p>

</div>

<div data-gb-custom-block data-tag="if">

<p>Gitcoin Akita Balance: {{ bal }}</p>

</div>

<div data-gb-custom-block data-tag="if">

<p>Akita Rescued: {{ total_burn }}</p>

</div>
```

这段代码收集了之前传入我们render_template函数的变量。检索这些变量后，我们就可以在解析并接收到来自Alchemy API的新数据后立即更新前台。

#### d) 添加其他 HTML 元素

这只是我们可以添加到仪表板前端的少量 HTML 元素。随意更改我们在下面包含的 UI；这是我们刚刚一起构建的示例 index.html 文件（对图形 / UI 采取了一些自由）

```html
<!DOCTYPE html>

<!DOCTYPE html>
<html>
<title>Akita Rescue</title>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<link rel="stylesheet" href="https://www.w3schools.com/w3css/4/w3.css">
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Raleway">
<style>
body,h1 {font-family: "Raleway", sans-serif}
body, html {height: 100%}
.bgimg {
  background-image: url('https://static.slab.com/prod/uploads/7adb25ff/posts/images/jsZSd8xIzicYLd9fj6XulBjZ.png');
  min-height: 100%;
  background-position: center;
  background-size: cover;
}
</style>
<body>

  <!-- Header -->
  <header class="w3-container w3-center w3-padding-32">
    <h1><b>Akita 🐕 Rescue 🛠</b></h1>
  </header>


<div class="bgimg w3-display-container w3-animate-opacity w3-text-black">
  <div class="w3-display-center w3-padding-large w3-large">
      A dashboard that tracks the "rescue" of the AKITA tokens in the Gitcoin multi-sig.  For every purchase of 1 AKITA, 13 are burnt from Gitcoin.
ETH from the sale lands back in the Gitcoin multi-sig and will be used for the ETH side of an ETH/AKITA LBP.
  </div>

  <div>
  </div>

  <div class="w3-display-bottomright w3-padding-large">
    For more info about Vitalik's donation of AKITA tokens to Gitcoin, check out this <a href="https://gitcoin.co/blog/announcement-gitcoin-community-receives-generous-gift-from-vitalik-buterin/" target="_blank"> blog post.</a>
  </div>

  <div class="w3-display-middle">
    <hr class="w3-border-grey" style="margin:auto;width:40%">
    <p class="w3-large w3-center">Gitcoin AKITA Rescue Statistics</p>
    <div class="container form">
        <hr>
        <br>
        <form action="/" method="post">
            <div class="form-group">
                {{ form.submit }}
            </div>
        </form>
        

<div data-gb-custom-block data-tag="if">

        <p>Block Number: {{ block_num }}</p>
        

</div>

        

<div data-gb-custom-block data-tag="if">

        <p>Gitcoin Akita Balance: {{ bal }}</p>
        

</div>

        

<div data-gb-custom-block data-tag="if">

        <p>Akita Rescued: {{ total_burn }}</p>
        

</div>
    </div>
  </div>
  <div class="w3-display-bottomleft w3-padding-large">
    Powered by <a href="https://www.alchemy.com/" target="_blank"> https://www.alchemy.com/</a>
  </div>
</div>
```

#### e) 部署！

在您想要的环境中部署此 webapp 后，它应该如下所示！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210201902818.png)

这是一个简单的示例，但您可以通过多种方式对此进行扩展，为您的用户构建仪表板和 dApp。

Fork 🍴, build 🏗️, and design 📝off this repo!

##  结论

区块链发展迅速，但并非所有开发人员功能都易于使用或直观。尤其是历史交易信息的查询，更是一直困扰着发展。但是，使用 Alchemy Transfers API，您的用户可以随时了解并自信地了解他们现在和过去的交易活动！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
