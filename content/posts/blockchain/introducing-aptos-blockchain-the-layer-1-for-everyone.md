---
title: 🏛️ 隆重介绍 Aptos 区块链：适合所有人的Layer 1！🌐
description: null
author: 李留白
weight: 0
date: 2023-08-11T16:05:17.356Z
lastmod: 2023-08-11T16:07:17.811Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230812000522.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230812000522.png)

📰 Aptos 或 Aptos Labs 是一家脱颖而出的尖端 Web3 初创公司，专注于构建第 1 层区块链。为了实现这一目标，Aptos 使用了 Move，这是一种最初为现已解散的 Diem 区块链项目开发的语言。Aptos 融合了 Diem 的重要方面，并利用 Meta 独立创建的基于 Rust 的编程语言，正在突破创新的界限。

## 💰 APT 代币：为 Aptos 生态系统提供动力！💼

🔑 平台运营的核心是其原生代币 APT。该代币在促进 Aptos 平台上的交易和支付网络费用方面发挥着至关重要的作用。交易费用以 Aptos 代币形式收取，使验证者有机会优先处理高价值交易，同时搁置低价值交易。这种机制提高了网络的效率和有效性。

## 💡 适用于多元化数字资产的 Aptos 🎨

💸 Aptos 支持多种数字资产，为用户提供了一个通用的平台来存储各种类型的代币：

1. ERC-20 代币：Aptos 确保与 ERC-20 代币的兼容性，从而实现区块链之间的无缝传输。
2. NFT：被称为不可替代代币 (NFT) 的独特数字资产在 Aptos 上找到了一个安全的家，代表着从艺术品、音乐到游戏内资产等各种物品的所有权。
3. 来自其他区块链的代币：Aptos 促进与其他区块链的代币桥接，包括比特币和以太坊等流行网络。这种互操作性允许 Aptos 和其他平台之间顺利进行代币传输。

## 🏗️ Aptos 区块链：了解区块和纪元 🧱

🔗 在 Aptos 区块链中，数据组织遵循按交易版本化的数据库方法。与仅存储块的结果状态的其他区块链不同，Aptos 单独存储每个事务的结果状态。这种设计允许更精确和细粒度的数据访问。

⏳ Epoch 代表 Aptos 区块链中较大的时间段，并充当同步重大更改的安全窗口，例如从验证器集中添加或删除验证器。了解区块和纪元的概念对于掌握 Aptos 区块链的设计和效率及其对网络活动的动态响应至关重要。

## 🚀 Aptos：TPS 野兽区块链！🌌

💪 Aptos 拥有令人印象深刻的可扩展性，每秒能够处理多达 10,000 笔交易，使其成为托管广泛数字资产的绝佳选择。其安全、节能的 Ouroboros Praos 共识算法让人们对 Aptos 上存储资产的安全性和可靠性充满信心。此外，它与其他区块链的互操作性为用户带来了无限的可能性。

🌐 因此，如果您正在寻求一种安全且可扩展的解决方案来满足您的数字资产存储需求，Aptos 将成为一个令人信服的选择。它作为一个平台的多功能性为无数应用程序带来了丰富的可能性，使其成为整个区块链领域用户的一个诱人的主张。

## 🧭 探索 Aptos 区块链交易之旅 🚶‍♂️

🗺️ 与我们一起踏上一段迷人的旅程，我们将全面深入了解 Aptos 交易的生命周期。这种富有洞察力的探索将为您提供详细的操作视角，跟踪交易从提交到全节点的路径，直到它成为 Aptos 区块链中的已提交条目。在整个旅程中，我们将阐明 Aptos 节点的逻辑组件及其与交易的交互。准备好接受启发性的体验吧！

### 📝 交易的生命周期 🔄

为了说明该过程，让我们考虑 Alice 和 Bob，他们都是在 Aptos 区块链上拥有账户的用户。Alice 持有 110 个 Aptos 币，并打算发送 10 个 Aptos 币给 Bob。目前，Alice 账户的序列号为 5，表示她之前已经执行过 5 笔交易。

在庞大的 Aptos 网络中，有 100 个验证节点，每个节点的标识范围为 V1 到 V100。Alice发起的交易在到达目的地之前将经过这些节点。

### 🏭第一阶段：接受交易🏭

以下是交易被接受和处理的方式：

1. Alice 的 Aptos 客户端构建原始交易 (Traw5)，将 10 个 Aptos 币转移到 Bob 的账户。客户端使用 Alice 的私钥签署此交易。签名交易 (T5) 包括原始交易、Alice 的公钥和她的签名等基本信息。
2. 原始交易 (Traw5) 包含关键字段，例如 Alice 的账户地址、指定要采取的操作的 Move 字节码点对点交易脚本、Alice 愿意支付的最大 Gas 金额、Gas 价格、到期时间、序列号和唯一的链ID。

### 🚀 第 2 阶段：与其他验证节点共享交易 🚀

