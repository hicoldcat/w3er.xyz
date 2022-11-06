---
title: Web3教程：仅仅一个周末，只用Javascript就可以构建出你自己的DAO（3）
description: null
author: 李留白
weight: 0
date: 2022-11-06T02:01:42.441Z
lastmod: 2022-11-06T03:04:09.527Z
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


## 🌈 部署你的 NFT 包。

### 🍪  开始使用thirdweb

真棒！我们现在可以连接到用户的钱包，这意味着我们现在可以检查他们是否在我们的 DAO 中！为了加入我们的 DAO，用户需要会员资格 NFT。如果他们没有会员 NFT，我们会提示他们创建会员 NFT 并加入我们的 DAO！

但是，有一个问题。为了让我们铸造 NFT，我们需要编写 + 部署我们自己的 NFT 智能合约。**这实际上是thirdWeb 发挥作用的地方。**

thirdWeb 为我们提供了一套工具，无需编写任何 Solidity 即可创建我们所有的智能合约。

我们不写 Solidity。我们需要做的就是只使用 JavaScript 编写一个脚本来创建和部署我们的合约。thirdweb 将使用他们在[这里](https://github.com/thirdweb-dev/contracts?utm_source=buildspace.so&utm_medium=buildspace_project)创建的一组安全、标准的合约。**很酷的部分是在您创建合约之后，您拥有它们并且它们与您的钱包相关联。**

部署合约后，您可以使用它们的客户端 SDK 从前端轻松地与这些合约进行交互。

我无法形容与编写你自己的Solidity代码相比，用thirdweb创建一个智能合约是多么容易，它感觉就像与一个普通的后台库互动。让我们开始吧。

[thirdweb 仪表板](https://thirdweb.com/dashboard?utm_source=buildspace&utm_medium=buildspace_project)允许我们在不编写任何代码的情况下创建合约，但在本教程中，我们将使用 JavaScript 创建它们。

重要的！**thirdweb 没有数据库，您的所有数据都存储在链上。**

### 📝 创建一个运行thirdweb脚本的地方

现在我们需要实际编写一些脚本，让我们使用thirdweb 创建/部署我们的合约到Goerli。我们要做的第一件事是在项目的根目录中创建一个看起来像这样的`.env`文件。

```plaintext
PRIVATE_KEY=YOUR_PRIVATE_KEY_HERE
WALLET_ADDRESS=YOUR_WALLET_ADDRESS
QUICKNODE_API_URL=YOUR_QUICKNODE_API_URL
```

*注意：在 Repli？你需要使用[这个](https://docs.replit.com/programming-ide/storing-sensitive-information-environment-variables?utm_source=buildspace.so&utm_medium=buildspace_project)。基本上，.env 文件在 Replit 上不起作用。您应该使用此方法逐一添加具有相同名称的变量。完成后，您需要通过停止/运行 repo 来启动 Replit，以便它获取新的 env 变量！*

thirdweb 需要所有这些东西来代表您部署合同。他们没有存储任何内容，所有内容都保留在本地`.env`文件中。**不要将您的`.env`文件提交到 Github。你的信息会被盗走。当心。**

要从 Metamask 获取您的私钥，请查看[此](https://metamask.zendesk.com/hc/en-us/articles/360015289632-How-to-Export-an-Account-Private-Key?utm_source=buildspace.so&utm_medium=buildspace_project)内容。

要获取您的钱包地址，请查看[此](https://metamask.zendesk.com/hc/en-us/articles/360015289512-How-to-copy-your-MetaMask-account-public-address-?utm_source=buildspace.so&utm_medium=buildspace_project)内容。

### 🚀 快速节点

您在`.env`文件中需要的最后一件事是`QUICKNODE_API_URL`.

QuickNode 本质上帮助我们广播我们的合约创建交易，以便它可以尽快被测试网上的矿工拾取。一旦交易被挖掘出来，它就会作为合法交易被广播到区块链上。从那里，每个人都会更新他们的区块链副本。

[因此，请在此处](https://www.quicknode.com/?utm_source=buildspace&utm_campaign=generic&utm_content=sign-up&utm_medium=buildspace)使用 QuickNode 注册一个帐户 。

看看下面的视频，看看如何为测试网获得你的API**密钥**! 不要乱来，创建一个主网密钥，**我们需要一个测试网密钥。** 查看下面的视频，了解如何获取测试网的 API！

https://www.loom.com/share/bdbe5470b4b745819782f6727ba60baa

您现在应该在`.env`文件中包含这三个项目了！

### 🥳 初始化 SDK

前往`scripts/1-initialize-sdk.js`。

```jsx
import { ThirdwebSDK } from "@thirdweb-dev/sdk";

// Importing and configuring our .env file that we use to securely store our environment variables
import dotenv from "dotenv";
dotenv.config();

// Some quick checks to make sure our .env is working.
if (!process.env.PRIVATE_KEY || process.env.PRIVATE_KEY === "") {
  console.log("🛑 Private key not found.");
}

if (!process.env.QUICKNODE_API_URL || process.env.QUICKNODE_API_URL === "") {
  console.log("🛑 QuickNode API URL not found.");
}

if (!process.env.WALLET_ADDRESS || process.env.WALLET_ADDRESS === "") {
  console.log("🛑 Wallet Address not found.");
}

const sdk = ThirdwebSDK.fromPrivateKey(
  // Your wallet private key. ALWAYS KEEP THIS PRIVATE, DO NOT SHARE IT WITH ANYONE, add it to your .env file and do not commit that file to github!
  process.env.PRIVATE_KEY,
  // RPC URL, we'll use our QuickNode API URL from our .env file.
  process.env.QUICKNODE_API_URL
)

(async () => {
  try {
    const address = await sdk.getSigner().getAddress();
    console.log("👋 SDK initialized by address:", address)
  } catch (err) {
    console.error("Failed to get apps from the sdk", err);
    process.exit(1);
  }
})();

// We are exporting the initialized thirdweb SDK so that we can use it in our other scripts
export default sdk;
```

确保我们的 sdk 初始化正确！

在执行该功能之前，请确保您已安装 Node 16+，您可以使用以下命令检查您的版本：

```plaintext
node -v
```

*注意：如果您在 Replit 上，您实际上可以从它提供的 shell 运行脚本：*

如果你有旧版本的 Node，你可以[在这里](https://nodejs.org/en/?utm_source=buildspace.so&utm_medium=buildspace_project)更新它。（下载 LTS 版本）

*注意：如果您在 Replit 上，您实际上可以通过从 shell 运行它来更新节点版本：*

```plaintext
npm init -y && npm i --save-dev node@17 && npm config set prefix=$(pwd)/node_modules/node && export PATH=$(pwd)/node_modules/node/bin:$PATH
```

让我们执行它！转到您的终端并粘贴以下命令：

```plaintext
node scripts/1-initialize-sdk.js
```

这是我运行脚本时得到的结果。

```plaintext
buildspace-dao-starter % node scripts/1-initialize-sdk.js
👋 SDK initialized by address: 0xF11D6862e655b5F4e8f62E00471261D2f9c7E380
```

*注意：您可能还会看到一些随机警告，例如`ExperimentalWarning`，请确保您的应用地址已打印出来！*

史诗。如果你看到它打印出你的钱包地址，那么这意味着一切都已初始化！

### 🧨 创建一个 ERC-1155 集合

我们现在要做的是创建 + 部署 ERC-1155 合约到 Goerli。这基本上是我们创建 NFT 所需的基本模块。**我们还没有在这里创建我们的 NFT。我们只是围绕集合本身设置元数据。**这类似于集合的名称（例如 CryptoPunks）以及与集合相关联的图像，该图像在 OpenSea 上显示为标题。

*注意：您可能知道 ERC-721，其中每个 NFT 都是唯一的，即使它们具有相同的图像、名称和属性。使用 ERC-1155，多人可以成为同一个 NFT 的持有者。在这种情况下，我们的“会员 NFT”对每个人都是一样的，所以我们不必每次都创建一个新的 NFT，而可以简单地将相同的 NFT 分配给我们的所有成员。这也更省gas！对于所有持有者的 NFT 相同的情况，这是一种非常常见的方法。*

前往`scripts/2-deploy-drop.js`并添加以下代码：

```jsx
import { AddressZero } from "@ethersproject/constants";
import sdk from "./1-initialize-sdk.js";
import { readFileSync } from "fs";

(async () => {
  try {
    const editionDropAddress = await sdk.deployer.deployEditionDrop({
      // The collection's name, ex. CryptoPunks
      name: "NarutoDAO Membership",
      // A description for the collection.
      description: "A DAO for fans of Naruto.",
      // The image that will be held on our NFT! The fun part :).
      image: readFileSync("scripts/assets/naruto.png"),
      // We need to pass in the address of the person who will be receiving the proceeds from sales of nfts in the contract.
      // We're planning on not charging people for the drop, so we'll pass in the 0x0 address
      // you can set this to your own wallet address if you want to charge for the drop.
      primary_sale_recipient: AddressZero,
    });

    // this initialization returns the address of our contract
    // we use this to initialize the contract on the thirdweb sdk
    const editionDrop = await sdk.getContract(editionDropAddress, "edition-drop");

    // with this, we can get the metadata of our contract
    const metadata = await editionDrop.metadata.get();

    console.log(
      "✅ Successfully deployed editionDrop contract, address:",
      editionDropAddress,
    );
    console.log("✅ editionDrop metadata:", metadata);
  } catch (error) {
    console.log("failed to deploy editionDrop contract", error);
  }
})();
```

一个非常简单的脚本！

我们给我们的集合一个`name`, `description`and `primary_sale_recipient`, and `image`。我们正在从本地文件加载`image`，因此请务必将该图像包含在`scripts/assets`. 现在确保它是 PNG、JPG 或 GIF，并确保它是本地图像 - 如果您使用 Internet 链接，这将不起作用！

当我使用 `node scripts/2-deploy-drop.js`运行它时，我得到了。

```plaintext
buildspace-dao-starter % node scripts/2-deploy-drop.js
👋 SDK initialized by address: 0xF11D6862e655b5F4e8f62E00471261D2f9c7E380
✅ Successfully deployed editionDrop contract, address: 0xE56fb4F83A9a99E40Af1C7eF08643e7bf1259A95
✅ editionDrop metadata: {
  name: 'NarutoDAO Membership',
  description: 'A DAO for fans of Naruto.',
  image: 'https://gateway.ipfscdn.io/ipfs/QmYTQFY2W1K7ucd9GgidKkLL37yJB9sqPGmUqfJiURETxQ/0',
  seller_fee_basis_points: 0,
  fee_recipient: '0x0000000000000000000000000000000000000000',
  merkle: {},
  symbol: ''
}
```

好吧，刚刚发生的事情真是太有意思了。发生了两件事：

**一，我们刚刚向 Goerli部署了[ERC-1155合约。](https://docs.openzeppelin.com/contracts/3.x/erc1155?utm_source=buildspace.so&utm_medium=buildspace_project)**这是正确的！如果您前往`https://goerli.etherscan.io/`并粘贴`editionDrop`合约地址，您会看到您刚刚部署了一个智能合约！最酷的部分是你**拥有**这个合约，它是从**你的**钱包中部署的。“发件人”地址将是**您的**公共地址。

*注意：请保留您的`editionDrop`地址，我们稍后需要它！*，如果您丢失了它，您可以随时从[thirdweb 仪表板中检索找到](https://thirdweb.com/dashboard?utm_source=buildspace&utm_medium=buildspace_project)。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211061004205.png)

很有史诗感。。一个部署好的，只用javascript的定制合同。[您可以在此处查看](https://github.com/thirdweb-dev/contracts/blob/main/contracts/drop/DropERC1155.sol?utm_source=buildspace.so&utm_medium=buildspace_project)thirdweb 使用的实际智能合约代码。

我们在这里做的另一件事是 thirdweb 自动上传并将我们收藏的图像固定到 IPFS。您会看到一个以`https://gateway.ipfscdn.io`打印出来的链接开头。如果您将其粘贴到浏览器中，您将看到您的 NFT 图像正在通过 CloudFlare 从 IPFS 检索！

你甚至可以直接使用 `ipfs://` URI 来访问 IPFS（注意——这不能在 Chrome 上工作，因为你需要运行一个 IPFS 节点，但可以在 Brave 浏览器上为你做到这一点！）

*注意：IPFS 基本上是一个去中心化的存储系统，在[这里](https://docs.ipfs.io/concepts/what-is-ipfs/?utm_source=buildspace.so&utm_medium=buildspace_project)阅读更多信息！*

如果您以前在 Solidity 中开发过自定义智能合约，这有点令人兴奋。我们已经将合约部署到 Goerli + IPFS 上托管的数据。狂野。接下来，我们需要实际创建我们的 NFT！

## 😤 部署 NFT 元数据

### 👾 设置NFT数据

好的，现在我们要实际部署与我们的会员资格NFT相关的元数据。我们还没有做到这一点。到目前为止，我们所做的就是创建ERC-1155合约，并添加一些基本的元数据。我们还没有实际设置我们的会员制NFT，让我们来做这件事!

前往`scripts/3-config-nft.js`并添加进去：

```jsx
import sdk from "./1-initialize-sdk.js";
import { readFileSync } from "fs";

(async () => {
  try {
    const editionDrop = await sdk.getContract("INSERT_EDITION_DROP_ADDRESS", "edition-drop");
    await editionDrop.createBatch([
      {
        name: "Leaf Village Headband",
        description: "This NFT will give you access to NarutoDAO!",
        image: readFileSync("scripts/assets/headband.png"),
      },
    ]);
    console.log("✅ Successfully created a new NFT in the drop!");
  } catch (error) {
    console.error("failed to create the new NFT", error);
  }
})();
```

很简单！

我们要做的第一件事是访问我们的`editionDrop`合约，它是一个ERC-1155。`INSERT_EDITION_DROP_ADDRESS`是上一步打印出来的地址。这就是`Successfully deployed editionDrop contract, address`后面打印出来的地址。 您还可以在您的[thirdweb 仪表板上](https://thirdweb.com/dashboard?utm_source=buildspace&utm_medium=buildspace_project)找到它。您的 thirdweb 仪表板将显示您当前正在处理的合同，它还会显示地址，以便您轻松复制和粘贴。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211061006846.png)

然后，我们在 ERC-1155 上使用`createBatch`设置我们的实际NFT. 我们需要设置一些属性：

- **name**：我们 NFT 的名称。
- **description** : 我们 NFT 的描述
- **image**：我们 NFT 的图像。这是用户声称能够访问您的 DAO 的 NFT 的图像。

*请记住，因为它是 ERC-1155，所以我们所有的成员都会铸造相同的 NFT。*

请务必替换`image: readFileSync("scripts/assets/headband.png")`为您自己的图像。和以前一样，请确保它是本地图像，因为如果您使用互联网链接，这将不起作用。

我正在建造 NarutoDAO，所以，我的成员需要一个 Leaf Village Headband 才能加入 ：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211061006973.png)

发挥你的创意，不要抄袭！

准备好后，运行：

```plaintext
node scripts/3-config-nft.js
```

这是我得到的：

```plaintext
👋 SDK initialized by address: 0xF11D6862e655b5F4e8f62E00471261D2f9c7E380
✅ Successfully created a new NFT in the drop!
```

### 😼 设置索赔条件

现在我们需要实际设置我们的“索赔条件”。可以铸造的最大 NFT 数量是多少？用户什么时候可以开始铸造 NFT？同样，这通常是您需要写入合约的自定义逻辑，但在这种情况下，thirdweb 使它变得容易。

前往`scripts/4-set-claim-condition.js`并添加：

```jsx
import sdk from "./1-initialize-sdk.js";
import { MaxUint256 } from "@ethersproject/constants";

(async () => {
  try {
    const editionDrop = await sdk.getContract("INSERT_EDITION_DROP_ADDRESS", "edition-drop");
    // We define our claim conditions, this is an array of objects because
    // we can have multiple phases starting at different times if we want to
    const claimConditions = [{
      // When people are gonna be able to start claiming the NFTs (now)
      startTime: new Date(),
      // The maximum number of NFTs that can be claimed.
      maxClaimable: 50_000,
      // The price of our NFT (free)
      price: 0,
      // The amount of NFTs people can claim in one transaction.
      maxClaimablePerWallet: 1,
      // We set the wait between transactions to unlimited, which means
      // people are only allowed to claim once.
      waitInSeconds: MaxUint256,
    }]

    await editionDrop.claimConditions.set("0", claimConditions);
    console.log("✅ Sucessfully set claim condition!");
  } catch (error) {
    console.error("Failed to set claim condition", error);
  }
})();
```

这里和以前一样，一定要替换`INSERT_EDITION_DROP_ADDRESS`为你的 ERC-1155 合约的地址。

`startTime`是允许用户开始铸造 NFT 的时间，在这种情况下，我们只需将该日期/时间设置为当前时间，这意味着铸造可以立即开始。

`maxClaimable`是可以铸造的最大成员 NFT 数。

`maxClaimablePerWallet`指定某人可以在单笔交易中索取多少代币，我们将其设置为1个，因为我们只希望用户一次铸造一个 NFT！在某些情况下，您可能希望一次向您的用户铸造多个 NFT（例如，当他们打开一个包含多个 NFT 的战利品箱时），但在这种情况下，我们只需要1个。

`price`设置我们 NFT 的价格，在我们的例子中，免费设置为 0。

`waitInSeconds`是交易之间的时间量，因为我们只希望人们认领一次，我们将其设置为区块链允许的最大数量。

最后，我们这样执行了`editionDrop.claimConditions.set("0", claimConditions)`，这实际上将**与我们在链上部署的合约进行交互**并调整条件，非常酷！为什么我们要传入  `0`？好吧，基本上我们的会员 NFT 有一个`tokenId`为`0`，因为它是我们 ERC-1155 合约中的第一个代币。请记住——使用 ERC-1155，我们可以让多个人铸造同一个 NFT。在这种情况下，每个人都会铸造一个带有 id 为 `0`的 NFT。但是，我们也可以有一个不同的 NFT 以及 id为 `1`，也许我们将 NFT 提供给我们的 DAO 中优秀的成员！这完全取决于我们。

运行后`node scripts/4-set-claim-condition.js`，我得到：

```
👋 SDK initialized by address: 0xF11D6862e655b5F4e8f62E00471261D2f9c7E380
✅ Successfully set claim condition!
```

砰！我们已经成功地与我们部署的智能合约进行了交互，并为我们的 NFT 提供了它必须遵循的某些规则！如果您复制粘贴打印在那里的bundle drop地址并在`https://goerli.etherscan.io/`其中搜索，您将在那里看到我们与合同进行交互的证据！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211061006719.png)



## 😲 让用户铸造你的 NFT

让我们前往`App.jsx`，我们现在要做的是

1. 如果我们检测到我们的用户拥有 NFT 会员资格，请向他们展示我们的“DAO 仪表板”屏幕，他们可以在其中对提案进行投票并查看 DAO 相关信息。
2. 如果我们检测到用户没有我们的 NFT，我们会给他们一个按钮来铸造一个。

我们开始做吧！我们将首先开发case#1，我们需要检测用户是否拥有我们的 NFT。

### 🤔 检查用户是否拥有会员 NFT

前往`App.jsx`，将我们的导入更新为：

```jsx
import { useAddress, ConnectWallet, useContract, useNFTBalance } from '@thirdweb-dev/react';
import { useState, useEffect, useMemo } from 'react';
```

在`console.log("👋 Address:", address);`下面，我们将在下面添加：

```jsx
  // Initialize our Edition Drop contract
  const editionDropAddress = "INSERT_EDITION_DROP_ADDRESS"
  const { contract: editionDrop } = useContract(editionDropAddress, "edition-drop");
  // Hook to check if the user has our NFT
  const { data: nftBalance } = useNFTBalance(editionDrop, address, "0")

  const hasClaimedNFT = useMemo(() => {
    return nftBalance && nftBalance.gt(0)
  }, [nftBalance])

  // ... include all your other code that was already there below.
```

我们首先初始化我们的`editionDrop`合约。

我们用`useNFTBalance`来检查当前连接的账户持有多少 NFT。这实际上将从我们部署的智能合约查询以获取数据。我们为什么要使用`"0"`？好吧，基本上是因为如果你记得`"0"`是我们的会员 NFT的`tokenId`。所以，在这里我们问我们的合约，“嘿，这个用户是否拥有一个 id为`"0"` 的代币？”。

现在，我们知道了一个用户没有NFT的情况 ！让我们创建一个按钮让用户铸造一个。

### ✨ 建立一个“Mint NFT”按钮

我们开始吧！回到`App.jsx`。我在添加的行上添加了一些评论：

```javascript
import { useAddress, ConnectWallet, Web3Button, useContract, useNFTBalance } from '@thirdweb-dev/react';
import { useState, useEffect, useMemo } from 'react';

const App = () => {
  // Use the hooks thirdweb give us.
  const address = useAddress();
  console.log("👋 Address:", address);
  // Initialize our Edition Drop contract
  const { contract: editionDrop } = useContract("INSERT_EDITION_DROP_ADDRESS", "edition-drop");
  // Hook to check if the user has our NFT
  const { data: nftBalance } = useNFTBalance(editionDrop, address, "0")

  const hasClaimedNFT = useMemo(() => {
    return nftBalance && nftBalance.gt(0)
  }, [nftBalance])

  // This is the case where the user hasn't connected their wallet
  // to your web app. Let them call connectWallet.
  if (!address) {
    return (
      <div className="landing">
        <h1>Welcome to NarutoDAO</h1>
        <div className="btn-hero">
          <ConnectWallet />
        </div>
      </div>
    );
  }

  // Render mint nft screen.
  return (
    <div className="mint-nft">
      <h1>Mint your free 🍪DAO Membership NFT</h1>
      <div className="btn-hero">
        <Web3Button 
          contractAddress={editionDropAddress}
          action={contract => {
            contract.erc1155.claim(0, 1)
          }}
          onSuccess={() => {
            console.log(`🌊 Successfully Minted! Check it out on OpenSea: https://testnets.opensea.io/assets/${editionDrop.getAddress()}/0`);
          }}
          onError={error => {
            console.error("Failed to mint NFT", error);
          }}
        >
          Mint your NFT (FREE)
        </Web3Button>
      </div>
    </div>
  );
}

export default App;
```

我们正在使用`Web3Button`创建一个按钮，该按钮将在我们的`editionDrop`合约上调用我们的`claim`函数。我们传入 用户的 `address`， `1`是NFT 的铸造`amount`，以及`0`是NFT的铸币`tokenId`。

这将使我们的智能合约产生一个交易，并为用户铸造 NFT。我们还传入`onSuccess`和`onError`回调来处理成功和错误情况。

当你真正去铸造 NFT 时，Metamask 会弹出，以便你可以支付 gas。完成铸币后，您应该会在控制台中看到`Successfully Minted!`，以及 Testnet OpenSea 的链接。我们实际上可以在[`testnets.opensea.io`](http://testnets.opensea.io/?utm_source=buildspace.so&utm_medium=buildspace_project)测试网上看到铸造的 NFT，这非常酷！当您前往您的链接时，您会看到如下内容：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211061008827.png)

很好！在这里你会看到我的 NFT 有“6 个所有者”。您还会看到它说“您拥有 1”，只要您铸造了一个，您就会看到它！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211061009431.png)

这是因为我实际上有几个朋友为我铸造了这个NFT来测试它。此外，因为它是 ERC-1155，所以**每个人都是同一个 NFT 的所有者**。这很酷，而且更省gas。铸造1个 ERC721 的成本为 96,073 gas。铸造 ERC1155 需要 51,935 个 gas。为什么？因为每个人都在共享相同的 NFT 数据。我们不必为每个用户复制新数据。

### 🛑 仅当用户拥有 NFT 时才显示 DAO Dashboard

好的，所以如果你还记得我们需要处理两种情况：

1. 如果我们检测到我们的用户拥有 NFT 会员资格，请向他们展示我们的“DAO 仪表板”屏幕，他们可以在其中对提案进行投票并查看 DAO 相关信息。
2. 如果我们检测到用户没有我们的 NFT，我们会给他们一个按钮来铸造一个。

这很容易。渲染 mint nft 屏幕之前，我们需要在`App.jsx`添加以下内容。

```jsx
if (!address) {
  return (
    <div className="landing">
      <h1>Welcome to NarutoDAO</h1>
      <div className="btn-hero">
        <ConnectWallet />
      </div>
    </div>
  );
}

// Add this little piece!
if (hasClaimedNFT) {
  return (
    <div className="member-page">
      <h1>🍪DAO Member Page</h1>
      <p>Congratulations on being a member</p>
    </div>
  );
};
```

现在，当您刷新页面时，您会看到您位于 DAO 成员页面中。是的！！！如果您断开您的钱包与您的网络应用程序的连接，您将被带到“连接钱包”页面。

最后，如果你连接你的钱包并且**没有**NFT 会员资格，它会提示你铸造一个。我建议你测试这个案例：

1. **断开**您的钱包与您的网络应用程序的连接
2. 实际[创建一个新帐户](https://metamask.zendesk.com/hc/en-us/articles/360015289452-How-to-create-an-additional-account-in-your-MetaMask-wallet?utm_source=buildspace.so&utm_medium=buildspace_project)

这将为您提供一个新的公共地址，以便您可以有一个新地址来接收 NFT。Metamask 让您拥有任意数量的帐户。

一定要测试所有三种情况！

1. 钱包未连接：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211061009385.png)

2. 钱包已连接，但用户没有会员 NFT：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211061010279.png)

3. 用户拥有会员资格 NFT，因此，向他们展示只有 DAO 成员才能看到的页面：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211061010893.png)

> Github源码：https://github.com/buildspace/buildspace-projects/tree/main/JS_DAO/
>
> 原文链接：https://buildspace.so/p/build-dao-with-javascript

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
