---
title: 使用 Reactjs、TailwindCSS、Etherjs、IPFS 和 Solidity 构建去中心化的动态新闻网站
description: null
author: 李留白
weight: 0
date: 2022-10-13T14:15:42.565Z
lastmod: 2022-10-13T14:15:49.297Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221006213741.png
---

去中心化应用程序（dApps）是区块链技术最有前途的应用之一。它们为具有前所未见的功能的消费者和以商业为中心的产品开辟了新的可能性。

看到如何构建强大的去中心化应用程序来补充商业环境是很有趣的。

这篇文章将教我们如何使用 Reactjs、TailwindCSS、Etherjs、IPFS 和 Solidity 构建组中心化的动态新闻网站。

这将是一个互联网上的任何人都可以阅读、分享和发布新闻的平台，数据使用智能合约存储在 Polygon 网络的区块链上。

智能合约是存储在区块链上的代码，可以读取和写入；我们稍后会详细介绍。

我们将构建和部署智能合约和一个允许人们连接钱包并与我们的智能合约交互的网站。

👉 GitHub 存储库

- [前端](https://github.com/Olanetsoft/newsfeed-fe)
- [智能合约](https://github.com/Olanetsoft/newsfeed-be)

## 先决条件

让我们确保我们的 PC 上安装了 Node/NPM。如果我们没有安装它，请前往[此处](https://hardhat.org/tutorial/setting-up-the-environment.html)获取指南。

## 项目设置和安装

让我们导航到终端。我们需要`cd`进入我们选择的任何目录，然后运行以下命令：

```
mkdir newsfeed-be
cd newsfeed-be
npm init -y
npm install --save-dev hardhat
```

让我们通过运行以下命令来获取示例项目：

```
npx hardhat
```

我们将使用以下选项：

- 一个示例项目。

- 接受所有其他请求。

安装`hardhat-waffle`和`hardhat-ethers`是示例项目所必需的。

万一它没有自动安装，我们将使用以下命令安装这个其他要求：

```
npm install --save-dev @nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers @openzeppelin/contracts
```

接下来，我们将为本教程后面使用的计数器安装`@openzeppelin/contracts`。

```
npm i @openzeppelin/contracts
```

我们将在控制台中看到通过的测试结果。

现在我们可以从测试文件夹中删除`sample-test.js`并从`scripts`目录中删除`sample-script.js`。之后，进入`contracts`并删除`Greeter.sol.`

> 不应删除文件夹本身！

我们将在contracts 目录中创建一个`NewsFeed.sol`文件。使用 Hardhat 时，文件布局至关重要，所以要注意！我们将从每份合同的基本结构开始。

```solidity
// SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.0;

import "hardhat/console.sol";

contract NewsFeed {

     constructor() {
        console.log("NewsFeed deployed");
    }
}
```



要构建和部署我们的智能合约，我们将导航到`scripts`文件夹，创建一个`run.js`文件，并使用以下代码片段对其进行更新：

```solidity
const main = async () => {
  // This will actually compile our contract and generate the necessary files we need to work with our contract under the artifacts directory.
  const newsFeedContractFactory = await hre.ethers.getContractFactory(
    "NewsFeed"
  );
  const newsFeedContract = await newsFeedContractFactory.deploy();

  await newsFeedContract.deployed(); // We'll wait until our contract is officially deployed to our local blockchain! Our constructor runs when we deploy.

  console.log("NewsFeed Contract deployed to: ", newsFeedContract.address);
};

const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.log(error);
    process.exit(1);
  }
};

runMain();
```

让我们使用以下命令运行它。

```
npx hardhat run scripts/run.js
```

您应该会看到类似于下面的内容：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132137467.png)

现在我们有了一个有效的智能合约🥳 让我们将它部署到本地网络。

在该`scripts`文件夹下，我们将创建一个`deploy.js`文件。添加以下代码片段。

```js
const main = async () => {
  const [deployer] = await hre.ethers.getSigners();
  const accountBalance = await deployer.getBalance();

  console.log("Deploying contracts with account: ", deployer.address);
  console.log("Account balance: ", accountBalance.toString());

  const Token = await hre.ethers.getContractFactory("NewsFeed");
  const portal = await Token.deploy();
  await portal.deployed();

  console.log("NewsFeed address: ", portal.address);
};

const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.error(error);
    process.exit(1);
  }
};

runMain();
```

在部署之前，让我们确保我们的本地节点已启动并使用以下命令在单独的终端中运行。

```
npx hardhat node
```

接下来，我们将部署我们的智能合约。

```
npx hardhat run scripts/deploy.js --network localhost
```

应该看起来像这样：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132139913.png)

## 构建和部署 NewsFeed 智能合约到区块链

确保包括测试脚本和`deploy.js`文件在内的一切，都已准备就绪。我们将使用以下代码片段更新智能合约、`run.js,`和`deploy.js`。

更新`contracts/NewsFeed.sol`文件。

https://gist.github.com/Olanetsoft/5d0d95bc7a7467469cab9384366a0100#file-newsfeed-sol

```solidity

//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;

import "hardhat/console.sol";
import "@openzeppelin/contracts/utils/Counters.sol";

contract NewsFeed {
    uint256 totalFeeds;

    using Counters for Counters.Counter;
    Counters.Counter private _feedIds;

    constructor() {
        console.log("NewsFeed deployed");
    }

    /*
     * I created a struct here named Feed.
     * A struct is a custom datatype where we can customize what we want to hold inside it.
     */
    struct Feed {
        uint256 id;
        string title;
        string description;
        string location;
        string category;
        string coverImageHash;
        string date;
        address author;
    }

    /*
     * A little magic is known as an event in Solidity!
     */
    event FeedCreated(
        uint256 id,
        string title,
        string description,
        string location,
        string category,
        string coverImageHash,
        string date,
        address author
    );

    /*
     * I declare variable feeds that let me store an array of structs.
     * This is what holds all the feeds anyone ever created.
     */
    Feed[] feeds;

    /*
     * This function will be used to get all the feeds.
     */
    function getAllFeeds() public view returns (Feed[] memory) {
        /*
         * This is a function that will return the length of the array.
         * This is how we know how many feeds are in the array.
         */
        return feeds;
    }

    /*
     * This function will be used to get the number of feeds.
     */
    function getTotalFeeds() public view returns (uint256) {
        return totalFeeds;
    }

    /*
     * This is a function that will be used to get a feed.
     * It will take in the following parameters:
     * - _id: The id of the feed
     */
    function getFeed(uint256 _id) public view returns (Feed memory) {
        /*
         * We are using the mapping function to get the feed from the mapping.
         * We are using the _id variable to get the feed from the mapping.
         */
        return feeds[_id];
    }

    /*
     * This function will be used to create a news feed.
     * It will take in the following parameters:
     * - _title: The title of the feed
     * - _description: The description of the feed
     * - _location: The location of the feed
     * - _category: The category of the feed
     * - _coverImageHash: The hash of the cover image of the feed
     * - _date: The date of the feed
     */

    function createFeed(
        string memory _title,
        string memory _description,
        string memory _location,
        string memory _category,
        string memory _coverImageHash,
        string memory _date
    ) public {
        // Validation
        require(bytes(_coverImageHash).length > 0);
        require(bytes(_title).length > 0);
        require(bytes(_description).length > 0);
        require(bytes(_location).length > 0);
        require(bytes(_category).length > 0);
        require(msg.sender != address(0));

        totalFeeds++;

        /* Increment the counter */
        _feedIds.increment();

        /*
         * We are using the struct Feed to create a news feed.
         To create a news feed* We use the id, title, description, location, category, coverImageHash, date, and author variables.
         */
        feeds.push(
            Feed(
                _feedIds.current(),
                _title,
                _description,
                _location,
                _category,
                _coverImageHash,
                _date,
                msg.sender
            )
        );

        /*
         * We are using the event FeedCreated to emit an event.
         To emit an event*, We use the id, title, description, location, category, coverImageHash, date, and author variables.
         */
        emit FeedCreated(
            _feedIds.current(),
            _title,
            _description,
            _location,
            _category,
            _coverImageHash,
            _date,
            msg.sender
        );
    }
}
```

更新`scripts/run.js`

```js
const main = async () => {
  // This will actually compile our contract and generate the necessary files we need to work with our contract under the artifacts directory.
  const newsFeedContractFactory = await hre.ethers.getContractFactory(
    "NewsFeed"
  );
  const newsFeedContract = await newsFeedContractFactory.deploy();

  await newsFeedContract.deployed(); // We'll wait until our contract is officially deployed to our local blockchain! Our constructor runs when we deploy.

  console.log("NewsFeed Contract deployed to: ", newsFeedContract.address);
};

const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.log(error);
    process.exit(1);
  }
};

runMain();
```

`scripts/deploy.js`



```js
const main = async () => {
  const [deployer] = await hre.ethers.getSigners();
  const accountBalance = await deployer.getBalance();

  console.log("Deploying contracts with account: ", deployer.address);
  console.log("Account balance: ", accountBalance.toString());

  const Token = await hre.ethers.getContractFactory("NewsFeed");
  const portal = await Token.deploy();
  await portal.deployed();

  console.log("NewsFeed address: ", portal.address);
};

const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.error(error);
    process.exit(1);
  }
};

runMain();
```

终于到了开始做生意并部署到区块链的时候了。

在部署到区块链之前，我们需要创建一个[Alchemy](https://alchemy.com/?r=37c4f8545ef30da7)帐户。

Alchemy 使我们能够广播我们的合约创建交易，以便矿工可以尽快获取它。一旦被挖掘，该交易将作为有效交易发布到区块链。之后，每个人的区块链副本都会更新。

注册后，我们将创建一个如下所示的应用程序。请记住将网络切换到我们将部署的孟买。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132146368.png)

我们将需要获取我们的密钥，如下所示，并将它们存储起来以备后用：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132146653.png)

