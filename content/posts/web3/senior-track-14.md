---
title: Web3系列教程之高级篇---14：MEV实战：让矿工按照你的规则行事
description: null
author: 李留白
weight: 0
date: 2022-08-14T01:12:09.809Z
lastmod: 2022-08-14T01:35:15.334Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/R3RYera.png
---

在MEV理论中，我们了解了什么是MEV，什么是Flashbots，以及Flashbots的一些使用案例。在这一关中，我们将学习如何使用Flashbots铸造一个NFT。这将是一个非常简单的使用案例，旨在教你如何使用Flashbots，而不一定是赚取利润。寻找可以使用MEV获利的机会是一个很难的问题，而且通常不是公共信息。每个搜索者都在努力做到最好，如果他们告诉你他们到底在使用什么策略，他们就是在自取灭亡。

这个教程只是为了告诉你如何使用Flashbots率先发送交易，其余的就看你自己了

## 构建

让我们建立一个关于如何使用Flashbots的例子

- 要设置一个Hardhat项目，请打开终端并执行这些命令

```
npm init --yes
npm install --save-dev hardhat
```

- 如果你是在Windows上，请做这个额外的步骤，也安装这些库 :)

```
npm install --save-dev @nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers
```

- 在你安装Hardhat的同一目录下运行。

```bash
npx hardhat
```

- 选择`Create a basic sample project`
- 对已指定的`Hardhat Project root`按回车键
- 如果你想添加一个`.gitignore`，请按回车键。
- 按回车键`Do you want to install this sample project's dependencies with npm (@nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers)?`

现在你有一个准备好的hardhat项目了!

让我们再安装几个依赖项，以进一步帮助我们

```
npm install @flashbots/ethers-provider-bundle @openzeppelin/contracts dotenv
```

让我们从创建一个FakeNFT合约开始。在你的`contracts`文件夹下创建一个名为`FakeNFT.sol`的新文件，并在其中添加以下几行代码

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

contract FakeNFT is ERC721 {

    uint256 tokenId = 1;
    uint256 constant price = 0.01 ether;
    constructor() ERC721("FAKE", "FAKE") {
    }

    function mint() public payable {
        require(msg.value == price, "Ether sent is incorrect");
        _mint(msg.sender, tokenId);
        tokenId += 1;
    }
}
```

这是一个相当简单的ERC-721合约，允许以0.01ETH铸造一个NFT。

现在，让我们用以下几行代码替换`hardhat.config.js`中的代码

```
require("@nomiclabs/hardhat-waffle");
require("dotenv").config({ path: ".env" });

const ALCHEMY_API_KEY_URL = process.env.ALCHEMY_API_KEY_URL;

const PRIVATE_KEY = process.env.PRIVATE_KEY;

module.exports = {
  solidity: "0.8.4",
  networks: {
    goerli: {
      url: ALCHEMY_API_KEY_URL,
      accounts: [PRIVATE_KEY],
    },
  },
};
```

请注意，我们在这里使用的是`goerli`，它是一个以太坊测试网，类似于Rinkeby和Ropsten，但却是Flashbots唯一支持的测试网。

现在是时候设置一些环境变量了，在你的根文件夹下创建一个新的文件`.env`，并在其中添加以下几行代码。

```
ALCHEMY_API_KEY_URL="ALCHEMY-API-KEY-URL"
PRIVATE_KEY="YOUR-PRIVATE-KEY"
ALCHEMY_WEBSOCKET_URL="ALCHEMY-WEBSOCKET-URL"
```

要获得你的`ALCHEMY_API_KEY_URL`和`ALCHEMY_WEBSOCKET_URL`，请进入[Alchemy](https://alchemy.com)，登录并创建一个新的应用程序。确保你在网络标签下选择`Goerli`

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814092304.png)

现在，复制`HTTP`网址并将其粘贴到`ALCHEMY-API-KEY`的位置，复制`WEBSOCKETS`并将其粘贴到`ALCHEMY-WEBSOCKET-URL`的位置。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814092344.png)

用你拥有Goerli Ether的账户的私钥替换你的`PRIVATE-KEY`，为了获得一些Goerli ether，请尝试[这个龙头](https://goerlifaucet.com/)。

现在是时候写一些代码，帮助我们与Flashbots互动。

在`scripts`文件夹下创建一个新文件，命名为`flashbots.js`，并在其中添加以下几行代码

```
const {
  FlashbotsBundleProvider,
} = require("@flashbots/ethers-provider-bundle");
const { BigNumber } = require("ethers");
const { ethers } = require("hardhat");
require("dotenv").config({ path: ".env" });

