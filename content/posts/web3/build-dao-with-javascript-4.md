---
title: Web3教程：仅仅一个周末，只用Javascript就可以构建出你自己的DAO（4）
description: null
author: 李留白
weight: 0
date: 2022-11-06T14:52:40.676Z
lastmod: 2022-11-06T15:01:17.386Z
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



## 💎 创建代币+治理

### 😈 部署 ERC-20 合约

我们的成员现在有一个 NFT 来证明自己作为我们 DAO 的成员。很好。让我们更进一步。让我们实际创建一个“治理代币”空投给我们的会员。

[你可能还记得这里](https://decrypt.co/85894/ethereum-name-service-market-cap-hits-1-billion-just-days-after-ens-airdrop?utm_source=buildspace.so&utm_medium=buildspace_project)的 ENS DAO 治理代币空投。这是什么意思呢？[为什么“治理代币”现在](https://coinmarketcap.com/currencies/ethereum-name-service/?utm_source=buildspace.so&utm_medium=buildspace_project)市值近十亿美元？

基本上，治理代币允许用户对提案进行投票。例如，提案可能会说“我希望 Naruto DAO 向`0xf79a3bb8d5b93686c4068e2a97eaec5fe4843e7d`钱包地址发送 100,000 美元 HOKAGE ，以成为尊贵成员”。然后，成员将对其进行投票。

拥有更多治理令牌的用户更强大。通常，代币会奖励给带来最大价值的社区成员。

例如，对于 ENS 空投，获得最多代币的人是核心开发团队和 Discord 中的活跃用户。但是，根据您持有 ENS 域（例如`farza.eth`）的时间长短，您也会收到 ENS DAO 令牌。顺便说一句，如果您不知道的话，一个ENS 名称就是一个 NFT。

因此，您拥有此 NFT 的时间越长，您获得的代币就越多。

为什么？因为 ENS 团队希望网络的早期支持者得到奖励。这是他们的公式：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211062223474.png)

我想说明的是，这是一个自定义的公式！你的DAO也可以有一个自定义的公式。你的DAO也可以有一个自定义的公式。也许你还想根据人们拥有会员资格NFT的时间长短，对你的DAO中的人给予更多奖励。这一切都取决于你。

### 🥵 部署你的代币

让我们创建和部署我们的代币智能合约！前往`scripts/5-deploy-token.js`并添加：

```jsx
import { AddressZero } from "@ethersproject/constants";
import sdk from "./1-initialize-sdk.js";

(async () => {
  try {
    // Deploy a standard ERC-20 contract.
    const tokenAddress = await sdk.deployer.deployToken({
      // What's your token's name? Ex. "Ethereum"
      name: "NarutoDAO Governance Token",
      // What's your token's symbol? Ex. "ETH"
      symbol: "HOKAGE",
      // This will be in case we want to sell our token,
      // because we don't, we set it to AddressZero again.
      primary_sale_recipient: AddressZero,
    });
    console.log(
      "✅ Successfully deployed token contract, address:",
      tokenAddress,
    );
  } catch (error) {
    console.error("failed to deploy token contract", error);
  }
})();
```

我们调用`sdk.deployer.deployToken`，为你部署一个标准的[ERC-20 代](https://docs.openzeppelin.com/contracts/2.x/api/token/erc20?utm_source=buildspace.so&utm_medium=buildspace_project)币合约，这是以太坊上所有大量代币所采用的标准。你需要给它的只是你的令牌的`name`和`symbol`！玩得开心，当然不要模仿我。我希望你正在构建一些你自己认为很酷的东西！

在这里，我给我的令牌符号 HOKAGE。如果你不知道那是什么——看看[这个](https://naruto.fandom.com/wiki/Hokage?utm_source=buildspace.so&utm_medium=buildspace_project)，哈哈。TLDR：如果你是hokage，你就是有史以来最好的忍者之一。

[顺便说一句——你可以在这里](https://github.com/thirdweb-dev/contracts/blob/main/contracts/token/TokenERC20.sol?utm_source=buildspace.so&utm_medium=buildspace_project)看到 thirdweb 使用的确切合同。

这是我运行它时得到的结果：

```plaintext
buildspace-dao-starter % node scripts/5-deploy-token.js
👋 SDK initialized by address: 0xF11D6862e655b5F4e8f62E00471261D2f9c7E380
✅ Successfully deployed token contract, address: 0xF93B8AE0a84325D1d7Aa09593DCA3Ad5Fe868eA7
```

轰！它部署了一个新的代币合约。如果您前往[`https://goerli.etherscan.io/`](https://goerli.etherscan.io/?utm_source=buildspace.so&utm_medium=buildspace_project)并搜索令牌模块的地址，您将看到刚刚部署的合约。同样，您会看到它从**您的钱包**中部署，因此**您拥有它**。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211062223578.png)

您甚至可以将您的令牌作为自定义令牌添加到 Metamask。

只需单击“导入令牌”：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211062224038.png)

然后，粘贴您的 ERC-20 合约地址，您将看到 Metamask 神奇地自动抓取您的代币符号：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211062224000.png)

然后，回到你的钱包，向下滚动，然后可以了！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211062224252.png)

您正式拥有自己的令牌:)。

