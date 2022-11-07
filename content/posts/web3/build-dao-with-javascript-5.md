---
title: Web3教程：仅仅一个周末，只用Javascript就可以构建出你自己的DAO（5）
description: null
author: 李留白
weight: 0
date: 2022-11-07T12:48:19.888Z
lastmod: 2022-11-07T14:08:24.912Z
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


### 👩‍⚖️ 建库+治理

治理代币很酷，但如果人们不能用它来治理任何东西，它就有点没用了！我们接下来要做的是建立一个治理合约，让人们使用他们的代币对提案进行投票

### 📝 部署治理合约

我不想把这件事搞得太复杂。

归根结底，投票合约实际上是一种让人们对事物进行投票、自动计算这些票数的方式，然后任何成员都可以在链上执行提案。所有这些都不需要任何中心方。

例如，您可能想创建一个提案，例如 *“将 1000 个令牌转移到 EpicDesign5222 以重新设计我们的登录页面”。* 谁可以投票？人们需要投票多长时间？某人创建提案所需的最少代币数量是多少？

所有这些问题都在我们创建的初始投票合约中得到解答

这几乎就像你正在建立一个小国家，你需要建立你的初始政府 + 投票系统！

前往 `8-deploy-vote.js` 并添加以下内容：

```jsx
import sdk from "./1-initialize-sdk.js";

(async () => {
  try {
    const voteContractAddress = await sdk.deployer.deployVote({
      // Give your governance contract a name.
      name: "My amazing DAO",

      // This is the location of our governance token, our ERC-20 contract!
      voting_token_address: "INSERT_TOKEN_ADDRESS",

      // These parameters are specified in number of blocks. 
      // Assuming block time of around 13.14 seconds (for Ethereum)

      // After a proposal is created, when can members start voting?
      // For now, we set this to immediately.
      voting_delay_in_blocks: 0,

      // How long do members have to vote on a proposal when it's created?
      // we will set it to 1 day = 6570 blocks
      voting_period_in_blocks: 6570,

      // The minimum % of the total supply that need to vote for
      // the proposal to be valid after the time for the proposal has ended.
      voting_quorum_fraction: 0,

      // What's the minimum # of tokens a user needs to be allowed to create a proposal?
      // I set it to 0. Meaning no tokens are required for a user to be allowed to
      // create a proposal.
      proposal_token_threshold: 0,
    });

    console.log(
      "✅ Successfully deployed vote contract, address:",
      voteContractAddress,
    );
  } catch (err) {
    console.error("Failed to deploy vote contract", err);
  }
})();
```

我们正在使用 `deployer.deployVote` 来实际建立合同。这将部署一个全新的投票合约！

注意我们如何给 `voting_token_address`。这是我们的合约，它知道要接受哪个治理令牌。我们不希望人们随机尝试使用 $DOGE 投票 。

我们有 `voting_delay_in_blocks`，如果您想在人们被允许对其进行投票之前给他们一些时间来审查提案，这可能会很有用。同样，我们有 `voting_period_in_blocks`， 它只是指定一旦提案生效后某人必须多长时间投票，我们以区块的形式进行投票，这取决于您所在的区块链，可能需要更长的时间，对于以太坊/Goerli，每 13 秒就有一个区块大约，所以平均来说，一天有 6570 个区块。

`voting_quorum_fraction` 真的很有趣。假设一个成员创建了一个提案，而其他 **199** 名 DAO 成员正在迪斯尼世界度假并且不在线。好吧，在这种情况下，如果该 DAO 成员创建提案并对他们自己的提案投了“YES”票——这意味着 100% 的选票说“YES”（因为只有一票）,一旦`vote_period_in_blocks`结束，该提案就会通过！为了避免这种情况，我们使用了一个法定人数，上面写着“为了使提案通过，必须在投票中使用至少 x % 的代币”。

