---
title: Web3之路- 如何使用 Alchemy 开发 NFT 智能合约（ERC721）
description: null
author: 李留白
weight: 0
date: 2022-08-14T14:33:47.309Z
lastmod: 2022-08-14T14:39:06.462Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814204307.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814223902.png)

**使用 Solidity 开发智能合约并将其部署在区块链上一**开始可能听起来令人生畏：solidity、安全性、gas 优化、开发人员环境和 gas 费用，这些只是你在区块链上托管你的代码所需要经历的一些事情。

幸运的是，在过去的几个月里，已经为开发人员发布了许多工具，使他们的智能合约开发工作变得更加容易。

像**OpenZeppelin Wizard**这样的工具，为开发者提供了点击和编写功能，以在短时间内创建可组合和安全的智能合约，与**Alchemy**等Web3开发者工具一起使用，使在区块链上编写和部署代码的经验变得前所未有的简单、快速和可靠。

在本教程中，你将学习如何使用Alchemy、OpenZeppelin、Remix和Ethereum Rinkeby开发和部署一个ERC721（NFT）智能合约。

更确切地说，你将学会：

- 如何使用OpenZeppelin和Remix编写和修改智能合约

- 使用rinkebyfaucet.com获得免费的Rinkeby ETH
- 在以太坊Rinkeby testnet区块链上部署，以节省汽油费

- 使用Filebase在IPFS上托管NFT令牌元数据。
- 打造一个NFT并在OpenSea上将其可视化

让我们从创建智能合约开始。

## 使用 OpenZeppelin 合约向导开发 ERC721 智能合约。

如前所述，在本教程中，您将**使用 OpenZeppelin Wizard 创建智能合约**，主要有两个原因：

- 它是安全的。
- 它提供了标准投诉的智能合约。

当谈到编写智能合约时，安全是关键。有大量的智能合约被利用的例子，由于安全性不好，恶意行为者偷走了数亿美元。

*你不希望一旦你部署在区块链上之后立即有人偷走你所有珍贵的加密货币或NFT，对吗？*

OpenZeppelin起到了这个作用，它是智能合约标准（ERC20、ERC721等）的最大维护者之一，允许开发者使用彻底审核的代码来开发可靠的合约。

开发我们的ERC721 NFT智能合约，你需要做的第一件事是[进入Open Zeppelin智能合约向导页面](https://docs.openzeppelin.com/contracts/4.x/wizard)。

一旦进入该页面，你将看到以下编辑器:

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814214312.png)

点击左上角的ERC721按钮，选择要使用的ERC标准类型和你想写的合同类型:

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814214335.png)

现在你已经选择了合同标准，在左手边的菜单上，你应该看到有一些选项。

让我们开始选择我们的令牌的名称和符号。点击文本框中的 "MyToken "并给它一个名字，对符号做同样的处理，并将基础URI字段留空（令牌的名字将被OpenSea和Rarible作为集合的名称）。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814214437.png)

## 选择 NFT (ERC721) 代币功能

现在你需要选择你想集成到我们的智能合约中的功能，就在 `settings`部分之后，你会发现 `features`部分，在那里你将能够选择不同的模块来包括在你的智能合约中。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814214558.png)

在这种情况下，你要选择以下集成:

- **Mintable**将创建一个只能由特权账户调用的mint函数
- **Autoincrement** **IDs** 将自动为你的NFT分配递增的ID
- **Enumerable**将使你能够访问链上令牌枚举和 "totalSupply "等功能，这些功能在默认的ERC721集成URI存储中不存在，以将元数据和图像与你的每个NFT相关联。

- **URI Storage** ，能够将URI与我们的NFT联系起来

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814215516.png)

为了这个教程，也因为你不想在我们的NFT周围创建任何类型的Tokenomic（通证经济），不要勾选以下模块：

- **Burnable** --销毁代币
- **Pausable** - 暂停代币转让、销售等
- **Votes** - 允许访问类似治理的功能，如代表和投票

