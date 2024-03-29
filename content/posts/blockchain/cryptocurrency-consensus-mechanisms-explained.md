---
title: 区块链的共识机制解释
description: null
author: 李留白
weight: 0
date: 2022-10-12T10:17:10.316Z
lastmod: 2022-10-12T10:36:25.930Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210121816443.png
---

共识机制是一个热门话题，涉及到环境、可扩展性和安全性。但它们究竟是什么？

## 什么是共识机制？🔐

在加密货币世界中，共识机制是使计算机网络能够共同工作以确保网络安全的协议和算法。

网络中51%的节点必须就网络的全球状态达成一致，以便达成共识。

在某种程度上，这只是一种通俗的说法，"这是我们要用的方法，以确保我们都同意对方的观点"。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210121818036.gif)

### 我们达成了什么共识？👍

区块链技术由经过验证的区块组成，然后添加到一个链上（因此得名区块链）。

每个区块包含一个特定时间段内经过验证的交易列表。这是每个人都可以看到的；也就是说，你可以回去看区块链上的每一笔交易。

共识机制被用来验证添加到区块链上的交易。

一些更高级的区块链项目也会使用共识机制来验证存储的数据、智能合约等。

但在基本层面上，共识机制是用来验证交易的。

## 协商一致机制是如何运作的？🤔

嗯，这取决于一个区块链决定使用的模式。有两种主要类型的共识机制：工作证明（PoW）和权益证明（PoS）。

### 什么是工作证明 🔨

工作证明是比特币使用的共识机制。其他使用它的加密货币项目有以太坊1.0，Dogecoin，和Litecoin。

它最初是由辛西娅-德沃克和莫尼-瑙尔在1992年开发的，作为防止垃圾邮件的一种方式。尽管是该模型的首次提出，但他们当时并没有将其称为工作证明。相反，它被称为 "定价功能"。

一直到2009年，中本聪创造了比特币，它使用工作证明作为共识机制。它被用来验证交易，以及在区块链上创建新区块。

从那时起，它已经发展成为加密货币项目中广泛使用的共识机制。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210121820517.png)



#### 工作证明是如何运作的？

网络上的节点将试图回答难以解决，但容易验证的加密难题--试图解决难题的节点被称为矿工。一旦一个矿工完成了一个谜题，他们就会把这个谜题广播到区块链上，以便其他矿工可以验证这个解决方案。

密码谜题的答案是一个叫做 "NONCE "的随机数，它代表只使用一次的数字。这个数字不能被预测，只能被猜测。

这意味着你必须投入大量的时间来寻找NONCE。

一旦与区块内的数据相结合，并通过哈希函数，NONCE将产生一个具有一系列条件的结果。这就是矿工将通过的过程，以验证解决方案是否正确。

当有效的NONCE与一个区块结合时，它会创建一个区块哈希。然后，这将被存储在经过验证的区块上，作为矿工工作的证明。 然后，区块将被创建并添加到区块链上。

现在你有证据证明你付出了工作来解决这个难题。因此，你会得到一个加密货币形式的奖励，称为矿工的奖励，。

这个奖励只给首先找到NONCE的人，而不管其他人可能为解开这个谜题投入了多少精力。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210121821768.gif)

##### 工作证明的能源问题

由于矿工的奖励，比特币挖矿是一种为自己创造被动收入的方式，这就是个人和大公司投资成为矿工的原因。

你的计算能力越强，解决加密难题的设备越好，你就越有可能收到钱作为奖励。

换句话说，一台超级计算机比一台2008年的戴尔电脑更有可能找到NONCE。

这导致了比特币网络上使用了大量的能源。它导致了环境保护者反对工作证明的共识机制。

比特币网络占全球电力消耗的0.66%，占全球能源消耗的0.23%。

比特币消耗的电力足以为剑桥大学供电1081年，或为英国所有的茶壶供电33年。

然而，并非所有都是坏事。比特币网络所消耗的大量计算能力实际上意味着该网络更加安全。

这是因为，为了攻击区块链，你需要控制网络上51%的计算能力（称为51%攻击）。拥有这么多计算能力可能要花费数十亿美元，这在经济上是不可行的。

### 什么是 "权益证明"💰

权益证明是Solana、Cardano和Tezos使用的共识机制。

它最早于2011年在比特币论坛上创建，作为工作证明的替代品，以改善旧的共识机制的不足之处。2012年，我们看到了第一个采用权益证明的加密货币项目 - Peercoin。

以太坊2.0将看到区块链从工作证明转向权益证明。试图做到 "更可扩展、更安全、更可持续"。

这种方法比工作证明方法使用的计算能力要少得多。而且，因此，使用它的项目被视为更环保。

#### 权益证明是如何工作的？

权益证明的共识机制是伪造区块，而不是像工作证明方法那样挖矿。

在工作证明项目中，代币被创造出来作为对矿工的奖励，但在权益证明项目中，交易费被用作奖励。因此，权益证明项目通过出售预先开采的代币启动，或者最初以工作证明方式启动，然后过渡到权益证明。