为了举例说明，我们就用 `voting_quorum_fraction: 0` ，这意味着无论投票中使用了多少百分比的代币，提案都会通过。这意味着如果其他成员正在度假，一个人在技术上可以自己通过提案，。就目前而言，这很好。您在现实世界中设置的法定人数取决于您的供应量以及您最初空投的数量。

最后，我们用 `proposal_token_threshold: "0"` 允许任何人实际创建提案，即使他们持有零治理令牌。由你决定你想设置什么！让我们暂时将其保持为零。

继续并运行 `node scripts/8-deploy-vote.js`. 这是我最终得到的：

```plaintext
buildspace-dao-starter % node scripts/8-deploy-vote.js
👋 SDK initialized by address: 0xF11D6862e655b5F4e8f62E00471261D2f9c7E380
✅ Successfully deployed vote contract, address: 0xE079991f3c26b832C3E8171F512694899E831eDB
```

这很酷。基本上，我们创建并部署了一个新的智能合约，让我们对链上的提案进行实际投票。这是一个标准的 [治理](https://docs.openzeppelin.com/contracts/4.x/api/governance?utm_source=buildspace.so&utm_medium=buildspace_project) 合同。您可以在 [此处](https://github.com/thirdweb-dev/contracts/blob/main/contracts/vote/VoteERC20.sol?utm_source=buildspace.so&utm_medium=buildspace_project)查看您部署的确切合同。

如果你前往https://goerli.etherscan.io/，你会在那里看到它!！

所以，现在我们有了三个合约：我们的 NFT 合约、我们的代币合约和我们的投票合约！请务必保存您的投票合约地址，我们稍后会再次使用它。

### 🏦 设置你的金库

所以现在我们有了治理合同，我们可以对东西进行投票。真棒。但是有一个问题。

**投票合约本身无法移动我们的代币。** 例如，假设我们现在想创建一个提案，例如“向 NarutoLover67 发送 1000 美元 HOKAGE 以表彰其成为一名出色的成员”。这实际上是行不通的。 *投票合约现在可以使用零代币。*

为什么？ **因为** **您创建了代币供应。您的钱包拥有对整个供应的访问权限。所以只有你有权访问供应、移动代币、空投等等。** 基本上，这是一个独裁哈哈。这就是我们要做的——我们将把 90% 的代币转移到投票合约中。一旦我们的代币被转移到我们的投票合约中，投票合约本身就可以访问供应。

**这将基本上成为我们的“社区金库”。**

在这里，我只是选择了 90% 作为随机 #。在实践中，这取决于。例如，以下是 ENS 分配方式：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211072125408.png)

他们决定将 50% 的供应分配给他们的社区金库！每个 DAO 的代币经济学是如此不同，目前还没有“标准”的方式来做事。我非常喜欢 ENS 的做法。50%在社区，25%空投，另外25%给核心团队+贡献者。

前往 `9-setup-vote.js` 并添加以下内容：

```jsx
import sdk from "./1-initialize-sdk.js";

(async () => {
  try {
    // This is our governance contract.
    const vote = await sdk.getContract("INSERT_VOTE_ADDRESS", "vote");
    // This is our ERC-20 contract.
    const token = await sdk.getContract("INSERT_TOKEN_ADDRESS", "token");
    // Give our treasury the power to mint additional token if needed.
    await token.roles.grant("minter", vote.getAddress());

    console.log(
      "Successfully gave vote contract permissions to act on token contract"
    );
  } catch (error) {
    console.error(
      "failed to grant vote contract permissions on token contract",
      error
    );
    process.exit(1);
  }

  try {
    // This is our governance contract.
    const vote = await sdk.getContract("INSERT_VOTE_ADDRESS", "vote");
    // This is our ERC-20 contract.
    const token = await sdk.getContract("INSERT_TOKEN_ADDRESS", "token");
    // Grab our wallet's token balance, remember -- we hold basically the entire supply right now!
    const ownedTokenBalance = await token.balanceOf(
      process.env.WALLET_ADDRESS
    );

    // Grab 90% of the supply that we hold.
    const ownedAmount = ownedTokenBalance.displayValue;
    const percent90 = Number(ownedAmount) / 100 * 90;

    // Transfer 90% of the supply to our voting contract.
    await token.transfer(
      vote.getAddress(),
      percent90
    ); 

    console.log("✅ Successfully transferred " + percent90 + " tokens to vote contract");
  } catch (err) {
    console.error("failed to transfer tokens to vote contract", err);
  }
})();
```

这里有一个非常简单的脚本！我们做两件事：

1. 我们使用 `token.balanceOf`获取我们钱包中的总代币数量 。请记住，除了我们空投的代币之外，现在我们的钱包基本上拥有全部供应。
2. 我们拿出我们拥有的总供应量，获得其中的 90%，然后使用 `token.transfer`将这 90% 转移到投票模块。如果您愿意，您可以 100% 转让！但是，也许你想为自己这个创造者保留一些代币！

完成后，我们可以使用 `node scripts/9-setup-vote.js`. 这是我得到的输出：

```plaintext
buildspace-dao-starter % node scripts/9-setup-vote.js
👋 SDK initialized by address: 0xF11D6862e655b5F4e8f62E00471261D2f9c7E380
✅ Successfully gave vote module permissions to act on token module
✅ Successfully transferred 900000 tokens to vote contract
```

好的，准备好看史诗般的结果了吗？前往您的投票合同 `https://goerli.etherscan.io/`。单击“令牌”一词旁边的下拉菜单。在这里，你会看到我的合同上有“844,527 $HOKAGE”。

当我第一次看到它时，你会大吃一惊。 *我们确实有自己的金库。*

*注意：根据您的供应量和空投量，您的金库中可能有不同的金额。*

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211072125596.png)

