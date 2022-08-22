---
title: Web3之路——使用Chainlink将API连接到你的智能合约上
description: null
author: 李留白
weight: 0
date: 2022-08-22T12:59:18.699Z
lastmod: 2022-08-22T14:01:24.471Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814204307.png
---

> 源码仓库：https://github.com/zeuslawyer/chainlink-dynamic-nft-alchemy

了解如何使用Chainlink Oracles建立一个基于市场数据变化的动态NFT，将数据从外部来源拉到智能合约中。

## 什么是动态NFT？

动态NFT是一种不可伪造的代币，可以根据某些情况而改变。

例如，目前有八个不同的LaMelo Ball NFT，每个NFT都记录了一组不同的LaMelo的球员统计数据，从篮板和助攻到得分，并根据这些数据改变（10次助攻？ 不同的颜色 - 1分的得分，不同的球）。

动态NFT持有者可以根据拉梅洛的持续表现，获得特殊的抽奖机会和其他NFT特有的福利。

它变得更酷了。

这八个NFT中的一个，黄金进化NFT，有一个独特的承诺。

如果拉梅洛-鲍尔赢得2021年NBA赛季的年度最佳新秀，NFT本身将进化以反映新的形象。拉梅洛赢得了这个奖项，NFT也进化了。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/lamelo.gif)


让我们开始学习教程。

## 关于这个动态NFT教程

在本教程中，您将使用 [Chainlink](https://chain.link/) 的去中心化和加密保护的预言机网络构建动态 NFT，以获取和跟踪资产价格数据。

然后，你将使用[Chainlink Keepers](https://docs.chain.link/docs/chainlink-keepers/introduction/)网络的自动化功能，使你的NFT智能合约根据你所追踪的资产价格数据来更新NFT。

如果市场价格上涨，智能合约将随机挑选NFT的URI指向这三个看涨的图像之一，NFT将被动态更新。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220822210543.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220822210550.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220822210557.png)

如果价格源的数据向下移动，NFT将动态更新到这些看跌的图像之一，这些图像也是随机选择的!

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220822210614.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220822210620.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220822210625.png)

(来源：Chainlink设计团队)

