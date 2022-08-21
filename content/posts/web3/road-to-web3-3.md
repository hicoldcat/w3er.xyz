---
title: Web3之路——如何用链上元数据制作NFT - 基于Hardhat和JavaScript
description: null
author: 李留白
weight: 0
date: 2022-08-21T03:01:18.310Z
lastmod: 2022-08-21T03:01:44.021Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814204307.png
---

在创建NFT时，将元数据存储在集中式对象存储器或像IPFS这样的分散式解决方案上是很好的做法，以避免直接在链上存储大量数据（如图像和JSON对象）所产生的巨大Gas费用。

但这也有一个问题。

不在区块链上存储元数据将使你的智能合约无法与之互动，因为区块链无法与 "外部世界 "沟通。

如果我们想直接从我们的智能合约中更新我们的元数据，我们就需要把它存储在链上，但是汽油费怎么办？

幸运的是，像Polygon这样的L2链在这里提供了帮助，大大降低了Gas成本，并引入了许多优势，使开发人员能够扩展其应用程序的功能。

在本教程中，你将学习如何创建一个区块链游戏的基础知识，开发一个完全动态的NFT，其链上元数据根据你与它的互动而变化，并将其部署在Polygon Mumbai上以降低煤气费。

更确切地说，你会学到：

- 如何在链上存储NFTs元数据
- 什么是Polygon，为什么它对降低Gas费用很重要
- 如何在Polygon Mumbai上进行部署
- 如何处理和存储链上的SVG图像和JSON对象
- 如何根据你与NFT的互动来修改你的元数据

在深入研究我们的代码和开发我们的动态NFTs智能合约之前，我们需要简单地了解几件事。

- 什么是Polygon
- 为什么我们要使用它

另外，请确保在这里注册一个Alchemy 账户，我们在以后的挑战中会需要它。

让我们开始吧!

## Polygon PoS - 更低的天然气费用和更快的交易速度

Polygon是一个去中心化的兼容EVM的扩展平台，使开发者能够在不牺牲安全的情况下建立可扩展的用户友好型DApps，交易费用低。

它属于一组被描述为第二层链（L2）的链，这意味着它是建立在以太坊之上的，以解决以太坊的一些问题--同时依靠它来运作。

众所周知，以太坊既不快也不便宜，在其上部署智能合约可能会迅速变得非常昂贵，这就是Polygon或Optimism等L2解决方案发挥作用的地方。

例如，Polygon有两个主要优势

- 更快的交易（65,000 tx/seconds vs ~14）。
- 每笔交易的气体成本比以太坊低约10,000倍

第二点正是我们在Polygon上部署带有链上元数据的NFTs智能合约的原因。如果一方面，当在以太坊上存储我们的元数据时，我们可以期待每笔交易有数百美元，而在Polygon上，其成本不会超过几美分。

如果你想深入了解Polygon和其他L2链如何降低交易成本--加快交易速度，我建议你去看看[这个指南](https://www.one37pm.com/nft/what-are-layer-2-solutions-and-why-are-they-important)。

现在我们已经简单了解了L2解决方案带来的好处，以及为什么在这种情况下使用Polygon是一个改变游戏规则的方法--让我们开始设置我们的钱包与Polygon Mumbai连接，并获得一些免费的Matic，我们以后需要支付天然气费用。

## 将Polygon Mumbai加入你的Metamask钱包

首先，让我们把Polygon Mumbai加入我们的Metamask钱包。

导航到mumbai.polygonscan.com，向下滚动到页面底部。你会看到 "添加Polygon网络 "按钮，点击它并确认你想把它添加到Metamask中。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821105928.png)

就这么简单，你现在就可以把Polygon Mumbai添加到你的Metamask钱包里了! 🎉

现在，我们已经将Polygon Mumbai连接到我们的Metamask扩展，我们需要获得一些测试MATIC来支付天然气费用。

## 获得免费的MATIC来部署你的NFTs智能合约

