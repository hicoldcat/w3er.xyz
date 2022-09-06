---
title: 如何用智能合约开发一个链上DAO组织
description: null
author: 李留白
weight: 0
date: 2022-09-06T13:41:54.352Z
lastmod: 2022-09-06T14:10:00.900Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220906214219.png
---

> 用hardhat、TypeScript、solidity和Openzeppelin从头开始建立一个去中心化的治理模式。这个DAO（去中心化自治组织）使用具有投票权的ERC20代币来进行决策

## 简介

我们将学习如何使用hardhat、solidity、typescript和Openzeppelin构建一个链上DAO。对于那些不知道的人来说，DAO是一个去中心化的自治组织，通常由区块链驱动。

我们将直接跳到代码中。我们将使用100%的链上治理模式，使用ERC20代币对提议的修改进行投票。一旦工具改善了链外投票（使用[Chainlink OCR](https://chain.link/)模型和像[IPFS](https://ipfs.io/)这样的去中心化数据库），我们可能会有一个教程来实践这个，以节省气体。

## 快速开始

最快的方法是执行以下操作。

```
git clone https://github.com/PatrickAlphaC/dao-template
cd dao-template
yarn
yarn hardhat test
```

然后轰隆一声! 你将通过模拟提议投票、投票、排队投票、然后执行的测试来运行!

以下是该测试套件的工作内容。

1. 我们部署了一个ERC20代币，我们将用它来管理我们的DAO。
2. 我们部署一个Timelock合约，用来在执行提案之间提供一个缓冲区。

> 注：Timelock是处理所有资金、所有权等的合同。

3. 我们部署我们的治理合同

> 注意：治理者合同是负责提案等的，但Timelock负责执行

4. 我们部署一个Box 合同的例子，它将被我们的治理过程所拥有! (也就是我们的Timelock合约)。

5. 我们提出一个新值，添加到我们的Box合同中。

6. 然后我们对该提议进行投票。

7. 然后我们排队执行该提案。

8. 然后，我们执行它!

但是，让我们把它分解给你看......

## 入门

建议你在进行这里的工作之前，先浏览一下[hardhat的入门文档](https://hardhat.org/getting-started/)。

## 要求

- [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)：如果你能运行`git --version`并看到git版本x.x.x这样的响应，你就知道你做对了。
- [Nodejs](https://nodejs.org/en/)：如果你能运行`node --version`，并得到类似：vx.x.x的输出，你就知道你已经正确安装了nodejs。
- [Yarn](https://classic.yarnpkg.com/lang/en/docs/install/)而不是npm：如果你能运行: `yarn --version`并得到一个类似于：x.x.x的输出，你就知道你已经正确地安装了yarn。

## 我们正在构建的东西

我们将建立一个DAO，使用ERC20代币对我们的基本Box.sol合约进行投票，它看起来像这样。

[contract.box.sol ](https://gist.github.com/BetterProgramming/0288018fe3f8dae91ccb281f50d4dc5d#file-contract-box-sol)

```solidity
// contracts/Box.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "@openzeppelin/contracts/access/Ownable.sol";
contract Box is Ownable {

  uint256 private value;

// Emitted when the stored value changes
  event ValueChanged(uint256 newValue);

// Stores a new value in the contract
  function store(uint256 newValue) public onlyOwner {
    value = newValue;
    emit ValueChanged(newValue);
  }

// Reads the last stored value
  function retrieve() public view returns (uint256) {
    return value;
  }
}
```

这一切运作方式的美妙之处在于，治理是模块化的，并且可以 "粘 "到任何合同上。这里的关键是，我们的合同是 "可拥有的"，这意味着只有所有者可以调用存储功能。而这个合约的所有者就是我们的DAO!

## 建立它

为了开始工作，建立一个TypeScript hardhat项目。

```
mkdir dao-template
cd dao-template
yarn add hardhat
yarn hardhat
```

并选择TypeScript选项。这将在你的目录中创建一些文件夹和文件以供使用。

在你的`contracts`文件夹中，创建一个名为Box.sol的文件。并添加你在上面看到的Box代码，这将是我们 "进行 "治理的合同。

在我们的hardhat.config.ts中，我们要把solidity的版本更新到0.8.12或任何高于0.8.4的版本。

我们需要添加openzeppelin合约，然后尝试用编译。

```
yarn add @openzeppelin/contracts
yarn hardhat compile
```

它应该能编译成功 我们有一个box...现在怎么办？

## 治理令牌

我们的治理令牌会有点特别，在你的`contracts`文件夹中创建一个名为GovernanceToken.sol的新文件。它应该看起来像这样。

[governancetoken.sol ](https://gist.github.com/BetterProgramming/f422799c4ed41cc9e55eccbb2aa8afb8#file-governancetoken-sol)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Votes.sol";
contract GovernanceToken is ERC20Votes {

  uint256 public s_maxSupply = 1000000000000000000000000;
  
constructor() ERC20("GovernanceToken", "GT") ERC20Permit("GovernanceToken") {
    _mint(msg.sender, s_maxSupply);
  }
  
// The functions below are overrides required by Solidity.
function _afterTokenTransfer(
    address from,
    address to,
    uint256 amount
  ) internal override(ERC20Votes) {
    super._afterTokenTransfer(from, to, amount);
  }

function _mint(address to, uint256 amount) internal override(ERC20Votes) {
    super._mint(to, amount);
  }

function _burn(address account, uint256 amount) internal override(ERC20Votes) {
    super._burn(account, amount);
  }
}
```

你会发现这不是一个 "普通"的ERC20代币，这是因为我们需要跟踪 "快照"。每当有人提议投票时，我们要确保使用人们在X个区块之前的余额，而不是在提议提出的时候。这将减少人们在认为他们想参与的投票即将到来时购买和出售投票代币，并确保投票数保持一致。

一旦 "检查点 "或人们的代币余额的 "快照 "被计算为一个投票期，，就是这样！提出投票后，您无法购买更多代币并获得更多选票！您必须已经持有令牌。

我们可以确保这是用编译的。

```
yarn hardhat compile
```

## Governance合同

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220906215346.png)

不要与州长混淆

现在，让我们在我们的`contracts`文件夹中创建一个文件夹，名为governance_standard 。在未来，我想添加一个governance_offchain文件夹，但在Chainlink的那部分加入之前，这就是我们得到的东西

我们将创建一个名为 GovernorContract.sol 的合约，看起来像。

[governorcontract.sol](https://gist.github.com/BetterProgramming/4bfe00e196e53804583170f3c985e124#file-governorcontract-sol)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;
import "@openzeppelin/contracts/governance/Governor.sol";
import "@openzeppelin/contracts/governance/extensions/GovernorCountingSimple.sol";
import "@openzeppelin/contracts/governance/extensions/GovernorVotes.sol";
import "@openzeppelin/contracts/governance/extensions/GovernorVotesQuorumFraction.sol";
import "@openzeppelin/contracts/governance/extensions/GovernorTimelockControl.sol";
contract GovernorContract is
  Governor,
  GovernorCountingSimple,
  GovernorVotes,
  GovernorVotesQuorumFraction,
  GovernorTimelockControl
{
  uint256 public s_votingDelay;
  uint256 public s_votingPeriod;
constructor(
    ERC20Votes _token,
    TimelockController _timelock,
    uint256 _quorumPercentage,
    uint256 _votingPeriod,
    uint256 _votingDelay
  )
    Governor("GovernorContract")
    GovernorVotes(_token)
    GovernorVotesQuorumFraction(_quorumPercentage)
    GovernorTimelockControl(_timelock)
  {
    s_votingDelay = _votingDelay;
    s_votingPeriod = _votingPeriod;
  }

function votingDelay() public view override returns (uint256) {
    return s_votingDelay; // 1 = 1 block
  }

function votingPeriod() public view override returns (uint256) {
    return s_votingPeriod; // 45818 = 1 week
  }


// The following functions are overrides required by Solidity.
function quorum(uint256 blockNumber)
    public
    view
    override(IGovernor, GovernorVotesQuorumFraction)
    returns (uint256)
  {
    return super.quorum(blockNumber);
  }

function getVotes(address account, uint256 blockNumber)
    public
    view
    override(IGovernor, GovernorVotes)
    returns (uint256)
  {
    return super.getVotes(account, blockNumber);
  }

function state(uint256 proposalId)
    public
    view
    override(Governor, GovernorTimelockControl)
    returns (ProposalState)
  {
    return super.state(proposalId);
  }

function propose(
    address[] memory targets,
    uint256[] memory values,
    bytes[] memory calldatas,
    string memory description
  ) public override(Governor, IGovernor) returns (uint256) {
    return super.propose(targets, values, calldatas, description);
  }

function _execute(
    uint256 proposalId,
    address[] memory targets,
    uint256[] memory values,
    bytes[] memory calldatas,
    bytes32 descriptionHash
  ) internal override(Governor, GovernorTimelockControl) {
    super._execute(proposalId, targets, values, calldatas, descriptionHash);
  }

function _cancel(
    address[] memory targets,
    uint256[] memory values,
    bytes[] memory calldatas,
    bytes32 descriptionHash
  ) internal override(Governor, GovernorTimelockControl) returns (uint256) {
    return super._cancel(targets, values, calldatas, descriptionHash);
  }

function _executor() internal view override(Governor, GovernorTimelockControl) returns (address) {
    return super._executor();
  }

function supportsInterface(bytes4 interfaceId)
    public
    view
    override(Governor, GovernorTimelockControl)
    returns (bool)
  {
    return super.supportsInterface(interfaceId);
  }
}
```

这是促进我们的 GovernorToken 投票的合同。下面是我们看的主要功能。

propose : 提出一个交易。propose函数是模块化的，因为它允许你在任何合同上调用任何交易。其参数为:

- targets : 一个你想调用某个函数的地址列表。
- values : 一个你想调用某个函数的地址列表。你想在交易中向每个地址发送的ETH（或第一层加密货币）的列表。
- calldatas : 你想在每个地址上调用的每个函数的编码函数和参数的列表。
- description : 你正在使用的提案的描述。

这个函数的好处是它允许你在一个交易中对许多地址做几乎任何事情。

castVote : 我们如何投票。

queue : 一旦投票通过，我们就将其排队执行。

execute : 在时间锁定结束后，我们执行该提案。

你会注意到，一旦投票通过，它不会立即生效，这是故意的。我们希望给人们一些时间，如果他们不喜欢所做的改变，可以 "退出 "协议。这种 "时间锁定 "或 "退出 "是由我们的 "时间锁定 "合同来执行的......也我们接下来要做的事

## 时间锁

在与我们的治理合同相同的文件夹中创建一个新的文件，名为TimeLock.sol 。这是一个将 "拥有 "盒子的合同。

> *注意：是的，你没看错。TimeLock拥有一切。这是因为每当我们的治理者通过某些东西时，我们要确保在执行该功能之前，我们要等待一个最小的延迟。时间锁执行了这一点。治理者合约将是唯一能够要求时间锁合约 "做 "事情的合约。而且，只有在投票通过的情况下，它才可以要求。*

[timelock.sol ](https://gist.github.com/BetterProgramming/f737a1d6b67a92868f1f819e56253661#file-timelock-sol)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "@openzeppelin/contracts/governance/TimelockController.sol";
contract TimeLock is TimelockController {
  // minDelay is how long you have to wait before executing
  // proposers is the list of addresses that can propose
  // executors is the list of addresses that can execute
  constructor(
    uint256 minDelay,
    address[] memory proposers,
    address[] memory executors
  ) TimelockController(minDelay, proposers, executors) {}
}
```

我们的时间锁有几个参数。

1. minDelay : 在投票通过和执行之间我们应该等待多长时间。
2. proposers : 谁可以向TimeLock合约提议交易（我们将设定为只有治理合约可以）。
3. executors：我们设置了这个，所以任何人都可以执行一个已经通过并等待了时间的函数。然而，这将是一个完美的时间来添加[Chainlink Keepers](https://docs.chain.link/docs/chainlink-keepers/introduction/)，以确保执行是去中心化的!

而这就是你所需要的所有稳固性! 运行`yarn hardhat compile`来编译所有的东西!

## 脚本和测试

现在，我不想把更多的代码添加到这篇文章，但这是它的要点。你可以在我的[dao-template github repo](https://github.com/PatrickAlphaC/dao-template)中看到这些脚本和测试，其中有名为vote、proposal和queueAndExecute的脚本，它们的作用与它们的名字完全一样。

你可以看到代码，并确切地看到如何做一些先进的solidity/hardhat概念，如:

1. 在etherscan上自动验证你的智能合约
2. 本地网络上的快进时间
3. 在本地网络上快进区块
4. 如何将函数及其参数编码为字节
5. 在一个命令行中，用所有你喜欢的合同来启动本地hardhat节点
6. 先进的气体报告
7. 类型链的使用

还有更多!

## 了解更多

如果你想继续获得最新的智能合约/区块链/Web3开发者内容，请务必我，以保持最新的信息。

## 灵感

- [Openzeppelin治理演练](https://docs.openzeppelin.com/contracts/4.x/governance)
- [Openzeppelin治理Github](https://github.com/OpenZeppelin/openzeppelin-contracts/tree/master/contracts/governance)
- [Vitalik谈DAO](https://blog.ethereum.org/2014/05/06/daos-dacs-das-and-more-an-incomplete-terminology-guide/)
- [Vitalik谈链上治理](https://vitalik.ca/general/2021/08/16/voting3.html)
- [Vitalik关于一般治理的观点](https://vitalik.ca/general/2017/12/17/voting.html)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
