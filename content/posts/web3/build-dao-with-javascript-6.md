---
title: Web3教程：仅仅一个周末，只用Javascript就可以构建出你自己的DAO（6）
description: null
author: 李留白
weight: 0
date: 2022-11-08T12:59:44.734Z
lastmod: 2022-11-08T12:59:55.747Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211032050628.png
---

### 目录

**🏗 开始**

- 🥑 让我们构建一些很酷的东西。
- 🤔 什么是 DAO？

**💻 为 DAO 设置客户端应用程序**

- 🛠 获取客户端代码 + 获取设置。
- 👜 将“连接到钱包”添加到您的 DAO 仪表板。

**😎 创建会员 NFT**
- 🌈 部署你的 NFT 包。
- 😤 部署 NFT 元数据。
- 😲 让用户铸造你的 NFT。

**💎 创建代币+治理**

- 😈 部署 ERC-20 合约。
- 😇 在 DAO Dashboard 上展示代币持有者。
- 👩‍⚖️ 建库+治理。
- ✅ 让用户对提案进行投票。

**🌟收尾工作**

- 🤠 删除您的管理员权限并处理基本错误。
- 🎉 完成并庆祝。

### 🤠 删除您的管理权限并处理基本错误

#### 😡 撤销角色

如果你还记得，你实际上仍然持有 ERC-20 合约的“铸币”权。这意味着您可以根据需要创建更多代币，这可能会吓坏你的 DAO 成员，哈哈。你可以去为自己铸造十亿个代币，哈哈。

最好完全撤销你的“铸币”角色。

这样，只有投票合约才能铸造新的代币。我们可以通过在`scripts/11-revoke-roles.js`中添加以下内容来做到这一点：

```jsx
import sdk from "./1-initialize-sdk.js";

(async () => {
  try {
    const token = await sdk.getContract("INSERT_TOKEN_ADDRESS", "token");
    // Log the current roles.
    const allRoles = await token.roles.getAll();

    console.log("👀 Roles that exist right now:", allRoles);

    // Revoke all the superpowers your wallet had over the ERC-20 contract.
    await token.roles.setAll({ admin: [], minter: [] });
    console.log(
      "🎉 Roles after revoking ourselves",
      await token.roles.getAll()
    );
    console.log("✅ Successfully revoked our superpowers from the ERC-20 contract");

  } catch (error) {
    console.error("Failed to revoke ourselves from the DAO trasury", error);
  }
})();
```

当我使用它运行`node scripts/11-revoke-roles.js`时，我得到：

```plaintext
buildspace-dao-starter % node scripts/11-revoke-roles.js
👀 Roles that exist right now: {
  admin: [ '0xF11D6862e655b5F4e8f62E00471261D2f9c7E380' ],
  minter: [ '0xF11D6862e655b5F4e8f62E00471261D2f9c7E380' ],
  transfer: [
    '0xF11D6862e655b5F4e8f62E00471261D2f9c7E380',
    '0x0000000000000000000000000000000000000000'
  ]
}
🎉 Roles after revoking ourselves {
  admin: [],
  minter: [],
  transfer: [
    '0xF11D6862e655b5F4e8f62E00471261D2f9c7E380',
    '0x0000000000000000000000000000000000000000'
  ]
}
✅ Successfully revoked our superpowers from the ERC-20 contract
```

一开始你可以看到我的地址`0xF79A3bb8`对 ERC-20 有很多特权。所以，在我们运行`token.roles.setAll({ admin: [], minter: [] })`之后，你会看到唯一拥有铸币者角色的人就是我的投票合约！

我们现在安全了，不会被管理员接管了  :)。

你会看到我仍然有与`AddressZero`结合的`transfer`角色，`AddressZero`在角色数组中意味着每个人都可以转移令牌（这是我们想要的）。我们的地址也在那里，这并不重要。

#### 👍 处理基本的不支持的网络错误

首先，让我们在`App.jsx`顶部导入最后一个钩子`useNetwork`，以识别 Goerli 网络之外的连接。另外，我们从 thirdweb SDK 中导入`ChainId`来获取 Goerli 的链 ID。

```jsx
import {
  useAddress,
  useNetwork,
  useContract,
  ConnectWallet,
  Web3Button,
  useNFTBalance,
} from '@thirdweb-dev/react';
import { ChainId } from '@thirdweb-dev/sdk';
```

然后，在我们的`useAddress`钩子下定义我们的`useNetwork`钩子：

```jsx
const network = useNetwork();
```

接下来，在`App.jsx`文件中的`const memberList =...`函数下方的添加以下内容：

```jsx
if (address && (network?.[0].data.chain.id !== ChainId.Goerli)) {
  return (
    <div className="unsupported-network">
      <h2>Please connect to Goerli</h2>
      <p>
        This dapp only works on the Goerli network, please switch networks
        in your connected wallet.
      </p>
    </div>
  );
}
```

我们要检查是否在我们的首选网络上找到一个链，在我们的例子中是Goerli，如果没有，我们要提示用户切换网络。