获取测试MATIC超级简单，只需导航到以下龙头之一。

- https://mumbaifaucet.com/
- https://faucet.polygon.technology/

将钱包地址复制到文本栏，然后点击 "给我发送MATIC"。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821102913.png)

10-20秒后，你会看到MATIC出现在Metamask钱包里。

你将能够在不登录的情况下，每24天获得最多1个MATIC，或者用Alchemy 账户获得5个。

太好了! 现在我们的钱包已经准备好了，是时候创建我们的项目，开发动态NFTs智能合约，并开始与之互动了

## 如何用链上元数据制作NFTs--项目设置

打开终端，创建一个名为 "ChainBattled "的新文件夹，运行以下命令安装Hardhat。

```
yarn add hardhat
```

然后初始化hardhat以创建项目模板。

```
npx hardhat init
```

选择 "Create a basic sample project "并确认所有的选项。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821103056.png)

现在我们需要安装OpenZeppelin软件包，以获得[ERC721智能合约](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721)标准，我们将使用该标准作为模板来构建我们的NFTs智能合约。

安装OpenZeppelin智能合约库

```
yarn add @openzeppelin/contracts
```

太神奇了! 我们现在已经安装了所有我们需要的东西，来制作具有链上元数据的NFTs 🔥

让我们清理和修改我们的项目模板并创建动态NFTs智能合约。

首先，我们需要修改hardhat.config.js文件，以便与Polygon Mumbai和polygon scan连接--以后我们需要它来验证代码。

## 修改hardhat.config.js文件

让我们在VSCode或你最喜欢的文本编辑器中打开项目，删除 "contract "文件夹中的Greeter.sol智能合约，以及 "scripts "文件夹中的 "test-deploy.js "脚本。

下一步是将Hardhat连接到Polygon Mumbai。打开包含在你项目根部的hardhat.config.js文件，在module.exports对象内，复制以下代码。

```
module.exports = {
  solidity: "0.8.10",
  networks: {
    mumbai: {
      url: process.env.TESTNET_RPC,
      accounts: [process.env.PRIVATE_KEY]
    },
  },
};
```

当我们部署我们的智能合约时，我们还想使用mumbai.polygonscan来验证它，为此我们需要向Hardhat提供一个etherscan，或者在这种情况下，Polygon scan API密钥。

我们稍后会抓取Polygonscan的API密钥，目前，只需在hardhat.config.js文件中添加以下代码。

```
etherscan: {
    apiKey: process.env.POLYGONSCAN_API_KEY
 }
```

在这一点上，你的hardhat.config.js文件应该看起来如下。

```
require("dotenv").config();
require("@nomiclabs/hardhat-waffle");
require("@nomiclabs/hardhat-etherscan");

module.exports = {
  solidity: "0.8.10",
  networks: {
    mumbai: {
      url: process.env.TESTNET_RPC,
      accounts: [process.env.PRIVATE_KEY]
    },
  },
  etherscan: {
    apiKey: process.env.POLYGONSCAN_API_KEY
  }
};
```

现在我们的配置文件已经准备好了，让我们开始开发智能合约吧!

## 带有链上元数据的NFT：开发智能合约

在合约文件夹中，创建一个新文件，并将其称为 "ChainBattles.sol"。

像往常一样，我们需要指定SPDX-Licence-Identifier，pragma，并从OpenZeppelin导入几个库，作为我们智能合约的基础。

```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
import "@openzeppelin/contracts/utils/Strings.sol";
import "@openzeppelin/contracts/utils/Base64.sol";
```

在这种情况下，我们正在导入:

- ERC721URIStorage合约，将被用作我们的ERC721智能合约的基础。
- counters.sol库，将负责处理和存储我们的tokenIDs
- string.sol库，实现 "toString() "函数，将数据转换为字符串--字符序列。
- Base64库，正如我们之前看到的，它将帮助我们处理base64数据，比如我们的链上SVG。