### 💸 创建你的代币供应

目前，**可供人们索取的代币为零。**我们的 ERC-20 合约并不知道有多少代币可用。我们必须告诉它！

前往`6-print-money.js`并添加：

```jsx
import sdk from "./1-initialize-sdk.js";

(async () => {
  try {
    // This is the address of our ERC-20 contract printed out in the step before.
    const token = await sdk.getContract("INSERT_TOKEN_ADDRESS", "token");
    // What's the max supply you want to set? 1,000,000 is a nice number!
    const amount = 1_000_000;
    // Interact with your deployed ERC-20 contract and mint the tokens!
    await token.mint(amount);
    const totalSupply = await token.totalSupply();

    // Print out how many of our token's are out there now!
    console.log("✅ There now is", totalSupply.displayValue, "$HOKAGE in circulation");
  } catch (error) {
    console.error("Failed to print money", error);
  }
})();
```

请记住，此处插入的地址是您的**代币合约地址**。如果您输入错误的地址，您可能会收到类似这样的错误：“UNPREDICTABLE_GAS_LIMIT”。

同样，如果你丢失了这个地址，你可以参考你很酷的[thirdweb仪表板！](https://thirdweb.com/dashboard?utm_source=buildspace&utm_medium=buildspace_project)您现在应该看到代币合约已经部署！![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211062225236.png)

因此，这里我们实际上是在铸造代币供应量，并将我们想要铸造的代币`amount`设置为最大供应量。

这是我运行脚本时得到的结果：

```plaintext
buildspace-dao-starter % node scripts/6-print-money.js
👋 Your app address is: 0xa002D595189bF9D50D5897C64b6e07BE5bdEe9b8
✅ There now is 1000000.0 $HOKAGE in circulation
```

现在是史诗般的部分。回到 Etherscan 中的 ERC-20 合约。您现在将看到您拥有自己的令牌跟踪器！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211062225207.png)

继续并单击跟踪器，您将看到所有供应信息以及以下内容：谁持有您的代币，谁在转移代币，以及转移了多少代币。您还会在这里看到我们有一个“最大总供应量”。

很酷。我们用几行 Javascript 完成了这一切。这很疯狂。如果你想大声笑，你可以在这一点上制作下一个纪念币。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211062225108.png)

### ✈️空投它

现在是空投时间。现在你可能是你的 DAO 的唯一成员，没关系！

打开`7-airdrop-token.js`并添加以下代码：

```jsx
import sdk from "./1-initialize-sdk.js";

(async () => {
  try {
    // This is the address to our ERC-1155 membership NFT contract.
    const editionDrop = await sdk.getContract("INSERT_EDITION_DROP_ADDRESS", "edition-drop");
    // This is the address to our ERC-20 token contract.
    const token = await sdk.getContract("INSERT_TOKEN_ADDRESS", "token");
    // Grab all the addresses of people who own our membership NFT, which has 
    // a tokenId of 0.
    const walletAddresses = await editionDrop.history.getAllClaimerAddresses(0);

    if (walletAddresses.length === 0) {
      console.log(
        "No NFTs have been claimed yet, maybe get some friends to claim your free NFTs!",
      );
      process.exit(0);
    }

    // Loop through the array of addresses.
    const airdropTargets = walletAddresses.map((address) => {
      // Pick a random # between 1000 and 10000.
      const randomAmount = Math.floor(Math.random() * (10000 - 1000 + 1) + 1000);
      console.log("✅ Going to airdrop", randomAmount, "tokens to", address);

      // Set up the target.
      const airdropTarget = {
        toAddress: address,
        amount: randomAmount,
      };

      return airdropTarget;
    });

    // Call transferBatch on all our airdrop targets.
    console.log("🌈 Starting airdrop...");
    await token.transferBatch(airdropTargets);
    console.log("✅ Successfully airdropped tokens to all the holders of the NFT!");
  } catch (err) {
    console.error("Failed to airdrop tokens", err);
  }
})();
```

