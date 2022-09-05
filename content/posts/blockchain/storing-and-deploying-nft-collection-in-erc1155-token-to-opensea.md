---
title: 在Opensea市场上以ERC1155代币存储和部署NFT集合
description: null
author: 李留白
weight: 0
date: 2022-09-05T14:02:38.525Z
lastmod: 2022-09-05T15:07:10.814Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220905220302.png
---

NFT 元数据是 NFT 的核心。它是一个 JSON 文档，包含 NFT 的名称、NFT 的描述、托管图像的链接、特征等。 NFT 元数据将成为您的 NFT 智能合约的输入，该合约将部署在您选择的网络上。

本文将向您展示存储 NFT 元数据、将其部署到区块链以及在 Opensea 上托管您的 NFT 的简单步骤。

## 第 1 步：打开 Remix IDE

Remix IDE 是一个开源 Web 应用程序工具，可帮助您直接从浏览器编写 Solidity 程序、编译和部署智能合约。 Remix-IDE 可以在 remix.ethereum.org 上找到，更多信息可以在文档中找到。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220905220618.png)

## 第 2 步：上传 NFT 文件

NFT 文件可以作为集合上传，也可以单独上传。对于收藏上传，如果您想将它们永远留在网络上，我建议您使用 https://nft.storage/（非常易于使用），或者如果您希望您的 NFT 文件位于一个 URL 下，请使用 https:// car.ipfs.io/ 上传你的文件，在你的目录中获取一个car文件并通过 https://nft.storage/ 上传。

## 第 3 步：将 CID 链接输入智能合约。

上传完成后获取 CID 链接，并将其输入到您的智能合约中。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220905220630.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220905220635.png)

智能合约中输入的 CID 链接

## 第 4 步. 部署智能合约

将智能合约直接从 remix ide 部署到区块链网络并复制合约地址。

登录opensea，输入合约地址，发布NFT。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220905220645.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
