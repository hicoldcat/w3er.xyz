---
title: 了解以太坊中的 Gas 以及如何优化智能合约
description: null
author: 李留白
weight: 0
date: 2023-06-15T10:12:10.660Z
lastmod: 2023-06-15T10:20:34.098Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230615181221.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230615181221.png)

## 1、简介

你好世界！欢迎来到迷人的以太坊世界，这是区块链对迪士尼乐园的回应。一个为去中心化应用程序和智能合约颠覆游戏规则的平台。这些具有超能力的合同可以根据预定义的条件完美执行，自动执行任务，并且正在彻底改变我们处理资金的方式、我们的治理方式以及我们追踪供应链的方式。

以太坊舞台上的明星选手是 gas——保持以太坊引擎运行的秘方。它是该网络上所有交易和智能合约背后的驱动力。通过确保公平并防止资源占用或流氓代码，gas 保障了网络的完整性和安全性。因此，gas不仅仅是一个平凡的概念；它是以太坊秀的超级英雄！

## 2、了解以太坊中的 Gas

你听说过以太坊中的“gas”吗？它不是为你的汽车提供动力的那种，而是让事情发生的“能量”。您在以太坊上所做的每项操作、每笔交易、每件小事都需要一些这种气体。这就像您放入街机玩游戏的代币——没有汽油，就没有乐趣。

以太坊使用 gas 来让每个人都公平竞争。这是一个配给系统，确保每个人都能在以太坊游乐场上轮到。但问题是——汽油不是免费的。它以以太币 (ETH) 定价，以太币是以太坊的原生加密货币，特别是以称为 Gwei 的较小以太币单位计价。

让我们用一个例子来分解它。假设您要向朋友发送 1 ETH。对于该交易，您可以将 gas 限制设置为 21,000（这是您认为需要多少“能量”）和 20 Gwei 的 gas 价格。这里的 gas limit 就像你猜测你的交易需要多少“能量”，而 gas price 是你愿意为每个单位的“能量”支付的价格。

如果以太坊网络不太拥挤，您的交易会得到处理，总 gas 成本将为 gas limit 乘以 gas price，即 21,000（gas limit）* 20（gas price）= 420,000 Gwei。该金额将从您的 ETH 余额中扣除。

但是，如果网络繁忙并且许多人试图同时进行交易，那么除非您愿意支付更高的 gas 价格，否则您的交易可能不会得到处理。这有点像您为网络的计算资源出价的拍卖！

这个概念可以用伪代码表示如下：

```js
function sendTransaction() {
  var gasLimit = 21000;
  var gasPrice = 20; // in Gwei
  var totalGasCost = gasLimit * gasPrice;

  if (totalGasCost <= yourCurrentBalance) {
    // go ahead, send the transaction
  } else {
    // sorry mate, you need more Ether
  }
}
```

所以这就是以太坊中 gas 的内幕——一切都是为了保持网络平稳运行，确保每个人都能公平运行，并确保你为你使用的东西付费。请记住，关键是找到 gas 价格的最佳点，让您的交易在不清空您的 Ether 钱包的情况下完成！

## 3、Gas 在智能合约执行中的作用

让我们深入探讨有趣的部分——gas 如何在智能合约执行中发挥作用。将气体想象成为您的智能合约中的每个动作提供动力的果汁。你明白了，你调用的每个函数，你运行的每个操作，都需要一些 gas 来实现。

把它想象成一个游戏厅。如果你想玩游戏（运行一个函数），你必须投入一些代币（gas）。现在，并非这个以太坊街机中的所有游戏（操作）都花费相同。让我们把它分成几类：