现在，交易开始通过网络进行旅程：

1. Alice 的客户端将事务 T5 提交到 Aptos 全节点的 REST 服务。全节点将交易转发到自己的内存池，内存池又与网络中的其他节点共享交易，最终到达验证器 V1。
2. 验证者 V1 充当本轮的提议者/领导者，通过共享内存池协议接收交易 T5 以及其他交易。

### 💡第三阶段：提出区块💡

在 V1 的带领下，提议的区块逐渐成形：

1. 验证器 V1 作为提议者，从其内存池中提取一个交易块（包括 T5）。然后，它向其他验证者提议该区块，努力就该区块内的交易顺序达成共识。

### 🤝 第四阶段：执行和共识 🤝

达成共识和执行区块的重要过程展开：

1. 验证器 V1 与其执行组件通信，共享事务块（包括 T5）以进行进一步处理。
2. 由虚拟机 (VM) 管理的执行组件开始块内交易的推测执行。共识并行进行，试图就提议区块的执行结果达成一致。
3. 执行交易后，执行组件用结果临时更新 Merkle 累加器。然后，该信息会与共识组件共享，从而促进共识过程。
4. 验证器 V1 作为领导者，与其他参与验证器节点合作，以就提议块的执行结果达成共识。

### ⛓️第5阶段：提交区块⛓️

达成共识后，是时候巩固成果了：

1. 在获得必要的法定投票数并就区块执行达成一致后，Validator V1 的执行组件将读取推测执行缓存并将所有交易（包括 T5）提交到持久存储，从而有效地更新 Aptos 区块链。

结果，Alice 的账户余额现在显示 100 Aptos Coins，并且她的序列号增加到 6。Bob 重播 T5 的任何尝试都将被拒绝，因为 Alice 的账户序列号已经超出了重播交易的序列号。

## 🔧 了解 Aptos 节点组件交互 🔧

现在我们已经深入研究了交易生命周期，让我们仔细看看 Aptos 节点如何相互交互。对于那些对系统内部运作感兴趣或考虑为动态 Aptos 区块链做出贡献的人来说，这种理解将非常宝贵。

### 🔑 Aptos 节点的关键组件 🔑

Aptos节点由几个核心组件组成，即：

1. Fullnode，包含 REST 服务并与客户端交互以提交事务。
2. 验证器节点，包含内存池、共识、执行、虚拟机和存储组件。

### 💬 组件之间的交互 💬

现在让我们分解一下这些组件在处理事务和响应区块链查询期间的交互：

1. REST 服务：任何客户端请求都会在全节点的 REST 服务上发起，该服务将事务转发到验证器全节点，然后转发到验证器节点。
2. 虚拟机（VM）：VM 负责验证和执行以 Move 字节码编写的事务。它验证各个方面，例如签名、帐户余额和序列号，以确保交易完整性。
3. 内存池：充当共享缓冲区，内存池保存等待执行的事务。它与其他验证器节点共享交易以优化网络使用。
4. 共识：这个关键组件有助于验证者之间就交易顺序及其执行结果达成一致。它在区块提案和执行过程中达成共识方面发挥着关键作用。
5. 执行：协调交易块的执行，执行组件在共识期间维持投票的瞬态状态。一旦达成一致，它就会将交易提交到存储中。
6. 存储：存储组件保存商定的交易块及其执行结果，有效地更新区块链。

了解这些组件间的交互可以让您对 Aptos 区块链的复杂运作有宝贵的见解，使您能够为其发展做出有效的贡献。

## 🔒 了解 Aptos 区块链中的 Gas 和存储费用 💼

💸 在 Aptos 区块链上执行的每笔交易都会产生处理费，由两部分组成：

1. 执行和 IO 成本：这涵盖了利用瞬态计算资源来处理事务并在分布式主网网络上传播经过验证的记录。它以天然气单位计量，其价格可能会根据网络负载而波动，从而在不太繁忙的时期降低成本。
2. 存储费用：这涵盖了分布式区块链存储中经过验证的记录的持久存储成本。它以固定 APT 价格衡量。

### 🧮 气体单位：消耗的基本单位 ⛽

Gas 单位是 Aptos 区块链中交易的一个基本方面，代表瞬时资源消耗的基本单位，例如计算和存储访问。交易中消耗的总 Gas 单位取决于其复杂性。另一方面，汽油价格以 Aptos 区块链的原生代币 Octas 表示。

### ⚖️ Gas 价格和交易优先级 ⚖️

在 Aptos 网络中，最低 Gas 单价由 Aptos 治理设定。然而，市场决定了特定天然气单价交易的处理速度。通过指定比当前市场价格更高的 Gas 单价，用户可以通过支付更大的处理费来提高交易的优先级。

### 🏛️ 由治理设置的 Gas 参数 🏛️

Aptos治理设置了一定的gas参数，包括一笔交易中max_gas_amount允许的最大gas单位，以及所需的最小gas单位，确保max_gas_amount大于这个值。