### ✅ 让用户对提案进行投票

#### 📜 创建你的 DAO 的前两个提案

酷。一切都准备好了，现在，我们只需要创建我们的第一个提案！前往 `10-create-vote-proposals.js` 并添加以下内容：

```jsx
import sdk from "./1-initialize-sdk.js";
import { ethers } from "ethers";

(async () => {
  try {
    // This is our governance contract.
    const vote = await sdk.getContract("INSERT_VOTE_ADDRESS", "vote");
    // This is our ERC-20 contract.
    const token = await sdk.getContract("INSERT_TOKEN_ADDRESS", "token");
    // Create proposal to mint 420,000 new token to the treasury.
    const amount = 420_000;
    const description = "Should the DAO mint an additional " + amount + " tokens into the treasury?";
    const executions = [
      {
        // Our token contract that actually executes the mint.
        toAddress: token.getAddress(),
        // Our nativeToken is ETH. nativeTokenValue is the amount of ETH we want
        // to send in this proposal. In this case, we're sending 0 ETH.
        // We're just minting new tokens to the treasury. So, set to 0.
        nativeTokenValue: 0,
        // We're doing a mint! And, we're minting to the vote, which is
        // acting as our treasury.
        // in this case, we need to use ethers.js to convert the amount
        // to the correct format. This is because the amount it requires is in wei.
        transactionData: token.encoder.encode(
          "mintTo", [
          vote.getAddress(),
          ethers.utils.parseUnits(amount.toString(), 18),
        ]
        ),
      }
    ];

    await vote.propose(description, executions);

    console.log("✅ Successfully created proposal to mint tokens");
  } catch (error) {
    console.error("failed to create first proposal", error);
    process.exit(1);
  }

  try {
    // This is our governance contract.
    const vote = await sdk.getContract("INSERT_VOTE_ADDRESS", "vote");
    // This is our ERC-20 contract.
    const token = await sdk.getContract("INSERT_TOKEN_ADDRESS", "token");
    // Create proposal to transfer ourselves 6,900 tokens for being awesome.
    const amount = 6_900;
    const description = "Should the DAO transfer " + amount + " tokens from the treasury to " +
      process.env.WALLET_ADDRESS + " for being awesome?";
    const executions = [
      {
        // Again, we're sending ourselves 0 ETH. Just sending our own token.
        nativeTokenValue: 0,
        transactionData: token.encoder.encode(
          // We're doing a transfer from the treasury to our wallet.
          "transfer",
          [
            process.env.WALLET_ADDRESS,
            ethers.utils.parseUnits(amount.toString(), 18),
          ]
        ),
        toAddress: token.getAddress(),
      },
    ];

    await vote.propose(description, executions);

    console.log(
      "✅ Successfully created proposal to reward ourselves from the treasury, let's hope people vote for it!"
    );
  } catch (error) {
    console.error("failed to create second proposal", error);
  }
})();
```

