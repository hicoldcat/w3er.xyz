---
title: 如何构建和部署 NFT 白名单智能合约
description: null
author: 李留白
weight: 0
date: 2022-09-07T14:03:50.851Z
lastmod: 2022-09-07T14:21:36.646Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/Ua8rD7Ulv.png
---

由于区块链技术，尤其是 NFT，艺术家和内容创作者具有将其作品货币化的独特潜力。

艺术家不再依赖画廊或拍卖行来出售他们的艺术品。相反，他们可以将其作为 NFT 直接出售给消费者，从而使他们能够保留更大部分的利润。

本文将指导我们构建和部署 NFT 白名单智能合约，使我们能够添加、删除、验证和验证用户是否是项目白名单的一部分。

## 先决条件

确保在您的计算机上安装了 Node.js 或 npm。如果您不这样做，请单击[此处](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)。

## 项目设置和安装

让我们为我们的项目创建一个新的文件夹/目录，即终端中的`whitelist-project`。在本教程的过程中，我们将在这个目录中工作。在我们刚刚创建的目录中，运行以下命令：

```
npm init -y
npm install --save-dev hardhat
```

让我们通过运行以下命令来获取示例项目：

```
npx hardhat
```

我们将使用以下选项：

- 一个示例项目。
- 接受所有其他请求。

示例项目需要安装`hardhat-ethers`和`hardhat-waffle`。

为了防止它没有自动安装，我们将用以下命令手动安装它。

```
npm install --save-dev @nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers
```

为确保一切正常，请运行以下代码：

```
npx hardhat test
```

如果一切正常，您将在控制台中看到通过的测试结果：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220907220855.png)

现在，从test文件夹中删除sample-test.js，从scripts文件夹中删除sample-script.js，并从contracts文件夹中删除Greeter.sol。

> 文件夹本身不应该被删除。

我们将在合同目录下创建一个Whitelist.sol文件。当使用Hardhat时，文件布局是至关重要的，所以要注意 我们将从任何合同的最基本结构开始。

```solidity
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;

import "hardhat/console.sol";

contract Whitelist {
    constructor() {
        console.log("Hello! from Whitelist Contract");
    }
}
```

要构建和部署我们的智能合约，我们将导航到该`scripts`文件夹，创建一个新`run.js`文件，并使用以下代码片段对其进行更新：

```js
const main = async () => {
  // This will compile our contract and generate the necessary files we need to work with our contract under the artifacts directory.
  const whitelistContractFactory = await hre.ethers.getContractFactory(
    "Whitelist"
  );
  const whitelistContract = await whitelistContractFactory.deploy();

  //We'll wait until our contract is officially deployed to our local blockchain! Our constructor runs when we deploy.
  await whitelistContract.deployed();

  console.log("Whitelist Contract deployed to: ", whitelistContract.address);
};

const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.log(error);
    process.exit(1);
  }
};

runMain();
```

在上面的代码片段中，我们已经创建了一个脚本，让我们部署我们之前写的智能合约。

让我们用以下命令来运行它。

```
npx hardhat run scripts/run.js
```

你应该看到与此类似的东西。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220907221111.png)

现在，我们有一个有效的智能合约。让我们将它部署到我们的本地网络。

在`scripts`文件夹中，让我们创建一个`deploy.js`文件并复制并粘贴以下代码：

```js
const main = async () => {
  const [deployer] = await hre.ethers.getSigners();
  const accountBalance = await deployer.getBalance();

  console.log("Deploying contracts with account: ", deployer.address);
  console.log("Account balance: ", accountBalance.toString());

  const Token = await hre.ethers.getContractFactory("Whitelist");
  const portal = await Token.deploy();
  await portal.deployed();

  console.log("Whitelist address: ", portal.address);
};

const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.error(error);
    process.exit(1);
  }
};

runMain();

```

在部署之前，让我们确保我们的本地区块链节点正在使用以下命令在单独的终端中运行：

```
npx hardhat node
```

接下来，我们将部署我们的智能合约：

```
npx hardhat run scripts/deploy.js --network localhost
```

我们应该有这样的东西：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220907221244.png)

## 建立白名单

在本节中，我们将分别更新智能合约`Whitelist.sol`和`deploy.js`文件。

`Whitelist.sol`使用以下代码片段更新文件：