### 💻 计算仓储费 💾

交易的存储费用取决于交易大小、使用的新存储槽数量、写入的字节数和发出的事件等因素。目前，一笔交易的总存储费包含在总gas_used值中，该值可能根据gas单价而变化。

### 📈 通过模拟估算 Gas 消耗 📉

要估计 Gas 消耗量，可以使用 SimulateTransaction API 或 Aptos CLI 的 Gas 分析功能来执行模拟。模拟中使用的气体单位代表模拟时所需的确切数量，并且可能会根据区块链的状态而变化。

## 🔍 探索 Aptos 区块链交易之旅 🔍

🚶‍♂️ 与我们一起沉浸式深入探索 Aptos 交易的迷人生命周期，为您提供详细的操作视角。让我们踏上旅程，追踪交易从提交到 Aptos 全节点的路径，并见证其转变为 Aptos 区块链中的已提交条目。沿着这条启发性的道路，我们将阐明 Aptos 节点的逻辑组件及其与交易的交互。

### 📋 了解交易和状态 📋

🔒 Aptos 区块链的基础建立在两个基本概念之上——交易和状态。交易是 Aptos 区块链上数据交换的表示，可以在账户之间转移 Aptos 币、NFT 和其他有价值的资产。

🏛️ Aptos 区块链账本状态，通常称为“状态”，包含网络内所有账户的当前状态。事务可以以多种状态结束，包括已提交和已执行、已提交和中止、已丢弃（验证检查）和已丢弃（预执行）。

### ⚖️ 交易改变账本状态 ⚖️

事务 Ti 的执行将 Aptos 区块链的状态从 Si-1 修改为 Si。该过程涉及确定性函数Apply()，当使用特定的初始状态和事务执行时，该函数始终生成相同的最终状态。将事务 Ti 应用于状态 Si-1 会产生新状态 Si。这确保了账户余额在交易执行后得到相应更新。

### 📋 交易内容 📋

区块链上的签名交易包含发送者的数字签名、发送者地址、发送者公钥、程序（Move 模块或函数名称）、Gas 价格、最大 Gas 量、序列号和过期时间等基本信息。

### 💡交易类型💡

事务可以针对入口点或脚本负载。目前，Python 和 Typescript SDK 仅支持针对入口点的事务。这些入口点包括代币转账和 aptos_account 创建等。虽然可以通过顺序调用入口点的多个事务来执行操作，但从单个事务原子地执行此类操作可能会证明是有益的。

## 🔗 了解 Aptos 区块链中的区块和纪元 🔗

🧱 在 Aptos 区块链中，数据组织遵循按交易版本化的数据库方法。与仅存储块的结果状态的其他区块链不同，Aptos 单独存储每个事务的结果状态。这种设计允许更精确和细粒度的数据访问。

🧱 区块仍然是 Aptos 的基本单位，其中交易被分组并一起执行。块内的交易数量可能会根据网络活动和可配置的最大块大小限制而变化。随着区块链使用量的增加，区块可能包含更多交易。

## 📜 Aptos 的主要特点：📜

1. 可扩展：Aptos 每秒可以无缝处理多达 10,000 笔交易。
2. 安全：Ouroboros Praos 确保强大的安全性和能源效率。
3. 可扩展：Aptos 专为适应性而设计，可轻松与新应用程序集成。
4. 社区驱动：在利益相关者社区的治理下，Aptos 经历了透明和负责任的开发。

## 💡 Aptos 成为未来有前途的区块链 💡

🚀 Aptos 是一个前景光明且资金充足的新区块链，有望在区块链领域产生重大影响。在 Andreessen Horowitz 和 Coinbase 等行业巨头的支持下，Aptos 拥有彻底改变数字资产交互的潜力。

🌐 Aptos 的设计具有可扩展性、安全性和节能性，采用创新的 Ouroboros Praos 共识算法，从卡尔达诺著名的协议中汲取灵感。其权益证明机制确保了更高的安全性和能源效率，这与比特币和以太坊等传统的工作量证明区块链不同。

🔧 Aptos 以其可扩展性脱颖而出，轻松适应新应用程序，并在社区驱动的治理模式下运营，促进透明度和问责制。

🌌 因此，如果您正在寻求一种安全且可扩展的解决方案来满足您的数字资产存储需求，Aptos 将成为一个令人信服的选择。它作为一个平台的多功能性为无数应用程序带来了丰富的可能性，使其成为整个区块链领域用户的一个诱人的主张。

***参考文献：***
- [ https://aptoslabs.com/](https://aptoslabs.com/)
- [ https://aptos.dev/](https://aptos.dev/)
- [ https://github.com/aptos-labs](https://github.com/aptos-labs)
- [ https://golden.com/wiki /Aptos-W3Z8B9D](https://golden.com/wiki/Aptos-W3Z8B9D)

> 原文：https://docak.hashnode.dev/introducing-aptos-blockchain-the-layer-1-for-everyone