如果你想参与伪造区块，你必须将你的代币入股。抵押过程要求你将你的代币锁定在网络中，这意味着，你在这段时间内无法使用它们。

这是因为如果你是一个坏的行为者，你将会被夺走你的抵押代币作为惩罚--使这样的行为在经济上是可行的。

Stakers，也被称为验证者，在工作证明中承担着与矿工类似的角色。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210121823381.gif)

接下来，使用伪随机选择方法选择验证人。其中最流行的三种方法是node wealth、coin age selection和randomized block selection。

让我们仔细看看每种方法。

##### **node wealth**🤑

简单地说，你押的代币越多，你被选中验证下一个区块的机会就越大。

如果这是选择验证者的唯一变量，那么只有最富有的投币者才能保证网络的安全；使项目的分散性降低，更容易受到坏人的攻击。

##### **coin age selection** 👴

一个节点将根据代币被抵押的时间来选择，使用这个公式。

*币龄 = 抵押的天数 x 抵押的币数*

一旦一个节点完成了区块的锻造，它的币龄就会被重置为零。因此，他们必须等待一段时间才能锻造另一个区块。这可以防止富裕的钉子户主导锻造过程。反过来，使区块链更加分散。

##### **randomized block selection** 🤷

随机区块选择将通过寻找具有最低哈希值和最高股权的节点来选择下一个伪造者。

值得注意的是，每个使用权益证明共识机制的加密货币项目在选择伪造者时都使用自己的一套规则和方法。

##### 回到验证一个区块...

当一个节点最终被选中时，它将验证该区块上的交易。每笔交易都用发件人的私钥签名。

验证者将使用这个密钥来找到发件人的公钥和地址。利用这些信息，他们将验证发件人是否真的有他们要花的钱，以及他们是否有多次花费代币（被称为双重消费问题）。

接下来，散列函数将经过验证的散列交易分组为一棵Merkle树。然后，伪造者使用其私钥签署区块，并将其广播到区块链上，让其他验证者（现在称为证明者）证明该区块是有效的。

槽和纪元是用来划分时间的。槽是为创建一个区块而设定的时间（以太坊2.0的12秒）。

一个纪元是一个更大的定义时间段，它通常预示着什么时候会选择一组新的验证者（以太坊2.0的32个槽位，大约6分24秒）。

通常情况下，每个槽会在区块链上产生一个区块。然而，如果伪造者未能及时产生区块，这可能是空的。

一旦区块被验证并添加到区块链上，伪造者节点将获得加密货币形式的奖励。

如前所述，这通常来自于交易费用。如果证明人正确证明，也会得到奖励。

并非所有的权益证明项目都会遵循这个确切的方法，并对系统稍作调整。

##### 委托持股证明

一些权益证明项目允许你把你的代币委托给其他验证者，而不是自己去设置一个节点。这降低了赌注奖励的准入门槛。

个人根据记录和声誉来选择将他们的代币委托给哪个验证者。其他因素也可以决定某人将他们的代币委托给哪里，比如他们是否使用环保能源，是否有一定比例的捐赠给慈善机构。

### 工作证明（Proof-of-Work）和权益证明（Proof-of-Stake）之间的区别

好吧，这是一个很大的信息。让我们快速总结一下共识机制的差异。

#### 工作证明

- 比特币使用它
- 区块被挖掘出来
- 矿工解决密码学难题
- 需要大量的计算能力来挖矿
- 只有解开谜题的人才能得到奖励
- 对环境非常不利

#### 权益证明

- Cardano使用它
- 区块是伪造的
- 密钥和签名被用来验证交易
- 验证者必须用他们的代币做赌注才能被选中来伪造区块
- 验证者和证明者都会得到奖励
- 更加环境友好

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210121826071.gif)

### 其他共识机制

尽管这是最流行的两种共识机制，但它们并不是唯一的。事实上，在我们说话的时候，更多的共识机制正在被创造出来。

以下是一些替代性共识机制的例子。

#### 空间和时间证明 👽

这种共识机制允许项目存储数据，而不仅仅是验证交易。

从本质上讲，该模型设置了一种将信息放到硬盘上的方式，然后会随机检查信息是否还在那里。

人们为他们的信息被存储而付费，然后那些存储数据的人则为存储付费。

#### 授权证明 🕴

这是一个更集中的权益证明的版本。

权威证明项目不允许任何人成为验证者，而是选择几个他们信任的节点。这样做是为了更快地处理交易，以及进一步减少对环境的影响。

#### 唯一节点列表 📜

这是瑞波（XRP）使用的共识机制。

与授权证明类似，你选择几个你信任的节点。这一次，节点不需要抵押任何东西，他们只需要验证交易。

交易被发送到大量的节点，他们被要求签署有效的交易。如果一个交易没有得到很多签名，它就被认为是无效的，所以它被扔掉。

共识机制对于确保区块链的安全并使其成为今天的样子至关重要。我们从工作证明开始，目前正在演变为权益证明，作为减少我们对地球伤害的一种方式。

但是，这将是剩下的时间里的共识机制吗？或者我们很快就会看到另一种演变？

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
