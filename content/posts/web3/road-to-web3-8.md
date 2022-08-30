---
title: Web3之路——如何用0x API建立一个Token交换Dapp
description: null
author: 李留白
weight: 0
date: 2022-08-30T14:53:50.886Z
lastmod: 2022-08-30T14:57:40.982Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814204307.png
---

了解如何使用0x交换API端点建立一个Token交换Dapp（一个简易版的Matcha.xyz）。这个应用程序在更大的DEX生态系统中聚合流动性，并将最佳价格浮现给用户。

你是否曾经进入你最喜欢的Token交易dapp，用ETH换取DAI，并想知道它是如何为你找到最佳价格的？

![使用Matcha.xyz这个DEX聚合器交易WETH<>DAI。](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830214721.png)

更有可能的是，它使用了一个[流动性聚合器](https://docs.0x.org/introduction/introduction-to-0x#makers-and-takers)，它在链外（如Market Makers, Orderbooks）和链上（如DEXs、AMMs）寻找所有可能的价格，并为用户提供最佳价格。

在本教程中，我们将学习如何使用[0x API交换端点](https://docs.0x.org/0x-api-swap/introduction)，该端点允许用户在整个流动性供应中获取可用的报价，并使用[智能订单路由](https://blog.0xproject.com/0x-apis-smart-order-routing-7af0195515e5)在分散的交易所网络中分割交易，以尽可能低的滑点填补，同时最大限度地降低交易成本。

这与MetaMask、Coinbase钱包、Zapper等主要钱包和交易所的互换背后的端点相同。

请注意，我们不需要编写任何智能合约来寻找和结算交易! 相反，0x API允许web3开发人员轻松地接入0x协议的智能合约，这些智能合约负责处理用于结算交易的所有逻辑，使web开发人员能够专注于构建最佳的交易体验。

在本教程结束时，你将学会如何做到以下几点:

- 理解为什么流动性聚合很重要
- 查询并显示一个ERC20代币列表
- 使用0x API /swap 端点
- 设置一个代币允许量
- 建立一个简单的代币交换DApp，使用web3.js连接到MetaMask

## 先决条件

为了准备本教程的其余部分，你需要具备以下条件。

- npm（npx）版本8.5.5
- node版本16.13.1
- [MetaMask](https://metamask.io/download/)

以下内容不是必须的，但极为有用。

- 对[命令行](https://developer.mozilla.org/en-US/docs/Learn/Tools_and_testing/Understanding_client-side_tools/Command_line)有一定的熟悉程度
- 对[JavaScript](https://www.codecademy.com/learn/introduction-to-javascript)有一定的了解
- [Visual Studio Code](https://code.visualstudio.com/)，我推荐的编码IDE

如果你需要帮助，这里是[github repo](https://github.com/0xProject/swap-demo-tutorial)。

## 第1部分:初始代码的设置

你需要做的第一件事是克隆项目的启动代码。我们将在这个初始用户界面上添加代币交易功能。

```
git clone git clone https://github.com/0xProject/swap-demo-tutorial/tree/main/swap-demo-tutorial-part-1
cd swap-demo-tutorial-part-1
```

在你喜欢的IDE中打开该项目，并启动一个本地开发服务器。如果你使用的是Visual Studio Code，一种方法是在左侧面板的扩展中安装Live Server。然后，在资源管理器窗口的index.html文件上点击右键，并点击用Live Server打开，以打开DEX Aggregator的骨架。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830215514.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830215522.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830215537.png)

需要指出的几个关键功能（代码见index.html）:

- 登录MetaMask按钮 - 当这个应用程序完成后，点击这个按钮将使用户连接到他们的MetaMask钱包并启用 "交换 "按钮。
- 交换框
  - 选择一个代币 - 目前这些部分只是在光标悬停在上面时改变颜色；到最后，用户将能够点击并显示可用的代币列表来进行交换。
  - 数量 - 这是一个输入表格，允许用户输入一个数字。
- 估计的气体 - 交换终端将返回估计的气体，以便进行交换。我们将在这里显示。
- 交换按钮 - 如上所述，"交换 "按钮目前是禁用的，但我们将在用户登录MetaMask后启用它。

看看index.js中的这些元素，注意它们的id，以及style.css中的相应样式。

## 第2部分:连接到MetaMask

现在，让我们使用户能够连接到MetaMask。

首先在你的浏览器上下载MetaMask。MetaMask是一个加密货币钱包，允许你与区块链应用程序互动。它将为我们保管我们将用于交易的资金。在这个应用程序中，我们只希望用户在安装了MetaMask钱包的情况下有能力进行互换。

接下来，index.js，创建一个connect()函数，并将其连接到login_button元素。请阅读注释，了解我们如何检查MetaMask是否连接：

```javascript
async  function  connect() {
/** MetaMask injects a global API into websites visited by its users at `window.ethereum`. This API allows websites to request users' Ethereum accounts, read data from blockchains the user is connected to, and suggest that the user sign messages and transactions. The presence of the provider object indicates an Ethereum user. Read more: https://ethereum.stackexchange.com/a/68294/85979**/

// Check if MetaMask is installed, if it is, try connecting to an account
    if (typeof  window.ethereum !== "undefined") {
        try {
            console.log("connecting");
            // Requests that the user provides an Ethereum address to be identified by. The request causes a MetaMask popup to appear. Read more: https://docs.metamask.io/guide/rpc-api.html#eth-requestaccounts
            await  ethereum.request({ method:  "eth_requestAccounts" });
        } catch (error) {
        console.log(error);
        }
        // If connected, change button to "Connected"
        document.getElementById("login_button").innerHTML = "Connected";
        // If connected, enable "Swap" button
        document.getElementById("swap_button").disabled = false;
        } 
        // Ask user to install MetaMask if it's not detected 
        else {
        document.getElementById("login_button").innerHTML =
            "Please install MetaMask";
        }
    }
// Call the connect function when the login_button is clicked
document.getElementById("login_button").onclick = connect;
```

现在，当你用LiveServer打开index.html，并点击 "用MetaMask登录 "按钮时，该按钮应自动更新为 "已连接"，如下所示。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830215839.png)

如果没有 MetaMask，文本将变为“请安装 MetaMask”：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830215908.png)

厉害！现在我们的应用程序可以检测用户何时连接了钱包。

**第 2 部分的最终代码**

```
git clone https://github.com/0xProject/swap-demo-tutorial/tree/main/swap-demo-tutorial-part-2
```

## 第3部分:为令牌列表创建一个模式

现在让我们看看当用户点击“SELECT A TOKEN”时如何创建一个token列表。我们可以用来帮助解决这个问题。

复制并粘贴Modal示例代码，并将其粘贴到最后。.您可以删除保存和关闭按钮。另外，请确保添加 ，以便我们稍后可以引用此modal 。modal 的第一行应该如下所示：

```html
<div  class="modal"  id="token_modal"  tabindex="-1"  role="dialog">
```

你现在应该是这样的：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <title>Javascript Test</title>
    <script src="https://cdn.jsdelivr.net/npm/web3@latest/dist/web3.min.js"></script>
    <script src="https://code.jquery.com/jquery-3.6.0.min.js" integrity="sha256-/xUj+3OJU5yExlq6GSYGSHk7tPXikynS7ogEvDej/m4=" crossorigin="anonymous"></script>
    <script src="https://unpkg.com/moralis/dist/moralis.js"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
    <link rel="stylesheet" href="./style.css">
  </head>
<body>
    <nav class="navbar navbar-expand-lg navbar-light bg-light">
        <a class="navbar-brand" href="#">My DEX Aggregator</a>
        <ul class="navbar-nav ml-auto">
          <li class="nav-item">
            <button id="login_button" class="btn btn-outline-primary my-2 my-sm-0" type="submit">Sign in with MetaMask</button>
          </li>
      </nav>
      <div class ="container">
        <div class="row">
            <div class="col col-md-6 offset-md-3" id="window">
                <h4>Swap</h4>
                <div id="form">
                    <div class="swapbox">
                        <div class="swapbox_select token_select" id="from_token_select">
                            SELECT A TOKEN
                        </div>
                        <div class="swapbox_select">
                            <input class="number form-control" placeholder="amount" id="from_amount">
                        </div>
                    </div>
                    <div class="swapbox">
                        <div class="swapbox_select token_select" id="to_token_select">
                            SELECT A TOKEN
                        </div>
                         <div class="swapbox_select">
                            <input class="number form-control" placeholder="amount" id="to_amount">
                        </div>
                    </div>  
                    <div class="gas_estimate_label">Estimated Gas: <span id="gas_estimate"></span></div>
                    <button disabled class="btn btn-large btn-primary btn-block" id="swap_button">Swap</button>                
                </div>
            </div>
        </div>
    </div>
    <!-- Add the new modal body here. Note we added id="token_modal" and updated the modal-title -->
    <div class="modal" id="token_modal" tabindex="-1" role="dialog">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Select a Token</h5>
            <button id="modal_close" type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <p>Modal body text goes here.</p>
          </div>
        </div>
      </div>
    </div>
      <script src="./index.js" type="text/javascript"></script>
    </body>
</html>
```

现在，我们需要一种在单击html元素时打开模态框的方法，即带有`from_token_select` id 的 html 元素。

在html 中，让我们创建一个名为openModal的函数，该函数在单击该元素时打开弹窗。添加以下行：

```javascript
document.getElementById("from_token_select").onclick = openModal;
```

接下来，创建函数：

```javascript
function  openModal(){
document.getElementById("token_modal").style.display = "block";
}
```

现在，当您单击“SELECT A TOKEN”时，会出现此模式：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830220514.png)

现在没有办法关闭弹窗，所以我们来添加这个。弹窗中已经有一个元素，这就是右上角的X。我们将把它连接到一个关闭弹窗的函数上。

创建一个函数，当该元素被点击时关闭模态。添加以下一行

```
document.getElementById("modal_close").onclick = closeModal;
```

接下来，创建该函数

```
function  closeModal(){
document.getElementById("token_modal").style.display = "none";
}
```

**第三部分的最终代码**

```
git clone https://github.com/0xProject/swap-demo-tutorial/tree/main/swap-demo-tutorial-part-3
```

## 第4部分:从CoinGecko API获取并显示令牌列表

我们目前有一个模版，但里面什么都没有。让我们检索一个ERC20代币的列表来填充模版，这样用户就可以选择一个代币来交易。

Uniswap拥有该项目，这是一个创建ERC20代币清单的标准，以从诈骗、伪造和重复中过滤掉高质量、合法的代币。阅读更多关于代币清单的重要性 。

CoinGecko名单是一个更强大的名单，所以我们将使用这个查询。我建议你直接在浏览器中运行这个查询，看看返回的结果是什么。你应该看到一个JSON对象，其中包含了每个令牌的NET参数。我们将在构建这个dapp时使用所有这些参数。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830220812.png)

现在，我们要在实际调用模态之前加载令牌列表；否则，用户必须等待它加载。我们可以通过在第一次加载页面时初始化的 init() 函数中加载它来实现。

在 index.js 中，创建一个 init() 函数。在里面，将以下 fetch 添加到 CoinGecko API。还要确保从 index.js 调用它，例如，我在 index.js 文件底部的 onclick 调用上方添加了它：

```
async function init(){
    console.log("initializing");
    let response = await fetch('https://tokens.coingecko.com/uniswap/all.json');
    let tokenListJSON = await response.json();
    console.log("listing available tokens: ", tokenListJSON);
}
...

// Add init() call
init();

document.getElementById("login_button").onclick = connect;
document.getElementById("from_token_select").onclick = openModal;
document.getElementById("to_token_select").onclick = openModal;
document.getElementById("modal_close").onclick = closeModal;
```

现在，当您为您的 dapp 提供服务并对其进行检查时，您应该会看到打印出的令牌列表 json 对象。如果这是您第一次使用 Chrome 的 Inspect DevTool，请[查看本文](https://www.hostinger.com/tutorials/website/how-to-inspect-and-change-style-using-google-chrome)以了解更多信息。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830221028.png)

请注意，返回的令牌列表 JSON 对象包含一个令牌键，其中包含 4954 个 JSON 对象的数组。我们将从此处提取信息以填充我们的令牌列表。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830221053.png)

现在，我们将创建一个新函数 listAvailableTokens() 来创建我们需要的列表，而不是直接在 init() 中创建令牌列表 - 令牌地址、符号、图像、小数 - 并从 init() 中调用它。

将以下内容添加到 index.js 中的 listAvailableTokens() 中。请务必阅读评论：

```
async function listAvailableTokens(){
  console.log("initializing");
  let response = await fetch('https://tokens.coingecko.com/uniswap/all.json');
  let tokenListJSON = await response.json();
  console.log("listing available tokens: ", tokenListJSON);
  tokens = tokenListJSON.tokens
  console.log("tokens:", tokens);

  // Create a token list for the modal
  let parent = document.getElementById("token_list");
  // Loop through all the tokens inside the token list JSON object
  for (const i in tokens){
    // Create a row for each token in the list
    let div = document.createElement("div");
    div.className = "token_row";
    // For each row, display the token image and symbol
    let html = `
    <img class="token_list_img" src="${tokens[i].logoURI}">
      <span class="token_list_text">${tokens[i].symbol}</span>
      `;
    div.innerHTML = html;
    parent.appendChild(div);
  }
}
```

另外，让我们用我们刚刚创建的“token_list”元素替换模态正文虚拟文本：

```
 <div class="modal" id="token_modal" tabindex="-1" role="dialog">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Select a Token</h5>
            <button id="modal_close" type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
              < !-- Replace the modal text with token_list -->
            <div id="token_list"></div>
          </div>
        </div>
      </div>
    </div>
```

最后，将模态的这个样式添加到 style.css 中，这样标记列表就不会无限流动，并且有一个滚动条：

```
.modal-body{
height: 500px;
overflow: scroll;
}
```

现在，当用户单击“SELECT A TOKEN”并出现模式时，您的应用程序应如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830221207.png)

> 您不会在此列表中找到 ETH，因为它实际上不是 ERC20 代币。需要包装WETH。了解有关 WETH 的更多信息。一些应用程序有一个精选列表，而不是显示所有选项。

**第 4 部分的最终代码**

```
git clone https://github.com/0xProject/swap-demo-tutorial/tree/main/swap-demo-tutorial-part-4
```

## 第5部分:在交换框中显示选定的令牌图像和符号

现在我们有一个供用户选择的令牌列表，我们希望在交换框中显示选定的令牌图像和符号。

首先，创建一个 selectToken() 函数。当单击指示令牌的 div 行之一时，使用匿名函数从 listAvailableTokens() 的底部调用它：

```javascript
async function listAvailableTokens(){
  // ... the rest of the function
    div.innerHTML = html;
    // selectToken() will be called when a token is clicked
    div.onclick = () => {
      selectToken(tokens[i]);
    };
    parent.appendChild(div);
  }
}
```

在我们创建selectToken()函数之前，我们需要一种方法来跟踪我们在令牌选择器的哪一边。我们是在from-side还是to-side上？

为了跟踪这一点，在index.js代码的最顶端创建一个全局的currentTrade对象和currentSelectSide变量。

```javascript
// top of index.js
let  currentTrade = {};
let  currentSelectSide;
```

现在我们有了这个全局对象，我们应该根据点击哪个令牌选择器来跟踪用户是在from-side还是to-side。在index.js的底部设置这个匿名函数，当 "from_token_select "被选中时传递 "from"。

```javascript
document.getElementById("from_token_select").onclick = () => {
openModal("from");
};
```

并使用以下代码更新 openModal()：

```javascript
// index.js
function  openModal(side){
    // Store whether the user has selected a token on the from or to side
    currentSelectSide = side;
    document.getElementById("token_modal").style.display = "block";
}
```

现在，创建一个selectToken()函数并添加以下代码。

```javascript
function  selectToken(token) {
    // When a token is selected, automatically close the modal
    closeModal();
    // Track which side of the trade we are on - from/to
    currentTrade[currentSelectSide] = token;
    // Log the selected token
    console.log("currentTrade:" , currentTrade);
}
```

运行程序并检查currentTrade是否正常记录。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830221317.png)


现在显示图像和令牌符号。

首先，在index.html中加入 "from_token_img"、"from_token_text"、"to_token_img"、"to_token_text"。在selectToken()下面创建一个renderInterface()并在selectToken()中调用它。

```html
< !-- Replace the SELECT A TOKEN text -->
< !-- From token -->
<div  class="swapbox_select token_select"  id="from_token_select">
    <img  class="token_img"  id="from_token_img">
    <span  id="from_token_text"></span>
</div>

< !-- To token -->
<div  class="swapbox_select token_select"  id="to_token_select">
    <img  class="token_img"  id="to_token_img">
    <span  id="to_token_text"></span>
</div>
```

在renderInterface()中，我们将通过调用index.html中的相关元素来设置从/到令牌图像和符号文本。记得logoURI和符号都是由CoinGeckoAPI返回的。

```javascript
function selectToken(token) {
  closeModal();
  currentTrade[currentSelectSide] = token;
  console.log("currentTrade:" , currentTrade);
  renderInterface();
}

// Function to display the image and token symbols 
function renderInterface(){
  if (currentTrade.from) {
    console.log(currentTrade.from)
    // Set the from token image
    document.getElementById("from_token_img").src = currentTrade.from.logoURI;
     // Set the from token symbol text
    document.getElementById("from_token_text").innerHTML = currentTrade.from.symbol;
  }
  if (currentTrade.to) {
      // Set the to token image
    document.getElementById("to_token_img").src = currentTrade.to.logoURI;
      // Set the to token symbol text
    document.getElementById("to_token_text").innerHTML = currentTrade.to.symbol;
  }
}
```

最后，在selectToken()函数的最后添加renderInterface()。

```js
function  selectToken(token) {
    closeModal();
    currentTrade[currentSelectSide] = token;
    console.log("currentTrade:" , currentTrade);
    // Display token image and symbol in swapbox
}
```

用户现在可以选择从代币和到代币，以及输入他们想在我们的dapp中进行交易的金额!

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830221330.png)



**第 5 部分的最终代码**

```
git clone https://github.com/0xProject/swap-demo-tutorial/tree/main/swap-demo-tutorial-part-5
```

## 第6部分:获取价格

现在，用户可以选择他们的代币并设置交易的代币金额，让我们帮助他们找到最好的价格吧

DeFi交易应用程序中常见的一个非常直观的功能是，当输入金额后，用户的光标离开金额框的焦点时，自动生成一个价格。请看Matcha.xyz的这个功能。

一旦输入WETH价格，DAI价格就会自动生成。要做到这一点，我们可以监听一个叫做onblur的Javascript事件。当用户从 "from_amount "元素上离开焦点时，在这里使用它。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830221352.png)



```javascript
// at the bottom of index.js add this
document.getElementById("from_amount").onblur = getPrice;
```

### /price 与 /quote 端点

现在我们需要创建一个名为getPrice()的函数。在里面，我们将调用[GET /swap/v1/price](https://docs.0x.org/0x-api-swap/api-references/get-swap-v1-price)端点。

/price与/quote几乎相同，但有几个关键的区别。/price不会返回一个可以在链上提交的交易；它只是为我们提供相同的信息。可以把它看作是/quote的 "只读 "版本。

这一点也很重要，因为/quote返回的订单是**Market Maker** 已经承诺了他们的资产。因此，如果我们在真正只是要求一个价格而不准备提交订单时，过多地调用/quote，那么这可能会堵塞系统

下面是一个GET HTTP /price请求的例子。该调用几乎与/quote相同：https://api.0x.org/swap/v1/price?sellToken=ETH&buyToken=DAI&sellAmount=1000000000000000000

> 该API端点使用的是以太坊（主网），由https://api.0x.org。
>
> \ 以下是支持的网络的所有API端点的列表。

为了在我们的index.js中使用这个，我们需要导入qs模块。在文件的顶部添加require语句。

```javascript
const  qs = require('qs');
```

### Browserify 在浏览器中使用节点模块

现在，由于我们使用require安装了一个模块，如果我们现在试图加载我们的index.html，我们的浏览器会出现问题。为了解决这个问题，请在终端运行[Browserify](https://browserify.org/)来安装它。

```
npm install -g browserify
```

安装[qs模块](https://www.npmjs.com/package/qs)模块与[npm](https://npmjs.org/)：

```
npm i qs
```

现在，用browserify命令将所有需要的模块从main.js开始递归到一个名为bundle.js的文件中（注意：你可能需要明确说明./index.js和./bundle.js的路径，以适应你的设置）。

```
browserify index.js --standalone bundle -o bundle.js
```

> 继续前进，请确保在需要提供 index.html 时重新运行 Browerify 命令以生成更新的 bundle.js

并确保将 index.html 中脚本的 src 从 src=./index.js 更新为 src=./bundles.js：

```html
< !-- Make sure your script now sources from the correct file --> 
<script  src="./bundle.js"  type="text/javascript"></script>
```

### 构建 getPrice() 函数

在index.js中，创建getPrice()。我将对这个函数的不同部分进行分解，然后在最后展示一个完整的函数代码片段。

我们添加了一个if语句，因为我们只想在选择了from和to tokens以及填写了from tokens的金额后运行/price查询。我们还从用户那里获得输入的金额，并将其乘以10，然后再乘以from标记的小数点的幂。例如，如果用户输入他们想交易1WETH，WETH有18位小数。WETH的最小单位是Wei。所以他们想交易的金额是（1 x 10到18的幂）Wei。你可以在EtherScan的[Profile Summary](https://etherscan.io/token/0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2)下仔细检查小数点位[。

```javascript
async  function  getPrice(){
    console.log("Getting Price");
    // Only fetch price if from token, to token, and from token amount have been filled in 
    if (!currentTrade.from || !currentTrade.to || !document.getElementById("from_amount").value) return;
    // The amount is calculated from the smallest base unit of the token. We get this by multiplying the (from amount) x (10 to the power of the number of decimal places)
    let  amount = Number(document.getElementById("from_amount").value * 10 ** currentTrade.from.decimals);
    ...
}
```

接下来，在getPrice()里面，我们将设置我们的参数，并将它们填入我们的价格报价。

```javascript
async  function  getPrice(){
...
const params = {
    sellToken: currentTrade.from.address,
    buyToken: currentTrade.to.address,
    sellAmount: amount,
  }
  // Fetch the swap price.
  const response = await fetch(
    `https://api.0x.org/swap/v1/price?${qs.stringify(params)}`
    );
...
}
```

最后，一旦响应被返回，我们将解析出JSON。JSON对象包含buyAmount和improvedGas的键/值对，我们可以在用户界面中直接填充到 "to_amount "和 "gas_estimate "html元素中。

```javascript
async  function  getPrice(){
...
    // Await and parse the JSON response 
    swapPriceJSON = await  response.json();
    console.log("Price: ", swapPriceJSON);
    // Use the returned values to populate the buy Amount and the estimated gas in the UI
    document.getElementById("to_amount").value = swapPriceJSON.buyAmount / (10 ** currentTrade.to.decimals);
    document.getElementById("gas_estimate").innerHTML = swapPriceJSON.estimatedGas;
...
}
```

现在为了运行它，确保重新运行Browerify命令，以生成一个更新的bundle.js。

你的项目现在应该像这样自动填入to-amount 和 Estimated Gas!

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830221409.png)

**第 6 部分的最终代码**

```
git clone https://github.com/0xProject/swap-demo-tutorial/tree/main/swap-demo-tutorial-part-6
```

## 第7部分:获取报价

现在获取报价设置！

这是剩下的游戏计划：

- ✅ 仅在连接 MetaMask 时启用“交换”按钮
- ⚪ 使用用户的 MetaMask账户 地址获取报价
- ⚪ 设置代币限额
- ⚪ 执行交换

我们已经完成了上面部分（2.连接到 MetaMask）的第一步。让我们创建一个函数来获取报价。

#### getQuote() - 使用用户的 MetaMask 账户地址获取报价

这个函数与getPrice()非常相似，唯一不同的是，我们将传入一个takerAddress，这是将填写报价的地址。在我们的例子中，这就是我们的MetaMask账户。你可以在[这里](https://docs.0x.org/0x-api-swap/api-references/get-swap-v1-quote#request)阅读更多关于takerAddress的参数。

我将复制并粘贴 getPrice() 代码并对 getQuote() 进行以下修改以传入并设置 takerAddress：

```javascript
// index.js

// Function to get a quote using /swap/v1/quote. We will pass in the user's MetaMask account to use as the takerAddress
async function getQuote(account){
  console.log("Getting Quote");

  if (!currentTrade.from || !currentTrade.to || !document.getElementById("from_amount").value) return;
  let amount = Number(document.getElementById("from_amount").value * 10 ** currentTrade.from.decimals);

  const params = {
    sellToken: currentTrade.from.address,
    buyToken: currentTrade.to.address,
    sellAmount: amount,
    // Set takerAddress to account 
    takerAddress: account,
  }

  // Fetch the swap quote.
  const response = await fetch(
    `https://api.0x.org/swap/v1/quote?${qs.stringify(params)}`
    );
  
  swapQuoteJSON = await response.json();
  console.log("Quote: ", swapQuoteJSON);
  
  document.getElementById("to_amount").value = swapQuoteJSON.buyAmount / (10 ** currentTrade.to.decimals);
  document.getElementById("gas_estimate").innerHTML = swapQuoteJSON.estimatedGas;

  return swapQuoteJSON;
}
```

第2步完成！

- ✅ 仅在连接 MetaMask 时启用“交换”按钮
- ✅ 使用用户的 MetaMask 账户地址获取报价
- ⚪ 设置代币限额
- ⚪ 执行交换

现在让我们来看看如何设置代币配额

**第 7 部分的最终代码**

```
git clone https://github.com/0xProject/swap-demo-tutorial/tree/main/swap-demo-tutorial-part-7
```

## 第8部分:设置代币补贴

#### 设置代币补贴

如果你想让第三方代表你移动资金，就需要有代币补贴。简而言之，你是允许他们移动你的代币。

在我们的例子中，我们希望[0x 交换代理智能合约](https://docs.0x.org/introduction/0x-cheat-sheet#exchange-proxy-addresses)为我们交易我们的 ERC20 代币，因此我们需要*批准*该合约的*津贴*（一定数量），*以代表我们转移一定数量的 ERC20 代币*。

这样做需要什么：

- (i)使用 web3 对象连接 ERC20 代币的方法`approve()`
- (ii) 将批准金额设置为`maxApproval`
- (iii) 使用approve() 给我们的allowanceTarget 一个最大数量的津贴

#### (i) 使用 web3 对象连接 ERC20 代币的approve() 方法

所有ERC20代币都需要实现[approve(address spender, uint256 amount) ](https://docs.openzeppelin.com/contracts/2.x/api/token/erc20#IERC20-approve-address-uint256-)函数。这个函数将金额设置为spender对调用者的代币的补贴（即第三方可以代表调用者移动多少这些ERC20代币）。

它返回一个表示操作是否成功的布尔值。

阅读[这里](https://docs.0x.org/0x-api-swap/advanced-topics/how-to-set-your-token-allowances)有关设置代币限额的更多信息

如所见[批准（地址支出者，uint256金额）](https://docs.openzeppelin.com/contracts/2.x/api/token/erc20#IERC20-approve-address-uint256-)上面的函数，为了调用令牌津贴，我们需要一些东西：

- **支出者地址**- 这将是0x Exchange Proxy智能合约地址。我们可以从JSON响应中得到这个，作为我们的/quote查询中返回的 allowanceTarget参数
- **金额**-  我们想给合同多少补贴？在这种情况下，我将用可能的最大金额（maxApproval）进行演示；但是，如果可能的话，我强烈建议只设置你需要的金额。请看[这个例子](https://docs.0x.org/0x-api-swap/guides/swap-tokens-with-0x-api#examples)，了解如何实现（提示，每周挑战，提示）
- **caller** -  用户的MetaMask账户将成为呼叫者地址（又称takerAddress）

**构建 trySwap()**

让我们开始构建trySwap()，我将沿途解释每一块。我将在最后发布完整的代码

首先，让我们得到收件人的地址，并将其传递给getQuote(address)，这样我们就可以从/quote请求中得到swapQuoteJSON来使用。

```javascript
// index.js
async  function  trySwap(){
  // The address, if any, of the most recently used account that the caller is permitted to access
  let accounts = await ethereum.request({ method: "eth_accounts" });
  let takerAddress = accounts[0];
  // Log the the most recently used address in our MetaMask wallet
  console.log("takerAddress: ", takerAddress);
    // Pass this as the account param into getQuote() we built out earlier. This will return a JSON object trade order. 
const  swapQuoteJSON = await  getQuote(takerAddress);
}
```

现在让我们调用 ERC20 代币的[批准（）方法](https://docs.openzeppelin.com/contracts/2.x/api/token/erc20#IERC20-approve-address-uint256-). 由于需要与 ERC20 的合约方法交互，我们需要设置一个 web3 对象。

为了让我们与 ERC20 合约的方法进行交互，需要创建一个 web3 对象，具体来说[web3.eth.Contract](https://web3js.readthedocs.io/en/v1.2.11/web3-eth-contract.html#web3-eth-contract)：

```
const ERC20TokenContract = new web3.eth.Contract(erc20abi, fromTokenAddress);
```

这个对象需要一个erc20abi，它是一个erc20合约的json表示。erc20abi是我们与任何遵循ERC20标准的代币互动的蓝图。它是以JSON格式表示的。快速搜索应该可以找到一个erc20abi.json，因为它是一个标准。我正在使用[这个](https://gist.github.com/veox/8800debbf56e24718f9f483e1e40c35c)。

该对象还需要我们感兴趣的特定代币地址，在这种情况下，它是fromTokenAddrss，因为我们希望第三方（即0x智能合约）对我们要交易的代币采取行动。

将以下内容添加到：`trySwap()`

```javascript
// index.js
async  function  trySwap(){
...
// Setup the erc20abi in json format so we can interact with the approve method below
const erc20abi= [{ "inputs": [ { "internalType": "string", "name": "name", "type": "string" }, { "internalType": "string", "name": "symbol", "type": "string" }, { "internalType": "uint256", "name": "max_supply", "type": "uint256" } ], "stateMutability": "nonpayable", "type": "constructor" }, { "anonymous": false, "inputs": [ { "indexed": true, "internalType": "address", "name": "owner", "type": "address" }, { "indexed": true, "internalType": "address", "name": "spender", "type": "address" }, { "indexed": false, "internalType": "uint256", "name": "value", "type": "uint256" } ], "name": "Approval", "type": "event" }, { "anonymous": false, "inputs": [ { "indexed": true, "internalType": "address", "name": "from", "type": "address" }, { "indexed": true, "internalType": "address", "name": "to", "type": "address" }, { "indexed": false, "internalType": "uint256", "name": "value", "type": "uint256" } ], "name": "Transfer", "type": "event" }, { "inputs": [ { "internalType": "address", "name": "owner", "type": "address" }, { "internalType": "address", "name": "spender", "type": "address" } ], "name": "allowance", "outputs": [ { "internalType": "uint256", "name": "", "type": "uint256" } ], "stateMutability": "view", "type": "function" }, { "inputs": [ { "internalType": "address", "name": "spender", "type": "address" }, { "internalType": "uint256", "name": "amount", "type": "uint256" } ], "name": "approve", "outputs": [ { "internalType": "bool", "name": "", "type": "bool" } ], "stateMutability": "nonpayable", "type": "function" }, { "inputs": [ { "internalType": "address", "name": "account", "type": "address" } ], "name": "balanceOf", "outputs": [ { "internalType": "uint256", "name": "", "type": "uint256" } ], "stateMutability": "view", "type": "function" }, { "inputs": [ { "internalType": "uint256", "name": "amount", "type": "uint256" } ], "name": "burn", "outputs": [], "stateMutability": "nonpayable", "type": "function" }, { "inputs": [ { "internalType": "address", "name": "account", "type": "address" }, { "internalType": "uint256", "name": "amount", "type": "uint256" } ], "name": "burnFrom", "outputs": [], "stateMutability": "nonpayable", "type": "function" }, { "inputs": [], "name": "decimals", "outputs": [ { "internalType": "uint8", "name": "", "type": "uint8" } ], "stateMutability": "view", "type": "function" }, { "inputs": [ { "internalType": "address", "name": "spender", "type": "address" }, { "internalType": "uint256", "name": "subtractedValue", "type": "uint256" } ], "name": "decreaseAllowance", "outputs": [ { "internalType": "bool", "name": "", "type": "bool" } ], "stateMutability": "nonpayable", "type": "function" }, { "inputs": [ { "internalType": "address", "name": "spender", "type": "address" }, { "internalType": "uint256", "name": "addedValue", "type": "uint256" } ], "name": "increaseAllowance", "outputs": [ { "internalType": "bool", "name": "", "type": "bool" } ], "stateMutability": "nonpayable", "type": "function" }, { "inputs": [], "name": "name", "outputs": [ { "internalType": "string", "name": "", "type": "string" } ], "stateMutability": "view", "type": "function" }, { "inputs": [], "name": "symbol", "outputs": [ { "internalType": "string", "name": "", "type": "string" } ], "stateMutability": "view", "type": "function" }, { "inputs": [], "name": "totalSupply", "outputs": [ { "internalType": "uint256", "name": "", "type": "uint256" } ], "stateMutability": "view", "type": "function" }, { "inputs": [ { "internalType": "address", "name": "recipient", "type": "address" }, { "internalType": "uint256", "name": "amount", "type": "uint256" } ], "name": "transfer", "outputs": [ { "internalType": "bool", "name": "", "type": "bool" } ], "stateMutability": "nonpayable", "type": "function" }, { "inputs": [ { "internalType": "address", "name": "sender", "type": "address" }, { "internalType": "address", "name": "recipient", "type": "address" }, { "internalType": "uint256", "name": "amount", "type": "uint256" } ], "name": "transferFrom", "outputs": [ { "internalType": "bool", "name": "", "type": "bool" } ], "stateMutability": "nonpayable", "type": "function" }]
    // Set up approval amount for the token we want to trade from
    const fromTokenAddress = currentTrade.from.address;
    
    // In order for us to interact with a ERC20 contract's method's, need to create a web3 object. This web3.eth.Contract object needs a erc20abi which we can get from any erc20 abi as well as the specific token address we are interested in interacting with, in this case, it's the fromTokenAddrss
// Read More: https://web3js.readthedocs.io/en/v1.2.11/web3-eth-contract.html#web3-eth-contract
    const  web3 = new  Web3(Web3.givenProvider);
    const ERC20TokenContract = new web3.eth.Contract(erc20abi, fromTokenAddress);
    console.log("setup ERC20TokenContract: ", ERC20TokenContract);
...
}
```

#### (ii) 设置审批量为 maxApproval

在此示例中，我将向您展示如何设置最大批准金额。我建议检查一下[这个例子](https://docs.0x.org/0x-api-swap/guides/swap-tokens-with-0x-api#sell-100-dai-for-eth)看看如何只设置需要的东西。

我们将使用[BigNumber](https://github.com/MikeMcl/bignumber.js/)库来创建一个真正的大数字。然后我们将其设置为一个maxApproval变量。在index.js的trySwap()的底部插入这个内容

```js
    // The max approval is set here. Using Bignumber to handle large numbers and account for overflow (https://github.com/MikeMcl/bignumber.js/)
    const maxApproval = new BigNumber(2).pow(256).minus(1);
    console.log("approval amount: ", maxApproval);
```

#### (iii) 使用approve() 给我们的allowanceTarget 一个最大数量的津贴

现在我们可以与approve()方法和批准金额进行互动，让我们用它来授予 allowanceTarget（即0x Exchange Proxy智能合约地址），允许我们花费ERC20代币。我们可以直接从/quote响应中返回的JSON对象中获得 allowanceTarget地址。

```javascript
// index.js
async  function  trySwap(){
...
 // Grant the allowance target (the 0x Exchange Proxy) an  allowance to spend our tokens. Note that this is a txn that incurs fees. 
  const tx = await ERC20TokenContract.methods.approve(
    swapQuoteJSON.allowanceTarget,
    maxApproval,
  )
  .send({ from: takerAddress })
  .then(tx => {
    console.log("tx: ", tx)
  });
...
}
```

#### 测试一下

现在给它一个测试运行！

- 从代币中选择一个（确保您的钱包有足够的该代币；否则您将收到错误消息）
- 选择一个令牌
- 输入金额（确保您的钱包至少有该金额；否则报价将无法通过）
- 连接您的 MetaMask 钱包，应启用“交换”按钮
- 如果您点击“Swap”，您应该会看到 MetaMask 弹出窗口，询问您是否批准allowanceTarget，0x Exchange 代理合约地址：！`0xdef1c0ded9bec7f1a1670819833240f027b25eff`

> 这项交易确实需要收费。签字前请阅读。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830221427.png)


快完成了！

- ✅ 仅在连接 MetaMask 时启用“交换”按钮。
- ✅ 使用用户的 MetaMask 账户地址获取报价
- ✅ 设定代币限额
- ⚪ 执行交换

**第 8 部分的最终代码**

```
git clone https://github.com/0xProject/swap-demo-tutorial/tree/main/swap-demo-tutorial-part-8
```

## 第9部分:执行交换

现在是最后一部分，执行互换。这一部分应该是非常简单的，因为从/quote传回来的是一个JSON对象，它已经准备好被签署并作为区块链上的有效交易提交。

在末尾添加：`trySwap()`

```javascript
// index.js
async  function  trySwap(){
...
    // Perform the swap
    const  receipt = await  web3.eth.sendTransaction(swapQuoteJSON);
    console.log("receipt: ", receipt);
}
```

我们之所以能直接传入/quote[响应](https://docs.0x.org/0x-api-swap/api-references/get-swap-v1-quote#response)，是因为它包含了[web3.eth.setTransaction()](https://web3js.readthedocs.io/en/v1.2.11/web3-eth.html#id84)的所有必要参数--*from, to, value, gas, data, etc*.

现在，如果你选择一个代币对（有你自己的起始代币），输入起始金额，并连接你的MetaMask钱包，然后点击 "交换"，你应该得到这个弹出窗口，询问你是否愿意进行交易 就这样，你的应用程序就完成了!

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830221438.png)

- ✅ 仅在连接 MetaMask 时启用“交换”按钮
- ✅ 使用用户的 MetaMask 账户地址获取报价
- ✅ 设定代币限额
- 🥳 执行交换

如果您完成整个流程，您将能够批准代币限额并执行交换并在您的钱包中收到您新交换的代币！

你也可以[在 Etherscan 上检查您的交易](https://etherscan.io/tx/0x55501496927394ec5072ce287668e2ad4b6db933391e96c561575ea2256a8aaa)！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220830224924.png)



**第 9 部分的最终代码**

```
git clone https://github.com/0xProject/swap-demo-tutorial/tree/main/swap-demo-tutorial-part-9
```

## 学到更多

- [0x简介](https://docs.0x.org/introduction/introduction-to-0x)
- [了解有关 0x API /swap 的更多信息](https://docs.0x.org/0x-api-swap/introduction)
- [0x API 参考](https://docs.0x.org/0x-api-swap/api-references#introduction)
- [使用 0x 构建的指南和代码示例](https://docs.0x.org/introduction/guides)

## 挑战

现在要把你的dapp提高到一个新的水平! 这里有一些挑战，你可以自己尝试，以测试你的理解！

- 使用sources[ response 参数](https://docs.0x.org/0x-api-swap/api-references/get-swap-v1-quote#response-1)显示交换品来源的百分比分类（例如：最好的价格来自50%的Uniswap，50%的Kyber）。
- 目前，我们将[代币限额设置为最大金额](https://docs.alchemy.com/alchemy/road-to-web3/weekly-learning-challenges/9.-how-to-build-a-token-swap-dapp-with-0x-api#ii-set-the-approval-amount-to-maxapproval)。改变这一点，使之更安全，以便用户只批准需要的数量。
- 当用户输入新的 "to"令牌时计算价格（现在只在用户输入新的 "from"令牌时自动计算）。
- 以 $ 显示估计的 gas
- 过滤长令牌列表
- 允许用户切换链并获得正确的报价（记住令牌列表也会改变！）

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)