如果您想了解更多关于这些模块的信息，[请查看OpenZeppelin官方关于ERC721标准的文档](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721)。

现在你已经选择了你想要的功能，OpenZeppelin向导将填充智能合约的代码，它应该看起来如下:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/access/Ownable.sol";

contract Alchemy is ERC721, ERC721Enumerable, ERC721URIStorage, Ownable {
    constructor() ERC721("Alchemy", "ALC") {}

    function safeMint(address to, uint256 tokenId, string memory uri)
        public
        onlyOwner
    {
        _safeMint(to, tokenId);
        _setTokenURI(tokenId, uri);
    }

    // The following functions are overrides required by Solidity.

    function _beforeTokenTransfer(address from, address to, uint256 tokenId)
        internal
        override(ERC721, ERC721Enumerable)
    {
        super._beforeTokenTransfer(from, to, tokenId);
    }

    function _burn(uint256 tokenId) internal override(ERC721, ERC721URIStorage) {
        super._burn(tokenId);
    }

    function tokenURI(uint256 tokenId)
        public
        view
        override(ERC721, ERC721URIStorage)
        returns (string memory)
    {
        return super.tokenURI(tokenId);
    }

    function supportsInterface(bytes4 interfaceId)
        public
        view
        override(ERC721, ERC721Enumerable)
        returns (bool)
    {
        return super.supportsInterface(interfaceId);
    }
}
```

现在是时候复制我们的代码，把它带到Remix IDE上，修改并部署到区块链上。

## 使用 REMIX IDE 修改和部署您的 ERC721 合约

现在你有了你的ERC721智能合约，让我们来修改，并将其部署在Rinkeby Testnet上。为此，您将使用Remix IDE，这是一个免费的、基于网络的集成开发环境，专门为使用Solidity开发智能合约而设计。

首先，你可能已经注意到，在OpenZeppelin Wizard编辑器的顶部，有一个 "Open in Remix "的按钮。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814220243.png)

点击它将在浏览器的一个新标签中打开REMIX IDE。

## 使用 Remix 修改 NFT 智能合约

从合同的顶部开始，有一个 "SPDX-License-Identifier"，它指定了你的代码将以何种类型的许可证发布--在web3应用程序中，保持代码的开放源代码是一个很好的做法，因为它可以确保可信度。

```shell
// SPDX-License-Identifier: MIT
```

然后是pragma - 你想用来编译智能合约代码的编译器版本。这个小小的"^"符号，告诉编译器，0.8.0到0.8.9之间的每个版本都适合编译我们的代码。

```shell
pragma solidity ^0.8.4;
```

然后我们要导入一堆库，初始化智能合约。

```solidity
import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
import "@openzeppelin/contracts/token/ERC721/extensions/ERC721URIStorage.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/Counters.sol";
```

然后我们要初始化合同，继承我们从OpenZeppelin资源库导入的所有标准。

```shell
contract Alchemy is ERC721, ERC721Enumerable, ERC721URIStorage, Ownable {...}
```

正如你所注意到的，`safeMint`函数有 "only owner "修改器--这将允许只有智能合约的所有者（部署智能合约的钱包地址）才能铸造NFTs。你有可能希望任何人都能铸造NFT，要做到这一点，你需要从Mint函数中删除`onlyOwner`修改器。

```solidity
function safeMint(address to, string memory uri) public {
        uint256 tokenId = _tokenIdCounter.current();
        _tokenIdCounter.increment();
        _safeMint(to, tokenId);
        _setTokenURI(tokenId, uri);
    }
```

你也可以把它从合同声明 "Ownable "中删除，而库中导入的

```solidity
import "@openzeppelin/contracts/access/Ownable.sol";
```

现在每个人都可以铸造我们的NFT，你需要避免人们铸造超过我们收藏的NFT的最大数量的NFT。为了做到这一点，让我们指定可铸币NFTs的最大数量。

我们希望用户能够总共铸币10,000个NFT。为此，让我们创建一个新的uint256变量，称之为MAX_SUPPLY，并赋予它10,000。

```solidity
Counters.Counter private _tokenIdCounter;
    uint256 MAX_SUPPLY = 100000;

    constructor() ERC721("Alchemy", "ALCH") {}