很简单！但是，非常有用。如果用户不在 Goerli 上，它会弹出一条消息！

#### 🤑 在 Uniswap 上查看你的代币

你可能会问自己，像[ENS DAO](https://coinmarketcap.com/currencies/ethereum-name-service/?utm_source=buildspace.so&utm_medium=buildspace_project)或最近的[宪法 DAO](https://coinmarketcap.com/currencies/constitutiondao/?utm_source=buildspace.so&utm_medium=buildspace_project)这样的代币是如何拥有价值真金白银的治理代币的。基本上，这是因为其他人实际上可以直接在 Uniswap 等去中心化交易所购买他们的治理代币。

例如——也许一个随机的人走到我们面前说，“嘿，我会给你 100 美元换 100 美元 HOKAGE，因为我想加入 NarutoDAO 并拥有一些治理权”。嗯，这意味着 `$HOKAGE` 现在具有真正的价值。这意味着 1 $HOKAGE = 1 美元。而且，由于有 1,000,000 美元 HOKAGE，这意味着我的代币完全稀释后的市值将为 1,000,000 美元。

很疯狂，对吧:)？

人们通常在 Uniswap 上进行此类交换。

信不信由你，你的代币现在将出现在 Goerli 下的 Uniswap 上。

这是一个快速视频，供您自己实际操作：

https://www.loom.com/share/8c235f0c5d974c978e5dbd564bbca59d

[您可以在此处](https://docs.uniswap.org/protocol/V2/concepts/core-concepts/pools?utm_source=buildspace.so&utm_medium=buildspace_project)阅读有关流动性池的更多信息。你会注意到在视频中，并没有一个针对$HOKAGE的。但是，从技术上讲，任何人都可以进来创建一个池子，让人们用ETH美元换HOKAGE美元。这个池子可以有100美元。或者，它可以有100万美元。这要看我的代币有多受欢迎!

#### 🎨 稍微自定义您的网络应用程序！

花一些时间来稍微定制您的网络应用程序。改变一些颜色。换个副本。添加一些很酷的表情符号。前往`public/index.html`并更改标题和描述等内容！

随机想法：当人们投票时，播放您的国家的国歌或其他东西，哈哈。

在继续前进之前，花些时间把这些页面变成你自己的。即使你所做的只是改变背景颜色也是好的。哈哈，玩得开心点。

### 🎉 完成并庆祝

#### 😍 你好，DAO大师

你已经做到了。你做到了最后。我很自豪。这就是你：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211082044462.gif)

DAO是一个非常新的、神秘的东西，很多人都在谈论。但是，你所做的不仅仅是谈论它们--你真的做了一个！！。在经典的构建空间风格中，你通过直接跳入代码，潜入了这个非常深刻和复杂的话题。祝贺你，我的朋友！

在这里快速回顾一下，您只需：

✅ 部署您自己的自定义 ERC-20 令牌。

✅ 部署您自己的 ERC-1155 NFT，人们可以铸造来加入您的 DAO。

✅ 部署你自己的治理合约+金库。

✅ 构建了一个 dapp，让人们可以连接他们的钱包，获得 NFT，查看 DAO 仪表板，在那里他们可以看到其他成员 + 对由你的治理合约直接执行的提案进行实际投票。

我知道这很难相信，但你创建了一个成熟的 DAO。我希望至少这个项目能让你了解 DAO 是什么以及它是如何工作的。当然，DAO 的内容比我们在这个项目中所涵盖的要多，比如代币经济学、运行一个实际的社区、规划你的 DAO 路线图等等——这些都是我们没有涵盖的东西，但是嘿，我敢肯定你会弄清楚的；）。

### 🌈出发前

转到 Discord 中的**#showcase**  ，然后给我们一个指向您最终产品的链接，我们可以随意体验。如果您没有使用 Replit，请务必使用 Vercel 或 Netlify 之类的工具进行部署。**你已经走到了最后一步，你最好部署你的网站。**

此外，您应该完全发布您的最终项目，并向世界展示您的创作！你所做的无论如何都不容易。甚至可以制作一个小视频来展示你的项目并将其附加到推文中。让您的推文看起来漂亮并炫耀！

如果您愿意，请标记[@_buildspace](https://twitter.com/_buildspace?utm_source=buildspace.so&utm_medium=buildspace_project) :)。 **每当我们看到人们发布他们的项目时，它都会给我们很大的动力。** 另外，您可以激励其他人进入 web3。

请给我们更多多巴胺的冲击吧。

最后，如果你在#feedback 中告诉我们你喜欢这个项目以及项目的结构，那也很棒。你最喜欢 buildspace 的什么？什么地方不好？希望我们为未来的项目做出哪些改变？你的反馈会很棒！

下次见！！！

> Github源码：https://github.com/buildspace/buildspace-projects/tree/main/JS_DAO/
>
> 原文链接：https://buildspace.so/p/build-dao-with-javascript

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
