---
title: 什么是可升级的智能合约？完整指南帮你解决合约一旦部署无法更改的问题
description: null
author: 李留白
weight: 0
date: 2022-08-28T15:11:35.555Z
lastmod: 2022-08-28T15:11:40.723Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220828222956.png
---

> 项目源码：https://github.com/YosephKS/moralis-upgradeable-smart-contracts

精通智能合约的开发者努力将合约提升到新的水平。这其中包括修改或升级智能合约的方法。然而，"可升级的智能合约 "的概念，即使对有经验的人来说，也可能是具有挑战性的理解。因此，请记住，可升级智能合约绝对不属于 "初学者 "的范畴。然而，即使你是一个初学者，这篇文章也会非常有用。那么，什么是可升级的智能合约？跟随本指南，我们将探索可升级智能合约的基本原理，并看看它们是如何工作的。

接下来，你将首先了解什么是可升级的智能合约以及它们的功能。然后，我们会看一下transparent 代理与upgradeable 代理。此外，我们将解释如何使合约可升级，并解决这些类型的合约的主要限制。尽管如此，你将有机会承担一个实例项目。反过来，你将有机会跟随我们的带领，创建[自己的NFT铸币DApp](https://moralis.io/how-to-mint-nfts-full-tutorial-to-minting-an-nft/?utm_source=blog&utm_medium=post&utm_campaign=What%2520are%2520Upgradable%2520Smart%2520Contracts%253F%2520Full%2520Guide)（去中心化的应用程序）。后者将包含一个智能合约，你将对其进行升级。因此，你将正确地学习如何与可升级的智能合约一起工作。在这一过程中，你会了解到最终的[Web3](https://moralis.io/the-ultimate-guide-to-web3-what-is-web3/?utm_source=blog&utm_medium=post&utm_campaign=What%2520are%2520Upgradable%2520Smart%2520Contracts%253F%2520Full%2520Guide)开发平台Moralis（又称加密货币的Firebase）。这个优秀的工具使你能够轻松覆盖所有与区块链相关的后端需求。因此，今天一定要[创建你的Moralis账户](https://admin.moralis.io/register?utm_source=blog&utm_medium=post&utm_campaign=What%2520are%2520Upgradable%2520Smart%2520Contracts%253F%2520Full%2520Guide)。

如前所述，可升级的智能合约可能很难首次理解。所以，建议先学习一些基础知识。因此，在前进之前，我们建议了解 "Web3是如何工作的？"，"什么是Solidity？"和 "什么是智能合约？"这些问题。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220828224143.png)

## 什么是可升级的智能合约？

你可能知道，智能合约是所有可编程区块链（如以太坊上）的一个重要组成部分。通过确保事情按照预定的规则运行，智能合约强制执行秩序。没有智能合约，就没有加密代币、NFT，也没有DApps。但是，什么是可升级的智能合约？嗯，首先，你需要注意，在这种情况下，"可升级 "这个词并不意味着可变异。EVM的基本规则之一是，**一旦合同被部署，它就不能被改变**。相反，可升级的智能合约使用特殊的代理模式。后者涉及部署代理合同和执行合同（逻辑合同）。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220828224254.png)

## 可升级智能合约如何工作？

看上面的示意图，你可以看到用户通过代理合同与逻辑合同进行互动。由于代理合同能够存储逻辑合同的地址，这才成为可能。然后，我们通过部署一个新的逻辑合约来整合升级。当然，我们也需要在代理合同中更新逻辑合同的相关值，以实际实现升级。

有许多代理模式可用，它们可以用来创建可升级的合约。此外，虽然也有很多类型的代理模式，但大多数代理模式都使用transparent 透明代理和UUPS（通用可升级代理标准）。幸运的是，这两种类型在OpenZeppelin也有提供，这让开发者的工作变得简单了许多。此外，这也是一个捷径，在我们进行一个例子项目时，你会了解更多。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220828224416.png)

### 透明代理与UUPS代理

如上所述，透明和 UUPS 代理模式是最常见的两种类型。虽然这两种类型都遵循相同的基本原则（如上所述），但它们的设计却截然不同。因此，我们需要对它们进行快速比较。以下是两种代理模式类型各自的一些主要特征：

- 透明代理模式类型：
  - 升级由代理合同处理
  - 部署成本更高
  - 易于维护
