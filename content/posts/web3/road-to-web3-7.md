---
title: Web3之路——如何从零开始建立一个NFT市场
description: null
author: 李留白
weight: 0
date: 2022-08-24T13:31:42.955Z
lastmod: 2022-08-24T14:14:50.929Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814204307.png
---

本教程教你如何从头开始建立你自己的NFT市场：包括前端、数据存储和智能合约!

无论你是按用户数量还是按交易量排序，NFT市场都是Web3中最大的一些公司，例如：

- 2022年1月，以太坊最大的NFT市场平台Opensea销售了~250万个NFT，交易量达到50亿美元。
- 2022年5月，Magic Eden，Solana最大的NFT市场有~1130万交易和2亿美元的交易量。

这种规模只有通过出色的智能合约和可扩展的基础设施才能实现。因此，如果您是一名希望提高 web3 开发技能的 web3 开发人员，请按照本教程学习使用 Alchemy、IPFS、Hardhat 和 ethers.js 构建 NFT 市场。

一些需要注意的事项：
- 本教程的重点将是构建智能合约，而不是构建前端。然而，[NFT市场的前端代码](https://github.com/OMGWINNING/NFT-Marketplace-Tutorial)可以在GitHub上找到。
- 本教程中不涉及后台或数据库。只有当你开始归档数据和整合注册或登录功能时，才会需要后台和数据库。

##  第0步：注册一个Alchemy账户并创建一个新的应用程序

如果你还没有，请注册你的免费Alchemy账户。

然后您可以创建一个新的应用程序，并从应用程序仪表板上创建API Key。

或按照下面的书面步骤。

1. 导航至 "应用程序 "标签中的 "创建应用程序 "按钮

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824213541.png)

在弹出的窗口中填写详情，以获得你的新钥匙。对于本教程，你应该选择 "Ethereum "作为链，"Goerli "作为测试网络。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824213612.png)

你也可以通过悬停在 "应用程序 "上并选择一个来拉动现有的API Key。

你可以在这里 "查看密钥"，以及 "编辑应用程序"，将特定的域名列入白名单，看到几个开发者工具，并查看分析结果。

## 第1步：设置您的MetaMask钱包用于开发

如果您已经拥有MetaMask，并拥有Goerli地址和至少0.1个Goerli ETH，请跳到第2步。

如果您没有Goerli地址，请将MetaMask连接到Goerli网络，然后使用Goerli龙头来申请Goerli ETH。你将需要Goerli ETH来部署智能合约和上传NFT到你的NFT市场。

在添加一个新的网络时，请确保添加以下细节。

> 网络名称：Goerli测试网络
>
> RPC基础URL：https://eth-goerli.alchemyapi.io/v2/{插入你的API KEY}。
>
> 链ID: 5
>
> 区块资源管理器 URL：https://goerli.etherscan.io/
>
> 符号（可选）：ETH

## 第2步：设置存储库

为了方便起见，我们已经将基础代码上传到下面的GitHub仓库。这段代码已经写好了前端，但没有智能合约或与前端的集成。

[https://github.com/alchemyplatform/RTW3-Week7-NFT-Marketplace](https://github.com/alchemyplatform/RTW3-Week7-NFT-Marketplace)

要克隆仓库，在你的命令提示符中运行以下命令。

```bash
git clone https://github.com/alchemyplatform/RTW3-Week7-NFT-Marketplace.git
cd RTW3-Week7-NFT-Marketplace
npm install
npm start
```

> ### 注意
>
> 上述GitHub仓库是你应该建立在其上的基础仓库。
>
> 有一个不同的GitHub repo，里面有最终的NFT Marketplace代码。
>
> 如果你在跟随教程的过程中遇到困难，可以参考这个。

## 第3步：设置你的环境变量和Hardhat配置

在你的项目根部创建一个新的.env文件，就在RTW3-Week7-NFT-Marketplace文件夹内，然后添加。

- 你在步骤1中创建的Alchemy API URL
- 你将用于开发的MetaMask钱包的秘钥

当你完成后，你的.env文件应该看起来像这样。

```json
REACT_APP_ALCHEMY_API_URL="<YOUR_API_URL>"
REACT_APP_PRIVATE_KEY="<YOUR_PRIVATE_KEY>"
```

如果还没有安装，在你的根目录下安装dotenv。

```bash
npm install dotenv --save
```

dotenv帮助你管理在.env文件中提到的环境变量，使你的项目很容易访问它们。

> ### 警告
>
> 不要用.env文件中的秘钥来运送生产应用。本教程向你展示了如何通过你的react客户端直接上传至IPFS，仅作为示范。
>
> 当你准备投入生产时，你应该重构你的应用程序，使用后端服务上传IPFS文件。
>
> 阅读此文以了解更多关于React环境变量的情况。

在你的主目录中，确保以下代码被添加到你的hardhat.config.js文件中。

```solidity
require("@nomiclabs/hardhat-waffle");
require("@nomiclabs/hardhat-ethers");
const fs = require('fs');
// const infuraId = fs.readFileSync(".infuraid").toString().trim() || "";
require('dotenv').config();

task("accounts", "Prints the list of accounts", async (taskArgs, hre) => {
  const accounts = await hre.ethers.getSigners();

  for (const account of accounts) {
    console.log(account.address);
  }
});

module.exports = {
  defaultNetwork: "hardhat",
  networks: {
    hardhat: {
      chainId: 1337
    },
    goerli: {
      url: process.env.REACT_APP_ALCHEMY_API_URL,
      accounts: [ process.env.REACT_APP_PRIVATE_KEY ]
    }
  },
  solidity: {
    version: "0.8.4",
    settings: {
      optimizer: {
        enabled: true,
        runs: 200
      }
    }
  }
};
```

> ### 注意
>
> 即使在安装了dotenv之后，你也可能面临使process.env在上述hardhat配置中工作的问题。在这种情况下，只需将goerli URL和秘钥直接粘贴到该配置中。确保不把它推送到GitHub。

## 第4步：使用Piñata将数据上传到IPFS

如果你没有Piñata账户，注册一个免费的Piñata账户并验证你的电子邮件。

### 创建你的Piñata API Key

要创建你的Piñata密钥。

- 导航到https://pinata.cloud/keys
- 选择顶部的 "新钥匙 "按钮
- 将管理小组件设置为启用状态
- 命名你的钥匙

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824213824.png)

