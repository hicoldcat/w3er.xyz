---
title: 在去中心化存储上使用 Spheron UI 部署应用程序
description: null
author: 李留白
weight: 0
date: 2023-07-05T06:48:34.163Z
lastmod: 2023-07-05T06:48:43.223Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705144339.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705144339.png)

## 什么是Spheron？

Spheron 是一个**基础设施即服务 (IaaS)**平台。它可用于存储 SDK、去中心化托管和去中心化计算，让您可以在 IPFS 和 Filecoin 上托管您的应用程序，而不是集中式解决方案。

Spheron 提供了创建组织的功能，您可以在其中协作处理项目。它支持 Github、GitLab 和 BitBucket 来部署应用程序。并通过内容交付网络 (CDN) 从边缘交付数据，从而提高性能并减少加载时间。

Spheron 将应用程序托管在*IPFS*上，IPFS具有专用网关，可以提供更快的内容交付速度、更高的速率限制，并且不会限制您依赖一台服务器。

**Spheron 的命令行界面 (CLI)**是创建 Dapp 并将其部署到去中心化存储的锦上添花。

## 为什么使用 Spheron 进行部署？

有许多云平台可帮助您部署应用程序，例如 AWS、Google Cloud、Vercel 和 Azure。那为什么选择Spheron呢？

引用的平台与 Spheron 之间的主要区别在于，所有引用的云平台都提供集中式解决方案。将应用程序数据存储在一个中央位置可以为一个权威机构提供巨大的权力。如果他们的服务器出现故障，那么客户将面临巨大的损失。**当AWS服务器出现故障时，麦当劳、OKCubid、Taco Bell和达美航空等公司不得不遭受巨大损失。**

此外，这些集中式解决方案更容易因硬件故障或网络攻击而丢失数据，或者将数据泄露给第三方。

**Spheron 允许您在去中心化存储上部署应用程序。**由区块链支持的去中心化存储分布在全球范围内。因此，它不向任何一方或个人授予权力。分散存储意味着您的文件存储在多台计算机上，这也意味着整个服务器永远不会宕机。您的应用程序将始终启动并运行。

此类存储中的数据分发由附近的对等点处理，最终由于本地网络带宽而提供更高的传输速度。由于数据的加密和分片，任何人都无法访问它，从而提高了应用程序的安全性和隐私性。

与 AWS、Google Cloud 或 Azure 相比，存储成本较低，因为有数百万个节点可用于托管数据，因此存储成本更高。

### NextJS 应用程序部署分步教程

现在，当您足够确信为什么我们需要在去中心化存储上托管我们的应用程序时，让我们深入了解 Spheron 如何帮助我们做到这一点。

Spheron 提供无缝的用户界面来托管您的应用程序，并提供出色的技术支持。无论您托管哪个应用程序，他们都能满足您的需求。

让我们看一下在 Spheron 上托管应用程序的步骤。

### 使用 Spheron UI 部署 NextJs 应用程序

1. 创建 NextJS 应用程序（如果您没有），请使用以下命令创建一个。


```bash
npx create-next-app@latest my-app
```

2. 将以下代码添加到您的 next.config.js 文件中。


```javascript
const nextConfig = {
 ...,
  images: {
    loader: "akamai",
    path: "",
  },
  trailingSlash: true,
};

module.exports = nextConfig;
```

1. 为了进行部署，我们需要将我们的应用程序放在 GitHub 上。推送您在 GitHub 上创建的 my-app。如果您是 Github 新手，[请参阅以下分步指南，](https://docs.github.com/en/migrations/importing-source-code/using-the-command-line-to-import-source-code/adding-locally-hosted-code-to-github)将您的应用程序推送到 GitHub 存储库。
2. 现在转到[Spheron 主页](https://spheron.network/)并开始在 Spheron 上启动您的应用程序。单击“立即启动您的 DApp“ 按钮。

![img](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705144540.png)

1. 点击 ”继续使用 GitHub当您将更改推送到 Git 存储库时，Spheron 会自动同步所有更改，您无需手动重新部署。

![img](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705144557.png)

1. 选择存储库并允许 Spheron 访问 GitHub 存储库。选择您要使用的去中心化存储Filecoin 或 IPFS。

![img](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705144609.png)

1. 现在添加任何特定配置，例如 Spheron 仪表板上的 ENVIORNMENT_VARIABLES 和根存储库。设置正确的分支和繁荣，您就可以部署您的应用程序了。
2. 单击“部署“ 按钮。

![img](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705144622.png)

等待几秒钟。恭喜🎉🎉！您刚刚在 Spheron 上部署了您的应用程序。Spheron 为您所做的所有部署提供部署日志。

![img](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705144635.png)

等等……什么？您遇到错误并且部署失败？**在Spheron 聊天支持**的帮助下，5 小时内解决问题。最好的事情是您不必专门邮寄它们或等待“n”天直到问题得到解决。

![img](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705144703.png)

如果您想使用任何其他框架部署应用程序，请在此处查找[配置指南。](https://docs.spheron.network/framework-guide/)

### 结论：

1. Spheron 是一个**易于使用的**去中心化基础设施即服务平台，可将您的网站部署在去中心化存储上。
2. **Spheron 为去中心化托管、存储 SDK、去中心化计算**和 NFT 存储提供快速支持。
3. Spheron 从距离用户最近的服务器传送您的应用程序数据，这使得**应用程序加载速度更快**。
4. Spheron为解决查询提供**快速技术支持，并拥有丰富的文档。**
5. 与其他集中式解决方案相比，Spheron 为部署应用程序提供了**公平的价格。**

> 原文：https://quaintrelle7.hashnode.dev/deploy-apps-using-spheron-ui-on-decentralized-storage