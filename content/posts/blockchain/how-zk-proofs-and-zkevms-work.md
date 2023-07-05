---
title: ZK 证明和 zkEVM 的工作原理（无需数学）
description: null
author: 李留白
weight: 0
date: 2023-07-05T06:48:55.514Z
lastmod: 2023-07-05T06:53:30.155Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705144903.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705144903.png)

零知识 (ZK) 证明正在为 web3 的最新创新提供动力。它们已经被用于[Polygon 的 zkEVM](https://polygon.technology/polygon-zkevm)等产品中，为以太坊带来可验证的可扩展性，而[Polygon ID](https://polygon.technology/polygon-id)则用于在不泄露任何个人信息的情况下验证您的身份。

一句话：**ZK 证明可以让你在不泄露事物本身的情况下证明某件事**。这有几个实际应用，例如在不提供完整许可证/护照信息的情况下验证您的年龄。

但这*实际上*是如何运作的呢？幕后发生了什么让这一切成为可能？在这篇文章中，我将介绍您需要了解的有关 ZK 证明的所有内容，但忽略数学。

我们开始做吧！

## ZK 证明如何工作？

为了在不泄露声明本身的情况下证明声明的有效性，涉及两方：

1. 证明**者**：试图证明某事的人。
2. **验证者**：试图验证声明是否“真实”的人。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705144956.png)

有时，涉及第三方（即**颁发者**），该第三方向证明者授予证书（例如许可证），验证者将其作为信息来源信任。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705145001.png)

让我们看一个例子...我想向一个网站证明我已年满 18 岁，但*不*上传我的护照照片。在这种情况下，有：

- 证明**者：**我，试图证明我达到法定年龄的人。
- 验证**者：**网站所有者，试图验证我是否达到法定年龄。
- **发行人**：向我提供护照的政府；网站所有者信任的证明我年龄的文件。

通常，我需要将整个护照的屏幕截图上传到网站（我*真的*不想这样做），只是为了证明我已年满 18 岁。

[斯蒂芬](https://twitter.com/0ceans404)制作的一个有趣的例子是将其应用于海绵宝宝。海绵宝宝想要证明他的名字确实是海绵宝宝，但为了做到这一点，他需要向验证者（警察……或者我猜，警鱼）提供他的完整许可证。

他的驾照包含敏感信息，如出生日期、地址、性别等；所有这些都不是证明他的名字所必需的；但可惜的是，他别无选择。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705145009.png)

如果海绵宝宝能证明他的名字，或者我能以某种方式证明我已经超过 18 岁，*而不*需要交出那么多敏感信息，那就更好了。

这是 ZK 证明如何为世界提供真正价值的一个典型例子。有了 ZK 证明，我现在能够向验证者证明我的部分身份*（或其他任何内容） ，而无需提供我身份的任何方面或任何支持文件来证明这一事实。*

这样，我就不会将敏感的个人信息转移给第三方存储在数据库中；容易受到攻击和泄露；目前，我们几乎每次注册网站时都会面临一些风险。

这一切听起来都很棒。但如何在不透露任何内容的情况下证明某件事呢？为此，我们可以更深入地了解 ZK 证明是什么。

## ZK 证明由什么组成？

所以，我们想要证明某件事，而不透露我们如何知道那件事，或者那件事到底是什么。这怎么可能？

在我们回答这个问题之前，ZK 证明分为两大类：

1. 交互的
2. 非交互式

在 web3 中我们关心的是非交互性的，但让我们快速了解一下 ZK 上下文中的“交互性”指的是什么。

### 交互式 ZK 证明

想象一下，有一个环形洞穴，里面有一扇门，需要密码才能进入。

**作为证明者**，您的目标是向您的朋友（**验证者）**证明您知道秘密密码（称为“**证人”）**，而不告诉他们实际的密码。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705145023.png)