```

接下来，让我们进入`safeMint`函数，在第18行添加一个require语句。

```solidity
require(_tokenIdCounter.current() <= MAX_SUPPLY, "I'm sorry we reached the cap");
```

让我们花几句话来更好地理解什么是Solidity中的 "require "语句。

你可以在官方文档中阅读关于[ Solidity "require" 语句的更多信息](https://docs.soliditylang.org/en/v0.4.24/control-structures.html#error-handling-assert-require-revert-and-exceptions)。

现在你已经限制了我们的NFTs的最大供应量，是时候编译智能合约并在Rinkeby Testnet上部署它了。要做到这一点，你需要在[Alchemy.com](https://alchemy.com/?r=jEzMjQ2OTM4OTY5N)上创建一个免费账户，将其添加为Metamask的节点提供者，并获得一些免费的Rinkeby ETH。

## 创建一个免费的Alchemy 帐户

首先，让我们需要导航到alchemy.com点击 "登录 "并创建一个新账户。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814221313.png)

选择Ethereum生态系统。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814221332.png)

给你的应用程序和团队起个名字，选择Rinkeby网络并点击创建应用程序。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814221359.png)

一旦你完成了onboarding 过程，我们就会被重定向到仪表板。点击你决定的名称的应用程序，在这种情况下，"测试"，点击右上角的 **VIEW KEY**按钮，并复制HTTP URL。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814221445.png)

接下来，你需要将Alchemy添加到Metamask作为Rineby RPC提供者。如果你没有安装Metamask，请确保按照本指南将其添加到你的浏览器并创建一个新钱包。

## 将 Alchemy Rinkeby 添加到您的 Metamask 钱包

安装 Metamask 后，单击网络下拉菜单，然后单击“添加网络”！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814221538.png)

您将被重定向到以下页面，您需要在其中填写 Rinkeby 网络和 RPC URL 信息。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814221611.png)

将以下信息添加到表单中：

- **Network name**：Alchemy Rinkeby
- **New RPC URL:** Rinkeby Alchemy 应用程序的 HTTP URL
- **Chain ID**：4
- **Currency Symbol**： ETH
- **Block Explorer**:  https://rinkeby.etherscan.io/

太棒了，您刚刚使用 Alchemy 将 Rinkeby 添加到 Metamask！🎉

现在是时候**在 Rinkeby 上部署我们的智能合约了，**但首先，你需要获得一些 Rinkeby 测试 ETH。

##  获得免费的 Rinkeby 测试 ETH

获取 Rinkeby 测试 ETH 非常简单，只需导航到[rinkebyfaucet.com](https://rinkebyfaucet.com/)，将钱包地址复制到文本栏中，然后点击“Send Me ETH”：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814221831.png)

10-20 秒后，您会看到 Rinkeby ETH 出现在 Metamask 钱包中。

无需登录，您每 24 次最多可以获得 0.1 ETH，或者使用 Alchemy 帐户可以获得 0.5 ETH。

现在您已经有了测试 ETH，**是时候在区块链上编译和部署我们的 NFT 智能合约了。**

## 在 Rinkeby 测试网上编译和部署 NFT 智能合约

回到 Remix，让我们点击页面左侧的编译器菜单，然后点击蓝色的“编译”按钮：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814221929.png)

然后点击“Deploy and Run Transactions”菜单，点击Environment下拉菜单并选择“injected Web3”：

**确保 Metamask 钱包在 Alchemy Rinkeby 网络上**，从合约下拉菜单中选择 NFT 智能合约，然后单击部署。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814221947.png)

将出现 Metamask 弹出窗口，单击“签名”，然后继续支付 Gas 费用。

如果一切都按预期工作，10 秒后，您应该会在 Deployed Contracts 下看到该合约：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814221959.png)

现在智能合约已部署在 Rinkeby 测试网上，是时候铸造我们的 NFT，但首先，您需要在 IPFS 上创建和上传元数据，让我们了解“元数据”一词的含义。

##  什么是 NFT 元数据？

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814222021.png)

对于OpenSea来说，要拉入ERC721代币的链外元数据，合同将需要返回一个指向托管元数据的URI。为了找到这个URI，OpenSea、Rarible和其他流行的市场将使用ERC721Uristorage标准中的tokenURI方法。

ERC721中的tokenURI函数应该返回一个HTTP或IPFS URL，例如ipfs://bafkreig4rdq3nvyg2yra5x363gdo4xtbcfjlhshw63we7vtlldyyvwagbq。当被查询时，这个URL应该返回一个包含你的令牌元数据的JSON blob数据。

你可以在OpenSea官方文档中阅读更多[关于元数据标准的内容](https://docs.opensea.io/docs/metadata-standards)。

## 如何格式化你的 NFT 元数据

根据 OpenSea 文档，NFT 元数据应存储在文件中，结构如下：`.json`

```json
{ 
  "description": "YOUR DESCRIPTION",
  "external_url": "YOUR URL",
  "image": "IMAGE URL",
  "name": "TITLE", 
  "attributes": [
    {
      "trait_type": "Base", 
      "value": "Starfish"
    }, 
    {
      "trait_type": "Eyes", 
      "value": "Big"
    }, 
    {
      "trait_type": "Mouth", 
      "value": "Surprised"
    }, 
    {
      "trait_type": "Level", 
      "value": 5
    }, 
    {
      "trait_type": "Stamina", 
      "value": 1.4
    }, 
    {
      "trait_type": "Personality", 
      "value": "Sad"
    }, 
    {
      "display_type": "boost_number", 
      "trait_type": "Aqua Power", 
      "value": 40
    }, 
    {
      "display_type": "boost_percentage", 
      "trait_type": "Stamina Increase", 
      "value": 10
    }, 
    {
      "display_type": "number", 
      "trait_type": "Generation", 
      "value": 2
    }
  }
```

以下是每个属性存储内容的简要说明：

| 属性                 | 解释                                                         |
| :------------------- | :----------------------------------------------------------- |
| image                | 这是项目图像的 URL。可以是几乎任何类型的图像（包括 SVG，OpenSea 将缓存到 PNG），并且可以是 IPFS URL 或路径。我们建议使用 350 x 350 的图像。 |
| image_data           | 原始 SVG 图像数据，如果您想动态生成图像（不推荐）。仅当您不包含图像参数时才使用此选项。 |
| **external_url**：   | 这是显示在 OpenSea 上资产图像下方的 URL，允许用户离开 OpenSea 并在您的站点上查看该项目。 |
| **description**      | 项目的人类可读描述。支持降价。                               |
| **name**             | 项目名称                                                     |
| **attributes**       | 这些是项目的属性，将显示在项目的 OpenSea 页面上。（见下文）  |
| **background_color** | OpenSea 上项目的背景颜色。必须是六个字符的十六进制，没有前置 |
| **animation_url**    | 项目多媒体附件的 URL。支持文件扩展名 GLTF、GLB、WEBM、MP4、M4V、OGV 和 OGG，以及仅音频扩展名 MP3、WAV 和 OGA。Animation_url 还支持 HTML 页面，允许我们使用 JavaScript 画布、WebGL 等构建丰富的体验和交互式 NFT。现在支持 HTML 页面中的脚本和相对路径。但是，不支持访问浏览器扩展。**youtube_url** YouTube 视频的 URL |

现在我们对你的令牌元数据将包含的内容有了一个简单的了解，让我们来学习如何创建它并将其存储在IPFS上。

## 在 IPFS 上创建和上传元数据

首先，导航到[`filebase.com`](https://filebase.com/)并创建一个新帐户。

登录后，单击左侧菜单上的bucket按钮，然后创建一个新bucket ：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814222451.png)

导航到bucket ，单击**上传按钮**，然后上传您要用于 NFT 的图像，[我将使用以下内容](https://ipfs.filebase.io/ipfs/bafybeihyvhgbcov2nmvbnveunoodokme5eb42uekrqowxdennt2qyeculm).

上传后单击它并复制 IPFS 网关 URL：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814222816.png)

使用任何文本编辑器，**粘贴以下 JSON 代码：**

```json
{ 
  "description": "This NFT proves I've created and deployed my first ERC20 smart contract on Rinkeby with Alchemy Road to Web3",
  "external_url": "Alchemy.com/?a=roadtoweb3weekone",
  "image": "https://ipfs.filebase.io/ipfs/bafybeihyvhgbcov2nmvbnveunoodokme5eb42uekrqowxdennt2qyeculm",
  "name": "A cool NFT", 
  "attributes": [
    {
      "trait_type": "Base", 
      "value": "Starfish"
    }, 
    {
      "trait_type": "Eyes", 
      "value": "Big"
    }, 
    {
      "trait_type": "Mouth", 
      "value": "Surprised"
    }, 
    {
      "trait_type": "Level", 
      "value": 5
    }, 
    {
      "trait_type": "Stamina", 
      "value": 1.4
    }, 
    {
      "trait_type": "Personality", 
      "value": "Sad"
    }, 
    {
      "display_type": "boost_number", 
      "trait_type": "Aqua Power", 
      "value": 40
    }, 
    {
      "display_type": "boost_percentage", 
      "trait_type": "Stamina Increase", 
      "value": 10
    }, 
    {
      "display_type": "number", 
      "trait_type": "Generation", 
      "value": 2
    }
  }
```

并将文件保存为“metadata.json”。移回 Filebase 并将文件上传到我们上传图片的同一bucket中。`metadata.json`

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814222857.png)

最后，点击 CID 并复制它，我们将在下一部分中需要它来构建 NFT 时的令牌 URI：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814222907.png)

## 铸造你的 Rinkeby NFT

返回 Remix 并在 Deploy & Run Transactions 菜单中，进入“已部署的合约” - 然后单击我们刚刚部署的合约，它将打开您的智能联系人中包含的所有方法的列表：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814222951.png)

橙色方法是在区块链上实际写入的方法，而蓝色方法则是从区块链上学习的方法。

单击 safeMint 方法下拉图标**并将您的地址**和以下字符串粘贴到 uri 字段中：

```
ipfs://\<your\_metadata\_cid>
```

单击交易将创建一个 Metamask 弹出窗口，提示您支付 gas 费用。

点击“签名”并继续铸造你的第一个 NFT！

等待几秒钟，为确保铸币成功通过，将您的地址复制并粘贴到 balanceOf 方法输入中，然后运行它 - 它应该显示您有 1 个 NFT。

对 tokenUri 方法做同样的事情，插入“0”作为 id 参数——它应该显示你的 tokenURI。

伟大的！你刚刚铸造了你的第一个 NFT！🎉

现在是时候**转移到 OpenSea**来检查元数据是否是良性读取的。

## 在 OpenSea 上可视化您的 NFT

导航[测试网.opensea.io](https://testnets.opensea.io/)并**使用您的 Metamask 钱包登录**。然后单击您的个人资料图片，您应该会在那里看到您新铸造的 NFT。如果图像尚不可见，请单击它，然后单击“刷新元数据”按钮。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814223143.png)

有时 OpenSea 很难识别测试网元数据 - 并且可能需要长达 6 小时才能看到它。一段时间后，[您的 NFT 应该如下所示](https://testnets.opensea.io/assets/mumbai/0x5a411430964664412e69cff1134759f6bb57c5d7/1)：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814223241.png)

**恭喜，您已成功创建、修改和部署您的第一个智能合约。铸造了你的第一个 NFT，并在 IPFS 上发布了你的图像！**🔥

**下一步？**你为什么不修改你的智能合约，让用户只能铸造一定数量的 NFT？每个用户 5 个就足够了，否则有人可能会开始铸造数千个 NFT！

为此，请查看mapping类型，这里有一个很棒的指南可以引导您完成它。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