- UUPS 代理模式类型：
  - 升级由实施合同处理。这也意味着您需要将升级功能实现到实现合同中。否则，您将无法升级您的智能合约
  - 部署更便宜
  - 它使维护更具挑战性

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220828225017.png)

## 如何使智能合约可升级？

至此，您知道什么是可升级合约，它们是如何工作的，以及两种主要的代理模式类型是什么。因此，您已准备好学习如何使合同可升级。而且，如上所述，我们不需要从头开始。相反，我们可以使用 OpenZeppelin 实现。因此，我们不需要自己构建代理模式。考虑到这一点，以下是我们必须完成的步骤以使合同可升级：

1. 首先，我们需要继承一个可初始化的合约。这是通过一行简单的代码完成的：

```solidity
contract ExampleContractName is initializable {}
```

默认情况下，上述代码行用于透明代理模式。但是，在使用 UUPS 代理模式时，我们需要添加“UUPSUpgradable”。因此，这是在这些情况下使用的代码行：

```solidity
contract ExampleContractName is initializable, UUPSUpgradable {}
```

2. 接下来，我们需要将构造函数更改为初始化程序。这意味着我们从`“constructor () {}”`到`“function initialize() public initializer {}”`。另外，请注意函数的名称不必是“initialize”。但是，“initializer {}”部分是必不可少的。

3. 然后，我们需要将所有 OpenZeppelin 合约库更改为其可升级版本。反过来，我们确保我们的可升级智能合约使用升级的库。这里有些例子：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220828225257.png)

4. 展望未来，我们需要在我们的初始化函数中调用可升级合约的“_init”函数。这是一个例子：

```solidity
function initialize() initializer public {
	__ERC1155_init(“”);
	__Ownable_init();
	__UUPSUpgradeable_init();
}
```

5. 最后，我们需要将“msg.sender”更改为“_msgSender()”。这将使我们能够使用用户的钱包地址而不是代理地址。

### 可升级智能合约的主要限制

在开始我们的示例项目之前，我们还要解决可升级智能合约的主要限制。要考虑的最重要方面是存储冲突：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220828225400.png)

查看上图，您可以看到在新的实现合约版本中，顶部（也不在旧变量中间）应该没有新变量。但是，如果将新变量放在底部，则可以避免存储冲突。

## 可升级的智能合约在行动——一个示例项目

如前所述，您现在将有机会使用上述关于可升级智能合约的知识。按照我们的说明，您将轻松创建 NFT 铸造 dApp。当然，除了使用 OpenZeppelin，我们还会严重依赖 Moralis。为什么？好吧，它让事情变得更简单、更快捷。当我们将 Moralis 的强大功能与最实用的以太坊 dApp 样板相结合时，尤其如此。这个终极的 Web3 样板使我们能够在几分钟内拥有功能齐全的 dApp 并运行干净的 UI。当然，这两个工具都需要初始设置。因此，让我们从那开始。因此，我们将向您展示如何创建 Moralis 服务器、初始化 Moralis SDK，并介绍以太坊 dApp 样板的初始设置。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220828225519.png)

### Moralis 设置

无论您是想从头开始构建 dApp 还是使用我们的 Web3 样板，您都需要从简单的 Moralis 设置开始。要完成初始 Moralis 设置，请执行以下步骤：

1. 创建您的免费 Moralis 帐户 – 单击链接https://moralis.io/，您将进入注册页面。在那里，输入您的电子邮件，创建您的密码，然后通过单击确认链接确认帐户创建（检查您的电子邮件收件箱）。另一方面，如果您已经有一个活跃的 Moralis 帐户，只需登录即可。

2. 创建 Moralis 服务器 - 在您的 Moralis 管理区域中，转到“服务器”选项卡。接下来，单击“+ 创建新服务器”（右上角）。然后，您将看到一个提供不同服务器类型的下拉菜单（请参见下面的屏幕截图）。确保选择最适合您需要的网络类型。在处理示例项目或测试您的 dApp 时，请关注“Testnet Server”（以太坊测试网）或“Local Devchain Server”选项。但是，一旦您想让您的 dApp 对公共用户可用，请选择“主网服务器”。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220828225638.png)

继续前进，您将看到一个弹出窗口，要求您输入服务器的名称（可以是您想要的任何名称），然后选择您的区域、网络类型和链。要最终启动您的服务器，请单击“添加实例”按钮：