看起来很多。来吧，一步一步地阅读它！我们实际上正在创建两个新提案供成员投票：

**1) 我们正在创建一个提案，允许金库铸造 420,000 个新代币。**你可以看到我们在代码中执行了了`"mint"`。

也许金库快用完了，我们想要更多的代币来奖励会员。请记住，早些时候我们赋予我们的投票合约铸造新代币的能力——所以这很有效！这是一个民主的国库。如果您的成员认为这个提议很愚蠢并投反对票，这根本不会通过！

**2) 我们正在创建一个提案，将 6,900 代币从国库转移到我们的钱包。**你可以看到我们在代码中执行力`"transfer"`。

也许我们做了一些好事并希望得到回报！在现实世界中，您通常会创建提案以向其他人发送代币。例如，也许有人帮助为 DAO 编写了一个新网站并希望获得奖励。我们可以转移他们的代币！

顺便说一句，我想针对`nativeTokenValue`做个说明. 假设我们想让我们的提案说，“我们想 用 2500 个治理代币和 0.1 ETH 奖励 NarutoFangirl27帮助我们进行营销”。这真的很酷！这意味着你可以用 ETH 和治理代币奖励人们——两全其美。*注意：如果我们想发送 0.1 ETH，那么0.1ETH就需要在我们的金库中！*

当我运行`node scripts/10-create-vote-proposals.js` 时， 我得到：

```plaintext
buildspace-dao-starter % node scripts/10-create-vote-proposals.js
👋 SDK initialized by address: 0xF11D6862e655b5F4e8f62E00471261D2f9c7E380
✅ Successfully created proposal to mint tokens
✅ Successfully created proposal to reward ourselves from the treasury, let's hope people vote for it!
```

酷。这就是我们的提案。我们要做的最后一件事就是让用户现在就从我们的 DAO 仪表板对提案进行投票！

注意：如果您设置了`proposal_token_threshold`> 0 ----- 代码可能会引发错误。你可能不得不将你的代币委托给 投票合约（在相关的网络上），以便在部署提案之前发挥作用。

### ✍️ 让用户从仪表板对提案进行投票

最后，让我们把这一切带回家。现在，我们的提案基于我们的智能合约。但是，我们希望我们的用户能够轻松地看到他们并投票！让我们这样做。前往 `App.jsx`。

继续并在 `token`下添加：

```jsx
const { contract: vote } = useContract("INSERT_VOTE_ADDRESS", "vote");
```

我们的网络应用程序需要访问我们的`vote` 权限， 以便用户可以与该合约进行交互。

从这里，让我们在`shortenAddress`函数下方的某处添加以下内容：