然后你会看到一个弹出窗口，显示你的API信息。把它复制到安全的地方。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824220531.png)

确保将您的API Key和秘钥保存在安全的地方

现在Piñata密钥已经设置好了，把它添加到你的项目中，这样你就可以使用它了。

添加你的API Key和秘钥，使.env文件现在看起来像下面这样。

```json
REACT_APP_ALCHEMY_API_URL="<YOUR_API_URL>"
REACT_APP_PRIVATE_KEY="<YOUR_PRIVATE_KEY>"
REACT_APP_PINATA_KEY="<YOUR_PINATA_KEY>"
REACT_APP_PINATA_SECRET="<YOUR_PINATA_SECRET>"
```

## 第5步：了解需求

下面是你在本教程结束时将要制作的NFT市场。

我们为这个市场选择了狗。随意切换到你喜欢的任何其他照片!

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824213838.png)


在我们深入编写代码之前，让我们先看看单独的页面，以了解我们需要的功能集，无论是从前端还是从智能合约的角度。

### 列出你的NFT页面

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824213851.png)

对于任何艺术家或创作者，这是他们可以在市场上列出他们的NFT进行销售的页面。

正如你所看到的，这需要以下NFT属性。

- `NFT名称`
- `描述`
- `价格（以ETH为单位）`
- `NFT图片`

一旦完成，这些内容将被上传到NFT市场上。

为了实现这一目标，我们需要以下条件。

#### 智能合约

- 函数createToken()
- 输入: 个有元数据的IPFS URL
- 它做什么: 为您的NFT分配一个_tokenId,将相应的数据保存到市场合约中,完成后发出一个上市成功事件

[请看这里的实现](https://docs.alchemy.com/docs/how-to-build-an-nft-marketplace-from-scratch#createtoken-and-createlistedtoken)

#### 前端

完成以下工作的脚本：

-  输入NFT的所有相关细节
-  上传NFT图片到IPFS
-  将带有图像链接的NFT元数据上传至IPFS
-  发送IPFS链接和价格到智能合约中的createToken()函数
-  通知用户上传成功

 你可以在 `src/contracts/SellNFT.js `中找到该实现

### 市场主页

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824214448.png)


这是市场的主页，所有NFT都在这里列出。

为了实现这一目标，我们需要

#### 智能合约

- 函数 getAllNFTs()
- **输入**: 无
- 输出：当前正在销售的所有NFTs的列表，以及它们的元数据

请看这里的实现:[https://docs.alchemy.com/docs/how-to-build-an-nft-marketplace-from-scratch#getallnfts](https://docs.alchemy.com/docs/how-to-build-an-nft-marketplace-from-scratch#getallnfts)。

#### 前台

- 使用智能合约中的getAllNFTs()函数获取所有正在销售的NFTs 以网格的形式显示它们
-  让用户点击进入单个NFT以查看更多细节

你可以在 

src/components/Marketplace.js ,

 src/components/NFTPage.js 和

 src/components/NFTTile.js 中找到该实现。

### 用户资料页

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824215115.png)

这是NFT市场上的用户档案，并显示。

- 用户的钱包地址
- 关于用户拥有的NFT的数据
- 所有这些NFT的网格视图与细节

为了实现这一目标，我们需要

#### 智能合约

- 函数getMyNFTs()，返回用户在过去卖出的所有NFTs 实现方法可以在这里找到。

#### 前端

- 使用和getMyNFTs()从智能合约中获取数据 
- 分析数据以获得总的数字和统计数据
- 以上述格式显示数据

### 个人NFT页面



![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824215202.png)

如果你点击市场页面上的任何NFT或从简介页面上点击，这就是访问者将看到的页面。该页面显示。

- NFT 的元数据
- 一个 "购买此NFT "的按钮，让其他用户购买此NFT。

为了实现这一点，我们需要:

#### 智能合约

一些函数：

