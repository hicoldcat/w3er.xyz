---
title: 理解和解决 MetaMask 错误代码
description: null
author: 李留白
weight: 0
date: 2022-11-15T13:06:19.896Z
lastmod: 2022-11-15T13:23:07.086Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211152106195.png
---

MetaMask 使我们能够访问去中心化应用程序 (dApp)，同时使用 MetaMask 钱包提供一种身份验证形式。它提供一键式安全登录流程，允许我们在前端使用[以太币访问区块链资源。](https://docs.ethers.io/)MetaMask 抽象出微妙的过程，例如在与区块链交互时签署交易，并将您的 MetaMask 的公共地址提供给应用程序。

因此，作为开发者，在构建这些dApps时，难免会出现错误，而这些错误应该得到妥善处理，让开发者和用户都知道哪里出了问题。由于 MetaMask 文档没有针对使用 MetaMask 时可能出现的多种错误的全面而清晰的指南，因此我在此处编制了一份最常见错误及其含义的列表。

## 4001

当尝试连接到钱包时，如果用户在此界面上的任意位置单击“取消”并终止该过程，则会返回`4001`错误。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211152108482.png)

这是错误的 JSON 结构：

```json
'4001': {
  standard: 'EIP-1193',
  message: 'User rejected the request.',
},
```

## 4100

当 dApp 想要对未经授权的帐户采取行动时，会发生此错误。

例如，要执行某些操作（如签名消息），您需要先使用以下`eth_requestAccounts`方法从 MetaMask 获得帐户权限：

```js
import { ethers } from "ethers";

const accounts = await ethereum.request({
  method: "eth_requestAccounts",
});

const address = accounts[0];
const provider = new ethers.providers.Web3Provider(ethereum);
const signer = provider.getSigner();
const signature = await signer.signMessage(address);
console.log(signer);
```

同样，要切换你的钱包链，你需要使用该`wallet_switchEthereumChain`方法，该方法依次通过 MetaMask 扩展程序请求权限：

```json
// switching the chain for MetaMask wallet

await provider.request({
  method: "wallet_switchEthereumChain",
  params: [{ chainId: "0x89" }],
});
```

如果权限被拒绝，钱包将返回此错误：

```json
'4100': {
  standard: 'EIP-1193',
  message: 'The requested account and/or method has not been authorized by the user.',
},
```

## 4200

MetaMask 启用两种方法：受限和不受限。这些方法允许 dApp 执行连接到钱包、签署交易以及添加或切换网络等操作。

MetaMask 不支持的方法会返回此错误：

```json
'4200': {
  standard: 'EIP-1193',
  message: 'The requested method is not supported by this Ethereum provider.',
},
```

