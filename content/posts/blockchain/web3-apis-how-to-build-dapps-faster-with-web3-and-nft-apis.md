---
title: Web3 API——如何使用 Web3 和 NFT API 更快地构建dApp
description: null
author: 李留白
weight: 0
date: 2022-09-18T13:14:50.192Z
lastmod: 2022-09-18T13:18:58.602Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220918204809.png
---

## 介绍

API 可帮助您创建与区块链交互的高质量且可维护的去中心化应用程序 (dApp)，以获取有关块、交易、元数据、代币定价等的信息。

在本文中，我们将讨论不同的 web3 平台，这些平台提供高效的 API 来支持您的 dApp 和区块链之间的通信。

## dApps 如何与区块链通信？

DApp 是基于区块链的应用程序，允许用户与部署在区块链上的智能合约进行交互。

每个 dApp 都需要一个远程过程调用 (RPC) 节点来执行其功能，因为它促进了 dApp 和区块链之间的通信。

这意味着如果没有 RPC，您的 dApp 将无法在区块链上执行交易。

## 什么是 RPC 节点？

> 节点：计算机或服务器

RPC 是在不同位置的两个或多个系统之间建立通信通道的传统方式。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220918204905.png)

RPC 节点也被称为*加密的大脑盒*，因为它管理系统之间的交互。在分布式系统（比如你的 dApp）中，子程序在不同地方的执行是很频繁的。