你不想告诉你的朋友密码，所以你要证明你知道它；首先，随机进入洞穴的一侧；在他们看不见的情况下。

此时，你的朋友不知道你站在哪一边。但作为挑战，他们会喊出“A！” 或“B！”；要求您从**A**侧或**B侧****退出**。

在这个简单的例子中，这可能会导致两种结果：

1. 你进入了A面，所以你需要密码才能通过大门进入B面：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705145033.png)

1. 您进入B面，所以不需要密码；你可以直接退出：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705145039.png)

*这就是所谓的* [*阿里巴巴洞穴故事*](https://en.wikipedia.org/wiki/Zero-knowledge_proof#The_Ali_Baba_cave)*，供参考。*

这是一个简单的例子，因为无论您是否需要密码来满足您朋友的挑战，这都是 50/50 的比例；因此，仅执行一次此挑战不足以确定您知道密码。

这意味着您需要完成更多次挑战，正确退出 A 侧或 B 侧，直到您的朋友满意为止；或者在理论世界中，直到你不可能伪造证人的知识（密码）。

因此，这是**交互式的**；您（证明者）和您的朋友（验证者）来回交互。你的朋友提出了一个**挑战**，你提出了一个**回应。**重复这个循环，直到验证者满意为止，此时验证者已经证明了证人的**知识**。

这构成了交互式 ZK 证明的三个部分：

1. **见证人**：证明者想要证明其了解的秘密信息。
2. **挑战**：只有了解证人的人才能回答这个*问题*；虽然可能是一个幸运的猜测。
3. **Response**：证明者对挑战的响应；包含*（希望）*正确的答案。

重复步骤2和3，直到验证者满意为止。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705145047.png)

最终，一旦验证者满意，循环就会中断；验证者不会产生另一个挑战，而是承认证明者了解证人。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705145053.png)

虽然这个过程有效，但它需要证明者和验证者之间进行多轮通信；这是低效的，并且在区块链环境中效果不佳。