[您可以在此处找到指向现有方法的链接](https://docs.metamask.io/guide/rpc-api.html#ethereum-json-rpc-methods)。

## 4900

当用户的 MetaMask 钱包未连接任何链时返回此错误：

```js
ethereum.on('disconnect', (error) => console.log(error));
```

这与`disconnect`事件有关。当钱包断开连接并且无法向链提交请求时会触发此事件。除了断开连接，这也可能由于网络连接问题而发生。

一旦我们发出`disconnect`，提供者将不会接受任何新的请求，直到重新建立与链的连接，这需要重新加载页面。您还可以使用`ethereum.isConnected()` [方法](https://docs.metamask.io/guide/ethereum-provider.html#ethereum-isconnected)来确定提供程序是否已断开连接。

这是错误的 JSON 响应：

```json
'4900': {
  standard: 'EIP-1193',
  message: 'The provider is disconnected from all chains.',
},
```

## 4901

此错误意味着用户未连接到该交易的适当链。例如，如果交易要求用户在 Polygon 链上，而他们在[Harmony 或以太坊区块](https://blog.logrocket.com/ethereum-vs-harmony-which-blockchain-right-for-you/)链上。

需要注意的一件事是，MetaMask 提供程序允许我们监听`chainChanged`事件，如果当前连接的链发生变化，该事件将监听并采取行动：

```js
ethereum.on('chainChanged', (chainId) => {console.log(chainId)});
```

我们[将 RPC 请求提交](https://blog.logrocket.com/leverage-ethereum-blockchain-data-json-rpc/)给当前连接的链，这使得通过监听变化来跟踪当前链 ID 变得很重要。每条链都有其唯一的 chainID，您可以在[Chainlist 上找到它。](https://chainlist.org/)

如果它无法向该特定链提交 RPC 请求，它会返回错误：

```json
'4901': {
  standard: 'EIP-1193',
  message: 'The provider is disconnected from the specified chain.',
}
```

## 32700

如果用户向合约发送不完整的请求对象，则会返回此错误。如果发送给合约的对象不包含它需要的所有数据，就会发生这种情况。

这是 JSON 响应：

```json
'-32700': {
    standard: 'JSON RPC 2.0',
    message: 'Invalid JSON was received by the server. An error occurred on the server while parsing the JSON text.',
  },
```

## 32600

此处，对象有效，但结构或属性不正确。它有点类似于 32700，但在这种情况下，它的内部结构不正确。

这是 JSON 响应：

```json
'-32600': {
    standard: 'JSON RPC 2.0',
    message: 'The JSON sent is not a valid Request object.',
  },
```

## 32601

如果指定的方法根本不存在，则返回此错误：

```json
'-32601': {
    standard: 'JSON RPC 2.0',
    message: 'The method does not exist / is not available.',
  },
```

[您可以在此处找到指向现有方法的链接](https://docs.metamask.io/guide/rpc-api.html#ethereum-json-rpc-methods)。

## 32602

[如果传递给RPC 方法](https://docs.metamask.io/guide/rpc-api.html#ethereum-json-rpc-methods)的参数不正确，我们会收到此错误。例如，在定义`transactionParameters` 时，`from`属性引用`accounts[0]`。

通常，`accounts[0]`应该是用户的钱包地址，但在这种情况下，我们将其分配给一个空数组：

```js
let accounts = [];
  const amountEth = 1
   const paymentAddress = '0x71C7656EC7ab88b098defB751B7401B5f6d8976F'
  const transactionParameters = { from: accounts[0], to: paymentAddress, value: web3.toWei(amountEth, 'ether') };

  function Mint(){
  ethereum.request({ method: 'eth_sendTransaction',  params: [transactionParameters] });

  }
```

该`Mint`函数将返回此特定错误，因为参数显然无效：

```json
'-32602': {
    standard: 'JSON RPC 2.0',
    message: 'Invalid method parameter(s).',
  },
```

## 32603

这是由几件事引起的一揽子错误。这可能是因为您正在尝试向您的钱包添加一条新链（手动或通过 dApp）但添加了错误的链数据。或者，你正试图完成一笔交易，但你没有足够的代币来支付 gas 费。例如，如果您在以太坊主网上进行交易，则无论您是否使用其他代币进行交易，都必须以 ETH 支付汽油费。

最后，它可能与没有最新版本的 MetaMask 一样微不足道。

这是错误的 JSON 响应：

```json
'-32603': {
    standard: 'JSON RPC 2.0',
    message: 'Internal JSON-RPC error.',
  },
```

## 32000

可能触发此错误的一种情况是，生产中使用的合约地址是您部署到测试网的合约。这是一个我们可以纠正的简单错误。

类似于我们拥有开发环境和生产环境的 Web2，在构建 dApp 时，我们使用测试网部署我们的合约，因此我们可以在构建时测试它，而无需在主网上使用真正的 ETH。因此，部署在测试网和主网上的同一份合约将具有不同的地址。如果您为您所在的链使用了错误的地址，则会返回此错误。

一般来说，在处理智能合约时，你需要像合约地址、ABI 文件和你的签名者这样的参数：

```js
const provider = new ethers.providers.Web3Provider(ethereum);
const signer = provider.getSigner();

const countContract = new ethers.Contract(contractAddress, contractABI, signer);

/*
 * Call the getAllCount method from your Smart Contract
 */
const count = await countContract.getAllCount()
```

如果这些参数中的任何一个错误，您很可能会得到他的错误，也称为“用户输入错误”：

```json
  '-32000': {
    standard: 'EIP-1474',
    message: 'Invalid input.',
  },
```

## 32001

在这种情况下，我们请求的资源在区块链上不存在。这可能是客户端的错字。

想象一下，您正在尝试获取有关 ETH 链上不存在的块号的信息：

```js
import Web3 from 'web3';

const web3 = new Web3(web3Provider);
var block = await web3.eth.getBlock({invalid block number})
```

你会得到这个 JSON 响应：

```json
'-32001': {
    standard: 'EIP-1474',
    message: 'Resource not found.',
  },
```

## 32002

此错误表示请求的资源确实存在，但在请求时当前不可用。当我们尝试访问当前正在使用的资源时，可能会发生这种情况。当您尝试使用特定资源/方法时，它可能会在 MetaMask 扩展上发生，例如当 MetaMask 当前正在执行相同操作时切换链。

在构建你的 dApp 时，你应该学会在该方法成功启动后禁用你的按钮，这样用户就不会快速连续点击。

这是此错误的 JSON 响应：

```json
'-32002': {
    standard: 'EIP-1474',
    message: 'Resource unavailable.',
  },
```

## 32003

这个错误可能是很多事情的结果。可能是因为发件人地址不存在，资金不足，账户被锁定，或者我们无法签署交易。要解决此问题，请确保交易中的所有内容都是正确的。

如果不满足完成交易所需的条件，则可以拒绝交易：

```json
'-32003': {
    standard: 'EIP-1474',
    message: 'Transaction rejected.',
  },
```

## 32004

完全不支持该方法。可能它不存在或者只是一个印刷错误：

```json
'-32004': {
    standard: 'EIP-1474',
    message: 'Method not supported.',
  },
```

## 32005

此错误表示 RPC 提供程序已超出速率限制。如果 RPC 提供程序终结点对请求数有速率限制，则可能会出现此问题：

```json
'-32005': {
    standard: 'EIP-1474',
    message: 'Request limit exceeded.',
  },
```

此外，错误消息“intrinsic gas too low”是一个相当常见的错误。这仅仅是因为在启动交易时分配的气体限制小于所需的限制。

这种情况最常发生在交易复杂的情况下，这可能会使汽油费无法预测：

```js
ethereum
.request({
  method: 'eth_sendTransaction',
  params: [
    {
      from: accounts[0],
      to: '0xbCfDCCDbE7B3D681A1144D25a31e0D4BE869079a',
      value: '0x293434x341af62c0000',
      gasPrice: '0x09184e242',
      gas: '0x2709',
      gasLimit: '0x2723'
    },
  ],
})
.then((txHash) => console.log(txHash))
.catch((error) => console.error);
```

## 结论

如果你已经走到最后，恭喜你！我们介绍了一堆可能的错误以及如何处理它们。现在，在构建你的下一个 dApp 时，你可以轻松地解释这些错误并修复它们，而不必对最初导致它们的原因感到困惑。这有助于改善您处理这些 Web3 技术的开发人员体验。

[您可以从官方资源](https://eips.ethereum.org/EIPS/eip-1474)中查看有关 MetaMask 错误的更多信息。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