这是一个很大的问题。但你现在已经是第三网络的专家了，Ezpz。

首先，您会看到我们需要`editionDrop`和`token`合同，因为我们将与这两个合同进行交互。

我们需要首先从 `editionDrop` 中获取NFT 持有者，然后使用`token`上的函数将他们的代币铸造出来。

我们`getAllClaimerAddresses`用来获取所有拥有我们 NFT w/tokenId `"0"` 会员资格的人的`walletAddresses`。

从那里，我们遍历所有`walletAddresses`并选择一个`randomAmount`令牌空投给每个用户，并将这些数据存储在`airdropTarget`字典中。

最后，我们在我所有的`airdropTargets`上运行`transferBatch`。就是这样！`transferBatch`将自动循环遍历所有目标，并发送令牌！

当我运行脚本时，这是我得到的：

```plaintext
buildspace-dao-starter % node scripts/7-airdrop-token.js
👋 SDK initialized by address: 0xF11D6862e655b5F4e8f62E00471261D2f9c7E380
✅ Going to airdrop 8761 tokens to 0xF11D6862e655b5F4e8f62E00471261D2f9c7E380
✅ Going to airdrop 9606 tokens to 0x14fb3a9B317612ddc6d6Cc3c907CD9F2Aa091eE7
✅ Going to airdrop 7376 tokens to 0xF79A3bb8d5b93686c4068E2A97eAeC5fE4843E7D
✅ Going to airdrop 9418 tokens to 0xc33817A8e3DD0687FB830666c2658eBBf4696245
✅ Going to airdrop 8311 tokens to 0xe50b229DC4D053b95fA586EBd1874423D9Be5145
✅ Going to airdrop 9603 tokens to 0x7FA42Aa5BF1CA8084f51F3Bab884c3aCB5180C86
🌈 Starting airdrop...
✅ Successfully airdropped tokens to all the holders of the NFT!
```

yooo。你刚刚做了一个空投，太棒了！在我的例子中，你可以看到我的 DAO 中有 6 个独特的成员，他们都收到了空投。就您而言，现在可能只有您一个人！随着更多成员的加入，请随时再次运行此脚本。

**在现实世界**中，空投通常只发生一次。但是，我们现在只是在闲逛，所以没关系。另外，这个世界没有真正的规则，哈哈。如果您想每天进行 4 次空投，那就去吧！

您可以像 ENS 一样创建自己的小空投公式，例如：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211062226844.png)

你会想——“收到代币的人将对 DAO 拥有更多的权力。这个好吗？最大的代币持有者会为 DAO 做正确的事吗？”。这涉及到非常广泛的代币经济学主题，您可以在[此处](https://www.google.com/search?q=tokenomics&utm_source=buildspace.so&utm_medium=buildspace_project)阅读。

好的，现在如果我回到我在 Etherscan 上的 ERC-20 合约，我可以看到我所有的新代币持有者以及他们拥有多少`$HOKAGE`。

让我们开始吧。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211062226766.png)

## 😇 在 DAO Dashboard 上展示代币持有者

### 🥺 在网络应用程序上检索代币持有者

如果我们的 DAO 的所有成员能够轻松地看到 DAO 中持有代币的所有人以及他们持有的代币数量，那就太好了。为此，我们需要从客户端实际调用我们的智能合约并检索该数据。

前往`App.jsx`，然后在`editionDrop`下方，添加您的`token`.

```jsx
// Initialize our token contract
const { contract: token } = useContract('INSERT_TOKEN_ADDRESS', 'token');
```

我们需要这个，以便我们可以与 ERC-1155 合约和 ERC-20 合约进行交互。从 ERC-1155，我们将获得所有成员的地址。从 ERC-20 中，我们将检索每个成员拥有的代币数。

接下来，在`const hasClaimedNFT...`下面添加以下代码：