RPC 可以与您自己托管的[Rest API](https://www.redhat.com/en/topics/api/what-is-a-rest-api)进行比较，但它们仅在两个不同系统（您的 dApp 和区块链）之间提供**GET**和**POST通信方法。**另一方面，Rest API 支持**GET**、**POST**、**PATCH**、**PUT**和**DELETE**方法。

## 你应该开发一个 RPC 节点吗？

**简短的回答**：不！不要这样做。

**答案**：RPC 节点可能很难自己开发和维护，特别是如果您正在为您的 dApp 构建一个节点，而几乎不知道何时以及如何正确使用它们。

以下是开发和自托管 RPC 节点的一些限制：

- RPC 不是标准的，这个概念可以以不同的方式实现。
- RPC 需要复杂的基础架构才能正常工作。
- RPC 由于其复杂的基础架构而非常耗时。
- RPC 的创建和维护成本很高。
- RPC 需要大量资源。
- RPC 只是基于交互的。
- 围绕 RPC 节点构建并不是一个长期的解决方案。

RPC 限制可能会让人筋疲力尽。您甚至可能不需要自己开发或托管 RPC 节点，因为现在有许多 web3 平台提供了这些限制的解决方案。

RPC 服务提供商提供 web3 后端服务和一些最好的**web3**和**NFT API**，以支付少量费用为您的 dApp 提供动力。

## 什么是 Web3 API？

Web3 API 是由[Moralis](https://moralis.io/)、[Alchemy](https://www.alchemy.com/)和[QuickNode](https://www.quicknode.com/)等 web3 平台提供的区块链 API 。这些平台解决了 RCP 节点的限制，允许任何人以一种节省时间和资源的简单方式跨多个区块链项目查询数据。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220918204943.png)

通过一行代码，web3 API 可用于验证用户并检索有关块、交易、NFT 元数据、代币价格等的信息。

在从头开始创建 RPC 之前，您应该查看这些 web3 平台，看看它们是否提供了满足您的 dApp 要求的 API。

以下是一些可以使用 web3 和 NFT API 创建的 dApp 示例：

- NFT 市场
- 链上 NFT 游戏
- 数字资产所有权验证者
- NFT 铸币厂
- [Web3 身份验证](https://web3.hashnode.com/how-to-build-a-web3-login-with-web3js-library)。
- [NFT 浏览器](https://web3.hashnode.com/how-to-build-your-own-nft-explorer-with-moralis-react-sdk)。

下面列出了 Moralis、Alchemy 和 QuickNode 提供的一些 web3 API 和 NFT API：

## 1. Moralis Web3 API

[Moralis](https://moralis.io/)是一个为区块链项目提供后端服务的 web3 平台。他们提供最多数量的 web3 和 NFT API 用于身份验证、区块链帐户信息等。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220918204959.png)

### Moralis Web3 API - 身份验证

Moralis 允许您只用一行代码对任何区块链上的用户进行身份验证：

```js
Moralis.authenticate()
```

### Moralis Web3 API - 账户信息

- 使用 Moralis web3 API 获取用户的账户交易历史：

```js
const transactions = await Moralis.Web3API.account.getTransactions();
```

- 使用 Moralis web3 API 获取用户的代币余额：

```js
const balances = await Moralis.Web3API.account.getTokenBalances();
```

- 使用 Moralis web3 API 为当前用户获取 ERC20 代币传输：

```js
const userTrans = await Moralis.Web3API.account.getTokenTransfers();
```

### Moralis Web3 API - 原生资产转移

- 使用 Moralis web3 API 在 ETH（以太坊）、BNB（币安智能链）和 MATIC（多边形）区块链中的账户之间转移代币：

```js
// sending 0.5 ETH
const options = {
  type: "native",
  amount: Moralis.Units.ETH("0.5"),
  receiver: "0x.."
};
let result = await Moralis.transfer(options);
```

### Moralis Web3 API - 文件上传 (IPFS)

Moralis 提供了一个端点来存储和共享区块链上的文件。该系统被称为**IPFSInterPlanetary File System (IPFS)**。

- 在区块链上上传多个文件，并使用 Moralis web3 API 将它们放置在 Moralis 文件夹目录中：

```js
const options = {
  abi: [
    {
      path: "moralis/logo.jpg",
      content:
        "iVBORw0KGgoAAAANSUhEUgAAABgAAAAYCAYAAADgdz34AAAABHNCSVQICAgIfAhkiAAAAAlwSFlzAAAApgAAAKYB3X3",
    },
  ],
};
const path = await Moralis.Web3API.storage.uploadFolder(options);
```

> 代码片段示例来自[Moralis Web3 API 文档](https://docs.moralis.io/moralis-dapp/web3-sdk)。

## 2. Moralis NFT API

- 使用 Moralis NFT API 获取当前用户拥有的所有 NFT：

```js
const userEthNFTs = await Moralis.Web3API.account.getNFTs();
```

- 使用 Moralis NFT API 获取当前用户的任何 NFT 转账：

```JS
const transfersNFT = await Moralis.Web3API.account.getNFTTransfers();
```

- 使用 Moralis NFT API 获得过去 X 天内任何 NFT 的最低价格（以 Eth 计）：

```JS
const options = { address: "0xd...07", days: "3" };
const NFTLowestPrice = await Moralis.Web3API.token.getNFTLowestPrice(options);
```

- 使用 Moralis NFT API 基于元数据搜索获取 NFT 数据：

```JS
const options = { q: "Pancake", chain: "bsc", filter: "name" };
const NFTs = await Moralis.Web3API.token.searchNFTs(options);
```

- 使用 Moralis NFT API 获取给定代币合约地址的 NFT 所有者和数组及其 NFT 元数据（名称、符号）：

```JS
const options = { address: "0xd...07", chain: "bsc" };
const nftOwners = await Moralis.Web3API.token.getNFTOwners(options);
```

- 使用 Moralis NFT API 将 NFT 从一个账户转移到另一个账户：

```JS
// sending a token with token id = 1
const options = {
  type: "erc721",
  receiver: "0x..",
  contractAddress: "0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2",
  tokenId: 1,
};
let transaction = await Moralis.transfer(options);
```

- 使用 Moralis NFT API 在 Solana 网络上获取 NFT 元数据：

```JS
// get devnet metadata for a given SPL NFT address
const options = {
  network: "devnet",
  address: "6XU36wCxWobLx5Rtsb58kmgAJKVYmMVqy4SHXxENAyAe",
};
const nftMetadata = await Moralis.SolanaAPI.nft.getNFTMetadata(options);
```

> 代码片段示例来自[Moralis NFT 文档](https://docs.moralis.io/moralis-dapp/web3-sdk/nft-api)。

## 3. Alchemy NFT API

[Alchemy Web3 API](https://alchemy.com/)是[web3.js](https://web3.hashnode.com/what-is-web3js-an-introduction-into-the-web3js-libraries)的替代品，它的创建是为了与 Alchemy 一起轻松操作。它还提供了强大的 API 来为 dApp 提供普通节点所不具备的功能。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220918205352.png)

- 使用 Alchemy NFT API 获取当前由给定地址拥有的所有 NFT：

```JS
const nfts = await web3.alchemy.getNfts({owner: "0xC33881b8FD07d71098b440fA8A3797886D831061"})
```

- 使用 Alchemy NFT API 获取与给定 NFT 关联的元数据：

```JS
const response = await web3.alchemy.getNftMetadata({
  contractAddress: "0x5180db8F5c931aaE63c74266b211F580155ecac8",
  tokenId: "1590"
})
```

- 使用 Alchemy NFT API 获取 ERC721 和 ERC1155 合约的代币所有者：

```JS
import fetch from 'node-fetch';

var requestOptions = {
   method: 'GET',
   redirect: 'follow'
};

const apiKey = "demo"
const baseURL = `https://eth-mainnet.alchemyapi.io/v2/${apiKey}/getOwnersForToken`;
const contractAddr = "0x04b14e3383d42685ae16af3c47b21b2d5941d27e";
const tokenId = "867";
const fetchURL = `${baseURL}?contractAddress=${contractAddr}&tokenId=${tokenId}`;

fetch(fetchURL, requestOptions)
.then(response => response.json())
.then(response => JSON.stringify(response, null, 2))
.then(result => console.log(result))
.catch(error => console.log('error', error));
```

> 代码片段示例来自[Alchemy 的文档](https://docs.alchemy.com/alchemy/enhanced-apis/nft-api)。

## 4. QuickNode NFT API

[QuickNode](https://quicknode.com/)是一个 web3 平台，允许用户通过他们的 RPC 端点为包括以太坊和 Solana 在内的 10 多个网络构建和发展 dApp。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220918205451.png)

- 使用 QuickNode NFT API 验证以太坊区块链上的 NFT 所有者：

```JS
const heads = await provider.send(
    "qn_verifyNFTsOwner",
[
   "0x8ae6422631292c31aeeb2efe154d6326f703f46b",
  [
     "0x60e4d786628fea6478f785a6d7e704777c86a7c6:1090",
  ]
]
```

- 使用 QuickNode NFT API 从以太坊和 Solana 网络中获取和过滤 NFT：

```JS
const heads = await provider.send("qn_fetchNFTs", [
    "0x63a63d7b0a4da84e095bac389845615a09e05546",
   [
      "0xba30e5f9bb24caa003e9f2f0497ad287fdf95623",
   ],
]);
```

- 使用 QuickNode NFT API 查找具有创建者地址的 NFT：

```JS
// not currently supported by solanaJS
const axios = require("axios");
(() => {
  const url = "https://quick-infra-structure.solana-mainnet.quiknode.pro/4456aaba19e98f28c900e9d5f997d6f39728f551/";
  const config = {
    headers: {
      "Content-Type": "application/json",
    },
  };
  const data = {
    jsonrpc: "2.0",
    id: 1,
    method: "qn_fetchNFTsByCreator",
    params: [{
      creator: "5GUrnehPCrVeAeo29sgH3KbPhTvEDaH8HJqonYUceVM",
      page: 1,
      perPage: 3
    }]
  };
  axios
    .post(url, data, config)
    .then(function (response) {
      // handle success
      console.log(response.data);
    })
    .catch((err) => {
      // handle error
      console.log(err);
    });
})();
```

- 使用 QuickNode NFT API 获取 gas 价格：

```JS
const ethers = require("ethers");
(async () => {
  const provider = new ethers.providers.JsonRpcProvider("http://sample-endpoint-name.network.quiknode.pro/token-goes-here/");
  const gasPrice = await provider.getGasPrice();
  console.log(gasPrice);
})();
```

- 使用 QuickNode NFT API 获取用户钱包余额：

```JS
const ethers = require("ethers");
(async () => {
  const provider = new ethers.providers.JsonRpcProvider("http://sample-endpoint-name.network.quiknode.pro/token-goes-here/");
  const balance = await provider.getBalance(
    "0x8D97689C9818892B700e27F316cc3E41e17fBeb9",
    "latest"
  );
  console.log(balance);
})();
```

> 代码片段示例来自[QuickNode 文档](https://www.quicknode.com/docs/ethereum)。

## 总结

您可能已经意识到为什么从头开始构建 RPC 可能不是您的 dApp 的最佳解决方案。相反，利用来自 Web3 API 提供商（如 Moralis、Alchemy 和 Quicknode）的 web3 和 NFT API 可能更容易。

使用 web3 和 NFT API 可以加快开发速度并确保开发的可靠性。您还应该探索每个 web3 和 NFT API 提供商，看看哪一个为您的 dApp 提供了最佳解决方案。

## 你下一步要去哪里？

现在您已经了解了支持 dApp 的 web3 和 NFT API：

- [在此处](https://web3.hashnode.com/how-to-build-your-own-nft-explorer-with-moralis-react-sdk)了解如何使用 Moralis React SDK 构建您自己的 NFT Explorer 。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/WFt8uZi1z.gif)

- [在此处](https://web3.hashnode.com/how-to-build-a-web3-login-with-web3js-library)了解如何使用 Web3.js 库构建 Web3 登录。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/EWkN7pJkS.gif)

- [Solidity 教程 -在此处](https://web3.hashnode.com/solidity-tutorial-how-to-build-your-first-smart-contract)了解如何构建您的第一个智能合约 。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)