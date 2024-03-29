---
title: 以太坊中的 Gas：关于交易费用你需要知道的一切
description: null
author: 李留白
weight: 0
date: 2023-03-07T13:44:10.272Z
lastmod: 2023-03-07T13:44:14.671Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230307213003.png
---

## 介绍

要了解以太坊网络，您需要了解 Gas 这个术语。

让我们从一个基本的类比开始：

> 正如汽车需要汽油来运行一样，以太坊同样需要 Gas 来运行它。

现在每个加密和区块链初学者都有一些问题：

- 交易费用是如何计算的？
- 您需要为一笔交易支付多少 ETH？
- 为什么有些交易比其他交易更昂贵？
- 为什么存在汽油费？

让我们了解 Gas 背后的概念，以回答您所有的脑洞大开的问题。

## 气体一般概念

与任何其他数量一样，Gas 也有一些单位，例如，`seconds`是时间单位，`metres`是距离单位，同样`gas`它本身是以太坊网络上的计算单位。

Gas 费用以 Ether 或 ETH 支付。

### 天然气升级

在开始了解 Gas 之前，您应该知道最近有一次升级**——2021 年 8 月的伦敦升级**，它略微改变了交易费用的计算方式和 Gas 的工作方式。

在此之前的天然气概念是伦敦前升级，后来的概念被称为伦敦后升级。

## **Gas费是怎么计算的？**

## 伦敦前升级

在伦敦升级之前，使用了一个简单的公式来确定您必须为一笔交易花费多少以太币。

> gas 费用 = gas 花费 * gas 价格

- **Gas Spent**是用于执行交易的 gas 总量（以 gas 为单位）
- **Gas Price**是您愿意为每个 gas 单位执行支付的以太币数量

Gas 价格以**gwei**计价——一种 ETH 的计价单位。

```
1 Gwei = 0.000000001 ETH
1 ETH = 10^9 Gwei
```

> Gwei 代表 Giga-Wei，等于 1,000,000,000 (10^9) wei。Wei 是 ETH 的最小面额。1 ETH = 10^18 Wei。

### 天然气成本计算

智能合约在部署到以太坊网络之前被编译成 OPCODES。这些算不了什么，就是一些基本的操作，比如 ADD、SUB、MUL、DIV 等等。

每个 OPCODE 都有固定的 gas 成本。智能合约中特定函数的 gas 成本是其所有 OPCODES 的 gas 成本之和。

所以，现在看看伦敦升级之前使用的公式 -

> gas 费用 = gas 花费 * gas 价格

### 气体限制

现在，我们知道汽油费取决于智能合约的功能。但如果我们有更复杂的功能，比如涉及循环的功能，或者依赖于用户输入，就很难估计出正确的价格。

因此，在决定为交易支付多少费用时，您可以指定一个上限，而不是指定确切的 gas 成本。

**Gas Limit**是指您愿意为交易使用的最大 Gas 量（单位）。这是由用户设置的。像 Metamask 这样的钱包已经为我们合理估价了。

**如果您的交易使用的 gas 少于您的限额，未使用的 gas 将退还到您的帐户。**

要在提交交易时支付 gas，您的钱包必须有`gas limit * gas price`以太币。交易被处理和开采后，任何未使用的气体将被退还。

**但是，如果您的交易使用的 gas 超过您的限制，交易将失败并且您的 gas 将消失。**

### 区块气体限制

以太坊网络对单个区块允许的最大气体量（单位）有限制，即区块气体限制。

这样做是为了保证每个块都保持在合理的计算成本范围内。因为更复杂的交易需要更长的时间才能完成，这确保了节点不会随着计算复杂性的增加而与网络的其余部分不同步。

## 后伦敦升级

2021 年 8 月 5 日之后，伦敦升级在以太坊网络上实施。所以，现在所有的gas费用都是用这个方法计算的。

此升级带来了以下好处：

- 更好的汽油费估算
- 更快的交易包含
- 销毁一定比例的 ETH 作为交易费用

在伦敦升级之前，像 Metamask 这样的钱包过去常常根据过去的网络活动来显示 gas 价格的估计值，即。它扫描 ETH 上的最后 1000 个区块并为我们预测 gas 价格。

但是现在方法改变了，每个区块都设置了一个**基本的 gas price fee**，这是让你的交易包含在区块中所需的最低金额。

这是由网络根据对块空间的需求量计算得出的。这个特定数量被以太坊网络**销毁**，因此永远摆脱 ETH 以抵消发行。

> **你知道吗？**
>
> 比特币的最大供应量为 2100 万比特币，而以太坊没有任何总量限制。

因此，为了控制 ETH 供应而不是无限膨胀，销毁非常重要并达到平衡。

嗯......现在我们有一个术语 - 基本汽油价格。**引入的第二个术语是小费（优先费）**的概念。

我们努力获取利润，很简单。这就是矿工也要工作的原因。随着基本费用被烧掉，小费是用来补偿矿工执行和传播用户交易的。大多数钱包会自动设置此设置，但是，您可以根据需要手动更改它。较高的小费交易通常被优先考虑。

通过此次升级，计算 gas 费用的公式更改为以下内容：

> gas 费用 = gas spent * (base fees + priority fees)

### 可变块大小

在伦敦升级之前，所有区块都有相同的区块 gas 限制。每个区块最多可容纳 15M 的气体。在高需求时期，这会导致糟糕的用户体验，因为区块以最大容量运行，用户必须等待需求减少才能被包含在区块中。

由于更新，以太坊现在具有可变大小的块。现在每个区块的目标气体限制为 1500 万气体，但大小可以根据网络需求而变化，最高可达 3000 万气体。

通过调整区块大小和基本费用，网络通常在 1500 万气体时达到平衡。

如果区块 gas 高于 15M 阈值，则提高下一个区块的基本费用。同样，如果区块 gas 低于 15M 阈值，则下一个区块的基本费用会降低。block gas 与 15M 目标的偏差决定了基础费用增加多少。

### GAS需要什么？

由于天然气成本，以太坊网络更加安全。通过为在网络上执行的每个计算强制收费，阻止不良行为者向网络发送垃圾邮件。

交易的 Gas 限制限制了交易可能需要多少处理，以防止智能合约中的意外或恶意无限循环，这将使所有以太坊节点无限期地卡住。

## 结论

这就是这个博客的全部内容！

请务必阅读更多关于 Gas 及其代码实现的信息。以下是一些资源：

[以太坊气体解释](https://www.youtube.com/watch?v=AJvzNICwcwc)

[伦敦升级](https://ethereum.org/en/history/#london)

[智能合约中的气体优化](https://medium.com/coinmonks/8-ways-of-reducing-the-gas-consumption-of-your-smart-contracts-9a506b339c0a)

感谢阅读！保持学习！😄

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)