```solidity
//SPDX-License-Identifier: Unlicensed
pragma solidity ^0.8.0;

contract Whitelist {
    // The number of accounts we want to have in our whitelist.
    uint256 public maxNumberOfWhitelistedAddresses;

    // Track the number of whitelisted addresses.
    uint256 public numberOfAddressesWhitelisted;

    // The owner of the contract
    address owner;

    // To store our addresses, we need to create a mapping that will receive the user's address and return if he is whitelisted or not.
    mapping(address => bool) whitelistedAddresses;

    constructor(uint256 _maxWhitelistedAddresses) {
        owner = msg.sender;
        maxNumberOfWhitelistedAddresses = _maxWhitelistedAddresses;
    }

    // Validate only the owner can call the function
    modifier onlyOwner() {
        require(msg.sender == owner, "Error: Caller is not the owner");
        _;
    }

    function addUserAddressToWhitelist(address _addressToWhitelist)
        public
        onlyOwner
    {
        // Validate the caller is not already part of the whitelist.
        require(
            !whitelistedAddresses[_addressToWhitelist],
            "Error: Sender already been whitelisted"
        );

        // Validate if the maximum number of whitelisted addresses is not reached. If not, then throw an error.
        require(
            numberOfAddressesWhitelisted < maxNumberOfWhitelistedAddresses,
            "Error: Whitelist Limit exceeded"
        );

        // Set whitelist boolean to true.
        whitelistedAddresses[_addressToWhitelist] = true;

        // Increasing the count
        numberOfAddressesWhitelisted += 1;
    }

    function verifyUserAddress(address _whitelistedAddress)
        public
        view
        returns (bool)
    {
        // Verifying if the user has been whitelisted
        bool userIsWhitelisted = whitelistedAddresses[_whitelistedAddress];
        return userIsWhitelisted;
    }

 // Is the user whitelisted?
    function isWhitelisted(address _whitelistedAddress)
        public
        view
        returns (bool)
    {
        // Verifying if the user has been whitelisted
        return whitelistedAddresses[_whitelistedAddress];
    }

  // Remove user from whitelist
    function removeUserAddressFromWhitelist(address _addressToRemove)
        public
        onlyOwner
    {
        // Validate the caller is already part of the whitelist.
        require(
            whitelistedAddresses[_addressToRemove],
            "Error: Sender is not whitelisted"
        );

        // Set whitelist boolean to false.
        whitelistedAddresses[_addressToRemove] = false;

        // This will decrease the number of whitelisted addresses.
        numberOfAddressesWhitelisted -= 1;
    }

    // Get the number of whitelisted addresses
    function getNumberOfWhitelistedAddresses() public view returns (uint256) {
        return numberOfAddressesWhitelisted;
    }

    // Get the maximum number of whitelisted addresses
    function getMaxNumberOfWhitelistedAddresses()
        public
        view
        returns (uint256)
    {
        return maxNumberOfWhitelistedAddresses;
    }

    // Get the owner of the contract
    function getOwner() public view returns (address) {
        return owner;
    }

}

```

使用以下代码片段更新`script`目录中的`deploy.js`文件：

```js
const main = async () => {
  //...

  // Deploy the contract and set the maximum number of whitelisted addresses to 5
  const portal = await Token.deploy(5);

  // Wait for it to finish deploying
  await portal.deployed();

  console.log("Whitelist address: ", portal.address);
};

//...

```

在上面的代码片段中，我们更新了deploy.js脚本，在构造函数中指定5作为白名单的最大地址数量。

## 智能合约单元测试

在本节中，我们将编写一个基本测试来测试我们将使用的最关键的功能。

为此，我们将在测试目录中创建一个whitelist-test.js文件，并编写以下代码。