我们的测试网帐户中需要一些 MATIC 代币，并且我们必须从网络中请求一些。Polygon Mumbai 可以通过使用水龙头获得一些虚假的 MATIC。这个假 MATIC 只能在这个测试网上使用。

我们可以[在这里获取一些 MATIC 代币](https://faucet.polygon.technology/)

让我们更新根项目目录中的`hardhat.config.js`文件。

```js
require("@nomiclabs/hardhat-waffle");
require("dotenv").config();

// This is a sample Hardhat task. To learn how to create your own go to
// https://hardhat.org/guides/create-task.html
task("accounts", "Prints the list of accounts", async (taskArgs, hre) => {
  const accounts = await hre.ethers.getSigners();

  for (const account of accounts) {
    console.log(account.address);
  }
});

// You need to export an object to set up your config
// Go to https://hardhat.org/config/ to learn more

/**
 * @type import('hardhat/config').HardhatUserConfig
 */
module.exports = {
  solidity: "0.8.4",
  networks: {
    mumbai: {
      url: process.env.STAGING_ALCHEMY_KEY,
      accounts: [process.env.PRIVATE_KEY],
    },
  },
};
```

如果我们查看上面的代码片段，我们可以看到从`.env`文件中读取了一些键，以及顶部的导入`require("dotenv").config(),`意味着我们需要安装`dotenv`包并使用以下命令创建一个`.env`文件：

```
npm install -D dotenv

touch .env
```

在`.env`文件中，我们将添加以下键：

```
STAGING_ALCHEMY_KEY= // Add the key we copied from the Alchemy dashboard here
PRIVATE_KEY= // Add your account private key here
```

获取我们的私人帐户密钥很容易。看看这篇[文章](https://metamask.zendesk.com/hc/en-us/articles/360015289632-How-to-Export-an-Account-Private-Key)。

接下来，让我们编写一个基本测试来测试我们将使用的最关键的功能。

为此，我们将在 test 目录中创建一个`feed-test.js`文件，并使用以下代码对其进行更新：

https://gist.github.com/Olanetsoft/23424ab459cfd10b02187a816d4ea60e#file-feed-test-js

```js
const { expect } = require("chai");
const { ethers } = require("hardhat");

describe("NewsFeed", function () {
  this.timeout(0);

  let NewsFeed;
  let newsFeedContract;

  before(async () => {
    NewsFeed = await ethers.getContractFactory("NewsFeed");
    newsFeedContract = await NewsFeed.deploy();
  });

  it("should deploy", async () => {
    expect(newsFeedContract.address).to.not.be.null;
  });

  it("should have a default value of 0", async () => {
    const value = await newsFeedContract.getTotalFeeds();
    expect(value.toString()).to.equal("0");
  });

  it("should be able to create feed", async () => {
    const tx = await newsFeedContract.createFeed(
      "Hello World",
      "New York world",
      "New York",
      "Sports",
      "0x123",
      "2022-05-05"
    );
    expect(tx.hash).to.not.be.null;
  });

  it("should be able to get feeds", async () => {
    const tx = await newsFeedContract.createFeed(
      "Hello World",
      "New York world",
      "New York",
      "Sports",
      "0x123",
      "2022-05-05"
    );

    // get feeds
    const feeds = await newsFeedContract.getAllFeeds();
    expect(feeds.length).to.equal(2);
  });

  it("should be able to get feed count", async () => {
    const tx = await newsFeedContract.createFeed(
      "Hello World",
      "New York world",
      "New York",
      "Sports",
      "0x123",
      "2022-05-05"
    );
    const newsCount = await newsFeedContract.getTotalFeeds();
    expect(newsCount.toString()).to.equal("3");
  });

  it("should be able to get feed by id", async () => {
    const tx = await newsFeedContract.createFeed(
      "Hello World",
      "New York world",
      "New York",
      "Sports",
      "0x123",
      "2022-05-05"
    );
    const news = await newsFeedContract.getFeed(2);
    expect(news.title).to.equal("Hello World");
  });
});
```

接下来，我们将使用以下命令运行测试：

```
npx hardhat test
```

现在我们可以运行命令将我们的合约部署到真正的区块链网络。

```
npx hardhat run scripts/deploy.js --network mumbai
```

我们的输出应该如下所示。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132150785.png)

我们刚刚部署了我们的合约。🥳🥳🥳

## 构建前端 React 客户端

为了快速开始项目设置和安装，我们将[在 GitHub 上克隆这个项目](https://github.com/Olanetsoft/newsfeed-fe/tree/project-setup)并确保我们在`project-setup`分支上。

接下来，我们将在终端上使用以下命令克隆项目后在本地启动项目。

```
cd newsfeed-fe && yarn && yarn start
```

或者

```
cd newsfeed-fe && npm install && npm start
```

克隆和安装项目后，我们应该有类似下面的内容：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132151967.png)

我们希望从我们刚刚启动的智能合约中获取所有动态欣慰，而不需要用户连接他们的钱包。这意味着任何人都可以使用我们的应用程序来浏览信息，而无需链接他们的钱包，并且只有在他们希望创建动态新闻时才连接钱包。

让我们使用以下代码片段更新utilities文件夹中的`getContract.js`文件。

```
import ContractAbi from "./newsFeed.json";
import { ethers } from "ethers";

export default function getContract() {
  const provider = new ethers.providers.Web3Provider(window.ethereum);
  const signer = provider.getSigner(
    "0x2C08B4B909F02EA5D8A0E44986720D76BAC8224B" // Random (fake) wallet address
  );
  let contract = new ethers.Contract(
    "0x545ed82953b300ae5a8b21339c942788599Cd239", // Our contract adress
    ContractAbi.abi,
    signer
  );
  return contract;
}
```

在上面的代码片段中，我们得到了我们的合约并在`getSigner`函数中包含了一个随机的钱包地址。这是因为我们希望每个访问我们网站的人都无需连接钱包即可阅读新闻。

> 在区块链上创建/保存记录时确保传递有效的以太坊钱包地址

当我们将合约部署到区块链时，我们还添加了显示在终端中的合约地址。

让我们回到我们之前工作的智能合约项目，然后导航到`artifacts/contracts/NewsFeed.json`并复制其中的全部内容。我们将使用我们复制的`utilities`内容更新文件夹中的 newsfeed.json 文件。

### 构建 FeedList 组件

在本节中，我们将在`component`文件夹中创建一个`FeedList.js`文件，并使用以下代码片段对其进行更新。

https://gist.github.com/Olanetsoft/d0270f1bba5ded06903b83b21f072979#file-feedlist-js

```react
import React from "react";
import { BiCheck } from "react-icons/bi";

export default function FeedList({ horizontal, feed }) {
  return (
    <div
      className={`${
        horizontal
          ? "flex flex-row mx-5 mb-5 item-center justify-center"
          : "flex flex-col m-5"
      } `}
    >
      <img
        className={
          horizontal
            ? "object-cover rounded-lg w-60 h-40"
            : "object-cover rounded-lg w-full h-40"
        }
        src={`https://ipfs.infura.io/ipfs/${feed.coverImageHash}`}
        alt="cover"
      />
      <div className={horizontal && "ml-3  w-80"}>
        <h4 className="text-md font-bold dark:text-white mt-3 text-black">
          {feed.title}
        </h4>
        {horizontal && (
          <p className="text-sm flex items-center text-textSubTitle mt-1">
            {feed.category}
          </p>
        )}
        {horizontal && (
          <p className="text-sm flex items-center text-textSubTitle mt-1">
            {feed.description.slice(0, 30)}...
          </p>
        )}
        <p className="text-sm flex items-center text-textSubTitle mt-1">
          {horizontal ? null : feed.category + " • "}
          {feed?.author?.slice(0, 12)}...{" "}
          <BiCheck size="20px" color="green" className="ml-1" />
        </p>
      </div>
    </div>
  );
}
```

接下来，我们将导入`FeedList`组件、toast  response，`ToastContainer`并使用以下代码片段更新`HomePage.js`文件。

https://gist.github.com/Olanetsoft/a212f4850fba9ea9c6de1230581086de#file-homepage-js

```react
import React, { useState, useEffect } from "react";
import { Header } from "./components/Header";
import FeedList from "./components/FeedList";
import { Link } from "react-router-dom";