3. 访问服务器详细信息 - 创建服务器后，您可以通过单击“查看详细信息”按钮访问其详细信息（服务器 URL 和应用程序 ID）：

您现在应该在新窗口中看到服务器的详细信息。此外，您可以简单地复制服务器的 URL 和应用程序 ID。为了更加方便，每个细节的右侧都有复制图标：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220828225708.png)

#### 初始化Moralis

注意：暂时跳过此步骤。您将被指示何时返回并完成它。

4. 初始化Moralis - 一旦你准备好你的编码文件，你就可以通过填充相关文件来初始化Moralis和它的SDK。当使用模板时，你可以使用".env "文件来实现这一目的。因此，只需将你的服务器的详细信息粘贴到指定位置。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220828225834.png)

### 以太坊样板设置

为了这个示例项目，我们将使用最终的 Web3 样板。这将显着简化和加快我们的工作。事实上，我们建议您在不需要创建独特 UI 时始终使用此样板。此外，这个样板文件在 GitHub 上是公开的（只需使用 Google 并在搜索栏中输入“以太坊样板文件”）。但是，我们决定让您访问我们完成的代码（[“moralis-upgradeable-smart-contracts”](https://github.com/YosephKS/moralis-upgradeable-smart-contracts)），让事情变得更简单。不过，为了让您进行一些实际的编码，我们建议您从我们的[起始代码](https://github.com/YosephKS/moralis-upgradeable-smart-contracts/tree/starter)开始，它本质上只是一个稍微调整的以太坊样板。通过克隆起始代码或完成版本，您将轻松地按照此处的代码演练进行操作。

#### 以太坊样板设置 - 分步说明

以下是使用我们修改后的样板文件需要完成的步骤：

1. 首先单击上面的“starter code”链接以访问 GitHub 上的代码。
2. 然后，单击“代码”按钮，然后单击“复制”图标复制地址：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220828225926.png)

3. 接下来，使用代码编辑器的终端克隆上面复制的代码地址。我们通常使用 Visual Studio Code (VSC)；但是，请随意选择您的喜好。这是您需要使用的命令行：

```
git clone [paste the copied HTTPS address here] 
```

4. 现在，是时候安装所有依赖项了。因此，我们使用 VSC 的终端。这些是我们需要输入的命令行：

```
cd moralis-upgradeable-smart-contracts
yarn  
```

注意：除了使用“yarn”，您还可以使用“npm”包管理器。

5. 然后，将“.env.example”文件重命名为“.env”。这也意味着您已准备好完成上一部分的最后一步（第四步）。因此，使用您的服务器的详细信息填充您的“.env”文件。
6. 完成上述步骤后，您就可以运行您的应用程序了。因此，只需将“”粘贴或键入 VSC 的终端：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220828230144.png)

完成上述设置后，您还需要进行简单的设置。为此，请务必从 20:25 开始观看下面的视频。

### 使用可升级的智能合约——Web3 应用预览和代码演练

完成上述设置后，您就可以卷起袖子创建自己的 NFT 铸币 dApp 版本了。为此，请按照以下视频教程中提供的详细说明进行操作（从 23:32 开始）。在那里，Moralis 专家将主要关注智能合约，并演示如何确保它们实际上是可升级的。此外，您还可以自己升级合同。此外，他还将向您展示如何使用 OpenZeppelin 和 Etherscan。

此外，如果您知道自己实际创建的是什么，那么创建 dApp 会简单得多，以下是我们的示例 dApp 预览：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220828230246.png)

如您所见，我们的 dApp 看起来与样板 dApp 非常相似。这也是“身份验证”按钮和“NFT”部分不需要额外工作的原因。因此，下面的视频教程主要关注“NFT Minter”部分。

这是我们在整篇文章中一直引用的视频教程：

[https://youtu.be/af1i0z0jhkg](https://youtu.be/af1i0z0jhkg)

## 什么是可升级的智能合约？完整指南总结

到目前为止，您知道什么是可升级的智能合约以及如何创建它们。您还应该了解两种主要的代理模式类型及其主要区别。此外，可升级合约的关键限制也应该不再是陌生人。此外，我们希望您已经接受了上述示例项目。因此，您应该已经学会了如何正确设置 Moralis 以及如何使用以太坊样板加速您的 dApp 开发。有了这些强大的工具，你未来的区块链开发会简单很多。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)