接下来，让我们来初始化合约。

## 初始化智能合约

首先，我们需要创建一个新的合约，继承我们从OpenZeppelin导入的ERC721URIStorage扩展。我们可以通过使用 "is "关键字来做到这一点。

```
contract ChainBattles is ERC721URIStorage {}
```

在合约中，初始化字符串和计数器库。

```
contract ChainBattles is ERC721URIStorage {
    using Strings for uint256;
    using Counters for Counters.Counter; 
}
```

在这种情况下，"using Strings for uint256 "意味着我们将 "Strings"库中的所有方法关联到uint256类型。你可以在这里了解更多关于[将库与类型关联的信息](https://forum.openzeppelin.com/t/what-does-this-mean-using-strings-for-uint256-in-erc721-contracts/7964)。

这同样适用于 "using Counters for Counters.Counter "--你可以在[OpenZeppelin论坛](https://forum.openzeppelin.com/t/what-does-this-mean-using-strings-for-uint256-in-erc721-contracts/7964)上阅读更多相关信息。

现在我们已经初始化了我们的库，声明一个新的tokenIds函数，我们将需要它来存储我们的NFT IDs。

```
contract ChainBattles is ERC721URIStorage {
    using Strings for uint256;
    using Counters for Counters.Counter; 
    Counters.Counter private _tokenIds;
}
```

我们需要声明的最后一个全局变量是tokenIdToLevels映射，我们将用它来存储与其tokenId相关的NFT的级别。

```
mapping(uint256 => uint256) public tokenIdToLevels;
```

该映射将把一个int256，即NFTId，连接到另一个int256，即NFT的级别。

接下来，我们需要声明我们的智能合约的构造函数。

```
constructor() ERC721 ("Chain Battles", "CBTLS"){
}
```

在这一点上，你的代码应该如下所示。
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
import "@openzeppelin/contracts/utils/Strings.sol";
import "@openzeppelin/contracts/utils/Base64.sol";

contract ChainBattles is ERC721URIStorage  {
    using Strings for uint256;
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIds;

    mapping(uint256 => uint256) public tokenIdToLevels;

    constructor() ERC721 ("Chain Battles", "CBTLS"){
    }
}
```

现在我们有了NFT智能合约的基础，我们需要实现4个不同的函数。

- generateCharacter：生成并更新我们的NFT的SVG图像。
- getLevels：获取NFT的当前级别
- getTokenURI：获取一个NFT的TokenURI。
- mint: 铸币--当然了
- train: 训练一个NFT并提高其水平

让我们从第一个函数开始，该函数将获取一个SVG，将其转换为Base64数据，并将其保存在链上 - 但首先，我们应该花几个字来理解为什么SVG可以用来保存链上的图像，以及它们与Base64数据的关系。

## 什么是SVG以及为什么它们很重要

SVG文件是可扩展矢量图形文件的简称，是一种标准的图形文件类型，用于在互联网上渲染二维图像。与其他流行的图像文件格式不同，SVG格式将图像存储为矢量，这是一种由点、线、曲线和基于数学公式的形状组成的图形类型。

SVG文件是用XML编写的，这是一种用于存储和传输数字信息的标记语言。SVG文件中的XML代码指定了构成图像的所有形状、颜色和文本。

```xml
<svg xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMinYMin meet" viewBox="0 0 350 350">
   <style>.base { fill: white; font-family: serif; font-size: 14px; }</style>
   <rect width="100%" height="100%" fill="black" />
   <text x="50%" y="40%" class="base" dominant-baseline="middle" text-anchor="middle">Warrior</text>
   <text x="50%" y="50%" class="base" dominant-baseline="middle" text-anchor="middle">Levels: getLevels(tokenId)</text>
 </svg>
```

SVG最酷的地方在于，它们可以:

- 很容易使用代码进行修改和生成
- 很容易转换为Base64数据

现在，你可能想知道为什么我们要把SVG文件转换成Base64数据，答案非常简单。

你可以在浏览器中显示base64图片，而不需要托管商。

让我们以这张图片为例。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821104138.png)

在浏览器的URL栏中复制并粘贴以下代码，就可以显示相同的图片。

```
data:image/svg+xml;base64,IDxzdmcgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiBwcmVzZXJ2ZUFzcGVjdFJhdGlvPSJ4TWluWU1pbiBtZWV0IiB2aWV3Qm94PSIwIDAgMzUwIDM1MCI+CiAgICAgICAgPHN0eWxlPi5iYXNlIHsgZmlsbDogd2hpdGU7IGZvbnQtZmFtaWx5OiBzZXJpZjsgZm9udC1zaXplOiAxNHB4OyB9PC9zdHlsZT4KICAgICAgICA8cmVjdCB3aWR0aD0iMTAwJSIgaGVpZ2h0PSIxMDAlIiBmaWxsPSJibGFjayIgLz4KICAgICAgICA8dGV4dCB4PSI1MCUiIHk9IjQwJSIgY2xhc3M9ImJhc2UiIGRvbWluYW50LWJhc2VsaW5lPSJtaWRkbGUiIHRleHQtYW5jaG9yPSJtaWRkbGUiPldhcnJpb3I8L3RleHQ+CiAgICAgICAgPHRleHQgeD0iNTAlIiB5PSI1MCUiIGNsYXNzPSJiYXNlIiBkb21pbmFudC1iYXNlbGluZT0ibWlkZGxlIiB0ZXh0LWFuY2hvcj0ibWlkZGxlIj5MZXZlbHM6IGdldExldmVscyh0b2tlbklkKTwvdGV4dD4KICAgICAgICA8L3N2Zz4=
```

你可以注意到你粘贴的代码不是一个普通的URL，它实际上是由两部分组成的。

- **数据指令** --告诉浏览器如何处理数据（ data:image/svg+xml;base64,)
- Base64 数据 - 包含实际的数据

这很有用，因为即使Solidity不能处理图像，它也能处理字符串，而SVG并不是什么别的东西，只是标签和字符串的序列，我们可以很容易地在运行时检索，另外，将所有东西转换为base64，将允许我们在链上存储图像，而不需要对象存储。

现在我们解释了为什么SVG很重要，让我们学习如何生成我们自己的链上SVG并将其转换为Base64数据。

## 创建generateCharacter函数来创建SVG图像

我们需要一个函数来生成链上的NFT图像，使用一些SVG代码，考虑到NFT的级别。

在Solidity中这样做有点棘手，所以让我们先复制下面的代码，然后将通过它的不同部分。

```solidity
function generateCharacter(uint256 tokenId) public returns(string memory){

    bytes memory svg = abi.encodePacked(
        '<svg xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMinYMin meet" viewBox="0 0 350 350">',
        '<style>.base { fill: white; font-family: serif; font-size: 14px; }</style>',
        '<rect width="100%" height="100%" fill="black" />',
        '<text x="50%" y="40%" class="base" dominant-baseline="middle" text-anchor="middle">',"Warrior",'</text>',
        '<text x="50%" y="50%" class="base" dominant-baseline="middle" text-anchor="middle">', "Levels: ",getLevels(tokenId),'</text>',
        '</svg>'
    );
    return string(
        abi.encodePacked(
            "data:image/svg+xml;base64,",
            Base64.encode(svg)
        )    
    );
}
```

你应该注意到的第一件事是 "字节 "类型，这是一个动态大小的数组，最多32字节，你可以存储字符串和整数。

如果你想深入了解Bytes，另一方面，我真的建议你去读[Jean Cvllr](https://jeancvllr.medium.com/?source=post_page-----9d88fdb22676--------------------------------)的这篇指南。

在这种情况下，我们用它来存储代表我们的NFT图像的SVG代码，由于abi.encodePacked()函数将一个或多个变量转化为字节数组，并将它们编码为abi。

你可以在Solidity文档中阅读更多关于[abi全局Solidity对象和编码函数](https://docs.soliditylang.org/en/latest/abi-spec.html#non-standard-packed-mode)的信息。

正如你所注意到的，SVG代码采用了getLevels()函数的返回值，并使用它来填充 "Levels: "属性--我们将在后面实现这个函数，但是请注意，你可以使用函数和变量来动态地改变你的SVG。

正如我们之前所看到的，为了在浏览器上实现图像的可视化，我们需要有它的base64版本，而不是字节版本--另外，我们还需要在 "data:image/svg+xml;base64, "字符串前加一个，以便向浏览器指定Base64字符串是一个SVG图像，以及如何打开它。

为了做到这一点，在上面的代码中，我们使用ali.encodePacked()函数，用Base64.encode()返回我们的SVG的编码版本，并预加了浏览器的规范字符串，变成了Base64。

现在我们已经实现了生成图像的函数，我们需要实现一个函数来获取我们的NFTs的级别。

## 创建getLevels函数以检索NFT级别

为了获得我们的NFT的级别，我们需要使用我们在智能合约中声明的tokenIdToLevels映射，将我们想要获得级别的tokenId传入函数。

```
function getLevels(uint256 tokenId) public view returns (string memory) {
    uint256 levels = tokenIdToLevels[tokenId];
    return levels.toString();
}
```

正如你所看到的，这是非常简单的，唯一需要注意的是toString()函数，它来自OpenZeppelin字符串库，并将我们的级别，即uint256，转换成一个字符串--然后将被我们之前看到的generateCharacter函数所使用。

接下来，我们需要创建getTokenURI函数来生成和检索我们的NFT TokenURI。

## 创建getTokenURI函数来生成tokenURI

getTokenURI函数将需要一个参数，即tokenId，并将使用它来生成图像，并建立NFT的名称。

像往常一样，让我们先看看代码，并通过它的不同部分。

```solidity
function getTokenURI(uint256 tokenId) public returns (string memory){
    bytes memory dataURI = abi.encodePacked(
        '{',
            '"name": "Chain Battles #', tokenId.toString(), '",',
            '"description": "Battles on chain",',
            '"image": "', generateCharacter(tokenId), '"',
        '}'
    );
    return string(
        abi.encodePacked(
            "data:application/json;base64,",
            Base64.encode(dataURI)
        )
    );
}
```

首先要注意的是，我们再次使用abi.encodePacked函数，不过这次是为了创建一个JSON对象。

如果你不知道什么是JSON对象，让我们简单地说，它是一系列的键和值对，一个属性的名称，以及该属性的值。

本例中 "name"的值是。"Chain Battles "加上 "#"和tokenId toString()，另一方面，"image "属性的值是由generateCharacter()函数返回的值。

最后，我们返回一个包含字节数组的字符串，代表带有JSON数据指示的DataURI的Base64编码版本--类似于我们对SVG图片所做的。

现在我们已经创建了我们的getTokenURI，我们需要创建一个函数来实际铸造我们的NFT并初始化我们的变量--让我们看看如何做

## 创建Mint函数来创建带有链上元数据的NFT

本例中的mint函数将有3个目标。

- 创建一个新的NFT。
- 初始化级别值。
- 设置令牌URI。

复制以下代码:

```
function mint() public {
    _tokenIds.increment();
    uint256 newItemId = _tokenIds.current();
    _safeMint(msg.sender, newItemId);
    tokenIdToLevels[newItemId] = 0;
    _setTokenURI(newItemId, getTokenURI(newItemId));
}
```

像往常一样，我们首先增加我们的_tokenIds变量的值，并将其当前值存储在一个新的uint256变量上，在这种情况下，"newItemId"。

接下来，我们调用OpenZeppelin ERC721库中的_safeMint()函数，传递msg.sender变量和当前id。

然后我们在tokenIdToLevels映射中创建一个新的项目，并将其赋值为0，这意味着我们的NFTs/character将从level开始。

最后，我们通过newItemId和getTokenURI()的返回值来设置token URI。

这将铸成一个NFT，其中的元数据，包括图像，是完全存储在链上的 🔥

这也意味着我们将能够直接从智能合约中更新元数据，让我们看看如何创建一个函数来训练我们的NFT，并让它们提升等级

## 创建训练函数以提高你的NFT等级



正如我们所说，现在我们的NFTs的元数据完全在链上，我们将能够直接从智能合约中与它互动。

假设我们想在强化训练后提高我们的NFT的水平，要做到这一点，我们需要创建一个训练函数，它将。

- 确保受训的NFT存在，并且你是它的所有者。
- 将你的NFT的级别提高1。
- 更新令牌URI以反映训练情况。

```solidity
function train(uint256 tokenId) public {
    require(_exists(tokenId), "Please use an existing token");
    require(ownerOf(tokenId) == msg.sender, "You must own this token to train it");
    uint256 currentLevel = tokenIdToLevels[tokenId];
    tokenIdToLevels[tokenId] = currentLevels + 1;
    _setTokenURI(tokenId, getTokenURI(tokenId));
}
```

你可以注意到，使用require()函数，我们正在检查两件事。

- 如果令牌存在，使用ERC721标准中的_exists（）函数。
- NFT的所有者是否是msg.sender（调用该函数的钱包）。

一旦这两项检查都通过了，我们就从映射中获得NFT的当前级别，并将其递增1。

最后，我们要调用_setTokenURI函数，传递tokenId，以及getTokeURI()的返回值。

调用train函数现在将提高NFT的级别，这将自动反映在图像中。

祝贺你！你刚刚完成了聪明人的编写。你刚刚完成了为NFTs编写带有链上元数据的智能合约。🏆

下一步是在Polygon Mumbai上部署智能合约，并通过Polygonscan与之互动。要做到这一点，我们需要抓住我们的Alchemy和Polygonscan密钥。

## 用链上元数据智能合约部署NFTs

首先，让我们在我们项目的根文件夹中创建一个新的.env文件，并添加以下变量。

```
TESTNET_RPC=""
PRIVATE_KEY=""
POLYGONSCAN_API_KEY=""
```

然后，导航到alchemy.com，创建一个新的Polygon Mumbai应用程序。

点击新创建的应用程序，复制API的HTTP URL，并将API作为 "TESTNET_RPC "值粘贴在我们上面创建的.env文件中。

打开你的Metamask钱包，点击三点菜单>账户详情>并复制粘贴你的私钥作为 "PRIVATE_KEY "值在.env中。

最后，上polygonscan.com，并创建一个新的账户。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821104950.png)

一旦你登录，进入你的个人资料菜单，点击API密钥。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821105000.png)

然后点击 "添加"，给你的应用程序起个名字。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821105009.png)

现在将Api-Key Token作为 "POLYGONSCANAPI_KEY "值复制粘贴到.env中。

*在部署我们的智能合约之前的最后一步，我们需要创建部署脚本。*

## 创建部署脚本

部署脚本，正如你在上一课学到的，顾名思义，是用来告诉Hardhat如何将智能合约部署到指定的区块链上。

在这种情况下，我们的部署脚本是非常简单的。

```js
const main = async () => {
  try {
    const nftContractFactory = await hre.ethers.getContractFactory(
      "ChainBattles"
    );
    const nftContract = await nftContractFactory.deploy();
    await nftContract.deployed();

    console.log("Contract deployed to:", nftContract.address);
    process.exit(0);
  } catch (error) {
    console.log(error);
    process.exit(1);
  }
};
  
main();
```

我们正在调用get.contractFactory，传递我们的智能合约的名称，从Hardhat以太坊。然后我们调用deploy()函数，等待它被部署，记录地址。

一切都被包裹在一个try{} catch{}块中，以捕捉任何可能发生的错误，并将其打印出来用于调试。

现在我们的部署脚本已经准备好了，是时候在Polygon Mumbai上编译和部署我们的动态NFT智能合约了。

## 编译和部署智能合约

要编译智能合约，只需在项目内部的终端运行以下命令。

```
npx hardhat compile
```

如果一切按预期进行，你会看到你的智能合约在工件文件夹中被编译。

现在，让我们在运行的Polygon Mumbai链上部署该智能合约。

```
npx hardhat run scripts/deploy.js --network mumbai
```

等待10-15秒，你应该看到智能合约的地址在你的终端上打印出来了

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821105148.png)

令人惊讶的是，你刚刚在Polygon Mumbai上部署了你的第一个智能合约! 接下来，我们需要验证我们的智能合约代码，通过Polygon扫描与之互动。

## 在Polygon扫描上检查你的智能合约

复制刚刚部署的智能合约的地址，进入mumbai.polygonscan.com，并在搜索栏中粘贴智能合约的地址。

一旦进入你的智能合约页面，点击 "合约 "标签。

你会注意到，合同代码是不可读的。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821105212.png)

这是因为我们还没有验证我们的代码。

为了验证我们的智能合约，我们需要回到我们的项目中，在终端中，运行以下代码。

```
npx hardhat verify --network mumbai YOUR_SMARTCONTRACT_ADDRESS
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821105235.png)

有时你可能会得到 "发送合约验证请求失败 "的错误--只要再试一次，它就会通过。

这将使用我们在hardhat.config.js文件中添加的Polygon 扫描API密钥来验证智能合约的代码。我们现在可以通过Polygon扫描与之互动，让我们试试吧。

## 通过PolygonScan与你的智能合约互动

现在智能合约已经被验证了，mumbai.polygonscan.com会在它附近显示一个绿色的小勾。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821105315.png)