import { success, error, warn } from "./utilities/response";

import "react-toastify/dist/ReactToastify.css";

export default function Main() {
  //...

  // Create a state variable to store the feeds in the blockchain
  const [feeds, setFeeds] = useState([]);

  return (
    <div className="w-full  flex flex-row">
      <div className="flex-1 flex flex-col">
        <Header />
        <div className="flex-1 flex flex-row flex-wrap">
          {feeds.map((feed, index) => {
            return (
              <Link to={`/feed?id=${feed.id}`} key={index}>
                <div className="w-80 h-80 m-2">
                  <FeedList feed={feed} />
                </div>
              </Link>
            );
          })}
          {loading && (
            <div className="flex-1 flex flex-row flex-wrap">
              {Array(loadingArray)
                .fill(0)
                .map((_, index) => (
                  <div key={index} className="w-80">
                    <Loader />
                  </div>
                ))}
            </div>
          )}
        </div>
      </div>
    </div>
  );
}

const Loader = () => {
  return (
    <div className="flex flex-col m-5 animate-pulse">
      <div className="w-full bg-gray-300 dark:bg-borderGray h-40 rounded-lg "></div>
      <div className="w-50 mt-3 bg-gray-300 dark:bg-borderGray h-6 rounded-md "></div>
      <div className="w-24 bg-gray-300 h-3 dark:bg-borderGray mt-3 rounded-md "></div>
    </div>
  );
};

