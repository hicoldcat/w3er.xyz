---
title: 使用 Next、IPFS、The Graph、Solidity 和 Livepeer 构建全栈 Web3 YouTube 克隆
description: null
author: 李留白
weight: 0
date: 2023-12-13T02:35:36.023Z
lastmod: 2023-12-13T02:51:11.597Z
tags: []
categories:
    - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213103549.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213103549.png)

每天都有越来越多的人转向 Web3。对开发人员的需求不断增加，区块链开发技能是科技行业最紧缺的技能之一。

提高 Web3 技能的最佳方法是使用它们来创建项目。在本文中，您将使用以下技术堆栈在 Polygon 区块链之上构建全栈 YouTube 克隆。

- 前端框架：Next.js
- 智能合约：Solidity
- 以太坊网络客户端库：Ethers.js
- 文件存储：IPFS
- 查询数据：The Graph
- CSS 框架：TailwindCSS
- 以太坊开发环境：Harhat
- 第 2 层区块链：Polygon
- 视频基础设施：Livepeer

[您可以在此处找到应用程序的最终代码](https://github.com/suhailkakar/Decentralized-YouTube)。

## 先决条件

在开始学习本教程之前，请确保您的计算机上安装了[Node.js](https://nodejs.org/en/) v14 或更高版本以及[Metamask](https://metamask.io/download/)浏览器扩展。

## 设置 Next.js 应用程序

第一步是设置 next.js 应用程序并安装所需的依赖项。为此，您需要在终端中运行以下命令。

```
mkdir web3-youtube && cd web3-youtube && npx create-next-app .
```

以下命令创建一个名为 的新目录`web3-youtube`，然后导航到该目录并创建一个 Next.js 应用程序。

项目创建成功后，运行以下命令来安装其他一些依赖项。

```
npm install react-icons plyr-react moment ipfs-http-client ethers @apollo/client graphql dotenv
```

- `react-icons`是我们将在应用程序中使用的图标库。
- `plyr-react`是一个具有丰富插件和功能的视频播放器组件。
- `moment`是一个 JavaScript 日期库，用于解析、验证、操作和格式化日期。
- `ipfs-http-client`用于将视频和缩略图上传到IPFS。
- `ethers`是一个以太坊客户端文学，将用于与智能合约交互

您还可以运行以下命令将 Hardhat 安装为项目中的开发依赖项。

```
npm install --dev hardhat @nomicfoundation/hardhat-toolbox
```

### 初始化本地以太坊环境

接下来，是时候使用 Hardhat 初始化本地智能合约开发了。为此，只需在终端中运行以下命令即可。

```
npx hardhat
```

上面的命令将构建基本的 Solidity 开发环境。您应该在下面看到项目目录中生成的新文件/文件夹。

`test`：该文件夹包含用 Chai 编写的测试脚本，用于测试智能合约。

`hardhat.config.js`：此文件包含 Hardhat 的配置。

`scripts`：此文件夹包含一个示例脚本，用于展示如何部署智能合约。

`contracts`：这是包含我们编写智能合约代码的文件的文件夹。

### 添加 TailwindCSS

Tailwind CSS 是一个实用程序优先的 CSS 框架，用于快速构建用户界面。我们将使用它来设计我们的应用程序。运行以下命令来安装 tailwindcss 及其依赖项。

```
npm install --dev tailwindcss postcss autoprefixer
```

安装依赖项后，我们需要启动 Tailwind CSS。为此，请在终端中运行以下代码。

```
npx tailwind init -p
```

上述命令将生成两个名为`tailwind.config.js`和的文件`postcss.config.js`。接下来，在任何代码编辑器中打开项目，并将其中的代码替换`tailwind.config.js`为以下代码。

```js
module.exports = {
  content: [
    "./pages/**/*.{js,ts,jsx,tsx}",
    "./components/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

最后，将 Tailwind 每个层的 tailwind 指令添加到文件中`./styles/globals.css`。

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```

您还可以通过更新文件内的代码来检查 Tailwind CSS 是否已成功集成`pages/index.js`。

```js
import React from "react";

export default function index() {
  return (
    <div className="flex flex-col justify-center items-center h-screen">
      <h1 className="text-6xl font-bold text-slate-900">Web3 YouTube Clone</h1>
      <h3 className="text-2xl mt-8 text-slate-900">
        Next.js, TailwindCSS, Solidity, IPFS, The Graph and Polygon
      </h3>
    </div>
  );
}
```

保存文件并运行`npm run dev`以启动 next.js 应用程序，您应该看到类似的页面。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104007.png)

## 智能合约

现在项目设置已完成，我们可以开始为我们的应用程序编写智能合约。在本文中，我将使用 Solidity。

> 智能合约是一种去中心化的程序，通过执行业务逻辑来响应事件。

在contracts文件夹中，创建一个名为的新文件`Youtube.sol`，并向其中添加以下代码。

```solidity
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;

contract YouTube {
    // Declaring the videoCount 0 by default
    uint256 public videoCount = 0;
    // Name of your contract
    string public name = "YouTube";
    // Creating a mapping of videoCount to Video
    mapping(uint256 => Video) public videos;

    //  Create a struct called 'Video' with the following properties:
    struct Video {
        uint256 id;
        string hash;
        string title;
        string description;
        string location;
        string category;
        string thumbnailHash;
        string date;
        address author;
    }

    // Create a 'VideoUploaded' event that emits the properties of the video
    event VideoUploaded(
        uint256 id,
        string hash,
        string title,
        string description,
        string location,
        string category,
        string thumbnailHash,
        string date,
        address author
    );

    constructor() {}

    // Function to upload a video
    function uploadVideo(
        string memory _videoHash,
        string memory _title,
        string memory _description,
        string memory _location,
        string memory _category,
        string memory _thumbnailHash,
        string memory _date
    ) public {
        // Validating the video hash, title and author's address
        require(bytes(_videoHash).length > 0);
        require(bytes(_title).length > 0);
        require(msg.sender != address(0));

        // Incrementing the video count
        videoCount++;
        // Adding the video to the contract
        videos[videoCount] = Video(
            videoCount,
            _videoHash,
            _title,
            _description,
            _location,
            _category,
            _thumbnailHash,
            _date,
            msg.sender
        );
        // Triggering the event
        emit VideoUploaded(
            videoCount,
            _videoHash,
            _title,
            _description,
            _location,
            _category,
            _thumbnailHash,
            _date,
            msg.sender
        );
    }
}
```

### 修改Hardhat配置

现在，我们需要对 Hardhat 配置文件进行一些修改才能部署我们的智能合约。在代码编辑器中打开 `hardhat.config.js`并将 module.exports 对象更新为以下代码。

```js
require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config();

module.exports = {
  solidity: "0.8.9",
  networks: {
    mumbai: {
      url: "<https://rpc-mumbai.maticvigil.com>",
      accounts: process.env.PRIVATE_KEY,
    },
  },
  paths: {
    artifacts: "./artifacts",
  },
};
```

为了部署我们的合约，我们需要一个私钥。在浏览器中打开 Metamask，然后单击右上角的三个并选择帐户详细信息。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104039.png)

