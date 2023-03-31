---
title: 使用 IPFS 加密安全地存储文件
description: null
author: 李留白
weight: 0
date: 2023-03-31T16:15:03.716Z
lastmod: 2023-03-31T16:21:31.577Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230401001516.png
---

您是否厌倦了将私人文件存储在可能被黑客攻击或审查的集中式云服务上？

您是否希望确保您的数据安全且仅供您和您授权的同行访问？

如果是这样，您可能会对[IPFS-Encrypted](https://github.com/0xVikasRushi/ipfs-encrypt)感兴趣，这是一个 Node.js 模块，允许您使用 AES-256-CBC 加密将加密文件夹上传到 IPFS 或从 IPFS 下载加密文件夹。

### 如何运行

[IPFS-Encrypted](https://www.npmjs.com/package/ipfs-encrypted)利用星际文件系统 (IPFS)，这是一种点对点网络，可提供去中心化的内容寻址存储和检索系统。IPFS 允许您通过为文件分配一个基于内容而不是位置或名称的唯一内容 ID (CID) 来存储和共享文件。这意味着您可以从任何 IPFS 节点访问您的文件，而不必担心它们的物理位置或可用性。IPFS 还提供内置的版本控制、重复数据删除和缓存机制，可以降低分发和共享大文件的存储和带宽成本。

然而，虽然 IPFS 非常适合存储和共享公共文件，但它不提供内置加密或访问控制机制。这意味着任何知道您文件的 CID 的人都可以访问它们，无论他们是否有权这样做。为了解决这个限制，IPFS-Encrypted 使用 AES-256-CBC 加密来使用只有您和您的授权伙伴知道的密码来加密您的文件夹的内容。这样，即使有人获得了您的加密文件夹的 CID，他们也无法在没有密码的情况下访问其中的内容。

IPFS-Encrypted 还集成了 Web3 存储，这是一个分散的存储平台，为 IPFS 提供企业级的可靠性、安全性和性能。Web3 存储允许您使用可以通过注册他们的服务获得的令牌来验证您对 IPFS 网络的请求。通过使用 Web3 存储，您可以确保您的文件存储在安全且冗余的节点网络中，这些节点网络可防止数据丢失、审查和其他攻击。

### 网站

我们使用了 IPFS 加密的 Node.js 模块，并构建了一个网站，用户可以在其中将文件上传/检索到 IPFS。上传到我们网站的所有文件都经过加密，确保我们用户数据的安全和隐私。要试用我们的网站，请访问链接：[https ://ipfs-encrypt-web.vercel.app/](https://ipfs-encrypt-web.vercel.app/)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230401002042.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230401002050.png)

### 要求

要使用`ipfs-encrypted`，您需要具备以下条件：

- Node.js 安装在您的计算机上。您可以从官方[Node.js 网站](https://nodejs.org/en/download/)下载它。
- 用于验证您对 IPFS 网络的请求的 Web3 存储令牌。[您可以通过注册Web3 Storage](https://web3.storage/)来获取令牌。

### 安装

您可以`ipfs-encrypted`通过运行以下命令使用 npm安装

```javascript
npm install ipfs-encrypted
```

### 要求

要使用`ipfs-encrypted`，您需要具备以下条件：

- Node.js 安装在您的计算机上。您可以从官方[Node.js 网站](https://nodejs.org/en/download/)下载它。
- 用于验证您对 IPFS 网络的请求的 Web3 存储令牌。[您可以通过注册Web3 Storage](https://web3.storage/)来获取令牌。

### 如何使用 IPFS 加密

使用 IPFS-Encrypt 简单直接。以下是您可以使用的一些主要功能：[DOCS](https://github.com/0xVikasRushi/ipfs-encrypt#readme)

- `uploadEncryptionIpfs(Web3Storagetoken, folderPath, password)`：将文件夹上传到 IPFS 并使用密码加密其内容。
- `decryptFolderIpfs(Web3Storagetoken, cid, password, downloadLocation)`：从 IPFS 检索加密文件夹并使用密码解密其内容。
- `uploadToIpfs(Web3Storagetoken, folderLocation)`：将文件夹上传到 IPFS 而不加密。
- `downloadFile(Web3Storagetoken, cid, downloadLocation)`: 从 IPFS 下载一个文件到当前目录。

> 原文：https://vikasrushi.hashnode.dev/securely-store-files-with-ipfs-encrypted

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)