```

因为区块链上还没有记录，而且我们还没有创建检索所有动态新闻的函数，所以我们应该有类似于上面显示的内容，它显示为空。

### 构建用户的 Connect 钱包功能

本部分将构建允许用户在我们的平台上联系他们的钱包以创建动态新闻的功能。

让我们用下面的代码片段来更新`HomePage.js`

https://gist.github.com/Olanetsoft/c7a9fa4b737aef275b5e72ed803cb126#file-homepage-js

```js
import React, { useState, useEffect } from "react";
import { Header } from "./components/Header";
import { ToastContainer } from "react-toastify";
import FeedList from "./components/FeedList";
import { Link } from "react-router-dom";
import getContract from "./utilities/getContract";

import { success, error, warn } from "./utilities/response";

import "react-toastify/dist/ReactToastify.css";

export default function Main() {
  const [loading, setLoading] = useState(false);
  const [loadingArray] = useState(15);

  // Create a state variable to store the feeds in the blockchain
  const [feeds, setFeeds] = useState([]);

  /*
   * A state variable we use to store our user's public wallet.
   */
  const [currentAccount, setCurrentAccount] = useState("");

  /*
   * A function to check if a user wallet is connected.
   */
  const checkIfWalletIsConnected = async () => {
    try {
      const { ethereum } = window;

      /*
       * Check if we're authorized to access the user's wallet
       */
      const accounts = await ethereum.request({ method: "eth_accounts" });

      if (accounts.length !== 0) {
        const account = accounts[0];
        setCurrentAccount(account);
        success("🦄 Wallet is Connected!");
      } else {
        success("Welcome 🎉  ");
        warn("To create a feed, Ensure your wallet Connected!");
      }
    } catch (err) {
      error(`${err.message}`);
    }
  };

  /**
   * Implement your connectWallet method here
   */
  const connectWallet = async () => {
    try {
      const { ethereum } = window;

      if (!ethereum) {
        warn("Make sure you have MetaMask Connected");
        return;
      }

      const accounts = await ethereum.request({
        method: "eth_requestAccounts",
      });
      setCurrentAccount(accounts[0]);
    } catch (err) {
      error(`${err.message}`);
    }
  };

  /*
   * This runs our function when the page loads.
   */
  useEffect(() => {
    checkIfWalletIsConnected();

    /*
     * This is a hack to make sure we only run the function once.
     * We need to do this because we're using the useEffect hook.
     * We can't use the useEffect hook more than once.
     * https://reactjs.org/docs/hooks-effect.html
     * https://reactjs.org/docs/hooks-faq.html#how-do-i-implement-the-effects-api
     * https://reactjs.org/docs/hooks-faq.html#how-do-i-optimize-the-effects-of-a-component
     */
    const onFeedCreated = async (
      id,
      title,
      description,
      location,
      category,
      coverImageHash,
      date,
      author
    ) => {
      setFeeds((prevState) => [
        ...prevState,
        {
          id,
          title,
          description,
          location,
          category,
          coverImageHash,
          date,
          author,
        },
      ]);
    };

    let contract;

    if (window.ethereum) {
      contract = getContract();
      contract.on("FeedCreated", onFeedCreated);
    }

    return () => {
      if (contract) {
        contract.off("FeedCreated", onFeedCreated);
      }
    };
  }, []);

  return (
    <div className="w-full  flex flex-row">
      <div className="flex-1 flex flex-col">
        <Header
          currentAccount={currentAccount}
          connectWallet={connectWallet}
          ToastContainer={ToastContainer}
        />
        <div className="flex-1 flex flex-row flex-wrap">
          //..
        </div>
      </div>
    </div>
  );
}