- 读取操作：从区块链中读取数据就像玩最简单的街机游戏，比方说来一场空中曲棍球。不会花费太多 gas，因为您所做的只是检查已经存在的数据。你没有改变任何东西。
- 算术运算：在你的合约中做一些数学运算，比如加减数字，就像你的老派吃豆人。就gas而言仍然相当实惠，但不仅仅是读取数据。
- 存储数据：现在，如果您要存储数据或创建新合同——就像角落里的豪华 VR 游戏一样。存储数据意味着你正在积极地改变区块链上的某些东西。而且，我的朋友，这会花费你更多的汽油。
- 复杂的操作：然后有一些操作相当于每个人都在排队玩的华丽的新赛车游戏。这些可能是调用其他合约或在您的函数中使用循环之类的事情。这些将是最耗油的操作。

在你开始玩（运行交易）之前，你需要估计你需要多少代币（gas）。那是你的气体限制。如果您在完成游戏之前用完了代币，游戏就结束了！在以太坊中，我们称之为“气体不足”错误。就像在街机中一样，没有退款或重做。您的代币（gas）已用完，您需要更多才能继续玩。

简而言之，智能合约中的每个操作就像街机中的游戏，需要花费一定数量的代币（gas）。明智地选择您的操作，并始终关注您的气体限制！

## 4、估算gas成本

好吧，准备好，我们正在进入估算gas成本的领域。可以把它想象成计算越野公路旅行需要多少汽油。你已经到达了目的地，现在你必须计算出燃料。

你是怎么做到的？好吧，有几种方法。有些人使用 Etherscan 或 ETH Gas Station 等在线平台来查看平均汽油成本。其他人可能会选择 MetaMask，这是一种浏览器扩展程序，可以在您即将进行交易时为您估算燃料。这就像在乘客座位上有一个擅长心算的伙伴。

但是，如果您真的想深入了解，可以使用以太坊的 JavaScript API web3.js 中的“estimateGas”函数。这个漂亮的工具就像您汽车的车载电脑。您向它提供旅程的详细信息（交易），它会计算您需要的燃料（汽油）。快速浏览一下：

```js
web3.eth.estimateGas({
    to: "0xSomeAddress",
    data: "0xSomeData"
})
.then(console.log);
```

那么，这是怎么回事？那么，您要求“estimateGas”查看某个地址（“0xSomeAddress”）和特定数据（“0xSomeData”）的假设交易。它运行这些数字，然后记录估计的gas成本。

但这里有一个转折点：gas 估算并不能为您提供以太币的确切成本。不，它更像是一块拼图。您需要将 gas 估算值乘以当前 gas 价格（以 Gwei 为单位）以获得以 Ether 为单位的实际成本。这就像计算餐厅的总账单——菜单会告诉您每道菜的价格，然后您计算得出最终账单。

让我们看一个实际的例子，让事情变得一目了然：

```js
const gasEstimate = 50000; // Gas estimate received from a tool
const gasPrice = 20; // Gas price in Gwei
const actualCost = gasEstimate * gasPrice; // Actual cost in Ether

console.log(`The estimated gas cost is ${gasEstimate} units.`);
console.log(`To calculate the actual cost, multiply it by the gas price (${gasPrice} Gwei).`);
console.log(`The total cost will be ${actualCost} Ether.`);

```

但请记住，这就像天气预报。这是一个很好的猜测，但请务必带上雨伞以防万一！

## 5、优化智能合约以降低 Gas 消耗

是时候一头扎进精益、平均、省油的智能合约世界了！我们提供了一些技巧、很酷的工具和真实示例，可帮助您在气体优化游戏中取得胜利。所以，系好安全带，准备好开始狂野之旅吧！

以下是一些可以在编写高效智能合约方面创造奇迹的最佳实践：

- 尽量减少那些烦人的存储操作，就像整理房间一样整洁。
- 避免那些像瘟疫一样吞噬气体的永无止境的循环。选择优雅的算法，让事情顺利进行。

现在，让我们探索一些方便的工具来分析和优化智能合约中的 gas 使用情况。这些工具就像可以拯救世界的超级英雄：