```jsx
// Holds the amount of token each member has in state.
const [memberTokenAmounts, setMemberTokenAmounts] = useState([]);
// The array holding all of our members addresses.
const [memberAddresses, setMemberAddresses] = useState([]);

// A fancy function to shorten someones wallet address, no need to show the whole thing.
const shortenAddress = (str) => {
  return str.substring(0, 6) + '...' + str.substring(str.length - 4);
};

// This useEffect grabs all the addresses of our members holding our NFT.
useEffect(() => {
  if (!hasClaimedNFT) {
    return;
  }

  // Just like we did in the 7-airdrop-token.js file! Grab the users who hold our NFT
  // with tokenId 0.
  const getAllAddresses = async () => {
    try {
      const memberAddresses = await editionDrop?.history.getAllClaimerAddresses(
        0,
      );
      setMemberAddresses(memberAddresses);
      console.log('🚀 Members addresses', memberAddresses);
    } catch (error) {
      console.error('failed to get member list', error);
    }
  };
  getAllAddresses();
}, [hasClaimedNFT, editionDrop?.history]);

// This useEffect grabs the # of token each member holds.
useEffect(() => {
  if (!hasClaimedNFT) {
    return;
  }

  const getAllBalances = async () => {
    try {
      const amounts = await token?.history.getAllHolderBalances();
      setMemberTokenAmounts(amounts);
      console.log('👜 Amounts', amounts);
    } catch (error) {
      console.error('failed to get member balances', error);
    }
  };
  getAllBalances();
}, [hasClaimedNFT, token?.history]);

// Now, we combine the memberAddresses and memberTokenAmounts into a single array
const memberList = useMemo(() => {
  return memberAddresses.map((address) => {
    // We're checking if we are finding the address in the memberTokenAmounts array.
    // If we are, we'll return the amount of token the user has.
    // Otherwise, return 0.
    const member = memberTokenAmounts?.find(({ holder }) => holder === address);

    return {
      address,
      tokenAmount: member?.balance.displayValue || '0',
    };
  });
}, [memberAddresses, memberTokenAmounts]);
```

乍一看好像很多！但只要知道我们正在做三件事：

1. 我们调用`getAllClaimerAddresses`从我们的 ERC-1155 合约中获取持有 NFT 的所有成员的地址。
2. 我们调用`getAllHolderBalances`获取在 ERC-20 合约中持有我们代币的每个人的代币余额。
3. 我们将数据组合成`memberList`数组，该数组结合了成员的地址和他们的代币余额。随便看看[这里](https://reactjs.org/docs/hooks-reference.html#usememo?utm_source=buildspace.so&utm_medium=buildspace_project)`useMemo`有什么。这是 React 中存储计算变量的一种奇特方式。

现在，您可能会问自己，“我们不能直接使用`getAllHolderBalances获取所有持有我们代币的人吗？”。好吧，基本上，有人可以在我们的 DAO 中并持有零代币！*而且，这也没关系。*所以仍然希望他们出现在名单上。

在我的控制台中，我得到了类似这样的信息，我现在成功地从我的两个合约——ERC-20 和我的 ERC-1155 中检索数据。是的！！随意在这里改动并检查所有数据。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211062228690.png)

### 🤯 在 DAO Dashboard 上呈现成员数据

现在我们已经在 React 应用程序的状态中很好地保存了所有数据，让我们渲染它。

**替换** `if (hasClaimedNFT) { }`为以下内容：

```jsx
// If the user has already claimed their NFT we want to display the internal DAO page to them
// only DAO members will see this. Render all the members + token amounts.
if (hasClaimedNFT) {
  return (
    <div className="member-page">
      <h1>🍪DAO Member Page</h1>
      <p>Congratulations on being a member</p>
      <div>
        <div>
          <h2>Member List</h2>
          <table className="card">
            <thead>
              <tr>
                <th>Address</th>
                <th>Token Amount</th>
              </tr>
            </thead>
            <tbody>
              {memberList.map((member) => {
                return (
                  <tr key={member.address}>
                    <td>{shortenAddress(member.address)}</td>
                    <td>{member.tokenAmount}</td>
                  </tr>
                );
              })}
            </tbody>
          </table>
        </div>
      </div>
    </div>
  );
}
```

很简单！我们只是在渲染一个漂亮的小表格，它将展示我们的`memberList`. 一旦你检查了你的页面，你会看到类似下面的截图！*注意：没有居中，这是故意的。稍后我们将添加其他内容！*

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211062228331.png)

现在，我们有一个地方可以让我们的所有成员在一个内部的、由令牌控制的DAO仪表板上看到其他成员。真棒 :)。


> Github源码：https://github.com/buildspace/buildspace-projects/tree/main/JS_DAO/
>
> 原文链接：https://buildspace.so/p/build-dao-with-javascript

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