```jsx
const [proposals, setProposals] = useState([]);
const [isVoting, setIsVoting] = useState(false);
const [hasVoted, setHasVoted] = useState(false);

// Retrieve all our existing proposals from the contract.
useEffect(() => {
  if (!hasClaimedNFT) {
    return;
  }

  // A simple call to vote.getAll() to grab the proposals.
  const getAllProposals = async () => {
    try {
      const proposals = await vote.getAll();
      setProposals(proposals);
      console.log("🌈 Proposals:", proposals);
    } catch (error) {
      console.log("failed to get proposals", error);
    }
  };
  getAllProposals();
}, [hasClaimedNFT, vote]);

// We also need to check if the user already voted.
useEffect(() => {
  if (!hasClaimedNFT) {
    return;
  }

  // If we haven't finished retrieving the proposals from the useEffect above
  // then we can't check if the user voted yet!
  if (!proposals.length) {
    return;
  }

  const checkIfUserHasVoted = async () => {
    try {
      const hasVoted = await vote.hasVoted(proposals[0].proposalId, address);
      setHasVoted(hasVoted);
      if (hasVoted) {
        console.log("🥵 User has already voted");
      } else {
        console.log("🙂 User has not voted yet");
      }
    } catch (error) {
      console.error("Failed to check if wallet has voted", error);
    }
  };
  checkIfUserHasVoted();

}, [hasClaimedNFT, proposals, address, vote]);
```

所以，我们在这里做两件事！

首先第一个`useEffect`，我们正在 `vote.getAll()` 获取存在于我们的治理合同中的所有提案，然后 `setProposals` ，这样我们可以稍后再渲染它们。

在第二个 useEffect 中，我们正在`vote.hasVoted(proposals[0].proposalId, address)`检查该地址是否对第一个提案进行了投票。如果有，那么我们`setHasVoted`这样做是为了让用户不能再次投票！即使我们没有这个，如果用户尝试双重投票，我们的合约也会拒绝交易！

thirdweb 的魔力在于，它不仅让智能合约的部署变得非常容易，而且还让我们的客户端通过简单的功能与它们进行交互变得非常容易，比如`vote.getAll()`！

继续刷新您的页面，您应该会在 🌈 旁边看到您的提案打印出来，您可以浏览所有数据！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211072128574.png)

如果您已经投票，您会看到如下内容：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211072128468.png)

下一段代码有点大，它涉及到实际渲染我们刚刚检索到的提案，以便用户可以有三个选项来投票：

1. 支持
2. 反对
3. 弃权

如果您熟悉 React/JS，您可以轻松地浏览它并自己弄清楚它是如何工作的。如果你不太了解 React/JS，请不要担心。只需复制粘贴即可。这并不丢人!

在现有导入之后添加AddressZero导入：

```jsx
import { AddressZero } from "@ethersproject/constants";
```

继续用[这里](https://github.com/buildspace/buildspace-dao-final/blob/main/src/App.jsx#L184?utm_source=buildspace.so&utm_medium=buildspace_project)的代码替换 `if (hasClaimedNFT) { }`的内容。

当您检查您的网络应用程序时，您会看到如下内容：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202211072128392.png)

很好。您现在可以实际使用这些按钮进行投票。

我们将治理合约设置为在 24 小时后停止投票。这意味着 24 小时后，如果：

```plaintext
votes "for" proposal > votes "against" proposal
```

那么任何成员都可以通过我们的治理合约执行该提案。提案不能自动执行。但是，一旦提案通过， DAO 的**任何成员**都可以触发被接受的提案。

例如，假设我们正在处理额外铸造 420,000 个代币的提案。如果`votes "for" proposal > votes "against" proposal`  ——那么任何人都可以触发提案，然后我们的合约将铸造代币。有点疯狂，对吧？除了区块链，我们必须信任任何人。

想象一下，在一个腐败的国家，为某事投票，然后你的政府对你撒谎并说“嘿，实际上我们没有得到足够的选票 jk”，而你真的这样做了，哈哈。或者，想象他们说，“好吧，我们得到了足够的选票，我们会做到我们承诺的”，但永远不会这样做！

在这种情况下，一切都被编码，代码不会说谎。

无论如何，现在不是讨论 DAO 如何潜在地改善我们的政府的时候；）。我们必须在此时此地完成我们的 meme DAO！马上了。

> Github源码：https://github.com/buildspace/buildspace-projects/tree/main/JS_DAO/
>
> 原文链接：https://buildspace.so/p/build-dao-with-javascript

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