1. 一个tokenURI函数，返回一个tokenId的tokenURI。然后我们获取该tokenURI的元数据。
2.  一个executeSale()函数，当用户点击 "购买此NFT "按钮时，帮助做必要的检查并转移所有权。该实现可以在这里找到 => (#executesale)

#### 前台

脚本可以完成以下工作：

- *使用tokenURI方法获取tokenURI*
- 使用axios从该IPFS tokenURI获取数据
- *显示数据*
- 同时，在点击 "购买此NFT "按钮时，调用executeSale()函数

现在你已经完全了解了建立一个NFT市场所需的功能。

让我们继续吧!

## 第6步：编写智能合约

让我们开始建立一个NFT市场! 如果你感到困惑，请参考完成的智能合约。

### 添加导入

在你的合约文件夹中，有一个文件NFTMarketplace.sol。

在这个文件的顶部添加以下进口，并添加一个带有构造函数的空类。

```solidity
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;

//Console functions to help debug the smart contract just like in Javascript
import "hardhat/console.sol";
//OpenZeppelin's NFT Standard Contracts. We will extend functions from this in our implementation
import "@openzeppelin/contracts/utils/Counters.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

contract NFTMarketplace is ERC721URIStorage {
    constructor() ERC721("NFTMarketplace", "NFTM") {
        owner = payable(msg.sender);
    }
}
```

代码在评论中作了解释。

### 添加全局变量

在你的智能合约顶部的类声明中添加以下全局变量。

```solidity
    using Counters for Counters.Counter;
    //_tokenIds variable has the most recent minted tokenId
    Counters.Counter private _tokenIds;
    //Keeps track of the number of items sold on the marketplace
    Counters.Counter private _itemsSold;
    //owner is the contract address that created the smart contract
    address payable owner;
    //The fee charged by the marketplace to be allowed to list an NFT
    uint256 listPrice = 0.01 ether;

    //The structure to store info about a listed token
    struct ListedToken {
        uint256 tokenId;
        address payable owner;
        address payable seller;
        uint256 price;
        bool currentlyListed;
    }

    //the event emitted when a token is successfully listed
    event TokenListedSuccess (
        uint256 indexed tokenId,
        address owner,
        address seller,
        uint256 price,
        bool currentlyListed
    );

    //This mapping maps tokenId to token info and is helpful when retrieving details about a tokenId
    mapping(uint256 => ListedToken) private idToListedToken;
```

- _tokenIds。这是与该智能合约铸造的NFT相对应的最新令牌ID。tokenIDs映射到tokenURI，这是包含相应NFT元数据的URL。
- _itemsSold。是市场上销售的物品数量的计数。
- owner：这是智能合约的所有者。是唯一可以发出提款请求的地址。
- listPrice。任何用户在市场上列出他们的NFT需要支付的价格（以ETH为单位）。
- ListedToken。一个solidity结构（类似于Javascript对象），决定了NFT数据的存储格式。
- TokenListedSuccess。当一个令牌成功上市时发出的事件。
- idToListedToken：它是所有现有tokenId到相应NFT令牌的映射。

### createToken和createListedToken

这个函数将tokenURI（带有元数据的URL）变成链上实际的NFT，其细节存储在智能合约中。这对List your NFT页面很有用。

在你的合约类中添加以下函数，就在你的全局变量声明下。

```solidity
    //The first time a token is created, it is listed here
    function createToken(string memory tokenURI, uint256 price) public payable returns (uint) {
        //Increment the tokenId counter, which is keeping track of the number of minted NFTs
        _tokenIds.increment();
        uint256 newTokenId = _tokenIds.current();

        //Mint the NFT with tokenId newTokenId to the address who called createToken
        _safeMint(msg.sender, newTokenId);

        //Map the tokenId to the tokenURI (which is an IPFS URL with the NFT metadata)
        _setTokenURI(newTokenId, tokenURI);

        //Helper function to update Global variables and emit an event
        createListedToken(newTokenId, price);

        return newTokenId;
    }

    function createListedToken(uint256 tokenId, uint256 price) private {
        //Make sure the sender sent enough ETH to pay for listing
        require(msg.value == listPrice, "Hopefully sending the correct price");
        //Just sanity check
        require(price > 0, "Make sure the price isn't negative");

        //Update the mapping of tokenId's to Token details, useful for retrieval functions
        idToListedToken[tokenId] = ListedToken(
            tokenId,
            payable(address(this)),
            payable(msg.sender),
            price,
            true
        );

        _transfer(msg.sender, address(this), tokenId);
        //Emit the event for successful transfer. The frontend parses this message and updates the end user
        emit TokenListedSuccess(
            tokenId,
            address(this),
            msg.sender,
            price,
            true
        );
    }
```

每一行代码的相关性都在注释中提到。请花2分钟时间浏览一下。

#### getAllNFTs

这个函数返回市场中所有 "活跃 "的NFT（当前正在销售）。这对市场的主页很有用。

在你的合同类中添加以下函数，就在createListedToken函数的下面。

```solidity
    //This will return all the NFTs currently listed to be sold on the marketplace
    function getAllNFTs() public view returns (ListedToken[] memory) {
        uint nftCount = _tokenIds.current();
        ListedToken[] memory tokens = new ListedToken[](nftCount);
        uint currentIndex = 0;

        //at the moment currentlyListed is true for all, if it becomes false in the future we will 
        //filter out currentlyListed == false over here
        for(uint i=0;i<nftCount;i++)
        {
            uint currentId = i + 1;
            ListedToken storage currentItem = idToListedToken[currentId];
            tokens[currentIndex] = currentItem;
            currentIndex += 1;
        }
        //the array 'tokens' has the list of all NFTs in the marketplace
        return tokens;
    }
```

每一行代码的相关性都在注释中提到。

#### getMyNFTs

这个函数返回当前登录的用户在市场上拥有的所有 "活跃 "的NFTs（当前正在销售）。这对个人资料页很有用。

在你的合同类中添加以下函数，就在getAllNFTs函数的下面。

```solidity
    //Returns all the NFTs that the current user is owner or seller in
    function getMyNFTs() public view returns (ListedToken[] memory) {
        uint totalItemCount = _tokenIds.current();
        uint itemCount = 0;
        uint currentIndex = 0;
        
        //Important to get a count of all the NFTs that belong to the user before we can make an array for them
        for(uint i=0; i < totalItemCount; i++)
        {
            if(idToListedToken[i+1].owner == msg.sender || idToListedToken[i+1].seller == msg.sender){
                itemCount += 1;
            }
        }

        //Once you have the count of relevant NFTs, create an array then store all the NFTs in it
        ListedToken[] memory items = new ListedToken[](itemCount);
        for(uint i=0; i < totalItemCount; i++) {
            if(idToListedToken[i+1].owner == msg.sender || idToListedToken[i+1].seller == msg.sender) {
                uint currentId = i+1;
                ListedToken storage currentItem = idToListedToken[currentId];
                items[currentIndex] = currentItem;
                currentIndex += 1;
            }
        }
        return items;
    }
```

每一行代码的相关性都在注释中提到。

#### executeSale

当用户在个人资料页面上点击 "购买此NFT "时，executeSale函数被触发。

如果用户支付了相当于NFT价格的足够ETH，NFT就会被转移到新的地址，并将销售收入发送给卖家。

在你的智能合约中添加以下函数。

```solidity
    function executeSale(uint256 tokenId) public payable {
        uint price = idToListedToken[tokenId].price;
        address seller = idToListedToken[tokenId].seller;
        require(msg.value == price, "Please submit the asking price in order to complete the purchase");

        //update the details of the token
        idToListedToken[tokenId].currentlyListed = true;
        idToListedToken[tokenId].seller = payable(msg.sender);
        _itemsSold.increment();

        //Actually transfer the token to the new owner
        _transfer(address(this), msg.sender, tokenId);
        //approve the marketplace to sell NFTs on your behalf
        approve(address(this), tokenId);

        //Transfer the listing fee to the marketplace creator
        payable(owner).transfer(listPrice);
        //Transfer the proceeds from the sale to the seller of the NFT
        payable(seller).transfer(msg.value);
    }
```

#### 其他辅助功能

下面是其他的辅助函数，这些函数在你的智能合约中是很好的测试工具，如果你决定扩展更多的功能，也会有帮助。

请随意在你的类中添加这些功能。

```solidity
    function updateListPrice(uint256 _listPrice) public payable {
        require(owner == msg.sender, "Only owner can update listing price");
        listPrice = _listPrice;
    }

    function getListPrice() public view returns (uint256) {
        return listPrice;
    }

    function getLatestIdToListedToken() public view returns (ListedToken memory) {
        uint256 currentTokenId = _tokenIds.current();
        return idToListedToken[currentTokenId];
    }

    function getListedTokenForId(uint256 tokenId) public view returns (ListedToken memory) {
        return idToListedToken[tokenId];
    }

    function getCurrentToken() public view returns (uint256) {
        return _tokenIds.current();
    }
```

做完上述所有工作后，下面是你的智能合约应该有的样子。

```solidity
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;

import "hardhat/console.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

contract NFTMarketplace is ERC721URIStorage {

    using Counters for Counters.Counter;
    //_tokenIds variable has the most recent minted tokenId
    Counters.Counter private _tokenIds;
    //Keeps track of the number of items sold on the marketplace
    Counters.Counter private _itemsSold;
    //owner is the contract address that created the smart contract
    address payable owner;
    //The fee charged by the marketplace to be allowed to list an NFT
    uint256 listPrice = 0.01 ether;

    //The structure to store info about a listed token
    struct ListedToken {
        uint256 tokenId;
        address payable owner;
        address payable seller;
        uint256 price;
        bool currentlyListed;
    }

    //the event emitted when a token is successfully listed
    event TokenListedSuccess (
        uint256 indexed tokenId,
        address owner,
        address seller,
        uint256 price,
        bool currentlyListed
    );

    //This mapping maps tokenId to token info and is helpful when retrieving details about a tokenId
    mapping(uint256 => ListedToken) private idToListedToken;

    constructor() ERC721("NFTMarketplace", "NFTM") {
        owner = payable(msg.sender);
    }

    function updateListPrice(uint256 _listPrice) public payable {
        require(owner == msg.sender, "Only owner can update listing price");
        listPrice = _listPrice;
    }

    function getListPrice() public view returns (uint256) {
        return listPrice;
    }

    function getLatestIdToListedToken() public view returns (ListedToken memory) {
        uint256 currentTokenId = _tokenIds.current();
        return idToListedToken[currentTokenId];
    }

    function getListedTokenForId(uint256 tokenId) public view returns (ListedToken memory) {
        return idToListedToken[tokenId];
    }

    function getCurrentToken() public view returns (uint256) {
        return _tokenIds.current();
    }

    //The first time a token is created, it is listed here
    function createToken(string memory tokenURI, uint256 price) public payable returns (uint) {
        //Increment the tokenId counter, which is keeping track of the number of minted NFTs
        _tokenIds.increment();
        uint256 newTokenId = _tokenIds.current();

        //Mint the NFT with tokenId newTokenId to the address who called createToken
        _safeMint(msg.sender, newTokenId);

        //Map the tokenId to the tokenURI (which is an IPFS URL with the NFT metadata)
        _setTokenURI(newTokenId, tokenURI);

        //Helper function to update Global variables and emit an event
        createListedToken(newTokenId, price);

        return newTokenId;
    }

    function createListedToken(uint256 tokenId, uint256 price) private {
        //Make sure the sender sent enough ETH to pay for listing
        require(msg.value == listPrice, "Hopefully sending the correct price");
        //Just sanity check
        require(price > 0, "Make sure the price isn't negative");

        //Update the mapping of tokenId's to Token details, useful for retrieval functions
        idToListedToken[tokenId] = ListedToken(
            tokenId,
            payable(address(this)),
            payable(msg.sender),
            price,
            true
        );

        _transfer(msg.sender, address(this), tokenId);
        //Emit the event for successful transfer. The frontend parses this message and updates the end user
        emit TokenListedSuccess(
            tokenId,
            address(this),
            msg.sender,
            price,
            true
        );
    }
    
    //This will return all the NFTs currently listed to be sold on the marketplace
    function getAllNFTs() public view returns (ListedToken[] memory) {
        uint nftCount = _tokenIds.current();
        ListedToken[] memory tokens = new ListedToken[](nftCount);
        uint currentIndex = 0;

        //at the moment currentlyListed is true for all, if it becomes false in the future we will 
        //filter out currentlyListed == false over here
        for(uint i=0;i<nftCount;i++)
        {
            uint currentId = i + 1;
            ListedToken storage currentItem = idToListedToken[currentId];
            tokens[currentIndex] = currentItem;
            currentIndex += 1;
        }
        //the array 'tokens' has the list of all NFTs in the marketplace
        return tokens;
    }
    
    //Returns all the NFTs that the current user is owner or seller in
    function getMyNFTs() public view returns (ListedToken[] memory) {
        uint totalItemCount = _tokenIds.current();
        uint itemCount = 0;
        uint currentIndex = 0;
        
        //Important to get a count of all the NFTs that belong to the user before we can make an array for them
        for(uint i=0; i < totalItemCount; i++)
        {
            if(idToListedToken[i+1].owner == msg.sender || idToListedToken[i+1].seller == msg.sender){
                itemCount += 1;
            }
        }

        //Once you have the count of relevant NFTs, create an array then store all the NFTs in it
        ListedToken[] memory items = new ListedToken[](itemCount);
        for(uint i=0; i < totalItemCount; i++) {
            if(idToListedToken[i+1].owner == msg.sender || idToListedToken[i+1].seller == msg.sender) {
                uint currentId = i+1;
                ListedToken storage currentItem = idToListedToken[currentId];
                items[currentIndex] = currentItem;
                currentIndex += 1;
            }
        }
        return items;
    }

    function executeSale(uint256 tokenId) public payable {
        uint price = idToListedToken[tokenId].price;
        address seller = idToListedToken[tokenId].seller;
        require(msg.value == price, "Please submit the asking price in order to complete the purchase");

        //update the details of the token
        idToListedToken[tokenId].currentlyListed = true;
        idToListedToken[tokenId].seller = payable(msg.sender);
        _itemsSold.increment();

        //Actually transfer the token to the new owner
        _transfer(address(this), msg.sender, tokenId);
        //approve the marketplace to sell NFTs on your behalf
        approve(address(this), tokenId);

        //Transfer the listing fee to the marketplace creator
        payable(owner).transfer(listPrice);
        //Transfer the proceeds from the sale to the seller of the NFT
        payable(seller).transfer(msg.value);
    }

    //We might add a resell token function in the future
    //In that case, tokens won't be listed by default but users can send a request to actually list a token
    //Currently NFTs are listed by default
}
```

## 第7步：在Goerli上部署智能合约

好样的！通过这个巨大的智能合约的编码，你做得很好。你真了不起！ :sparkling_heart:

现在我们需要部署合约。Alchemy推荐使用Goerli测试网，因为Rinkeby将随着Ethereum合并的到来而被废弃。

在scripts/文件夹内有一个名为deploy.js的脚本。在该文件中，粘贴此代码。

```javascript
const { ethers } = require("hardhat");
const hre = require("hardhat");
const fs = require("fs");

async function main() {
  //get the signer that we will use to deploy
  const [deployer] = await ethers.getSigners();
  
  //Get the NFTMarketplace smart contract object and deploy it
  const Marketplace = await hre.ethers.getContractFactory("NFTMarketplace");
  const marketplace = await Marketplace.deploy();

  await marketplace.deployed();
  
  //Pull the address and ABI out while you deploy, since that will be key in interacting with the smart contract later
  const data = {
    address: marketplace.address,
    abi: JSON.parse(marketplace.interface.format('json'))
  }

  //This writes the ABI and address to the marketplace.json
  //This data is then used by frontend files to connect with the smart contract
  fs.writeFileSync('./src/Marketplace.json', JSON.stringify(data))
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

点击保存。

然后打开你的命令提示符，执行以下命令。

```bash
npx hardhat run --network rinkeby scripts/deploy.js
```

> ！确保你已经按照步骤3更新了你的hardhat.config.js，以便能够部署智能合约。

如果你没有看到任何错误或警告，你的智能合约已经成功部署了！你应该能够看到地址。

你应该能够在 src/Marketplace.json 中看到它被部署到的地址和智能合约的 ABI。

## 第8步：添加函数来上传NFT元数据到Piñata

在你的主目录中，在名为pinata.js的空文件中添加这段代码。

```javascript
//require('dotenv').config();
const key = process.env.REACT_APP_PINATA_KEY;
const secret = process.env.REACT_APP_PINATA_SECRET;

const axios = require('axios');
const FormData = require('form-data');

export const uploadJSONToIPFS = async(JSONBody) => {
    const url = `https://api.pinata.cloud/pinning/pinJSONToIPFS`;
    //making axios POST request to Pinata ⬇️
    return axios 
        .post(url, JSONBody, {
            headers: {
                pinata_api_key: key,
                pinata_secret_api_key: secret,
            }
        })
        .then(function (response) {
           return {
               success: true,
               pinataURL: "https://gateway.pinata.cloud/ipfs/" + response.data.IpfsHash
           };
        })
        .catch(function (error) {
            console.log(error)
            return {
                success: false,
                message: error.message,
            }

    });
};

export const uploadFileToIPFS = async(file) => {
    const url = `https://api.pinata.cloud/pinning/pinFileToIPFS`;
    //making axios POST request to Pinata ⬇️
    
    let data = new FormData();
    data.append('file', file);

    const metadata = JSON.stringify({
        name: 'testname',
        keyvalues: {
            exampleKey: 'exampleValue'
        }
    });
    data.append('pinataMetadata', metadata);

    //pinataOptions are optional
    const pinataOptions = JSON.stringify({
        cidVersion: 0,
        customPinPolicy: {
            regions: [
                {
                    id: 'FRA1',
                    desiredReplicationCount: 1
                },
                {
                    id: 'NYC1',
                    desiredReplicationCount: 2
                }
            ]
        }
    });
    data.append('pinataOptions', pinataOptions);

    return axios 
        .post(url, data, {
            maxBodyLength: 'Infinity',
            headers: {
                'Content-Type': `multipart/form-data; boundary=${data._boundary}`,
                pinata_api_key: key,
                pinata_secret_api_key: secret,
            }
        })
        .then(function (response) {
            console.log("image uploaded", response.data.IpfsHash)
            return {
               success: true,
               pinataURL: "https://gateway.pinata.cloud/ipfs/" + response.data.IpfsHash
           };
        })
        .catch(function (error) {
            console.log(error)
            return {
                success: false,
                message: error.message,
            }

    });
};
```

这两个函数是

1. uploadFileToIPFS()

这个函数将NFT图像文件上传到IPFS，然后返回一个IPFS的URL，可以通过查询来获得图像。

2. uploadJSONToIPFS(JSON)

此函数将整个要上传的JSON作为输入，并将其上传到IPFS。该函数返回的值是一个IPFS URI，可以通过查询来获得元数据。当我们以后想检索NFT元数据信息时，这个URI是非常有用的。

## 第9步：将前端与智能合约结合起来

为了让平台无缝工作，将前端与智能合约的功能整合起来。

> ### 关于前台的说明
>
> 为其构建前台是一项巨大的任务。虽然我们很想在这个教程中把它全部教给我们的开发人员，但我们不想让你不知所措。
>
> 因此，Github资源库中有所有的前端代码，每个独立的页面都有独立的组件。
>
> 每个前端组件，例如 `src/components/SellNFT.js`。
>
> 1. 有一个创建提供者、签名者和合同对象的函数
> 2. 从智能合约中获取相关数据
> 3. 通过Axios从IPFS获取相关数据
> 4. 有一个返回项，用于返回页面的JSX/HTML。
>
> 
> 虽然我们在本教程中跳过了对第4项的讨论，但我们仍然涵盖了第1、2和3项。我们将在未来发布一个关于第4项的教程，并将保持本页面的更新。

#### src/components/SellNFT.js

最重要的整合将在src/components/SellNFT.js中进行，在这里我们要做3个步骤。

1. 将图像上传到IPFS
2. 将带有图像的元数据上传到IPFS
3. 将元数据tokenURI和价格发送给智能合约

将下面的代码添加到你的 src/components/SellNFT.js 文件中，就在顶部的状态变量声明之后。

```javascript
    //This function uploads the NFT image to IPFS
    async function OnChangeFile(e) {
        var file = e.target.files[0];
        //check for file extension
        try {
            //upload the file to IPFS
            const response = await uploadFileToIPFS(file);
            if(response.success === true) {
                console.log("Uploaded image to Pinata: ", response.pinataURL)
                setFileURL(response.pinataURL);
            }
        }
        catch(e) {
            console.log("Error during file upload", e);
        }
    }

    //This function uploads the metadata to IPDS
    async function uploadMetadataToIPFS() {
        const {name, description, price} = formParams;
        //Make sure that none of the fields are empty
        if( !name || !description || !price || !fileURL)
            return;

        const nftJSON = {
            name, description, price, image: fileURL
        }

        try {
            //upload the metadata JSON to IPFS
            const response = await uploadJSONToIPFS(nftJSON);
            if(response.success === true){
                console.log("Uploaded JSON to Pinata: ", response)
                return response.pinataURL;
            }
        }
        catch(e) {
            console.log("error uploading JSON metadata:", e)
        }
    }

    async function listNFT(e) {
        e.preventDefault();

        //Upload data to IPFS
        try {
            const metadataURL = await uploadMetadataToIPFS();
            //After adding your Hardhat network to your metamask, this code will get providers and signers
            const provider = new ethers.providers.Web3Provider(window.ethereum);
            const signer = provider.getSigner();
            updateMessage("Please wait.. uploading (upto 5 mins)")

            //Pull the deployed contract instance
            let contract = new ethers.Contract(Marketplace.address, Marketplace.abi, signer)

            //massage the params to be sent to the create NFT request
            const price = ethers.utils.parseUnits(formParams.price, 'ether')
            let listingPrice = await contract.getListPrice()
            listingPrice = listingPrice.toString()

            //actually create the NFT
            let transaction = await contract.createToken(metadataURL, price, { value: listingPrice })
            await transaction.wait()

            alert("Successfully listed your NFT!");
            updateMessage("");
            updateFormParams({ name: '', description: '', price: ''});
            window.location.replace("/")
        }
        catch(e) {
            alert( "Upload error"+e )
        }
    }
```

#### src/components/Marketplace.js

这里我们只需要从智能合约中提取所有的NFT。

把这个添加到你的文件中，就在顶部的状态变量声明之后和返回之前。

```javascript
async function getAllNFTs() {
    const ethers = require("ethers");
    //After adding your Hardhat network to your metamask, this code will get providers and signers
    const provider = new ethers.providers.Web3Provider(window.ethereum);
    const signer = provider.getSigner();
    //Pull the deployed contract instance
    let contract = new ethers.Contract(MarketplaceJSON.address, MarketplaceJSON.abi, signer)
    //create an NFT Token
    let transaction = await contract.getAllNFTs()

    //Fetch all the details of every NFT from the contract and display
    const items = await Promise.all(transaction.map(async i => {
        const tokenURI = await contract.tokenURI(i.tokenId);
        let meta = await axios.get(tokenURI);
        meta = meta.data;

        let price = ethers.utils.formatUnits(i.price.toString(), 'ether');
        let item = {
            price,
            tokenId: i.tokenId.toNumber(),
            seller: i.seller,
            owner: i.owner,
            image: meta.image,
            name: meta.name,
            description: meta.description,
        }
        return item;
    }))

    updateFetched(true);
    updateData(items);
}

if(!dataFetched)
    getAllNFTs();
```

#### src/components/Profile.js

添加下面的代码，它可以拉出登录用户拥有的所有NFTs。

```solidity
    async function getNFTData(tokenId) {
        const ethers = require("ethers");
        let sumPrice = 0;

        //After adding your Hardhat network to your metamask, this code will get providers and signers
        const provider = new ethers.providers.Web3Provider(window.ethereum);
        const signer = provider.getSigner();
        const addr = await signer.getAddress();

        //Pull the deployed contract instance
        let contract = new ethers.Contract(MarketplaceJSON.address, MarketplaceJSON.abi, signer)

        //create an NFT Token
        let transaction = await contract.getMyNFTs()

        /*
        * Below function takes the metadata from tokenURI and the data returned by getMyNFTs() contract function
        * and creates an object of information that is to be displayed
        */
        
        const items = await Promise.all(transaction.map(async i => {
            const tokenURI = await contract.tokenURI(i.tokenId);
            let meta = await axios.get(tokenURI);
            meta = meta.data;

            let price = ethers.utils.formatUnits(i.price.toString(), 'ether');
            let item = {
                price,
                tokenId: i.tokenId.toNumber(),
                seller: i.seller,
                owner: i.owner,
                image: meta.image,
                name: meta.name,
                description: meta.description,
            }
            sumPrice += Number(price);
            return item;
        }))

        updateData(items);
        updateFetched(true);
        updateAddress(addr);
        updateTotalPrice(sumPrice.toPrecision(3));
    }

    const params = useParams();
    const tokenId = params.tokenId;
    if(!dataFetched)
        getNFTData(tokenId);
```

#### src/components/NFTPage.js

这是每个NFT的单独页面，它有两个功能。

1. 显示某个特定NFT的所有数据
2. 让任何用户通过 "购买此NFT "按钮来购买它

所以在你的代码中粘贴以下两个函数。

```solidity
async function getNFTData(tokenId) {
    const ethers = require("ethers");
    //After adding your Hardhat network to your metamask, this code will get providers and signers
    const provider = new ethers.providers.Web3Provider(window.ethereum);
    const signer = provider.getSigner();
    //Pull the deployed contract instance
    let contract = new ethers.Contract(MarketplaceJSON.address, MarketplaceJSON.abi, signer)
    //create an NFT Token
    const tokenURI = await contract.tokenURI(tokenId);
    const listedToken = await contract.getListedTokenForId(tokenId);
    let meta = await axios.get(tokenURI);
    meta = meta.data;
    console.log(listedToken);

    let item = {
        price: meta.price,
        tokenId: tokenId,
        seller: listedToken.seller,
        owner: listedToken.owner,
        image: meta.image,
        name: meta.name,
        description: meta.description,
    }
    console.log(item);
    updateData(item);
    updateDataFetched(true);
}

async function buyNFT(tokenId) {
    try {
        const ethers = require("ethers");
        //After adding your Hardhat network to your metamask, this code will get providers and signers
        const provider = new ethers.providers.Web3Provider(window.ethereum);
        const signer = provider.getSigner();
        //Pull the deployed contract instance
        let contract = new ethers.Contract(MarketplaceJSON.address, MarketplaceJSON.abi, signer);
        const salePrice = ethers.utils.parseUnits(data.price, 'ether')
        let transaction = await contract.executeSale(tokenId, {value:salePrice});
        await transaction.wait();

        alert('You successfully bought the NFT!');
    }
    catch(e) {
        alert("Upload Error"+e)
    }
}
```

## 第10步。测试你的代码

当你在终端点击npm start命令时，市场应该在你的localhost中打开，看起来就像下面这样。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824215559.png)

> 如果你的代码在这一点上不工作，请参考GitHub上完成的NFT Marketplace教程的回购。如果你直接pull这个，市场就应该为你工作了

### 连接你的市场

首先，通过点击导航栏中的 "连接钱包 "按钮连接你的市场。

如果你使用的是与Goerli不同的网络，MetaMask会首先提示你切换网络。

然后，它会要求你连接到你的特定账户。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824215610.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824215618.png)

#### 上传一个NFT

登录成功后，你的市场可能看起来像下面这样。

它可能缺少NFT，因为你刚刚部署了合同。

很新鲜，对吗？

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824215635.png)

现在，前往导航栏中的 "列出我的NFT "页面，填写细节，上传你的第一个NFT。在你点击提交之前，它应该看起来有点像下面的样子。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824215646.png)

> 确保你在这一点上已经从Goerli Faucet得到了一些Goerli ETH。如果你没有足够的Goerli ETH，交易可能因资金不足而失败。

现在，如果你点击提交并等待一段时间（最多5分钟），你应该看到一个警告，说 "成功上传你的NFT！"。

如果你点击 "确定"，它就会将你重定向到你的市场主页。

现在，如果你前往市场和你的个人资料，你应该看到那个NFT!

### 购买NFT

为了测试购买NFT的功能，首先通过进入MetaMask钱包扩展中的 "我的账户"，将您的Metamask钱包切换到其他钱包。

它将显示以下屏幕。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824220058.png)


如果你还没有另一个账户，请创建一个账户并[加载Goerli ETH](https://goerlifaucet.com/)。

接下来，进入单个NFT的页面，点击 "购买此NFT "按钮。

经过一段时间的等待，你应该看到一个提示："成功购买NFT！"。

现在，如果你在你的个人资料部分抬头，该NFT应该会显示出来

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220824220110.png)


好了!

如果所有这些对你有用，你现在已经成功建立了一个工作的NFT市场的V1版。

传说中的!

## 第11步：[可选]扩展功能

你知道什么才是最酷的吗？最酷的事情是，如果你们中的一些人继续扩展我们在本教程中实现的一些功能!

几个潜在的扩展可以是

- 使用Alchemy的[getNFTs](https://docs.alchemy.com/reference/nft-api)和[getNFTsForCollection](https://docs.alchemy.com/reference/getnftsforcollection)结点，为市场和个人资料页面获取NFTs。
- 增加功能，让用户在市场上列出预先存在的NFTs
- 增加版税，使NFT的原始创造者在每次NFT被出售时获得10%的收益。

如果你最终实现了上述功能或任何其他功能，请与我们分享！我们甚至会与我们的社区分享。

## 总结

通过本教程，你已经成功地从头开始建立了自己的NFT市场

欢迎在此基础上添加更多的功能，如使用Alchemy的API和列出旧的NFT。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