然后，单击“导出私钥”。系统将提示您输入 Metamask 密码。输入您的密码并单击“确认”。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104047.png)

您应该会在红色框中看到您的私钥。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104055.png)

在项目根目录中创建一个`.env`文件并添加您的私钥。

```
PRIVATE_KEY="YOUR_METAMASK_PRIVATE_KEY"
```

> 永远不要分享您的私钥。拥有您私钥的任何人都可以窃取您帐户中持有的任何资产。

### 使用 Hardhat 编译智能合约

现在我们的智能合约已经完成，让我们继续编译它们。您可以使用下面的命令编译它。

```
npx hardhat compile
```

如果您遇到错误 HH801: `Plugin @nomicfoundation/hardhat-toolbox requires the following dependencies to be installed`。运行以下命令来安装Hardhat依赖项

```shell
npm install --save-dev "@nomicfoundation/hardhat-network-helpers@^1.0.0" "@nomicfoundation/hardhat-chai-matchers@^1.0.0" "@nomiclabs/hardhat-ethers@^2.0.0" "@nomiclabs/hardhat-etherscan@^3.0.0" "@types/chai@^4.2.0" "@types/mocha@^9.1.0" "@typechain/ethers-v5@^10.1.0" "@typechain/hardhat@^6.1.2" "chai@^4.2.0" "hardhat-gas-reporter@^1.0.8" "solidity-coverage@^0.7.21" "ts-node@>=8.0.0" "typechain@^8.1.0" "typescript@>=4.5.0"
```

安装包后，重新运行上述编译命令。

编译成功完成后，您应该会`artifacts`在项目目录中看到一个名为创建的新目录。

工件包含 JSON 格式的智能合约的编译版本。此 JSON 文件包含一个名为 ABI 的数组。ABI 或应用程序二进制接口是我们将客户端（Next 应用程序）与我们编译的智能合约连接起来所需的。

### 在 Polygon 上部署智能合约

现在，我们可以在 Polygon Mumbai 上部署我们的智能合约。我们已经添加了 RPC 和 Metamask 私钥，因此不需要再次添加。但是，您需要一些 $MATIC 才能部署智能合约。

导航至https://faucet.polygon.technology/并粘贴您的钱包地址。点击确认，您的钱包中应该会收到 0.2 MATIC。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104119.png)

默认情况下，Metamask 网络列表中没有 Polygon 区块链，因此我们需要手动添加它。转到 Metamask 设置并选择手动添加网络。使用以下信息将 Polygon Mumbai 添加到 Metamask。

```
Network Name: Mumbai Testnet
New RPC URL: <https://rpc-mumbai.maticvigil.com/>
Chain ID: 80001
Currency Symbol: MATIC
Block Explorer URL: <https://polygonscan.com/>
```

保存它，您应该会在 Metamask 钱包上看到 0.2 MATIC。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104130.png)

接下来，将里面的代码替换`scripts/deploy.js`为以下代码。

