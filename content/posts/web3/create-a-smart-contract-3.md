---
title: Web3教程：编写您的第一个“Hello, World”智能合约（3）
description: null
author: 李留白
weight: 0
date: 2022-10-30T01:08:56.618Z
lastmod: 2022-10-30T01:25:33.763Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210271827013.png
---

上一篇传送门：[Web3教程：编写您的第一个“Hello, World”智能合约（2）](https://mp.weixin.qq.com/s/ZPojn5PjZZ8WUBvUxCAWbw)

## 目录

1. 什么是智能合约
2. 部署您的第一个智能合约
3. 什么是气体
4. 与您的智能合约交互
5. 智能合约的结构
6. 将您的合同提交给 Etherscan
7. 智能合约安全挑战
8. 将合约集成到前端
9. 其他资源

## 6、将您的智能合约提交给 Etherscan

您为实现智能合约所做的所有艰苦工作完成了 - 现在是时候与世界分享它了！

通过在 Etherscan 上验证您的智能合约，任何人都可以查看您的源代码并与您的智能合约进行交互。让我们开始吧！

### 第 1 步：在您的 Etherscan 帐户上生成 API 密钥

需要 Etherscan API 密钥来验证您是您尝试发布的智能合约的所有者。

> 如果您没有 Etherscan 帐户，请先使用此[链接](https://etherscan.io/register)注册。

登录后，按右上角的用户名，然后选择“我的个人资料”按钮：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210300914842.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210300915228.png)

完成上述步骤后，您应该能够查看新的 API 密钥，我们在下面以红色突出显示。将此 API 密钥复制到剪贴板。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210300915540.png)

### 第 2 步：Hardhat部署的智能合约

#### 步骤 2.1 安装[hardhat-etherscan](https://hardhat.org/plugins/nomiclabs-hardhat-etherscan.html)插件

在你的 hello-world 目录中运行以下命令来安装 `hardhat-etherscan`：

```
npm install --save-dev @nomiclabs/hardhat-etherscan
```

使用 hardhat-etherscan 信息修改您的 hardhat.config.js，确保输入您的 etherscan api 密钥：

```js
/**
* @type import('hardhat/config').HardhatUserConfig
*/

require('dotenv').config();
require("@nomiclabs/hardhat-ethers");
require("@nomiclabs/hardhat-etherscan");

const { API_URL, PRIVATE_KEY } = process.env;

module.exports = {
   solidity: "0.7.3",
   defaultNetwork: "goerli",
   networks: {
      hardhat: {},
      goerli: {
         url: API_URL,
         accounts: [`0x${PRIVATE_KEY}`]
      }
   },
   etherscan: {
    // Your API key for Etherscan
    // Obtain one at https://etherscan.io/
    apiKey: "YOUR_ETHERSCAN_API_KEY" //
   }
};
```

#### 步骤 2.2 在 Etherscan 上验证您的智能合约！

在 hello-world 目录中，在终端中运行以下命令：

```
npx hardhat verify --network goerli DEPLOYED_CONTRACT_ADDRESS "Hello World!"
```

> 确保 DEPLOYED_CONTRACT_ADDRESS 是您在 goerli 测试网络上部署的智能合约的地址。此外，最后一个参数“Hello World！” 必须与您在第 1 部分的部署步骤中使用的字符串值相同。

如果一切顺利，您应该会在终端中看到以下消息：

Congrats! Your smart contract code should be on Etherscan!

### 第 3步：在 Etherscan 上查看您的智能合约！

当您导航到终端中提供的链接时，您应该能够看到您的智能合约代码和 ABI 在 Etherscan 上发布！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210300918907.png)

哇——你完成了，冠军！现在任何人都可以call或write给你的智能合约！我们迫不及待地想看看你接下来要建造什么！🎉

# 7、智能合约安全挑战

与更传统的编程语言的大多数用例不同，Solidity 合约倾向于转移大量资金作为其核心功能之一，因此容易受到攻击者的各种高风险攻击，这些攻击者试图从这些不可变的参与者身上榨取资金. 

因此，强烈建议开发人员在向主网发布智能合约之前进行审计或聘请审计机构——一旦发布，就很难追溯修复安全漏洞！