最后，我们将使用[Chainlink的可验证随机函数](https://docs.chain.link/docs/chainlink-vrf/)为我们的NFT智能合约添加加密保证的随机性，从选项列表中随机选择一个NFT图像。

## 所需工具和先决条件

本教程假定您有一些编码经验，并且您已经学习前述内容。

### 1. 集成开发环境

在本教程中，我们将使用[Remix IDE](https://remix.ethereum.org/)和内置的 "London VM "区块链网络，但同样可以使用Hardhat或任何其他Solidity智能合约开发框架和你最喜欢的代码编辑器来完成。

### 2. Github Repo

这里有一个[Github repo:https://github.com/zeuslawyer/chainlink-dynamic-nft-alchemy](https://github.com/zeuslawyer/chainlink-dynamic-nft-alchemy)，用于我们为你制作的动态NFT教程。

该Repo反映了我们将遵循的结构。

**主分支**

`main`分支包含使用OpenZeppelin向导的基线ERC721代币。

**price-feeds 分支**

`price-feeds`分支添加了Chainlink Keepers的实现，并连接到Chainlink资产价格数据，我们将用它来追踪特定资产的价格。

**randomness分支**

`randomness`分支包含添加随机性的逻辑，以便我们的动态NFT从我们的智能合约中的NFT元数据URI中随机选择。

这一点是为你做的一项特殊任务来培养你的技能！

### 3. IPFS Companion

安装[IPFS Companion](https://chrome.google.com/webstore/detail/ipfs-companion/nibjojkomfdiaoajekhjakgkdhaomnch?hl=en)浏览器扩展（用于任何基于Chromium的浏览器）。

这将持有你的代币的URI和元数据信息。

### 4. 水龙头和Testnet代币

确保你的MetaMask钱包已经[连接到Rinkeby](https://www.alchemy.com/overviews/rinkeby-testnet#testnet-3)。

一旦你的钱包连接到Rinkeby，[从Alchemy的Rinkeby龙头获得Rinkeby ETH](https://rinkebyfaucet.com/)。

你还需要获得[testnet LINK代币](https://faucets.chain.link/)。

对于你的任务，你将增加随机性，但你将部署到以太坊的Goerli testnet。

如果你需要Goerli testnet代币，请从[Alchemy的Goerli faucet获得Goerli ETH](https://goerlifaucet.com/)。

## 1. 设置ERC721代币

我们将从OpenZeppelin和Remix开始，创建一个NFT智能合约。

由于这已经涉及到了，你有两个选择来获得你的NFT智能合约。

### i. 从头开始创建合约

按照Welcome to the Road to Web3，记得用 "Bull&Bear "命名NFT。

然后给它一个 "BBTK "的符号名称，并仔细阅读下面这段话，以更新mintToken()的逻辑!

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220822211331.png)

### ii. 从参考Github repo中复制智能合约代码

这可能是最快的方法：[从参考 repo 的主分支复制智能合约代码](https://github.com/zeuslawyer/chainlink-dynamic-nft-alchemy/blob/main/contracts/Bull%26Bear.sol)，并在 Remix 中保存为 Bull&Bear.sol。

请注意，我们已经添加了对IPFS URI数组的引用，还更新了safeMint()方法，以设置一个初始代币URI作为起点。

## 2. 更新IPFS URIs中的链接

现在你需要确保更新bullUrisIpfs和bearUrisIpfs的IPFS URIs中的链接，以指向IPFS浏览器节点上托管的文件。

要在你的浏览器IPFS节点上设置NFT数据。

- 从 repo 中的[这个文件夹](https://github.com/zeuslawyer/chainlink-dynamic-nft-alchemy/tree/main/ipfs)复制 Token 元数据 JSON 和图像文件
- 点击IPFS Companion的浏览器扩展图标，打开你的本地IPFS节点
- 将所有这些文件导入到你的节点的FILES部分。
- 要获得URI链接，请点击三个点并复制 "共享链接 "中的字符串。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220822211417.png)

> 在你的IPFS节点中，你需要JSON和PNG，但只有JSON文件用于你的智能合约，因为JSON文件是指向PNG的。

## 3. 完成编译检查

- 在Remix中根据智能合约的pragma选择正确的编译器（0.8.0以上）。
- 确保你正在编译正确的文件--Bull&Bear.sol
- 将你的合约部署到浏览器的JavaScript VM London环境中
- 复制你的Remix提供的钱包地址，并将其粘贴到safeMint字段中，以铸造一个代币。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220822211454.png)

- 向下滚动并点击参数为 "0 "的tokenURI。

因为你的第一个令牌的令牌ID为0，它将返回指向'gamer bull'JSON文件的令牌URI。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220822211513.png)

很好--你的NFT智能合约开始工作了!

## 4. 使你的合约保持者兼容

现在，我们可以使我们的NFT合约不仅是动态的，而且是自动动态的

这段代码在 repo的[price-feeds分支](https://github.com/zeuslawyer/chainlink-dynamic-nft-alchemy/tree/price-feeds)中被引用。

首先，我们用Chainlink Keepers添加自动化层，这意味着我们需要扩展我们的NFT智能合约，使其 "兼容Keepers"。

### **以下是关键步骤。**

1. 导入"@chainlink/contracts/src/v0.8/KeeperCompatible.sol"
2. 让你的合约继承自KeeperCompatibleInterface
3. 调整你的构造函数，加入一个间隔期，该间隔期被设置为合约的状态变量，这将设置自动化发生的间隔。
4. 在NFT智能合约中实现checkUpkeep和executeUpkeep函数，这样我们就可以满足接口的要求。
5. 将 "upkeep "合约[注册到Chainlink Keeper网络](https://docs.chain.link/docs/chainlink-keepers/register-upkeep/)。

Chainlink Keepers网络将在每次有新区块被添加到区块链时检查我们的checkUpkeep()函数，并模拟我们的函数在链外的执行!

**该函数返回一个布尔值。**

- 如果它是假的，这意味着还没有自动维护到期。
- 如果它返回true，这意味着我们设定的时间间隔已经过了，一个维护行动就到期了。

Keepers Network会自动调用我们的 performUpkeep()函数，并在链上运行逻辑。

不需要开发人员的操作。

这就像魔术一样。

我们的checkUpkeep将是直接的，因为我们只想检查时间间隔是否已经过期，并返回布尔值，但我们的performUpkeep需要检查一个价格信息。

为此，我们需要让我们的智能合约与Chainlinks的价格反馈oracles互动。

我们将使用[Rinkeby上的BTC/USD feed代理合约](https://rinkeby.etherscan.io/address/0xECe365B379E1dD183B20fc5f022230C044d51404)，但你可以从Rinkeby网络中[选择另一个](https://docs.chain.link/docs/ethereum-addresses/)。

## 5. 与Chainlink价格信息互动

为了与选择的价格信息神谕互动，我们需要使用AggregatorV3Interface。

> 请务必了解数据信息的工作方式以及如何使用它们。

在我们[price-feeds分支的参考代码](https://github.com/zeuslawyer/chainlink-dynamic-nft-alchemy/blob/price-feeds/contracts/Bull&Bear.sol)中，构造函数接受聚合器oracle的地址作为构造函数的参数。在部署时接受一个参数是非常有用的，因为它使我们在本地开发时可以配置它。

为了与Rinkeby上的实时oracle进行交互，我们的合约需要被部署到Rinkeby。这对集成测试来说是必要的，但在开发时，它使我们的速度有点慢。

我们怎样才能加快本地编辑-编译-调试的开发循环？

### 模拟Live Net智能合约

我们可以（在迭代我们的智能合约时）使用[模拟](https://en.wikipedia.org/wiki/Mock_object)，而不是不断地重新部署到Rinkeby这样的测试网络，支付测试ETH等。

例如，我们可以使用[这个模拟的价格信息合约](https://github.com/zeuslawyer/chainlink-dynamic-nft-alchemy/blob/price-feeds/contracts/MockPriceFeed.sol)来模拟价格信息聚合器合约。

这样做的好处是，我们可以在我们的Remix中部署模拟，在浏览器中的伦敦虚拟机环境中，调整它返回的值，以测试不同的场景，而不必不断地部署新的合约到实时网络，然后通过MetaMask批准交易，每次支付测试ETH。

**下面是要做的事情。**

- 将该文件复制到你的Remix
- 将其保存为MockPriceFeed
- 部署它

这是简单地导[入Chainlink为price-feed聚合器代理编写的模拟文件](https://github.com/smartcontractkit/chainlink/blob/develop/contracts/src/v0.6/tests/MockV3Aggregator.sol)。

> 注意：你必须把编译器改为0.6.x来编译这个模拟。

当部署一个模拟时，你需要传入小数点，价格反馈将计算价格。

你可以在点击 "显示更多细节 "后，从[价格信息合约地址的列表](https://docs.chain.link/docs/ethereum-addresses/)中找到这些。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220822211606.png)

BTC/USD的信息需要输入8位小数。

你还需要传入饲料的初始值。

因为我随机选择了BTC/USD的资产价格，所以我给它传了一个我在测试时得到的旧值。3034715771688

> 当你在本地部署的时候，一定要注意Remix给你的合约地址。
>
> 这是你传递给NFT智能合约构造器的东西，这样它就知道要使用模拟物作为价格源

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220822211626.png)


你也应该玩玩你本地部署的模拟价格源。

调用 latestRoundData 来查看模拟价格源的最新价格，以及符合[ Chainlink Price Feed API ](https://docs.chain.link/docs/price-feeds-api-reference/#latestrounddata)的其他数据。

你可以通过调用updateAnswer并传入一个较高或较低的值来更新价格（模拟价格的上涨和下跌）。

你可以通过传递2534715771688使价格下降，或者通过传递4534715771688使价格上升。

这对你的NFT智能合约的浏览器内测试非常方便!

回到NFT智能合约，一定要更新它以反映[参考代码](https://github.com/zeuslawyer/chainlink-dynamic-nft-alchemy/blob/price-feeds/contracts/Bull%26Bear.sol)。

**所以我建议你这么做。**

1. 首先阅读这个[关于如何使我们的NFT智能合约Keepers兼容](https://docs.chain.link/docs/chainlink-keepers/compatible-contracts/)的简短文档
2. 阅读[使用数据源的简单方法](https://docs.chain.link/docs/get-the-latest-price/)
3. 部署[模拟数据源](https://github.com/zeuslawyer/chainlink-dynamic-nft-alchemy/blob/price-feeds/contracts/MockPriceFeed.sol)
4. 阅读其[源代码](https://github.com/smartcontractkit/chainlink/blob/develop/contracts/src/v0.6/tests/MockV3Aggregator.sol)，了解Chainlink Price Feed智能合约的编写方式

一旦你阅读了这些资源，就自己试一试吧。

如果你想直接跳到我们的实现，它在price-feeds分支上。

请注意，我们将价格信息设置为公共状态变量，因此我们可以使用setPriceFeed()辅助方法来改变它，而且我们还将关键的动态NFT逻辑添加到performUpkeep()中。

每次Chainlink Keepers网络调用时，它将在链上执行该逻辑，如果Chainlink Price Feed报告的价格与我们上次跟踪的价格不同，URI就会被更新。

> 这个演示并没有对智能合约中更新所有Token URI的气体成本进行优化。我们的重点是如何使NFT成为动态的。
>
> 更新所有正在流通的NFT的成本在以太坊网络上可能非常高，所以要仔细考虑，并探索第2层解决方案或其他架构来优化气体费用。

## 总结工作流程

当你完成这一切后，你的测试工作流程会是这样的。

**1) 在remix部署模拟价格信息**

你可以使用构造器参数8,3034715771688开始，并复制其地址。

> 记得为此将Remix的编译器设置为0.6.x范围。

**2) 重新部署Bull&Bear智能代币合约**

> 记住要更新编译器的版本。

对于构造器的参数，你可以传入10秒的时间间隔，并将Mock Price Feed的地址作为第二个参数。

**3) 铸造一到两个代币**

铸造一个或两个令牌，并在传递0、1或任何你拥有的铸造令牌ID后，点击tokenURI检查它们的tokenURI。

令牌URI应该都默认为gamer_bull.json。

**4. 检查NFT合约的构造器**

检查NFT合约的构造器是否调用了getLatestPrice()，并反过来更新currentPrice状态变量。

通过点击currentPrice按钮来完成 - 结果应该与你在Mock Price Feed中设置的价格一致。

**5) 传入一个空数组**

点击checkUpkeep，传入一个空数组（[]）作为参数。它应该返回一个布尔值为真，因为我们传入了10秒作为间隔时间，而且从你部署Bull&Bear开始，10秒就已经过去了。

[Reference repo包括一个setter函数](https://github.com/zeuslawyer/chainlink-dynamic-nft-alchemy/blob/price-feeds/contracts/Bull%26Bear.sol#L156)，这样你就可以更新间隔字段，以方便使用。

> 请记住，当你部署到Rinkeby时，你希望将间隔时间设置得更长--每个feed在配置的间隔时间或价格偏离设定的阈值时更新其聚合的价格。
>
> 如果你把你的Keepers检查配置得太频繁，这将是对你的测试LINK令牌的一种浪费。
>
> 这就是为什么对于模拟，我们传递给它一个非常短的时间间隔10秒，因为我们不消耗测试LINK，也因为我们可以快速运行performUpkeep()。

**6) 确保Mock Price Feed被更新**

确保你的Mock Price Feed被更新，以返回一个与你目前存储在NFT智能合约的currentPrice字段中的价格不同的价格。

例如，如果你用一个较低的数字更新模拟合约，你会期望你的NFT智能合约会切换NFT，以显示一个 "熊 "标记URI。

**7) 模拟你的合约被调用**

在传递给它一个空数组后，点击执行Upkeep。这就是你模拟你的合约被Rinkeby上的Chainlink Keepers网络调用的方式。

不要忘了，你可以部署到Rinkeby，[注册你的upkeep](https://docs.chain.link/docs/chainlink-keepers/register-upkeep/)，并连接到Rinkeby的Price feeds作为你任务的一部分。

由于现在我们是在Remix的浏览器网络上，我们需要通过自己调用performUpkeep来模拟自动化流程。

**8) 检查最新的价格并更新所有标记URI**

performUpkeep应检查最新价格并更新所有代币URI。

> 这在Remix浏览器中是即时的。在Rinkeby上，这可能需要一些时间。

在本地进行时，你不需要在MetaMask中签署任何交易，但当你连接到Rinkeby时，你会让MetaMask要求你签署每一步的交易。

**9) 刷新当前价格并检查tokenURI**

如果你点击currentPrice，你应该看到基于更新的Mock Price Feed的价格。

然后再次点击tokenURI，你应该看到你的token URI已经改变。

如果价格下降到以前的水平以下，它将被切换到熊市。

如果上一个代币URI是熊市，而价格上涨，它应该切换到牛市的代币URI。

## 本周作业

这项作业使用了一个新的工具：Chainlink可验证随机函数。

这个工具提供了密码学上可证明的随机性，并被广泛用于游戏和其他应用中，在这些应用中，可证明和防篡改的随机性对公平结果至关重要。

现在，我们已经硬编码了哪个令牌URI显示出来--数组中的第一个URI（索引0）。我们需要把它变成一个随机的索引号，这样一个随机的NFT图像就会显示为令牌URI。

### 以下是步骤。

**1）回顾一个Chainlink  VRF的例子**

看一下Chainlink VRF的[超级简单的使用例子](https://docs.chain.link/docs/get-a-random-number/)--你只需要实现两个函数就可以在NFT智能合约里面获得加密可证明的随机性。

**2）更新你的NFT智能合约，使用两个VRF函数**

更新你的NFT智能合约以使用requestRandomWords和fulfulRandomWords

**3) 在randomness分支中使用VRF模拟模型**

使用reference repo的randomness分支中提供的[VRF模拟](https://github.com/zeuslawyer/chainlink-dynamic-nft-alchemy/blob/randomness/contracts/MockVRFCoordinator.sol)，并确保你仔细阅读VRF模拟中的注释说明，以便你清楚地知道如何使用它。

### 在Rinkeby上部署你的动态NFT

最后，一旦你玩了NFT智能合约，并让它在Remix中动态改变tokenURI几次，就把Metamask和Remix连接到Rinkeby并部署NFT。

> 当你把NFT部署到Rinkeby时，你仍然可以使用mocks，但你也需要以正确的顺序部署它们。

按照正确的顺序完成以下工作。

**1) 将你的Metamask连接到Rinkeby**

**2) 从[Chainlink Faucet](https://faucets.chain.link/)获取测试LINK和测试ETH 。**

如果你打算部署模拟价格源聚合器，并在以后将其更新为Chainlink Rinkeby价格源，那么现在就部署模拟。同样，如果你打算在Rinkeby上使用模拟的VRF协调员进行测试，你必须在Rinkeby上部署它。

**3) 将NFT智能合约部署到Rinkeby上**

确保你传入正确的构造函数参数。

如果你使用模拟，确保它们首先被部署，这样你就可以把它们的Rinkeby地址传递给NFT合约的构造器。

如果您使用的是Chainlink实时价格源，那么它的地址必须是参考回购或您[从这里选择](https://docs.chain.link/docs/ethereum-addresses/)的任何Rinkeby价格源地址。

由于您可以将您的Remix "环境 "连接到Rinkeby上部署的NFT合约，并从Remix调用NFT合约的performUpkeep，您可以在第一次测试运行时保持较短的间隔时间。

> 记住要通过调用setInterval来增加间隔时间，否则Keepers网络运行你的exformUpkeep的频率将远远超过Price Feed显示新数据的频率。

你也可以通过调用setPriceFeed并传入你希望它指向的地址来改变你的价格反馈地址。

> 如果 performUpkeep发现价格没有变化，代币URI将不会更新！。

**4）铸造你的第一个代币，并通过Remix检查其URI**

它应该是gamer_bull.json。如果你愿意，可以在OpenSea上查看

**5) 玩转模拟值**

如果你正在使用两个模拟值，玩玩这些值，看看通过调用tokenURI对NFT的改变。

**6) 切换到Rinkeby上的实时Chainlink合约**

当你准备切换到Rinkeby上的实时Chainlink合约时，通过调用它们的setter函数来更新NFT合约中price-feed的地址和vrfCoordinator。

**7) 注册你的NFT智能合约**

接下来，在[Chainlink Keepers Registry](https://docs.chain.link/docs/chainlink-keepers/register-upkeep/)中注册你的NFT智能合约，该合约被部署到Rinkeby作为一个新的 "upkeep"。

**8）[创建并资助一个VRF订阅](https://docs.chain.link/docs/get-a-random-number/#create-and-fund-a-subscription)**。

如果你正在使用实时的Rinkeby Chainlink VRF，确保你调用setVrfCoordinator()，这样你就不再在Rinkeby上使用你的VRF Mock。

如果你没有实现它，这是你学习的一部分，你可以查看参考资料repo。

**8) 一两个小时后检查OpenSea**

取决于价格变化的频率（如果你想立即进行，那么继续使用Rinkeby上的Mock）。

> OpenSea缓存了元数据，即使你可以调用tokenURI并看到更新的元数据，但它可能会有一段时间不显示。
>
> 你可以尝试用force_update参数在OpenSea上强制更新，但它可能不会及时更新图像。NFT的名称至少应该被更新。

### 总结

祝贺你! 你已经编码了一个动态NFT，它反映了真实世界的价格反馈数据，并且用加密证明的、防篡改的随机性来决定动态NFT图像

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