```js
// We require the Hardhat Runtime Environment explicitly here. This is optional
// but useful for running the script in a standalone fashion through `node <script>`.
//
// When running the script with `npx hardhat run <script>` you'll find the Hardhat
// Runtime Environment's members available in the global scope.
const hre = require("hardhat");

async function main() {
  // Hardhat always runs the compile task when running scripts with its command
  // line interface.
  //
  // If this script is run directly using `node` you may want to call compile
  // manually to make sure everything is compiled
  // await hre.run('compile');

  // We get the contract to deploy
  const YouTube = await hre.ethers.getContractFactory("YouTube");
  const youtube = await YouTube.deploy();

  await youtube.deployed();

  console.log("YouTube deployed to:", youtube.address);
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

最后，运行以下命令来部署您的智能合约。

```
npx hardhat run scripts/deploy.js --network mumbai
```

此命令将需要一些时间，但一旦完成，您应该会看到类似以下内容的消息：

```
YouTube deployed to: 0x0AE42f411420b2710474e5e4f2F551b36350F9D1
```

这意味着我们的合约已成功部署🎉

## 设置The Graph

您可以在 ethers.js 等软件包的帮助下使用智能合约事件，也可以使用 The Graph 从区块链查询数据。The Graph 是一种链下索引解决方案，可以帮助您以更轻松的方式查询数据。

在本教程中，我们将使用 The Graph 来查询区块链中的视频，因为它使用 GraphQL 查询语言，并且非常简单。

### 创建subgraph

subgraph从区块链中提取数据，对其进行处理并存储，以便可以通过 GraphQL 轻松查询。

要创建subgraph，您首先需要安装 The Graph CLI。Graph CLI是用JavaScript编写的，您需要安装yarn或npm才能使用它。您可以运行以下命令来安装它。

```
npm install -g @graphprotocol/graph-cli
```

安装后，运行`graph init`以初始化项目中的subgraph。系统将提示您一些问题。您可以通过以下代码获取答案：

```
✔ Protocol · ethereum
✔ Product for which to initialize · hosted-service
✔ Subgraph name · suhailkakar/blog-yt-clone
✔ Directory to create the subgraph in · indexer
✔ Contract address · 0x0AE42f411420b2710474e5e4f2F551b36350F9D1
✖ Failed to fetch ABI from Etherscan: ABI not found, try loading it from a local file
✔ ABI file (path) · /Users/suhail/Desktop/web3-youtube/frontend/artifacts/contracts/Youtube.sol/YouTube.json
✔ Contract Name · YouTube
✔ Add another contract? (y/N) · false
```

确保更新合约地址、名称和 ABI。

接下来，让我们声明应用程序的架构。将索引器目录内部的代码替换`schema.graphql`为以下代码。

```
type Video @entity {
  id: ID!
  hash: String! # string
  title: String! # string
  description: String # string
  location: String # string
  category: String # string
  thumbnailHash: String! # string
  date: String # string
  author: Bytes! # address
  createdAt: BigInt! # timestamp
}
```

现在，将其中的代码替换`you-tube.ts`为以下代码。

```js
import { VideoUploaded as VideoUploadedEvent } from "../generated/YouTube/YouTube";
import { Video } from "../generated/schema";

export function handleVideoUploaded(event: VideoUploadedEvent): void {
  let video = new Video(event.params.id.toString());
  video.hash = event.params.hash;
  video.title = event.params.title;
  video.description = event.params.description;
  video.location = event.params.location;
  video.category = event.params.category;
  video.thumbnailHash = event.params.thumbnailHash;
  video.date = event.params.date;
  video.author = event.params.author;
  video.createdAt = event.block.timestamp;
  video.save();
}
```

导航到索引器目录并运行`yarn codegen`以从 GraphQL 操作和架构生成代码。

### 构建subgraph

在部署subgraph之前，我们需要构建它。为此，只需在终端中运行以下命令即可。

```
yarn build
```

接下来，为了部署我们的subgraph，我们需要在 The Graph 上创建一个帐户。

### 部署subgraph

继续创建一个帐户，然后导航到https://thegraph.com/hosted-service/dashboard。单击添加subgraph按钮。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104244.png)

在接下来的屏幕上填写与您的subgraph相关的信息，并在屏幕底部创建subgraph按钮

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104252.png)

创建subgraph后，复制其访问令牌，因为我们稍后会需要它。在您的终端中运行`graph auth`并选择托管服务。在部署密钥中，粘贴您之前复制的密钥。

最后，运行以下命令来部署您的subgraph。

```
yarn deploy
```

如果一切顺利，您应该会看到类似于以下输出的subgraph链接。🎉

```
Build completed: QmV19RJaCXCcKKBe3BTyrL8cGqKNaEo9kpwxMTgrPnDKYA

Deployed to <https://thegraph.com/explorer/subgraph/suhailkakar/test-blog-yt>

Queries (HTTP):     <https://api.thegraph.com/subgraphs/name/suhailkakar/test-blog-yt>
```

## 前端

现在我们已经完成了智能合约，是时候开发应用程序的前端了。让我们从应用程序的身份验证开始。

### 验证

第一步是在我们的应用程序中设置身份验证，允许用户连接他们的钱包。在pages文件夹中创建一个名为`landing`inside的新文件夹，并在其中创建一个名为index.js的新文件。该文件将包含我们应用程序中登陆页面的代码，这也将允许用户连接他们的钱包。

擦除`index.js`页面目录中的所有内容并将`Landing`文件导入到文件中。您的 index.js 文件应如下所示。

```js
import React from "react";
import Landing from "./landing";

export default function index() {
  return (
   <Landing />
  );
}
```

现在，在登陆页面上，我们将创建一个带有连接钱包按钮的简单英雄组件，该组件将允许用户连接他们的钱包并访问我们的应用程序。

将以下代码添加到着陆页。我已经添加了评论，以便您可以正确理解它们。

```js
import React, { useState } from "react";