async function main() {
  // Deploy FakeNFT Contract
  const fakeNFT = await ethers.getContractFactory("FakeNFT");
  const FakeNFT = await fakeNFT.deploy();
  await FakeNFT.deployed();

  console.log("Address of Fake NFT Contract:", FakeNFT.address);

  // Create a Alchemy WebSocket Provider
  const provider = new ethers.providers.WebSocketProvider(
    process.env.ALCHEMY_WEBSOCKET_URL,
    "goerli"
  );

  // Wrap your private key in the ethers Wallet class
  const signer = new ethers.Wallet(process.env.PRIVATE_KEY, provider);

  // Create a Flashbots Provider which will forward the request to the relayer
  // Which will further send it to the flashbot miner
  const flashbotsProvider = await FlashbotsBundleProvider.create(
    provider,
    signer,
    // URL for the flashbots relayer
    "https://relay-goerli.flashbots.net",
    "goerli"
  );

  provider.on("block", async (blockNumber) => {
    console.log("Block Number: ", blockNumber);
    // Send a bundle of transactions to the flashbot relayer
    const bundleResponse = await flashbotsProvider.sendBundle(
      [
        {
          transaction: {
            // ChainId for the Goerli network
            chainId: 5,
            // EIP-1559
            type: 2,
            // Value of 1 FakeNFT
            value: ethers.utils.parseEther("0.01"),
            // Address of the FakeNFT
            to: FakeNFT.address,
            // In the data field, we pass the function selctor of the mint function
            data: FakeNFT.interface.getSighash("mint()"),
            // Max Gas Fes you are willing to pay
            maxFeePerGas: BigNumber.from(10).pow(9).mul(3),
            // Max Priority gas fees you are willing to pay
            maxPriorityFeePerGas: BigNumber.from(10).pow(9).mul(2),
          },
          signer: signer,
        },
      ],
      blockNumber + 1
    );

    // If an error is present, log it
    if ("error" in bundleResponse) {
      console.log(bundleResponse.error.message);
    }
  });
}

main();
```

现在让我们试着理解这几行代码中发生了什么。

在最初的几行代码中，我们部署了我们编写的`FakeNFT`合约。

之后，我们创建了一个Alchemy WebSocket提供者、一个签名者和一个Flashbots提供者。请注意，我们这次创建WebSocket提供者的原因是，我们想创建一个socket来监听`Goerli`网络中的每一个新块。正如我们之前所使用的HTTP提供者，其工作方式是请求-响应模式，即客户端向服务器发送请求，而服务器则作出回应。然而，在WebSockets的情况下，客户端与WebSocket服务器打开一次连接，然后只要连接保持开放，服务器就会持续向他们发送更新。因此，客户端不需要一次又一次地发送请求。

这样做的原因是`Goerli`网络中的所有矿工都不是Flashbot矿工。这意味着在某些区块中，你发送的交易束可能不被包括在内。

因此，我们监听每个区块并在每个区块中发送一个请求，这样当coinbase矿工（当前区块的矿工）是一个flashbots矿工时，我们的交易就会被包括在内。

```
// Create a Alchemy WebSocket Provider
  const provider = new ethers.providers.WebSocketProvider(
    process.env.ALCHEMY_WEBSOCKET_URL,
    "goerli"
  );

  // Wrap your private key in the ethers Wallet class
  const signer = new ethers.Wallet(process.env.PRIVATE_KEY, provider);

  // Create a Flashbots Provider which will forward the request to the relayer
  // Which will further send it to the flashbot miner
  const flashbotsProvider = await FlashbotsBundleProvider.create(
    provider,
    signer,
    // URL for the goerli flashbots relayer
    "https://relay-goerli.flashbots.net",
    "goerli"
  );