- 气体分析器：它们就像夏洛克福尔摩斯，调查您的代码以发现任何耗气的罪魁祸首。一些示例包括：EthGasStation、GasNow、Blockscout、Gas Price Monitor、Gas Tracker 等
- 分析员：将他们视为私人教练，帮助您减轻不必要的负担并建立更精简的合同。它们通过运行您的合约并收集每个功能使用的气体数据来工作。然后可以使用此数据来确定您的合同使用了比必要更多的gas的区域。一些示例包括：Remix IDE、Truffle Suite、Tracer、EthVM、Gasper

但是等等，还有更多！让我们来看看一些优化的智能合约代码，让你的齿轮转动：

```solidity
// Voting System Example

contract Voting {
    mapping(address => bool) public hasVoted;
    uint256 public totalVotes;

    function vote() public {
        require(!hasVoted[msg.sender], "You've already cast your vote!");

        // Voting logic here

        hasVoted[msg.sender] = true;
        totalVotes++;
    }

```

投票系统：通过使用映射来跟踪投票并避免冗余存储更新，您可以创建精简高效的投票合约。

```solidity
// Token Transfer Example

contract Token {
    mapping(address => uint256) public balances;

    function batchTransfer(address[] memory recipients, uint256 amount) public {
        for (uint256 i = 0; i < recipients.length; i++) {
            balances[recipients[i]] += amount;
        }
    }
}

```

Token Transfer：通过使用批量操作和减少外部调用次数来优化令牌传输。这就像一次发送一堆礼物而不是单个包裹。

## 6、结论

就这样，我们的gas冒险之旅结束了！现在，您不仅是 Ethereum 的普通用户，您还是gas大师！您知道什么是气体，为什么它很重要，以及它在以太坊中的使用方式。您对 gas 价格和限制并不陌生，并且您知道如何让您的智能合约尽可能节省 gas。

以下是我们所涵盖内容的快速回顾：

- 我们了解了什么是气体，以太坊为何使用它，以及它与以太币的关系。还记得汽车和燃料的比喻吗？gas就像为以太坊网络上的每项操作提供动力的燃料。
- 我们在智能合约的背景下讨论了gas。就像合约中的每个操作如何需要 gas，以及 gas 耗尽如何导致“out-of-gas”错误。
- 我们使用 web3 的 estimateGas 函数等很酷的工具深入研究了 gas 成本的估算。它就像您的汽油表，告诉您特定交易需要多少汽油。
- 最后，我们谈到了优化智能合约以使用更少的 gas。因为谁不喜欢存钱，对吧？

请记住，以太坊和智能合约的世界总是在不断发展。所以不断探索，不断学习，不断优化。谁知道呢，也许您就是想出下一个大型节气技术的人！

所以，继续巡游，以太坊爱好者。掌握区块链的道路很漫长，但凭借您新获得的gas知识，我相信您会走得更远！

## 7、参考资料

好吧，伙计，你已经完成了我们的以太坊燃料之旅！但是，嘿，学习不止于此。我有很多资源可以让你在区块链探索中充满活力。检查这些：

1. **以太坊白皮书**：以太坊的 OG 指南。有点沉重，但值得一读。[一探究竟](https://ethereum.org/en/whitepaper/)
2. **以太坊黄皮书**：这篇深入探讨了以太坊的技术方面。把这个放在手边。[看一看](https://ethereum.github.io/yellowpaper/paper.pdf)
3. **Ethereum Gas Station**：一个专门提供以太坊世界实时汽油价格的网站。很酷吧？[看一眼](https://ethgasstation.info/)
4. **Solidity 文档**：编写智能合约的完整指南。它是每个编码员的必备工具。[读一读](https://docs.soliditylang.org/)
5. **Etherscan**：检查以太坊交易、合同和区块链上的所有其他内容。[干得好](https://etherscan.io/)
6. **StackExchange Ethereum**：遇到问题了吗？其他人也有可能。在这里找到你的答案。[继续，可以问任何问题](https://ethereum.stackexchange.com/)

请记住，区块链世界一直在变化。所以，跟上，保持好奇，并继续学习。您正在成为真正的以太坊冠军！



> 原文: https://rinwa.hashnode.dev/understanding-gas-in-ethereum-and-how-to-optimize-smart-contracts