function Landing() {
  // Creating a function to connect user's wallet
  const connectWallet = async () => {
    try {
      const { ethereum } = window;

      // Checking if user have Metamask installed
      if (!ethereum) {
        // If user doesn't have Metamask installed, throw an error
        alert("Please install MetaMask");
        return;
      }

      // If user has Metamask installed, connect to the user's wallet
      const accounts = await ethereum.request({
        method: "eth_requestAccounts",
      });

      // At last save the user's wallet address in browser's local storage
      localStorage.setItem("walletAddress", accounts[0]);
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <>
      {/* Creating a hero component with black background and centering everything in the screen */}
      <section className="relative bg-black flex flex-col h-screen justify-center items-center">
        <div className="max-w-7xl mx-auto px-4 sm:px-6">
          <div className="pt-32 pb-12 md:pt-40 md:pb-20">
            <div className="text-center pb-12 md:pb-16">
              <h1
                className="text-5xl text-white md:text-6xl font-extrabold leading-tighter tracking-tighter mb-4"
                data-aos="zoom-y-out"
              >
                It is YouTube, but{" "}
                <span className="bg-clip-text text-transparent bg-gradient-to-r from-blue-500 to-teal-400">
                  Decentralized
                </span>
              </h1>
              <div className="max-w-3xl mx-auto">
                <p
                  className="text-xl text-gray-400 mb-8"
                  data-aos="zoom-y-out"
                  data-aos-delay="150"
                >
                  A YouTube Clone built on top of Polygon network, allow users
                  to create, share and watch videos, without worrying about
                  their privacy.
                </p>
                <button
                  className="items-center  bg-white rounded-full font-medium  p-4 shadow-lg"
                  onClick={() => {
                    // Calling the connectWallet function when user clicks on the button
                    connectWallet();
                  }}
                >
                  <span>Connect wallet</span>
                </button>
              </div>
            </div>
          </div>
        </div>
      </section>
    </>
  );
}

export default Landing;
```

如果一切顺利，您应该会看到类似的屏幕。您还应该能够连接您的 MetaMask 钱包。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104339.png)

### 上传视频

现在用户可以连接他们的钱包了，是时候向我们的应用程序添加上传视频功能了。

在 page 目录中创建一个名为 的新文件夹`upload`，并添加一个名为 `index.js`. 文件内部添加以下代码。我已经在代码中添加了注释，所以我希望能帮助您理解它。

```js
import React, { useState, useRef } from "react";
import { BiCloud, BiMusic, BiPlus } from "react-icons/bi";
import { create } from "ipfs-http-client";

export default function Upload() {
  // Creating state for the input field
  const [title, setTitle] = useState("");
  const [description, setDescription] = useState("");
  const [category, setCategory] = useState("");
  const [location, setLocation] = useState("");
  const [thumbnail, setThumbnail] = useState("");
  const [video, setVideo] = useState("");

  //  Creating a ref for thumbnail and video
  const thumbnailRef = useRef();
  const videoRef = useRef();

  return (
    <div className="w-full h-screen bg-[#1a1c1f] flex flex-row">
      <div className="flex-1 flex flex-col">
        <div className="mt-5 mr-10 flex  justify-end">
          <div className="flex items-center">
            <button className="bg-transparent  text-[#9CA3AF] py-2 px-6 border rounded-lg  border-gray-600  mr-6">
              Discard
            </button>
            <button
              onClick={() => {
                handleSubmit();
              }}
              className="bg-blue-500 hover:bg-blue-700 text-white  py-2  rounded-lg flex px-4 justify-between flex-row items-center"
            >
              <BiCloud />
              <p className="ml-2">Upload</p>
            </button>
          </div>
        </div>
        <div className="flex flex-col m-10     mt-5  lg:flex-row">
          <div className="flex lg:w-3/4 flex-col ">
            <label className="text-[#9CA3AF]  text-sm">Title</label>
            <input
              value={title}
              onChange={(e) => setTitle(e.target.value)}
              placeholder="Rick Astley - Never Gonna Give You Up (Official Music Video)"
              className="w-[90%] text-white placeholder:text-gray-600  rounded-md mt-2 h-12 p-2 border  bg-[#1a1c1f] border-[#444752] focus:outline-none"
            />
            <label className="text-[#9CA3AF] mt-10">Description</label>
            <textarea
              value={description}
              onChange={(e) => setDescription(e.target.value)}
              placeholder="Never Gonna Give You Up was a global smash on its release in July 1987, topping the charts in 25 countries including Rick’s native UK and the US Billboard Hot 100.  It also won the Brit Award for Best single in 1988. Stock Aitken and Waterman wrote and produced the track which was the lead-off single and lead track from Rick’s debut LP “Whenever You Need Somebody."
              className="w-[90%] text-white h-32 placeholder:text-gray-600  rounded-md mt-2 p-2 border  bg-[#1a1c1f] border-[#444752] focus:outline-none"
            />

            <div className="flex flex-row mt-10 w-[90%]  justify-between">
              <div className="flex flex-col w-2/5    ">
                <label className="text-[#9CA3AF]  text-sm">Location</label>
                <input
                  value={location}
                  onChange={(e) => setLocation(e.target.value)}
                  type="text"
                  placeholder="Bali - Indonesia"
                  className="w-[90%] text-white placeholder:text-gray-600  rounded-md mt-2 h-12 p-2 border  bg-[#1a1c1f] border-[#444752] focus:outline-none"
                />
              </div>
              <div className="flex flex-col w-2/5    ">
                <label className="text-[#9CA3AF]  text-sm">Category</label>
                <select
                  value={category}
                  onChange={(e) => setCategory(e.target.value)}
                  className="w-[90%] text-white placeholder:text-gray-600  rounded-md mt-2 h-12 p-2 border  bg-[#1a1c1f] border-[#444752] focus:outline-none"
                >
                  <option>Music</option>
                  <option>Sports</option>
                  <option>Gaming</option>
                  <option>News</option>
                  <option>Entertainment</option>
                  <option>Education</option>
                  <option>Science & Technology</option>
                  <option>Travel</option>
                  <option>Other</option>
                </select>
              </div>
            </div>
            <label className="text-[#9CA3AF]  mt-10 text-sm">Thumbnail</label>

            <div
              onClick={() => {
                thumbnailRef.current.click();
              }}
              className="border-2 w-64 border-gray-600  border-dashed rounded-md mt-2 p-2  h-36 items-center justify-center flex"
            >
              {thumbnail ? (
                <img
                  onClick={() => {
                    thumbnailRef.current.click();
                  }}
                  src={URL.createObjectURL(thumbnail)}
                  alt="thumbnail"
                  className="h-full rounded-md"
                />
              ) : (
                <BiPlus size={40} color="gray" />
              )}
            </div>

            <input
              type="file"
              className="hidden"
              ref={thumbnailRef}
              onChange={(e) => {
                setThumbnail(e.target.files[0]);
              }}
            />
          </div>

          <div
            onClick={() => {
              videoRef.current.click();
            }}
            className={
              video
                ? " w-96   rounded-md  h-64 items-center justify-center flex"
                : "border-2 border-gray-600  w-96 border-dashed rounded-md mt-8   h-64 items-center justify-center flex"
            }
          >
            {video ? (
              <video
                controls
                src={URL.createObjectURL(video)}
                className="h-full rounded-md"
              />
            ) : (
              <p className="text-[#9CA3AF]">Upload Video</p>
            )}
          </div>
        </div>
        <input
          type="file"
          className="hidden"
          ref={videoRef}
          accept={"video/*"}
          onChange={(e) => {
            setVideo(e.target.files[0]);
            console.log(e.target.files[0]);
          }}
        />
      </div>
    </div>
  );
}
```

[如果您导航到http://localhost:3000/upload](http://localhost:3000/upload) ，您应该会看到类似的屏幕。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104401.png)

这是一个基本的上传页面，目前，我们只有输入并将它们的值保存在状态中。

在使用句柄提交功能之前，创建一个名为 的新文件夹`utils`，并在其中创建一个名为`getContract`. 该文件将用于与上传页面上的合约进行交互。添加以下代码，并确保将合约地址替换为您的合约地址。

```js
import ContractAbi from "../artifacts/contracts/YouTube.sol/YouTube.json";
import { ethers } from "ethers";

export default function getContract() {
  // Creating a new provider
  const provider = new ethers.providers.Web3Provider(window.ethereum);
  // Getting the signer
  const signer = provider.getSigner();
  // Creating a new contract factory with the signer, address and ABI
  let contract = new ethers.Contract(
    "0xf6F03b0837569eec33e0Af7f3F43B362916e5de1",
    ContractAbi.abi,
    signer
  );
  // Returning the contract
  return contract;
}
```

### 集成IPFS（Web3存储）

现在我们需要一个 IPFS 客户端来上传缩略图。有许多服务提供 IPFS 服务，但在本教程中，我们将使用[web3.storage](https://web3.storage/)。

在 web3.storage 中创建一个新帐户，然后导航到[“令牌”](https://web3.storage/tokens/)页面。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104426.png)

创建一个新令牌并复制生成的令牌，因为我们稍后会需要它。

在 utils 文件夹中，创建一个名为 的新文件`saveToIPFS.js`，并在其中添加以下代码。

```jsx
// importing axios
import axios from "axios";

const saveToIPFS = async (file) => {
  // create a new multipart form data
  const formData = new FormData();
  // add file to the form data
  formData.append("file", file);

  var config = {
    method: "post",
    url: "https://api.web3.storage/upload",
    headers: {
      Authorization: `Bearer WEB3_STORAGE_TOKEN`,
      "Content-Type": "text/plain",
    },
    data: formData,
  };

  // Posting the form data to the IPFS API
  const response = await axios(config);
  // returning the CID
  return response.data.cid;
};

export default saveToIPFS;
```

> 确保使用您从 Web3 存储网站复制的令牌更新“WEB3_STORAGE_TOKEN”。

我们将使用该文件将缩略图上传到 IPFS。

接下来，我们需要集成 Livepeer，以便上传视频并通过 Livepeer CDN 提供视频。

### 集成 Livepeer

Livepeer 是一个去中心化视频处理网络和开发者平台，您可以使用它来构建视频应用程序。它速度非常快、易于集成且价格便宜。在本教程中，我们将使用 Livepeer 上传视频并提供服务。

导航至https://livepeer.studio/register并在 Livepeer Studio 上创建一个新帐户。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104447.png)

创建帐户后，在仪表板中，单击侧边栏上的**开发人员。**

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104455.png)

然后，单击 **“创建 API 密钥”，** 为您的密钥命名，然后复制它，因为我们稍后会需要它。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104531.png)

现在回到代码，继续`livepeer.js`通过在终端中运行以下命令进行安装

```
npm i @livepeer/react
```

Livepeer.js 是一个 JavaScript SDK，带有现成的钩子，使我们能够快速上传视频、提供视频并连接到 Livepeer Studio。安装软件包后，在根目录中创建一个名为的新文件`livepeer.js`，并添加以下代码来初始化 Livepeer 客户端。

```jsx
import { createReactClient } from "@livepeer/react";
import { studioProvider } from "livepeer/providers/studio";

const LivePeerClient = createReactClient({
  provider: studioProvider({ apiKey: "YOUR_API_KEY" }),
});

export default LivePeerClient;
```

确保将 替换`YOUR_API_KEY`为您刚刚从 Livepeer 仪表板复制的密钥。并将`_app.js`页面目录中的代码替换为以下代码。

```jsx
import "../styles/globals.css";
import { LivepeerConfig } from "@livepeer/react";
import LivePeerClient from "../livepeer";

function MyApp({ Component, pageProps }) {
  return (
    <LivepeerConfig client={LivePeerClient}>
      <Component {...pageProps} />
    </LivepeerConfig>
  );
}

export default MyApp;
```

就是这样，您现在可以使用 Livepeer 上传资产/视频。

返回，让我们导入`saveToIPFS`并`Livepeer`在组件顶部添加以下代码

```
import saveToIPFS from "../../utils/saveToIPFS";
import { useCreateAsset } from "@livepeer/react";
```

您还可以在引用后添加以下 Livepeer 挂钩。

```jsx
const {
    mutate: createAsset,
    data: asset,
    uploadProgress,
    status,
    error,
  } = useCreateAsset();
```

上面的钩子将用于将视频上传到 Livepeer。接下来，将以下函数添加到上传页面的 Livepeer hook 后。

```jsx
// When a user clicks on the upload button
  const handleSubmit = async () => {
    // Calling the upload video function
    await uploadVideo();
    // Calling the upload thumbnail function and getting the CID
    const thumbnailCID = await uploadThumbnail();
    // Creating a object to store the metadata
    let data = {
      video: asset?.id,
      title,
      description,
      location,
      category,
      thumbnail: thumbnailCID,
      UploadedDate: Date.now(),
    };
    // Calling the saveVideo function and passing the metadata object
    await saveVideo(data);
  };

  // Function to upload the video to IPFS
  const uploadThumbnail = async () => {
    // Passing the file to the saveToIPFS function and getting the CID
    const cid = await saveToIPFS(thumbnail);
    // Returning the CID
    return cid;
  };

  // Function to upload the video to Livepeer
  const uploadVideo = async () => {
    // Calling the createAsset function from the useCreateAsset hook to upload the video
    createAsset({
      name: title,
      file: video,
    });
  };

  // Function to save the video to the Contract
  const saveVideo = async (data) => {
    // Get the contract from the getContract function
    let contract = await getContract();

    // Upload the video to the contract
    await contract.uploadVideo(
      data.video,
      data.title,
      data.description,
      data.location,
      data.category,
      data.thumbnail,
      false,
      data.UploadedDate
    );
  };
```

我已经对代码的每一行进行了注释，以便您可以了解发生了什么。

保存文件！！我们已经完成了上传功能。您现在应该能够将视频上传到合同中。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104605.png)

### 连接The Graph

为了从 The Graph 获取视频，我们需要设置一个 graphQL 客户端。在根目录中创建一个名为的新文件`client.js`，并在其中添加以下代码。

```js
import { ApolloClient, InMemoryCache } from "@apollo/client";

const client = new ApolloClient({
uri: "YOUR_GRAPHQL_URL_HERE",
cache: new InMemoryCache(),
});

export default client;
```

确保将 URI 替换为您的The Graph URL。并将`_app.js`页面目录中的代码替换为以下代码。

```js
import { ApolloProvider } from "@apollo/client";
import client from "../client";
import "../styles/globals.css";
import { LivepeerConfig } from "@livepeer/react";
import LivePeerClient from "../livepeer";

function MyApp({ Component, pageProps }) {
  return (
    <ApolloProvider client={client}>
            <LivepeerConfig client={LivePeerClient}>
          <Component {...pageProps} />
            </LivepeerConfig>
    </ApolloProvider>
  );
}

export default MyApp;
```

在上面的代码中，我们包装了我们的代码并`ApolloProvider`提供了我们之前创建的客户端作为道具。

### 从区块链获取视频

`index.js`在名为 的新文件夹中创建一个名为 的新文件`home`。现在您可以将以下代码添加到文件中。

```js
import React, { useEffect, useState } from "react";
import { useApolloClient, gql } from "@apollo/client";

export default function Main() {
  // Creating a state to store the uploaded video
  const [videos, setVideos] = useState([]);

  // Get the client from the useApolloClient hook
  const client = useApolloClient();

  // Query the videos from the the graph
  const GET_VIDEOS = gql`
    query videos(
      $first: Int
      $skip: Int
      $orderBy: Video_orderBy
      $orderDirection: OrderDirection
      $where: Video_filter
    ) {
      videos(
        first: $first
        skip: $skip
        orderBy: $orderBy
        orderDirection: $orderDirection
        where: $where
      ) {
        id
        hash
        title
        description
        location
        category
        thumbnailHash
        isAudio
        date
        author
        createdAt
      }
    }
  `;

  // Function to get the videos from the graph
  const getVideos = async () => {
    // Query the videos from the graph
    client
      .query({
        query: GET_VIDEOS,
        variables: {
          first: 200,
          skip: 0,
          orderBy: "createdAt",
          orderDirection: "desc",
        },
        fetchPolicy: "network-only",
      })
      .then(({ data }) => {
        // Set the videos to the state
        setVideos(data.videos);
      })
      .catch((err) => {
        alert("Something went wrong. please try again.!", err.message);
      });
  };

  useEffect(() => {
    // Runs the function getVideos when the component is mounted
    getVideos();
  }, []);
  return (
    <div className="w-full bg-[#1a1c1f] flex flex-row">
      <div className="flex-1 h-screen flex flex-col">
        <div className="flex flex-row flex-wrap">
          {videos.map((video) => (
            <div className="w-80">
              <p>{video.title}</p>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}
```

保存文件，您应该会看到类似的输出。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104635.png)

正如您所看到的，我们现在只是获取视频标题。因此，让我们创建一个可重用的组件来很好地显示视频。

> 确保上传一些视频，以便您可以看到上面的输出

创建一个名为 的文件夹`components`，然后`Video.js`在其中创建一个名为 的新文件。将以下代码添加到文件中。这是一个非常基本的视频组件。

```js
import React from "react";
import { BiCheck } from "react-icons/bi";
import moment from "moment";

export default function Video({ horizontal, video }) {
  return (
    <div
      className={`${
        horizontal
          ? "flex flex-row mx-5 mb-5  item-center justify-center"
          : "flex flex-col m-5"
      } `}
    >
      <img
        className={
          horizontal
            ? "object-cover rounded-lg w-60  "
            : "object-cover rounded-lg w-full h-40"
        }
        src={`https://ipfs.io/ipfs/${video.thumbnailHash}`}
        alt=""
      />
      <div className={horizontal && "ml-3  w-80"}>
        <h4 className="text-md font-bold dark:text-white mt-3">
          {video.title}
        </h4>
        <p className="text-sm flex items-center text-[#878787] mt-1">
          {video.category + " • " + moment(video.createdAt * 1000).fromNow()}
        </p>
        <p className="text-sm flex items-center text-[#878787] mt-1">
          {video?.author?.slice(0, 9)}...{" "}
          <BiCheck size="20px" color="green" className="ml-1" />
        </p>
      </div>
    </div>
  );
}
```

将视频组件导入到主文件中，并将地图函数替换为以下代码。

```js
{videos.map((video) => (
        <div
            className="w-80"
            onClick={() => {
                // Navigation to the video screen (which we will create later)
                window.location.href = `/video?id=${video.id}`;
       }}
            >
                <Video video={video} />
        </div>
))}
```

保存文件，现在您应该看到一个漂亮的主页，类似于下图。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104655.png)

### 视频页面

现在我们可以在主屏幕上获取视频了。让我们处理视频页面，如果用户单击任何视频组件，用户将被重定向。

在名为的组件文件夹中创建一个新文件`Player`，并将以下代码添加到其中。我们正在使用 Livepeer 播放器来创建视频播放器组件。

```jsx
import React from "react";
import { useAsset } from "@livepeer/react";
import Plyr from "plyr-react";
import "plyr-react/plyr.css";

export default function Player({ id }) {
  const { data: asset } = useAsset(id);

  return (
    <Plyr
      source={{
        type: "video",
        title: asset?.name,

        sources: [
          {
            src: asset?.downloadUrl,
            type: "video/mp4",
          },
        ],
      }}
      options={{
        autoplay: true,
      }}
      autoPlay={true}
    />
  );
}
```

在同一目录中创建另一个名为 `VideoContainer`. 将此组件想象为 YouTube 视频页面的左侧，其中包含播放器、视频标题、上传日期和描述。将以下代码添加到文件中。

```jsx
import React from "react";
import Player from "./Player";

export default function VideoComponent({ video }) {
  return (
    <div>
      <Player hash={video.hash} />
      <div className="flex justify-between flex-row py-4">
        <div>
          <h3 className="text-2xl dark:text-white">{video.title}</h3>
          <p className="text-gray-500 mt-1">
            {video.category} •{" "}
            {new Date(video.createdAt * 1000).toLocaleString("en-IN")}
          </p>
        </div>
      </div>
    </div>
  );
}
```

最后在pages文件夹中创建一个名为video的新文件夹并`index.js`为其创建一个新文件。

现在您可以将以下代码添加到该文件中。

```jsx
import React, { useEffect, useState } from "react";
import { useApolloClient, gql } from "@apollo/client";
import Video from "../../components/Video";
import VideoComponent from "../../components/VideoContainer";

export default function VideoPage() {
  const [video, setVideo] = useState(null);
  const [relatedVideos, setRelatedVideos] = useState([]);

  const client = useApolloClient();
  const getUrlVars = () => {
    var vars = {};
    var parts = window.location.href.replace(
      /[?&]+([^=&]+)=([^&]*)/gi,
      function (m, key, value) {
        vars[key] = value;
      }
    );
    return vars;
  };

  const GET_VIDEOS = gql`
    query videos(
      $first: Int
      $skip: Int
      $orderBy: Video_orderBy
      $orderDirection: OrderDirection
      $where: Video_filter
    ) {
      videos(
        first: $first
        skip: $skip
        orderBy: $orderBy
        orderDirection: $orderDirection
        where: $where
      ) {
        id
        hash
        title
        description
        location
        category
        thumbnailHash
        isAudio
        date
        author
        createdAt
      }
    }
  `;

  const getRelatedVideos = () => {
    client
      .query({
        query: GET_VIDEOS,
        variables: {
          first: 20,
          skip: 0,
          orderBy: "createdAt",
          orderDirection: "desc",
          where: {},
        },
        fetchPolicy: "network-only",
      })
      .then(({ data }) => {
        setRelatedVideos(data.videos);
        const video = data?.videos?.find(
          (video) => video.id === getUrlVars().id
        );
        setVideo(video);
      })
      .catch((err) => {
        alert("Something went wrong. please try again.!", err.message);
      });
  };

  useEffect(() => {
    getRelatedVideos();
  }, []);

  return (
    <div className="w-full   bg-[#1a1c1f]  flex flex-row">
      <div className="flex-1 flex flex-col">
        {video && (
          <div className="flex flex-col m-10 justify-between      lg:flex-row">
            <div className="lg:w-4/6 w-6/6">
              <VideoComponent video={video} />
            </div>
            <div className="w-2/6">
              <h4 className="text-md font-bold text-white ml-5 mb-3">
                Related Videos
              </h4>
              {relatedVideos.map((video) => (
                <div
                  onClick={() => {
                    setVideo(video);
                  }}
                  key={video.id}
                >
                  <Video video={video} horizontal={true} />
                </div>
              ))}
            </div>
          </div>
        )}
      </div>
    </div>
  );
}
```

保存文件并单击主屏幕上的任何视频。您应该被重定向到类似于以下页面的视频屏幕。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104724.png)

### 搜索功能

现在我们几乎完成了应用程序的功能。我们还添加搜索功能。

在组件文件夹中，创建一个名为`Header.js`. 现在，您可以添加以下代码。

```jsx
import React from "react";
import { AiOutlinePlusCircle } from "react-icons/ai";

export const Header = ({ search }) => {
  return (
    <header className="w-full flex justify-between h-20 items-center border-b p-4 border-[#202229]">
      <div className=" w-1/3    ">
        <img
          width={80}
          src={"<https://i.ibb.co/JHn1pjz/logo.png>"}
          alt="YouTube Logo"
        />
      </div>
      <div className=" w-1/3 flex justify-center items-center">
        {search ? (
          <input
            type="text"
            onChange={(e) => search(e.target.value)}
            placeholder="Type to search"
            className=" border-0 bg-transparent focus:outline-none text-white"
          />
        ) : null}
      </div>
      <div className=" w-1/3 flex justify-end">
        <AiOutlinePlusCircle
          onClick={() => {
            window.location.href = "/upload";
          }}
          size="30px"
          className="mr-8 fill-whiteIcons dark:fill-white cursor-pointer"
        />
      </div>
    </header>
  );
};
```

这是一个非常简单的组件，分为 3 部分。左侧有应用程序的徽标，中间有一个用户可以键入进行搜索的输入，最后有一个图标可将用户导航到上传屏幕。

回到首页（`pages/home/index.js`）导入Header组件，在第73行后添加if

```js
// <div className="flex-1 h-screen flex flex-col">
        <Header
          search={(e) => {
            console.log(e);
          }}
        />
// <div className="flex flex-row flex-wrap">
```

现在您应该在主页中看到一个标题组件。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104912.png)

在第 8 行之后在主页上声明一个新状态以捕获搜索屏幕中的值。

```jsx
const [search, setSearch] = useState("");
```

您还可以更新 Header 组件来设置上述 useState 中输入的值。

```jsx
<Header
    search={(e) => {
        setSearch(e);
    }}
 />
```

我们还可以更新`getVideos`搜索视频的功能，以防该州有一些价值。

```jsx
const getVideos = async () => {
    // Query the videos from the graph
    client
      .query({
        query: GET_VIDEOS,
        variables: {
          first: 200,
          skip: 0,
          orderBy: "createdAt",
          orderDirection: "desc",
                    // NEW: Added where in order to search for videos
          where: {
            ...(search && {
              title_contains_nocase: search,
            }),
          },
        },
        fetchPolicy: "network-only",
      })
      .then(({ data }) => {
        // Set the videos to the state
        setVideos(data.videos);
      })
      .catch((err) => {
        alert("Something went wrong. please try again.!", err.message);
      });
  };
```

在上面的函数中，我们只是添加了一个`where`对象来搜索视频，以防状态中有值。

最后，更新 useEffect 函数，以便在搜索状态发生变化时也运行该函数。

```jsx
useEffect(() => {
    // Runs the function getVideos when the component is mounted and also if there is a change in the search stae
        getVideos();
  }, [search]);
```

现在，如果您搜索任何内容，您应该会看到视频自动过滤。耶耶🎉

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213104940.png)

## 下一步是什么？

如果您已经走到这一步，则意味着您对构建 Web3 应用程序非常热衷。如果您有兴趣，可以将以下一些其他功能/改进添加到应用程序中。

- 允许用户根据视频类别搜索视频。（如果您需要参考，请检查此存储库）
- 尝试使用 Arweave 代替 IFPS，看看它是如何工作的。
- 尝试向应用程序添加灯光模式并允许用户切换
- 您还可以使应用程序响应

## 结论

👋感谢您的阅读，我们下次再见

> 原文：https://blog.suhailkakar.com/building-a-full-stack-web3-youtube-clone-with-next-ipfs-the-graph-solidity-and-livepeer