```

在初始化提供者和签名者之后，我们使用我们的提供者来监听区块事件。每当一个区块事件被调用，我们就打印区块号码，并发送一捆交易来铸造NFT。注意我们发送的交易束可能会也可能不会被包含在当前区块中，这取决于coinbase矿工是否是flashbot矿工。

现在，为了创建交易对象，我们指定链号（Goerli的链号为5），类型为2，因为我们将使用`Post-London Upgrade`的气体模型，即`EIP-1559`。为了恢复你对这种气体模型工作方式的记忆，请查看Sophomore中的气体模块。

我们指定价值为0.01，因为这是铸造1个NFT的金额，而地址是`FakeNFT`合约的地址。

现在，对于`data`，我们需要指定函数选择器，这是函数名称和参数的Keccak-256（SHA-3）哈希值的前四个字节，这将决定我们试图调用哪个函数，在我们的例子中，它将是mint函数。

然后，我们指定`maxFeePerGas`和`maxPriorityFeePerGas`分别为`3GWEI`和`2GWEI`。请注意，我在这里得到的数值是通过查看之前网络中的交易以及他们使用的气体费用而得到的。

另外，`1 GWEI = 10*WEI = 10*10^8 = 10^9`

我们希望该交易在下一个区块被开采，所以我们在当前区块号码上加1，并发送这捆交易。

在发送完bundle后，我们得到一个`bundleResponse`，在这个`bundleResponse`上，我们检查是否有错误，如果有，我们就记录下来。

现在注意，得到一个响应并不保证我们的包会被包含在下一个区块中。要检查它是否会被包含在下一个区块中，你可以使用`bundleResponse.wait()`，但在本教程中，我们只是耐心地等待几个区块并观察。

```
  provider.on("block", async (blockNumber) => {
    console.log("Block Number: ", blockNumber);
    // Send a bundle of transactions to the flashbot relayer
    const bundleResponse = await flashbotsProvider.sendBundle(
      [
        {
          transaction: {
            // ChainId for the Goerli network
            chainId: 5,
            // EIP-1559
            type: 2,
            // Value of 1 FakeNFT
            value: ethers.utils.parseEther("0.01"),
            // Address of the FakeNFT
            to: FakeNFT.address,
            // In the data field, we pass the function selctor of the mint function
            data: FakeNFT.interface.getSighash("mint()"),
            // Max Gas Fees you are willing to pay
            maxFeePerGas: BigNumber.from(10).pow(9).mul(3),
            // Max Priority gas fees you are willing to pay
            maxPriorityFeePerGas: BigNumber.from(10).pow(9).mul(2),
          },
          signer: signer,
        },
      ],
      blockNumber + 1
    );

    // If an error is present, log it
    if ("error" in bundleResponse) {
      console.log(bundleResponse.error.message);
    }
  });
```

现在要运行这段代码，在你的终端上指向根目录，执行以下命令。

```
npx hardhat run scripts/flashbots.js --network goerli
```

在你的终端上打印出一个地址后，进入[Goerli Etherscan](https://goerli.etherscan.io/)，不断刷新页面，直到你看到`Mint`交易出现（注意，它需要一些时间才能出现，因为flashbot矿工必须是coinbase矿工，我们的捆绑物才能被包括在区块中）。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814093128.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814093142.png)

我们现在学会了如何使用flashbots来铸造一个NFT，但你可以做得更多 👀

## 阅读

- [Flashbots Docs](https://docs.flashbots.net/)
- [Arbitrage bot using Flashbots](https://github.com/flashbots/simple-arbitrage)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