潜在漏洞的类型以及执行这些攻击的方式本身就是一个完整的过程。让我们快速概述一下主要漏洞，以便您了解需要注意的事项： 

### Reentrancy Attacks

这种类型的攻击非常危险，可以让易受攻击的智能合约耗尽所有的以太币，并且非常容易意外提交。重入攻击的发生是因为 Solidity 的两个关键特性： 

1. 智能合约强制执行——也就是说，它们在执行下一行之前等待每一行完成。
2. 智能合约可以调用外部的、不受信任的合约并在继续之前等待结果。

因此，当一个易受攻击的合约 A 对另一个不受信任的合约 B 进行外部调用时，另一个合约 B 可能被恶意更改为对原始合约 A 进行递归调用。如果从合约 A 到 B 的调用涉及发送任何数量的以太币，这个无限循环都可以在函数完成之前有效地耗尽合约 A 的所有资源。

这是一个简单的例子： 

```js
contract A {
	function withdraw() external {
  	uint256 amount = balances[msg.sender];
    (bool success, ) = msg.sender.call.value(amount)("");
    require(success);
    balances[msg.sender] = 0;
}}
```

通常，**msg.sender**是普通用户，例如 Metamask 帐户。在这种情况下，**withdraw()**函数将简单地提取一些钱，更新余额，然后结束。但是，如果**msg.sender**是恶意合约 B，则当合约 A 运行**msg.sender.call**时，可以设置合约 B 使其立即再次调用合约 A。在这种情况下，将重复调用**withdraw()**函数，直到合约 A 没有资源或 EVM 堆栈填满。很危险，对吧？ 

有关如何防止这种情况的一些示例，[请在此处查看本文。](https://medium.com/coinmonks/protect-your-solidity-smart-contracts-from-reentrancy-attacks-9972c3af7c21)

### Frontrunning

有趣的是，智能合约和交易不是在区块链上得到确认时才完全公开，而是在您将它们作为待处理交易提交到网络时。这些待处理的交易在以太坊节点的内存池中在整个网络中共享，允许一个区块的矿工选择具有最高 Gas 费的交易。 

这种设计的一个副作用是智能合约的预期结果在被确认进入区块链之前的一段时间内对所有人都是可见的。假设您有一个智能合约，在运行时将执行一个套利，为您赚取 1 ETH，部署成本为 0.05 ETH。监视内存池的恶意行为者可能会看到此交易，识别机会并复制您的智能合约，并以 0.06 ETH 的汽油费提交。然后，他们成功地“抢先”你的合约，通过先提交他们的交易窃取你的套利机会。

在实践中，这些攻击通常是由矿工自己进行的，导致一种称为 MEV（矿工可提取价值）的现象，每天价值数千 ETH。不幸的是，它们很难避免，但[本文描述了各种前沿实践。 ](https://forum.openzeppelin.com/t/protecting-against-front-running-and-transaction-reordering/1314)

### Integer Overflow & Underflow

这是许多编程语言中的常见攻击！以下是此类攻击在 Solidity 中如何发生的一般要点：

1. Solidity 智能合约使用 256 位作为字长构建，大约相当于 43 亿。 
2. 当减少无符号整数0的值时，它将循环回到最大值。
3. 因此，可以通过让智能合约记录的恶意地址零余额尝试发送 1 个单位的以太币来执行下溢攻击，迫使其余额一直循环回到允许的最大值：43 亿。 
4. 然后，因为智能合约认为该地址有 43 亿以太币的余额，它可能允许从该账户继续提款，直到智能合约的资金耗尽。

如您所见，这种黑客攻击很容易被用于内部跟踪地址余额的任何智能合约。为避免这种情况，只需确保您使用的是 0.8 版本的 Solidity 编译器，它会自动检查上溢和下溢。 

### 天哪……还有什么？ 

不幸的是，还有几十个其他重大漏洞。正如许多开发人员所说，“学习 Solidity 需要一周时间，避免编写漏洞需要三年时间”。 

认识到在你编写的任何中等复杂的智能合约中很可能最终会遇到安全问题就足够了，如果你打算在合约中存储任何重要的价值，你绝对应该让专业人士对代码进行审计机构。 

> 译者注：下一篇明天到来，保持关注。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)