要与它互动，并为第一个NFT造币，请点击 "**contract** "标签下的 "**Write Contract** "按钮，并点击 "连接到Web3"

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821105324.png)

然后寻找 "铸币 "功能并点击写入。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821105347.png)

这将打开一个Metamask弹出窗口，要求你支付天然气费用，点击签署按钮。

祝贺你! 你刚刚铸造了你的第一个动态NFT--让我们移到OpenSea Testnet上看看它的实况。

## 在OpenSea上查看你的动态NFT

复制智能合约地址，进入testnet.opensea.com，并将其粘贴到搜索栏。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821105410.png)

如果一切按预期进行，你现在应该看到你的NFT显示在OpenSea上，有它的动态图像、标题和描述。

到现在为止没有什么新东西，我们已经在第一课中建立了一个NFT集合，这里最酷的是我们现在可以实时更新图片。

让我们回到Polygon扫描。

## 更新动态NFT图像 训练NFT

回到mumbai.polygonscan.com，点击**contract** 标签> **Write Contract** ，寻找 "train"功能。

插入你的NFT的ID - "1 "在这种情况下，因为我们只铸造了一个，然后点击写入。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821105502.png)

然后回到testnets.opensea.com并刷新页面。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821105513.png)

正如你所看到的，你的NFT的图像刚刚改变，反映了新的水平!

恭喜你，你的NFT刚刚升了级! 干得好! 🎉

现在是时候通过本周的挑战将其提升到新的水平了!

## 本周的挑战

目前，我们只存储了NFT的等级，为什么不存储更多呢？

用一个结构代替当前的tokenIdToLevels[]映射，这个结构可以存储。

- 级别
- 速度
- 强度
- 寿命

你可以在[本指南中](https://www.tutorialspoint.com/solidity/solidity_structs.htm)阅读更多关于结构的信息。

一旦你创建了结构，就在mint()函数中初始化统计量，为此你可能想研究一下[Solidity上的伪数生成](https://blog.finxter.com/how-to-generate-random-numbers-in-solidity/)。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