const Loader = () => {
 //...
};
```

接下来，我们将更新`Header.js`文件。

https://gist.github.com/Olanetsoft/d6715d39901aad03d1b1c7fa302075b7#file-header-js

```js
import React from "react";
import { Link } from "react-router-dom";

export const Header = ({ currentAccount, connectWallet, ToastContainer }) => {
  return (
    <header className="w-full flex justify-between h-20 items-center border-b p-4 border-borderWhiteGray dark:border-borderGray">
      <div className=" w-1/3">
        <Link to="/" className="flex items-center">
          <h1 className="text-2xl font-bold text-green-700">Home</h1>
        </Link>
      </div>
      <div className=" w-1/3 flex justify-center items-center">
        <h1 className="text-2xl font-bold text-green-500 dark:text-green-400">
          News Feed!
        </h1>
      </div>

      {currentAccount ? (
        <div className="w-1/3 flex justify-end items-center">
          <Link to="/upload">
            <button className="items-center bg-green-600 rounded-full font-medium p-2 shadow-lg color-blue-500 hover:bg-green-500 focus:outline-none focus:shadow-outline text-white">
              <span className="">Create a New Feed</span>
            </button>
          </Link>
        </div>
      ) : (
        <div className=" w-1/3 flex justify-end">
          <button
            className="items-center bg-green-700 rounded-full font-medium p-3 shadow-lg color-blue-500 hover:bg-green-500 focus:outline-none focus:shadow-outline text-white"
            onClick={() => {
              connectWallet();
            }}
          >
            <span className="">Connect your wallet</span>
          </button>
        </div>
      )}
      <ToastContainer
        position="top-center"
        autoClose={5000}
        hideProgressBar={false}
        newestOnTop={false}
        closeOnClick
        rtl={false}
        pauseOnFocusLoss
        draggable
        pauseOnHover
      />
    </header>
  );
};
```

单击该`Connect your Wallet`按钮，我们将得到一个MetaMask登录弹出窗口。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132159304.png)

连接后，我们将被重定向回我们的应用程序，之前显示的`Connect your wallet`按钮现在显示`Create a Feed`如下。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132159249.png)

### 构建上传动态新闻页面

我们将建立一个页面，用户可以在其中输入新的动态新闻详细信息并将其上传到区块链。让我们在`src`目录中创建`UploadPage.js`并使用以下代码片段对其进行更新。

https://gist.github.com/Olanetsoft/270a02d0ed6d0745afa0859e169ed110#file-uploadpage-js

```js
import React, { useState, useRef } from "react";
import { create } from "ipfs-http-client";
import { BiCloud, BiPlus } from "react-icons/bi";
import getContract from "./utilities/getContract";
import { ToastContainer } from "react-toastify";

