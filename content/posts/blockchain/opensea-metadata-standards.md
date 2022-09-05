---
title: Opensea元数据标准：Opensea是怎样从智能合约中提取数字资产并显示的？
description: null
author: 李留白
weight: 0
date: 2022-09-05T14:13:35.387Z
lastmod: 2022-09-05T14:55:30.576Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220905221650.png
---

> [如何将丰富的元数据添加到您的 ERC721 或 ERC1155 NFT](https://docs.opensea.io/edit/metadata-standards)

提供资产元数据(*asset metadata*)允许像OpenSea这样的应用程序为数字资产提取丰富的数据，并在应用程序中轻松显示它们。特定智能合约上的数字资产通常仅由唯一的标识符（例如ERC721中的token_id）表示，因此元数据允许这些资产具有额外的属性，如名称、描述和图像。

## 实现token URI

为了让OpenSea为ERC721和ERC1155资产提取链外元数据，您的合约需要返回一个 URI，我们可以在其中找到元数据。为了找到这个URI，我们使用ERC721的tokenURI方法和ERC1155的uri方法。首先，让我们仔细看一下Creature合同中的tokenURI方法。

NFT.sol：

```solidity
/**
 * @dev Returns an URI for a given token ID
 */
function tokenURI(uint256 _tokenId) public view returns (string) {
  return Strings.strConcat(
      baseTokenURI(),
      Strings.uint2str(_tokenId)
  );
}
```

你的ERC721中的tokenURI函数或ERC1155合约中的uri函数应该返回一个HTTP或IPFS URL。当被查询时，这个URL应该反过来返回一个包含你的令牌元数据的JSON blob数据。你可以在[OpenSea creatures repo](https://github.com/ProjectOpenSea/opensea-creatures/tree/master/metadata-api)中看到一个用于提供元数据的简单的Python服务器的例子。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220905223208.png)

请参阅下面有关**IPFS 和 Arweave**的部分，了解如何处理去中心化元数据 URI。

##  元数据结构

OpenSea支持根据[官方ERC721元数据标准](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-721.md)或[Enjin元数据建议结构的元数据](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1155.md#erc-1155-metadata-uri-json-schema)。

此外，我们还支持其他几个允许多媒体附件的属性——包括音频、视频和 3D 模型——以及您的项目的交互式特征，为您提供 OpenSea 市场上的所有排序和过滤功能。

下面是一个OpenSea creatures 的元数据的例子。

```
{
  "description": "Friendly OpenSea Creature that enjoys long swims in the ocean.", 
  "external_url": "https://openseacreatures.io/3", 
  "image": "https://storage.googleapis.com/opensea-prod.appspot.com/puffs/3.png", 
  "name": "Dave Starbelly",
  "attributes": [ ... ], 
}
```

以下是每个属性的工作方式。


| 字段             | 简介                                                         |
| :--------------- | :----------------------------------------------------------- |
| image            | 这是项目图像的 URL。可以是几乎任何类型的图片（包括SVG，它将被OpenSea缓存为PNG），可以是[IPFS ](https://github.com/ipfs/is-ipfs)URLs或路径。我们建议使用350 x 350的图片。 |
| image_data       | 原始的SVG图片数据，如果你想动态生成图像（不推荐）。只有在你不包括image参数的情况下才使用这个。 |
| external_url     | 这是在OpenSea上出现在资产图片下面的URL，允许用户离开OpenSea，在你的网站上查看该项目。 |
| description      | 项目的可读描述。支持Markdown。                               |
| name             | 项目的名称。                                                 |
| attributes       | 这些是项目的属性，它们将显示在项目的OpenSea页面上。(见下文)  |
| background_color | 项目在OpenSea上的背景颜色。必须是一个不带前置#的六位数的十六进制。 |
| animation_url    | 一个指向项目的多媒体附件的URL。支持GLTF、GLB、WEBM、MP4、M4V、OGV和OGG等文件扩展名，以及纯音频扩展名MP3、WAV和OGA。Animation_url也支持HTML页面，允许你使用JavaScript画布、WebGL等建立丰富的体验和互动的NFTs。现在支持HTML页面内的脚本和相对路径。然而，不支持对浏览器扩展的访问。 |
| youtube_url      | 一个指向YouTube视频的URL。                                   |

## 属性

为了让你的项目更有吸引力，我们还允许你为你的元数据添加自定义的 "属性"，这些属性将显示在你的每个资产下面。例如，这里是[OpenSea其中一个creatures](https://testnets.opensea.io/assets/0x7dca125b1e805dc88814aed7ccc810f677d3e1db/25)的属性。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220905223846.png)

为了生成这些属性，在元数据中包含了以下的属性数组。

```
...
{
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
  ]
}
```

这里 trait_type 是 trait 的名称，value 是 trait 的值，而 display_type 是一个字段，指示您希望它如何显示。对于字符串特征，您不必担心 display_type。

###  **数值特征**

对于数字特征，OpenSea目前支持三种不同的选项：number（下图中的右下角）、boost_percentage（上图中的左下角）和boost_number（类似于boost_percentage，但不显示百分比符号）。如果你传入的值是一个数字，而你没有设置display_type，那么该特性将出现在排名部分（上图中的右上方）。

添加一个可选的max_value，为数字特征的可能值设置一个上限。它的默认值是OpenSea迄今为止在你的合同上看到的资产的最大值。如果你设置了max_value，请确保不要传入一个更高的值。

### **日期特质**

OpenSea也支持一个日期显示类型。这种类型的特征将出现在右栏的 "排名 "和 "统计 "附近。传入一个unix的时间戳（秒）作为值。

```
    {
      "display_type": "date", 
      "trait_type": "birthday", 
      "value": 1546360800
    }
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220905224136.png)

如果你不想为一个特定的特征有一个trait_type，你可以在特征中只包含一个值，它将被设置为一个通用属性。比如说。

```
  {
    "value": "Happy"
  }
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220905224203.png)

我们也支持Enjin Metadata风格。

```
{
    "properties": {
        "base": "starfish",
        "rich_property": {
            "name": "eyes",
            "value": "big",
            "display_value": "Big",
        }
                ...
    }
}
```

### 属性指南

当你想出你的属性时，有几个重要的注意事项! 你应该把字符串属性写成字符串（记住引号），把数字属性写成浮点数或整数，这样OpenSea就可以适当地显示它们。字符串属性应该是人类可读的字符串。



## 部署你的元数据API

我们欢迎你以你认为合适的方式部署你的元数据API：你可以把它托管在IPFS、云存储或你自己的服务器上。为了让你开始，我们在Python和NodeJS中都创建了一个API服务器样本。

- [Python 示例 API 服务器](https://github.com/ProjectOpenSea/metadata-api-python)
- [NodeJS 示例 API 服务器](https://github.com/ProjectOpenSea/metadata-api-nodejs)

我们将很快创建一个关于构建和部署元数据服务器到Heroku的教程。同时，请查看[Heroku关于这个主题的资源](https://devcenter.heroku.com/articles/git)。

## PFS and Arweave URIs

OpenSea支持在分散的文件网络中存储NFT元数据，这样它们就不能被中心方所修改。

如果你使用 [IPFS](https://ipfs.io/) 来托管你的元数据, 你的 应该是这样的格式： `ipfs://<hash>`。例如, `ipfs://QmTy8w65yBXgyfG2ZBg5TrfB2hPjrDQH3RCQFJGkARStJb`. 如果你打算在IPFS上存储,我们推荐 [Pinata](https://pinata.cloud/) 来轻松存储数据。 这里有一个Chainlink提供的入门教程: https://blog.chain.link/build-deploy-and-sell-your-own-dynamic-nft/.

[Arweave](https://www.arweave.org/)的等价物是 `ar://<hash>`.。例如，`ar://jK9sR4OrYvODj7PD3czIAyNJalub0-vdV_JAg1NqQ-o`.

### **冻结元数据**

你可以通过从智能合约中发出这个事件，向OpenSea表明一个NFT的元数据不再能被任何人改变（换句话说，它被 "冻结"）。

`event PermanentURI(string _value, uint256 indexed _id);`

如果你想把整个智能合约标记为冻结，[请联系我们](http://openseahelp.zendesk.com/)。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