交互式证明还有另一个很大的限制；即使验证者满意后，该证据也无法[用于独立验证](https://ethereum.org/en/zero-knowledge-proofs/#non-interactive-zero-knowledge-proofs)；这意味着只有验证它的一方才能信任它，而不是其他任何人。

由于这些原因，进行了非交互式 ZK 证明。

### 非交互式 ZK 证明

非交互式 ZK 证明只需要从证明者到验证者的一轮通信。证明者使用一种算法来计算 ZK 证明并将其发送给验证者，验证者也使用另一种算法来检查它。

非交互式 ZK 证明的另一个好处是它们也可供其他任何人验证；这意味着它不仅可以从验证者的 POV 中得到证明，而且可以供每个人验证自己；适合区块链。

这些能够证明信息和验证证据的“算法”是什么？嗯，答案是；这取决于。[它们的数量相当多](https://en.wikipedia.org/wiki/Zero-knowledge_proof#:~:text=The most popular interactive or,Delegation (VPD)%2C and Succinct)，但在区块链环境中通常使用两种 ZKP 系统；ZK-SNARK 和 ZK-STARK。

### 什么是 ZK-SNARK？

ZK-SNARK 的意思是零知识简洁非交互式知识论证。

- **ZK：**希望现在你能猜到这意味着什么（零知识）。
- **简洁**：它们很小，并且可以被验证者快速验证。
- **非交互式**：我们之前讨论过这一点。证明者和验证者之间只需要一轮通信。
- **论据**：理论上来说，“欺骗”系统的可能性极小。
- **（的）知识**：如果不访问秘密信息（见证人），就无法构建知识。

他们使用一种称为[椭圆曲线配对](https://medium.com/@VitalikButerin/exploring-elliptic-curve-pairings-c73c1864e627)的加密原语作为创建和验证这些证明的方法（我们不会在这里讨论数学）。

关于 ZK-SNARK 需要注意的一件关键事情是，在初始设置阶段，证明者和验证者必须同意使用“共享密钥”，即公共参考字符串 (CRS)。任何有权访问此共享密钥的人都可以验证这些证明。

这个共享密钥使 ZK-SNARK 成为可能；尽管这也可以说是它们最大的缺点，因为它创建了所谓的“可信环境”。

用于创建 CRS 的值（有时称为“有毒废物”）需要在 CRS 生成后销毁。如果不是，整个系统就会面临风险；因为不诚实的证明者能够计算出错误的证明；因此，用户必须相信价值已被破坏。

关于 ZK SNARK 还值得一提的是，它们不具有“抗量子性”。这意味着它们将来很容易受到量子计算机的攻击；尽管它们将来可能会升级以具有抗量子性。

### 什么是 ZK-STARK？

ZK-STARK 的意思是零知识可扩展透明知识论证。

- **可扩展**：它们不是“简洁”的，而是可扩展的；这意味着它们比 ZK-SNARK 更有效地生成和验证更多见证人的证明。
- **透明**：无需可信设置。他们依靠可公开验证的随机性来生成共享密钥。

这种透明度的提高通常需要权衡生成比 ZK-SNARK 大小大得多的证明；除非处理非常大的数据集。[证明的大小从 288 字节增加到几百 KB](https://vitalik.ca/general/2017/11/09/starks_part_1.html#:~:text=the size of a proof goes up from 288 bytes to a few hundred kilobytes)。

他们不使用椭圆曲线，而是使用[**多项式**](https://vitalik.ca/general/2017/11/09/starks_part_1.html)**；**我绝对没有资格告诉你。Vitalik 针对该主题推出了一个由三部分组成的系列：[1](https://vitalik.ca/general/2017/11/09/starks_part_1.html) , [2](https://vitalik.ca/general/2017/11/22/starks_part_2.html) , [3](https://vitalik.ca/general/2018/07/21/starks_part_3.html)。

ZK STARK 解决了我们与 ZK-SNARK 讨论的两个问题，它们：

1. 不需要“可信环境”。
2. 似乎是后量子安全的；这意味着它们将来不会容易受到量子计算机的攻击。

ZK-STARKS 比 ZK-SNARK 更新，Vitalik 称它们为“[更新、更闪亮的表弟](https://vitalik.ca/general/2017/11/09/starks_part_1.html#:~:text=a newer%2C shinier cousin)”！🤠 所以，快速回顾一下：

|  | **ZK-SNARK** | **ZK-STARK** |
| ---------- | ---------------------- | ------------------------------------------ |
| **尺寸**   | 简洁、可快速验证       | 更大，但在证明更大的证人时可以更有效地扩展 |
| **安全**   | 需要可信的环境         | 不需要可信环境                             |
| **后量子** | 尽管可以升级，但不安全 | 安全的                                     |

## zkEVM 如何工作？

现在我们已经介绍了零知识证明的工作原理以及 web3 世界中出现的两种常见的 ZKP 形式，让我们来探讨一下由 ZKP 支持的最新创新之一；zkEVM（零知识以太坊虚拟机）。

zkEVM 有几种不同的形式；正如 Vitalik 在他的博客文章“[不同类型的 ZK-EVM](https://vitalik.ca/general/2022/08/04/zkevm.html) ”中概述的那样。我将在这篇文章中引用的是[Polygon zkEVM](https://polygon.technology/polygon-zkevm)。

zkEVM 的目标是提高以太坊区块链的可扩展性，同时保持安全、去中心化和[EVM](https://ethereum.org/en/developers/docs/evm/)兼容。

细节很复杂，但核心原理与我们到目前为止讨论的相同。与所有 ZKP 系统一样，有：

1. 证明**者**：生成代表用户提交的一批交易真实性的有效性证明。
   - 首先，它创建多个 ZK-STARK 证明。
   - [它使用STARK Recursion](https://zkevm.polygon.technology/docs/zkProver/overview/#stark-recursion-component)将 ZK-STARK 捆绑在一起，以创建单个 ZK-STARK。
   - 这个 ZK-STARK 很大，因此它通过[CIRCOM 组件](https://zkevm.polygon.technology/docs/zkProver/overview/#circom-library)输出到 SNARK 构建器。
   - 顾名思义，SNARK 构建器生成 ZK-SNARK 有效性证明；这有助于将 Gas 成本从 5M 降低到 350K。
2. **验证者**：`PolygonZkEVM`部署在以太坊上的智能合约是 ZK 证明的验证者。

### zkEVM 中的数据流

下面是 Polygon zkEVM 中数据的简化流程图。

我将其分成按时间顺序排列的部分，以帮助其更容易理解。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705145237.png)

#### 提交交易

作为用户，您可以像平常使用任何其他 EVM 链（例如以太坊）一样提交交易；通过签署交易并通过 JSON RPC 发送它们。

运行 zkEVM 软件的定序器节点会选取这些交易并决定要处理哪些交易，并制定一些激励机制来正确处理这些交易。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705145243.png)

#### 批量交易

排序器将交易批量合并为一个，并将它们提交到`PolygonZkEvm`智能合约，该智能合约存储在以太坊主网上（以及以太坊 Goerli 测试网上的一个单独实例）。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705145249.png)

这些批次目前不一定正确或经过验证。

#### 验证交易

使用 ZKP，`PolygonZkEVM`智能合约在此设置中充当验证者。它想要验证刚刚收到的批次是否有效；它通过将批次发送到聚合器节点来实现这一点。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705145256.png)

#### 生成和验证 ZK 证明/有效性证明

智能`PolygonZkEVM`合约将刚刚收到的批次发送到聚合器节点，该节点是另一台运行 zkEVM 软件并与 ZK 证明器通信的机器。流程如下：

- 聚合器从智能合约接收批次
- 聚合器将批次发送到 ZK Prover
- ZK Prover 创建多个 ZK-STARK -> 单个 ZK-STARK -> 一个 ZK-SNARK
- ZK-SNARK（有效性证明）被发送回聚合器
- 聚合器将有效性证明发送回`PolygonZkEVM`智能合约
- 智能`PolygonZkEVM`合约验证有效性证明
  - 如果有效性证明有效，则接受。
  - 如果无效，则拒绝它。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705145304.png)

#### 阅读 ZK EVM

为了使 ZK EVM 发挥作用，去中心化应用程序（dApp）需要从中读取信息；这就是**同步器**发挥作用的地方。

它从以太坊智能合约中读取事件，存储来自聚合器的 ZK 有效性证明和从排序器提交的批次的知识。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230705145310.png)

这样，应用程序就可以通过 JSON RPC 轻松获取汇总状态的视图。

## 总结

零知识证明是密码学的现实应用，可以成为更加注重隐私的未来的基础；[在Polygon ID](https://polygon.technology/polygon-id)等产品中得到了证明。

在区块链的背景下，ZKP 被用来提高[Polygon 的 zkEVM](https://polygon.technology/polygon-zkevm)等产品中以太坊的可扩展性，通过提供一种验证批量交易的新方法，而不会出现我们今天在其他汇总解决方案中看到的典型的安全性或 EVM 兼容性损失。

在这篇文章中，我们介绍了：

- ZK 证明是什么，以及为什么它们很重要。
- ZK 证明如何工作，包括 ZK-SNARK 和 ZK-STARK。
- 这些证明如何在区块链世界中使用。

如果您想了解如何应用这些概念，我之前有一篇博客文章介绍了如何在 Polygon zkEVM 上构建您的第一个智能合约和去中心化应用程序，如下所示：

https://blog.jarrodwatts.com/the-ultimate-guide-to-building-on-polygon-zkevm

> 原文：https://blog.jarrodwatts.com/how-zk-proofs-and-zkevms-work