import { success, error, defaultToast } from "./utilities/response";

export default function Upload() {
  /*
   * A state variable we use to store new feed input.
   */

  const [title, setTitle] = useState("");
  const [description, setDescription] = useState("");
  const [category, setCategory] = useState("");
  const [location, setLocation] = useState("");
  const [coverImage, setCoverImage] = useState("");

  /*
   * Create an IPFS node
   */
  const client = create("https://ipfs.infura.io:5001/api/v0");
  const coverImageRef = useRef();

  /*
   * A function to handle validation of uploading a new feed.
   */
  const handleSubmit = async () => {
    if (
      title === "" ||
      description === "" ||
      category === "" ||
      location === "" ||
      coverImage === ""
    ) {
      error("Please, all the fields are required!");
      return;
    }

    /*
     * Upload the cover image to IPFS
     */
    uploadCoverImage(coverImage);
  };

  /*
   * A function to upload a cover image to IPFS
   */
  const uploadCoverImage = async (coverImage) => {
    defaultToast("Uploading Cover Image...");

    try {
      const image = await client.add(coverImage);

      /*
       * Save the new feed to the blockchain
       */
      await saveFeed(image.path);
    } catch (err) {
      error("Error Uploading Cover Image");
    }
  };

  /*
   * A function to save a new feed to the blockchain
   */
  const saveFeed = async (coverImage) => {
    defaultToast("Saving Feed...");

    console.log(title, description, category, location, coverImage);

    try {
      const contract = await getContract();
      const UploadedDate = String(new Date());

      /*
       * Save the new feed to the blockchain
       */
      await contract.createFeed(
        title,
        description,
        location,
        category,
        coverImage,
        UploadedDate
      );

      success("Feed Saved Successfully");

      // reset form
      setTitle("");
      setDescription("");
      setCategory("");
      setLocation("");
      setCoverImage("");

      // Redirect to Home Page
      window.location.href = "/";
    } catch (err) {
      error("Error Saving Feed");
    }
  };

  // Handles redirect to Home Page or previous page
  const goBack = () => {
    window.history.back();
  };

  return (
    <div className="w-full h-screen flex flex-row">
      <div className="flex-1 flex flex-col">
        <div className="mt-5 mr-10 flex  justify-end">
          <div className="flex items-center">
            <button
              className="bg-transparent  dark:text-[#9CA3AF] py-2 px-6 border rounded-lg  border-gray-600  mr-6"
              onClick={() => {
                goBack();
              }}
            >
              Discard
            </button>
            <button
              onClick={() => {
                handleSubmit();
              }}
              className="bg-blue-500 hover:bg-blue-700 text-white py-2 rounded-lg flex px-4 justify-between flex-row items-center"
            >
              <BiCloud />
              <p className="ml-2">Upload</p>
            </button>
          </div>
        </div>
        <div className="flex flex-col m-10 mt-5 lg:flex-row lg:justify-center">
          <div className="flex lg:w-3/4 flex-col ">
            <label className="text-gray-600 dark:text-[#9CA3AF] text-md font-bold mb-2">
              Title
            </label>
            <input
              value={title}
              onChange={(e) => setTitle(e.target.value)}
              placeholder="Web3 is taking over the world!"
              className="w-[60%] dark:text-white dark:placeholder:text-gray-600 rounded-xl mt-2 h-12 p-2 border border-borderWhiteGray bg-white dark:bg-backgroundBlack dark:border-[#444752] focus:outline-none"
            />
            <label className="text-gray-600 dark:text-[#9CA3AF] mt-10 text-md font-bold">
              Body
            </label>
            <textarea
              value={description}
              onChange={(e) => setDescription(e.target.value)}
              placeholder="Web3 is all about decentralization — it aims to give users more control over their data."
              className="w-[60%] dark:text-white dark:placeholder:text-gray-600 rounded-xl mt-2 h-32 p-2 border border-borderWhiteGray bg-white dark:bg-backgroundBlack dark:border-[#444752] focus:outline-none"
            />

            <div className="flex flex-row mt-10 w-[60%] justify-between">
              <div className="flex flex-col w-2/5    ">
                <label className="text-gray-600 dark:text-[#9CA3AF] text-md font-bold">
                  Location
                </label>
                <input
                  value={location}
                  onChange={(e) => setLocation(e.target.value)}
                  type="text"
                  placeholder="Lagos - Nigeria"
                  className="rounded-md dark:text-white mt-2 dark:placeholder:text-gray-600 h-12 p-2 border border-borderWhiteGray bg-white dark:bg-backgroundBlack dark:border-[#444752] focus:outline-none"
                />
              </div>
              <div className="flex flex-col w-2/5">
                <label className="text-gray-600 dark:text-[#9CA3AF] text-md font-bold">
                  Category
                </label>
                <select
                  value={category}
                  onChange={(e) => setCategory(e.target.value)}
                  className="dark:text-white mt-2 h-12 p-2 dark:border-gray-600 border rounded-xl border-borderWhiteGray bg-white dark:bg-backgroundBlack dark:text-[#9CA3AF] focus:outline-none"
                >
                  <option>Music</option>
                  <option>Sports</option>
                  <option>Gaming</option>
                  <option>News</option>
                  <option>Entertainment</option>
                  <option>Education</option>
                  <option>Technology</option>
                  <option>Travel</option>
                </select>
              </div>
            </div>
            <label className="text-gray-600 dark:text-[#9CA3AF] mt-10 text-md font-bold">
              Cover Image
            </label>

            <div
              onClick={() => {
                coverImageRef.current.click();
              }}
              className="border-2 w-64 dark:border-gray-600 border-dashed border-borderWhiteGray rounded-md mt-2 p-2 h-46 items-center justify-center flex flex-row"
            >
              {coverImage ? (
                <img
                  onClick={() => {
                    coverImageRef.current.click();
                  }}
                  src={URL.createObjectURL(coverImage)}
                  alt="coverImage"
                  className="h-full rounded-md w-full"
                />
              ) : (
                <BiPlus size={70} color="gray" />
              )}
            </div>

            <input
              type="file"
              className="hidden"
              ref={coverImageRef}
              onChange={(e) => {
                setCoverImage(e.target.files[0]);
              }}
            />
          </div>
        </div>
      </div>
      <ToastContainer
        position="top-center"
        autoClose={5000}
        hideProgressBar={false}
        newestOnTop={false}
        closeOnClick
        rtl={false}
        pauseOnFocusLoss
        draggable
        pauseOnHover
      />
    </div>
  );
}
```

接下来，我们将通过导入我们刚刚使用以下代码片段创建的新页面来更新`App.js`文件。

```js
//...