```js
const { expect, use } = require("chai");
const { ethers } = require("hardhat");

describe("Whitelist", async () => {
  let whitelist;
  let whitelistContract;

  before(async () => {
    whitelist = await ethers.getContractFactory("Whitelist");
    whitelistContract = await whitelist.deploy(5);
  });

  it("should deploy", async () => {
    expect(whitelistContract.address).to.be.a("string");
    expect(whitelistContract.address).to.not.be.null;
  });

  it("should allow address to be added to whitelist", async () => {
    const whitelistAddress = "0x0000000000000000000000000000000000000000";
    await whitelistContract.addUserAddressToWhitelist(whitelistAddress);
    const isWhitelisted = await whitelistContract.isWhitelisted(
      whitelistAddress
    );
    expect(isWhitelisted).to.be.true;
  });

  it("should not allow address to be added to whitelist if already whitelisted", async () => {
    const whitelistAddress = "0x0000000000000000000000000000000000000009";
    await whitelistContract.addUserAddressToWhitelist(whitelistAddress);
    const isWhitelisted = await whitelistContract.isWhitelisted(
      whitelistAddress
    );
    expect(isWhitelisted).to.be.true;
  });

  it("should allow address to be removed from whitelist if already whitelisted", async () => {
    const whitelistAddress = "0x0000000000000000000000000000000000000009";
    await whitelistContract.removeUserAddressFromWhitelist(whitelistAddress);
    const isWhitelisted = await whitelistContract.isWhitelisted(
      whitelistAddress
    );
    expect(isWhitelisted).to.be.false;
  });

  it("should not allow address to be removed from whitelist if not whitelisted", async () => {
    const whitelistAddress = "0x0000000000000000000000000000000000000000";
    await whitelistContract.removeUserAddressFromWhitelist(whitelistAddress);
    const isWhitelisted = await whitelistContract.isWhitelisted(
      whitelistAddress
    );
    expect(isWhitelisted).to.be.false;
  });

  // Get number of whitelisted addresses
  it("should return number of whitelisted addresses", async () => {
    const whitelistAddress = "0x0000000000000000000000000000000000000000";
    await whitelistContract.addUserAddressToWhitelist(whitelistAddress);
    const numberOfWhitelistedAddresses =
      await whitelistContract.getNumberOfWhitelistedAddresses();
    expect(numberOfWhitelistedAddresses).to.equal(1);
  });

  // Get the maximum number of whitelisted addresses
  it("should return the maximum number of whitelisted addresses", async () => {
    const maxNumberOfWhitelistedAddresses =
      await whitelistContract.getMaxNumberOfWhitelistedAddresses();
    expect(maxNumberOfWhitelistedAddresses).to.equal(5);
  });

  // Get the owner of the contract
  it("should return the owner of the contract", async () => {
    const owner = await whitelistContract.getOwner();
    expect(owner).to.be.a("string");
    expect(owner).to.not.be.null;
  });
});

```

接下来，让我们使用以下命令运行测试：

```
npx hardhat test
```

我们应该有类似于下图的内容：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220907221515.png)

## RPC（远程过程调用）设置

让我们设置一个 RPC 并将合约部署到区块链。

在部署到区块链之前，我们需要创建一个[Alchemy](https://alchemy.com/?r=37c4f8545ef30da7)帐户。

我们将使用 Alchemy 发布我们的合约创建交易。该交易将被挖掘并作为有效交易添加到区块链中。

注册后，我们将创建一个如下所示的应用程序。请记住将网络切换到我们将部署的mumbai。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220907221552.png)

我们需要获取我们的密钥，如下所示，并将它们存储起来以备后用：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220907221610.png)

要使用测试网，我们需要在我们的测试网帐户中使用一些假 MATIC 代币，因此我们将使用水龙头从 Polygon Mumbai 请求一些。

> 这个“假”的 MATIC 只能在测试网上使用。

我们可以[在这里](https://faucet.polygon.technology/)获取一些 MATIC 代币。

让我们更新根项目目录中的`hardhat.config.js`文件：

```js
require("@nomiclabs/hardhat-waffle");
require("dotenv").config();

// This is a sample Hardhat task. To learn how to create your own go to
// https://hardhat.org/guides/create-task.html
task("accounts", "Prints the list of accounts", async (taskArgs, hre) => {
  const accounts = await hre.ethers.getSigners();

  for (const account of accounts) {
    console.log(account.address);
  }
});

// You need to export an object to set up your config
// Go to https://hardhat.org/config/ to learn more

/**
 * @type import('hardhat/config').HardhatUserConfig
 */
module.exports = {
  solidity: "0.8.4",
  networks: {
    mumbai: {
      url: process.env.STAGING_ALCHEMY_KEY,
      accounts: [process.env.PRIVATE_KEY],
    },
  },
};
```

在上面的代码片段中，一些键被从`.env`文件中读取，以及在顶部的导入`require("dotenv").config()`。这意味着我们需要安装dotenv包，同时使用下面的命令创建一个.env文件。

```
npm install -D dotenv

touch .env

```

在`.env`文件中，让我们添加以下键：

```
STAGING_ALCHEMY_KEY= // Add the key we copied from the Alchemy
PRIVATE_KEY= // Add your private key here

```

在上面的代码中，我们需要输入我们的私钥。幸运的是，获取我们的私钥并不难。看看这篇[文章](https://metamask.zendesk.com/hc/en-us/articles/360015289632-How-to-Export-an-Account-Private-Key)。

## 智能合约部署到Polygon网络

是时候在 Polygon 网络上部署我们的应用程序了。

让我们运行以下命令将我们的合约部署到区块链网络：

```
npx hardhat run scripts/deploy.js --network mumbai
```

我们应该有与此类似的东西。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220907221959.png)

[我们可以在Polygon Mumbai Network](https://mumbai.polygonscan.com/)上验证我们的合约部署。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220907222014.png)

这里是[Github存储库](https://github.com/Olanetsoft/whitelist-smart-contract)的链接，以便你可以检查代码，或者以防你错过什么。

## 结论

我们在本文中构建了一个白名单智能合约，并将其部署到 Polygon 测试网。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
