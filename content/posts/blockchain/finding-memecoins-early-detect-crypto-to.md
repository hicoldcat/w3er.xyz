---
title: 如何尽早发现 Memecoin 并确保它们不是骗局
description: null
author: 李留白
weight: 0
date: 2024-05-12T17:08:37.164Z
lastmod: 2024-05-20T04:26:06.723Z
tags: []
categories:
    - Web3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240513010923.png
---

>免责声明：文章来自外网，观点来自原文作者，文章仅翻译供参考学习，请自行辨别真伪，风险自担。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240513010923.png)

## 帮助您在 Web3 中尽可能保持安全的完整指南

**尽早发现模因币可以带来一代人的财富，但如何确保您不会将宝贵的资金投入诈骗？这是许多 Web3 用户必须应对的挑战。通过本指南，我们希望帮助您利用市场上可用的各种工具更好地评估代币。**

如果您很早就开始使用 BODEN、BONK 或 WIF 等模因币，您可能会在交易中获得可观的利润。然而，事情并不总是那么容易。因为对于每一个成功的模因币来说，可能都有几十个失败的模因币。 

但是你如何找到这些给你带来如此多快乐的宝石呢？让我们从头开始吧。 

## 第 1 步：在启动时查找代币

尽早使用模因币是一个巨大的风险，但这仍然是本指南的重点。您可能会选择现有的具有更可靠声誉的模因币，例如 PEPE 或 DOGE。但是，如果您想尽可能早地进入其生命周期，您需要 DappRadar 的[Hot Contracts](https://dappradar.com/rankings/hot-contracts)。确保您是 PRO 会员，然后按“部署”对您选择的链上的合约进行排序。这将为您提供链上最新的合约。 

1 – 以 PRO 会员身份登录，然后进入[热门合约](https://dappradar.com/rankings/hot-contracts)

2 – 在左上角，您可以使用下拉菜单选择您想要探索的区块链。我们支持以太坊、Polygon、BNB Chain、Blast、Base、zkSync Era 等等。

![DappRadar 通过区块链过滤趋势智能合约，支持以太坊、Polygon、BNB Chain、Base、Blast 和 zkSync Era。](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240513011044.png)

3 – 您还可以单击“过滤器”，然后按智能合约类型和部署合约的时间进行过滤。

![按类型及其部署时间过滤智能合约。](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240513011201.png)

您可以在这些过滤器之间切换，以查找新部署的具有特定功能的智能合约。例如，我们可以选择“代币”和“24 小时”时间范围。 

在下面的截图中，您可以看到以太坊上有一个新的未经验证的代币，它已经吸引了 32 个 UAW。这就是我们今天的研究中将使用的合同。 

![以太坊上部署的新智能合约，按部署时间排序。](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240513011219.png)

### 索拉纳呢？

DappRadar 允许您发现跨各种链的新智能合约。然而，Solana 上却没有。 

要在 Solana 上查找新部署的代币，您将需要使用其他第三方工具。例如，[Fluxbeam](https://fluxbeam.xyz/)有自己的 Telegram 机器人，可以在新的流动性池启动时通知您。此外，您还可以使用[Birdseye](https://birdeye.so/find-gems?chain=solana)来发现 Solana 或各种其他链上的低市值代币。 

假设您通过 DappRadar 的热门合约找到了一份合约。下一步是什么？ 

## 第 2 步：使用 DEXscreener 检查是否存在诈骗

现在是时候开始检查是否存在可能的拉扯或其他诈骗行为。这将需要您利用各种深入研究智能合约的工具。这些工具可以帮助您发现可能的骗局和狡猾的项目，但它们并非完美无缺。请注意，并非所有骗局都能被这些类型的免费工具捕获。 

我们从[DEXscreener](https://dexscreener.com/)开始。 

要检测诈骗，您将需要您找到的模因币的智能合约。复制您通过热门合约找到的合约，然后转到 DEXscreener。 DEXscreener 可以帮助您发现各种安全问题。例如，在下面的屏幕截图中，您可以看到该代币是“蜜罐”，这意味着您可以购买该代币，但不能出售它。此外，似乎还有一个隐藏的所有者，同时还有可修改的税收和暂停转让的能力。这些都应该是大危险信号。

![发现代币合约的安全问题。](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240513011249.png)

![DEXscreener 发现代币合约的安全问题，包括蜜罐和隐藏所有者。](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240513011323.png)

然而，并非 DEXscreener 上的每个“问题”都意味着您不能信任某个项目。以 PEPE 模因币为例。据 DEXscreener 称，该代币也有 1 期。 PEPE 有一份钱包黑名单，显然不允许这些钱包交易该代币。对于普通用户来说，这很可能不是问题，但评估其中一些问题的严重程度很重要。 

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20240513011353.png)

通过 DEXscreener 检查令牌后，我们还可以看看另一个工具。 

## 第 3 步：使用 TokenSniffer 仔细检查是否存在诈骗

让我们再次复制通过 DappRadar 的热门合约找到的智能合约。现在我们将转到[TokenSniffer](https://tokensniffer.com/)，并将合约地址粘贴到搜索栏中。 

该服务将根据几次审核生成一份报告。例如，这包括交换的能力、合约的建立方式、代币持有者和提供的流动性。我们之前使用的示例获得[0/100 分数](https://tokensniffer.com/token/1/0xab120506599644839a0da3c0aaaed2dd4e0a0ae7)。这意味着我们需要远离它。获得此分数的原因包括极高的销售费用，以及合约所有者修改合约行为的能力，例如禁用销售、更改费用或铸造更多代币。  

我们是否查看过像 PEPE 这样信誉良好的模因币，然后我们发现它在 TokenSniffer 上的[得分为 70/100](https://tokensniffer.com/token/eth/ekmghvno1xlv6ms1s14c5sbdac0n122ja5osql9n1328os4capb02n066h00) 。这再次表明，即使是更成熟的项目也没有获得满分。因此，阅读 TokenSniffer 的分析并做出自己的判断非常重要。另请记住，即使是高分的模因币也可能是骗局。

## 第四步：社交媒体研究

如果您对 TokenSniffer 和 DEXscreener 的检查没有引发任何严重的危险信号，您可能想更深入地了解社区。因此，是时候对社交媒体进行一些研究了。 Web3 中的大多数项目在 X 上都有一个帐户，但拥有这样的帐户并不能保证任何事情。然而，我们仍然会环顾四周，看看是否能找到任何东西。您可以查找以下内容： 

- 在X上搜索合约地址，查看项目、机器人或用户是否已经共享。他们说什么？ 
- 您能否找到该项目的官方 X 帐户，例如通过在 X 的搜索栏中输入代码 ($MONKEY) 来找到。 
- 如果您在 X 上找到项目帐户，请务必检查谁关注了。还是那些机器人，还是真人？您自己的网络中是否有人关注您，您是否足够信任该人？ 

例如，在这种情况下，我在 X 上找不到任何有关智能合约的信息。代码 $MONKEY 主要指的是 Solana 上的新 memecoin。现在，这是另一个危险信号，因为我们的合约是在以太坊上的。因此，诈骗者很可能想要复制 Solana memecoin 并造成混乱。 

## 第五步：谁投资了？ 

在上一步中我已经提到，最好看看您在 X 上的社交圈。他们是否关注您感兴趣的项目？当你把钱投入到项目中时，也是如此。谁在投资这些模因币，您信任这些人吗？ 

您可以使用标准的区块浏览器来查找持有最多代币的钱包。这可行，但有点复杂。通过[气泡图](https://bubblemaps.io/)之类的东西，您还可以获得可视化效果，这使您可以看到代币是否分布良好或非常集中。 

南森有一项付费服务，可以让您看到“[聪明的钱](https://app.nansen.ai/smart-money)”。这将使您能够跟踪受人尊敬的成功交易者的投资。然而，您也有可能成为这些类型钱包的退出流动性，所以要小心。 

同样，[Debank](https://debank.com/)允许您追踪鲸鱼及其活动。您可以关注一组钱包并密切关注他们的投资。 Debank 可以根据您关注的钱包的活动为您留下通知。您可以使用[DappRadar 的投资组合工具](https://dappradar.com/hub/wallet/)执行相同的操作，但目前您无法收到任何通知。您需要手动关注这些钱包的内容。  

## 建议：不要把钱扔掉

根据 DEXscreener 和 TokenSniffer 的说法，即使您找到了 memecoin，也绝对不是骗局。也许世界上最好的加密货币投资者拥有一袋代币，但这仍然不能保证成功。因此，一个人的投资永远不应该超过他们愿意损失的金额。机会越大，涉及的风险就越高。请注意这一点。

## 结束语

永远不要因为某个 memecoin 或代币在 X、TikTok 或 Instagram 上推广而就投入其中。我们建议大家进行更多研究，希望本指南能够帮助您。此外，本指南现在允许您创造自己的发现并尽早找到模因币，而不是遵循别人的提示。如果您有兴趣，可以[在 Discord 上加入 DappRadar](https://dappradar.com/discord)，并与我们的团队和更广泛的社区讨论。继续探索！

> 原文：https://dappradar.com/blog/finding-memecoins-early-detect-crypto-token-scams