function App() {
  return (
    <Routes>
      //...
      <Route path="/upload" element={<Upload />} />
    </Routes>
  );
}

export default App;
```

点击主页上的`Create a New Feed`按钮会将我们重定向到上传页面，如下图所示。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132202183.png)

输入上传所需的所有详细信息后，我们可以继续提交动态新闻。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132202065.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132202057.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132203712.png)

我们被重定向到主页，但什么也没发生 :(。

我们将在主页上创建一个`getFeeds`函数来检索所有动态新闻。

`HomePage.js`

```js
//...

export default function Main() {

  //...

  /*
   * Get Feeds
   */
  const getFeeds = async () => {
    try {
      setLoading(true);
      const contract = await getContract();
      const AllFeeds = await contract.getAllFeeds();
      /*
       * We only need a title, category, coverImageHash, and author
       * pick those out
       */
      const formattedFeed = AllFeeds.map((feed) => {
        return {
          id: feed.id,
          title: feed.title,
          category: feed.category,
          coverImageHash: feed.coverImageHash,
          author: feed.author,
          date: new Date(feed.date * 1000),
        };
      });
      setFeeds(formattedFeed);
      setLoading(false);
    } catch (err) {
      error(`${err.message}`);
    }
  };

  /*
   * This runs our function when the page loads.
   */
  useEffect(() => {
    getFeeds();

    //...

  }, []);

  return (
    //...
  );
}

const Loader = () => {
  //...
};
```

让我们等待交易确认。这需要几秒钟，我们应该看到它实时出现。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132204750.png)

### 构建动态新闻页面

首先在 components 文件夹中创建`Feed.js`文件并使用以下代码片段对其进行更新。

```react
import React from "react";
import { BiCheck } from "react-icons/bi";
import {
  AiFillTwitterCircle,
  AiFillLinkedin,
  AiFillRedditCircle,
} from "react-icons/ai";

export default function Feed({ feed }) {
  return (
    <div>
      <img
        className=" rounded-lg w-full bg-contain h-80"
        src={`https://ipfs.infura.io/ipfs/${feed.coverImageHash}`}
        alt="cover"
      />
      <div className="flex justify-between flex-row py-4 border-borderWhiteGray dark:border-borderGray border-b-2">
        <div>
          <h3 className="text-2xl dark:text-white">{feed.title}</h3>
          <p className="text-gray-500 mt-4">
            {feed.category} • {feed.date}
          </p>
        </div>
        <div className="flex flex-row items-center">
          <a
            className="bg-transparent dark:text-[#9CA3AF] py-2 px-6 border rounded-lg border-blue-600 mr-6 text-blue-600 hover:bg-blue-600 hover:text-white"
            href={`https://twitter.com/intent/tweet?text=${feed.title}&url=https://ipfs.infura.io/ipfs/${feed.coverImageHash}`}
            target="_blank"
            rel="noopener noreferrer"
          >
            <AiFillTwitterCircle />
          </a>
          <a
            className="bg-transparent dark:text-[#9CA3AF] py-2 px-6 border rounded-lg border-blue-600 mr-6 text-blue-500 hover:bg-blue-600 hover:text-white"
            href={`https://www.linkedin.com/shareArticle?mini=true&url=https://ipfs.infura.io/ipfs/${feed.coverImageHash}&title=${feed.title}&summary=${feed.description}&source=https://ipfs.infura.io/ipfs/${feed.coverImageHash}`}
            target="_blank"
            rel="noopener noreferrer"
          >
            <AiFillLinkedin />
          </a>
          <a
            className="bg-transparent dark:text-[#9CA3AF] py-2 px-6 border rounded-lg border-red-600 mr-6 text-red-600 hover:bg-red-600 hover:text-white"
            href={`https://www.reddit.com/submit?url=https://ipfs.infura.io/ipfs/${feed.coverImageHash}&title=${feed.title}`}
            target="_blank"
            rel="noopener noreferrer"
          >
            <AiFillRedditCircle />
          </a>
        </div>
      </div>

      <div className="flex mt-5 flex-row items-center ">
        <div className="flex items-center text-textSubTitle mt-1">
          Author: {feed?.author?.slice(0, 12)}...
          <BiCheck size="20px" color="green" className="ml-1" />
        </div>
      </div>
      <p className="text-sm text-black mt-4">{feed.description}</p>
    </div>
  );
}
```

接下来，我们将在`src`目录中创建`FeedPage.js`文件并使用下面的代码片段对其进行更新。

https://gist.github.com/Olanetsoft/897a11fa72a1b7e92d5cf98c1b79f1fd#file-feedpage-js

```react

import React, { useEffect, useState } from "react";
import getContract from "./utilities/getContract";
import { Link } from "react-router-dom";
import FeedList from "./components/FeedList";
import Feed from "./components/Feed";

export default function FeedPage() {
  const [relatedFeeds, setRelatedFeeds] = useState([]);

  // state variable to store the current feed
  const [feed, setFeed] = useState([]);

  // Function to get the feed id from the url
  const getUrlValue = () => {
    let vars = {};
    window.location.href.replace(
      /[?&]+([^=&]+)=([^&]*)/gi,
      function (m, key, value) {
        vars[key] = value;
      }
    );
    return vars;
  };

  /*
   * Get Feed
   */
  const getFeed = async () => {
    try {
      const contract = await getContract();
      let feedId = getUrlValue()["id"];
      const singleFeed = await contract.getFeed(feedId);

      // Format feed
      const formattedFeed = {
        id: singleFeed[0],
        title: singleFeed[1],
        description: singleFeed[2],
        location: singleFeed[3],
        category: singleFeed[4],
        coverImageHash: singleFeed[5],
        date: singleFeed[6],
        author: singleFeed[7],
      };

      setFeed(formattedFeed);
    } catch (error) {
      console.log(error);
    }
  };

  /*
   * Get Related Feeds
   */
  const getRelatedFeeds = async () => {
    try {
      const contract = await getContract();
      let feedId = getUrlValue()["id"];
      // Get all feeds and return feeds and filter only the one in the same category as the feed
      const allFeeds = await contract.getAllFeeds();
      const singleFeed = await contract.getFeed(feedId);
      // Format feed
      const formattedSingleFeed = {
        id: singleFeed[0],
        title: singleFeed[1],
        description: singleFeed[2],
        location: singleFeed[3],
        category: singleFeed[4],
        coverImageHash: singleFeed[5],
        date: singleFeed[6],
        author: singleFeed[7],
      };
      const relatedFeeds = allFeeds.filter(
        (feed) => feed.category === formattedSingleFeed.category
      );

      const formattedFeeds = relatedFeeds.map((feed) => {
        return {
          id: feed.id,
          title: feed.title,
          description: feed.description,
          location: feed.location,
          category: feed.category,
          coverImageHash: feed.coverImageHash,
          author: feed.author,
          date: feed.date,
        };
      });

      setRelatedFeeds(formattedFeeds);
    } catch (error) {
      console.log(error);
    }
  };

  useEffect(() => {
    getFeed();
    getRelatedFeeds();
  }, []);

  return (
    <div className="w-full  flex flex-row">
      <div className="flex-1 flex flex-col">
        <div className="flex flex-col m-10 justify-between lg:flex-row">
          <div className="lg:w-4/6 w-6/6">{feed && <Feed feed={feed} />}</div>
          <div className="w-2/6">
            <h4 className="text-xl font-bold dark:text-white ml-5 mb-3 text-black">
              Related Feeds
              <Link to="/">
                <button className="bg-red-600 hover:bg-red-800 text-white font-bold px-2 rounded ml-10">
                  Go Back
                </button>
              </Link>
            </h4>
            {relatedFeeds.map((f) => {
              return (
                <Link
                  onClick={() => {
                    setFeed(f);
                  }}
                  to={`/feed?id=${f.id}`}
                >
                  <FeedList feed={f} horizontal={true} />
                </Link>
              );
            })}
          </div>
        </div>
      </div>
    </div>
  );
}
```

在上面的代码段中，我们检索单个动态新闻并在动态新闻类别中获取相关新闻。

接下来，我们将使用以下代码片段进行更新`App.js`。

```js
//...

import Feed from "./FeedPage";

function App() {
  return (
    <Routes>
      //...
      <Route path="/feed" element={<Feed />} />
    </Routes>
  );
}

export default App;
```

测试我们的应用程序🥳

我创建了几个帖子来测试应用程序，如下所示。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132206733.png)

详情页：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132207409.png)

社交分享

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210132207709.png)

> 上传后，当新闻出现在主页上时，我们可能会遇到一些延迟或故障。我们需要多等几分钟，然后一切都会变得稳定；）

## 结论

本文教我们在 Polygon Network 上使用 Reactjs、TailwindCSS、Etherjs、IPFS 和 Solidity 构建去中心化的 News Feed。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
