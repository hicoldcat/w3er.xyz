---
title: Web3系列教程之入门篇---8. Dapp白名单
description: null
author: 李留白
weight: 0
date: 2022-07-10T06:36:39.486Z
lastmod: 2022-07-10T06:37:37.111Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703215340.png
---

你正在推出名为`Crypto Devs`的NFT系列。你想让你的早期支持者访问你的收藏品的白名单，所以在这里你要为`Crypto Devs`创建一个白名单dapp

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220710135551.png)

## 要求

- 白名单访问应该免费提供给想要进入的前10个用户。
- 应该有一个网站，人们可以去那里进入白名单。

让我们开始吧🚀

## 先决条件

- 你可以用JavaScript写代码（[ Level-0 ](https://hicoldcat.com/posts/web3/freshman-track-0/)）。
- 已经建立了一个Metamask钱包([ Level-4 ](https://hicoldcat.com/posts/web3/freshman-track-4/))
- 你的电脑已经安装了Node.js。如果没有，请从[这里](https://nodejs.org/en/download/)下载

## Build

### 智能合约

为了构建智能合约，我们将使用[Hardhat](https://hardhat.org/)。Hardhat 是一个以太坊开发环境和框架，专为 Solidity 中的全栈开发而设计。简单来说，您可以编写智能合约、部署它们、运行测试和调试代码。

- 首先，您需要创建一个 Whitelist-Daap 文件夹，Hardhat 项目和您的 Next.js 应用程序稍后将进入该文件夹
- 打开终端并执行这些命令

```
mkdir Whitelist-Dapp
cd Whitelist-Dapp
```

- 然后，在 Whitelist-Daap 文件夹中，您将设置 Hardhat 项目

```
mkdir hardhat-tutorial
cd hardhat-tutorial
npm init --yes
npm install --save-dev hardhat
```

- 在安装 Hardhat 的同一目录中运行：

```
npx hardhat

```

- 选择`Create a basic sample project`
- 按回车键指定`Hardhat Project root`
- 如果您想添加一个问题，请按 Enter 键`.gitignore`
- 按回车键`Do you want to install this sample project's dependencies with npm (@nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers)?`

现在你有一个hardhat项目准备好了！

如果您不在 Mac 上，请执行此额外步骤并安装这些库：)

```
npm install --save-dev @nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers

```

- 首先在`contracts`下创建名为`Whitelist.sol`.

```
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.0;


contract Whitelist {

    // Max number of whitelisted addresses allowed
    uint8 public maxWhitelistedAddresses;

    // Create a mapping of whitelistedAddresses
    // if an address is whitelisted, we would set it to true, it is false by default for all other addresses.
    mapping(address => bool) public whitelistedAddresses;

    // numAddressesWhitelisted would be used to keep track of how many addresses have been whitelisted
    // NOTE: Don't change this variable name, as it will be part of verification
    uint8 public numAddressesWhitelisted;

    // Setting the Max number of whitelisted addresses
    // User will put the value at the time of deployment
    constructor(uint8 _maxWhitelistedAddresses) {
        maxWhitelistedAddresses =  _maxWhitelistedAddresses;
    }

    /**
        addAddressToWhitelist - This function adds the address of the sender to the
        whitelist
     */
    function addAddressToWhitelist() public {
        // check if the user has already been whitelisted
        require(!whitelistedAddresses[msg.sender], "Sender has already been whitelisted");
        // check if the numAddressesWhitelisted < maxWhitelistedAddresses, if not then throw an error.
        require(numAddressesWhitelisted < maxWhitelistedAddresses, "More addresses cant be added, limit reached");
        // Add the address which called the function to the whitelistedAddress array
        whitelistedAddresses[msg.sender] = true;
        // Increase the number of whitelisted addresses
        numAddressesWhitelisted += 1;
    }

}
```

- 让我们将合约部署到`rinkeby `网络。在文件夹`scripts`下创建一个名为的新文件`deploy.js`

- 现在我们将写一些代码来部署`deploy.js`文件中的合同。

```
const { ethers } = require("hardhat");

async function main() {
  /*
  A ContractFactory in ethers.js is an abstraction used to deploy new smart contracts,
  so whitelistContract here is a factory for instances of our Whitelist contract.
  */
  const whitelistContract = await ethers.getContractFactory("Whitelist");

  // here we deploy the contract
  const deployedWhitelistContract = await whitelistContract.deploy(10);
  // 10 is the Maximum number of whitelisted addresses allowed
  
  // Wait for it to finish deploying
  await deployedWhitelistContract.deployed();

  // print the address of the deployed contract
  console.log(
    "Whitelist Contract Address:",
    deployedWhitelistContract.address
  );
}

// Call the main function and catch if there is any error
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
  ```

- 现在在`hardhat-tutorial`文件夹中创建一个`.env`文件，并添加以下几行，使用注释中的说明来获取你的`Alchemy API Key URL`和`RINKEBY Private Key`。确保你获得`RINKEBY Private Key`的账户有Rinkeby的以太币资金。

```

// Go to https://www.alchemyapi.io, sign up, create
// a new App in its dashboard and select the network as Rinkeby, and replace "add-the-alchemy-key-url-here" with its key url
ALCHEMY_API_KEY_URL="add-the-alchemy-key-url-here"

// Replace this private key with your RINKEBY account private key
// To export your private key from Metamask, open Metamask and
// go to Account Details > Export Private Key
// Be aware of NEVER putting real Ether into testing accounts
RINKEBY_PRIVATE_KEY="add-the-rinkeby-private-key-here"
```

- 现在我们将安装`dotenv`包，以便能够导入env文件并在我们的配置中使用它。打开一个终端，指向`thardhat-tutorial`目录，执行以下命令

```
npm install dotenv
```

- 现在打开`hardhat.config.js`文件，我们将在这里添加`rinkeby`网络，这样我们就可以将我们的合同部署到`rinkeby`。将`hardhar.config.js`文件中的所有行替换为下面给出的行

```
require("@nomiclabs/hardhat-waffle");
require("dotenv").config({ path: ".env" });

const ALCHEMY_API_KEY_URL = process.env.ALCHEMY_API_KEY_URL;

const RINKEBY_PRIVATE_KEY = process.env.RINKEBY_PRIVATE_KEY;

module.exports = {
  solidity: "0.8.4",
  networks: {
    rinkeby: {
      url: ALCHEMY_API_KEY_URL,
      accounts: [RINKEBY_PRIVATE_KEY],
    },
  },
};
```

- 编译合同，打开终端，指向`thardhat-tutorial`目录，执行以下命令

```
   npx hardhat compile
```

- 要部署，打开终端指向`thardhat-tutorial`目录并执行以下命令

```
npx hardhat run scripts/deploy.js --network rinkeby
```

- 将打印在终端上的白名单合同地址保存在你的记事本中，你会在接下来的教程中需要它。

### 网站

- 为了开发网站，我们将使用[React](https://reactjs.org/)和[Next Js](https://nextjs.org/)。React 是一个用于制作网站的 javascript 框架，Next.js 是一个 React 框架，它还允许与前端一起编写后端 API 代码，因此您不需要两个单独的前端和后端服务。

- 首先，您需要创建一个新`next`应用程序。您的文件夹结构应该类似于

```
- Whitelist-Dapp
    - hardhat-tutorial
    - my-app
```

- 要创建`next-app`，在终端指向 Whitelist-Dapp 文件夹并输入

```
npx create-next-app@latest

```

并按下enter所有问题


- 现在运行应用程序，在终端中执行这些命令

```
cd my-app
npm run dev
```

- 现在转到`http://localhost:3000`，您的应用程序应该正在运行🤘

- 现在让我们安装[Web3Modal](https://github.com/Web3Modal/web3modal) 库。Web3Modal 是一个易于使用的库，可帮助开发人员轻松地让他们的用户使用各种不同的钱包连接到您的 dApp。默认情况下，Web3Modal 库支持注入的提供程序，如（Metamask、Dapper、Gnosis Safe、Frame、Web3 浏览器等）和 WalletConnect，您还可以轻松配置库以支持 Portis, Fortmatic, Squarelink, Torus, Authereum, D'CENT Wallet 和 Arkane. （这是[Codesandbox.io](https://codesandbox.io/s/j43b10)上的一个例子）

- 打开指向`my-app`目录的终端并执行此命令

```
npm install web3modal
```

- 在同一个终端也安装`ethers.js`

```
npm install ethers
```

- 在您的 `my-app/public `文件夹中，下载[此图像](https://raw.githubusercontent.com/LearnWeb3DAO/Whitelist-Dapp/318d87d52b166a89be8f485fc1d9d7acf131e3bd/my-app/public/crypto-devs.svg)并将其重命名为`crypto-devs.svg`

- 现在转到样式文件夹并用`Home.modules.css`以下代码替换文件的所有内容，这将为您的 dapp 添加一些样式：

```
.main {
  min-height: 90vh;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  font-family: "Courier New", Courier, monospace;
}

.footer {
  display: flex;
  padding: 2rem 0;
  border-top: 1px solid #eaeaea;
  justify-content: center;
  align-items: center;
}

.image {
  width: 70%;
  height: 50%;
  margin-left: 20%;
}

.title {
  font-size: 2rem;
  margin: 2rem 0;
}

.description {
  line-height: 1;
  margin: 2rem 0;
  font-size: 1.2rem;
}

.button {
  border-radius: 4px;
  background-color: blue;
  border: none;
  color: #ffffff;
  font-size: 15px;
  padding: 20px;
  width: 200px;
  cursor: pointer;
  margin-bottom: 2%;
}
@media (max-width: 1000px) {
  .main {
    width: 100%;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
}
```

- 打开 pages 文件夹下的 index.js 文件并粘贴以下代码，代码解释可以在评论中找到。如果您不熟悉React 和React Hooks,，请务必阅读它们的教程。

```

import Head from "next/head";
import styles from "../styles/Home.module.css";
import Web3Modal from "web3modal";
import { providers, Contract } from "ethers";
import { useEffect, useRef, useState } from "react";
import { WHITELIST_CONTRACT_ADDRESS, abi } from "../constants";

export default function Home() {
  // walletConnected keep track of whether the user's wallet is connected or not
  const [walletConnected, setWalletConnected] = useState(false);
  // joinedWhitelist keeps track of whether the current metamask address has joined the Whitelist or not
  const [joinedWhitelist, setJoinedWhitelist] = useState(false);
  // loading is set to true when we are waiting for a transaction to get mined
  const [loading, setLoading] = useState(false);
  // numberOfWhitelisted tracks the number of addresses's whitelisted
  const [numberOfWhitelisted, setNumberOfWhitelisted] = useState(0);
  // Create a reference to the Web3 Modal (used for connecting to Metamask) which persists as long as the page is open
  const web3ModalRef = useRef();

  /**
   * Returns a Provider or Signer object representing the Ethereum RPC with or without the
   * signing capabilities of metamask attached
   *
   * A `Provider` is needed to interact with the blockchain - reading transactions, reading balances, reading state, etc.
   *
   * A `Signer` is a special type of Provider used in case a `write` transaction needs to be made to the blockchain, which involves the connected account
   * needing to make a digital signature to authorize the transaction being sent. Metamask exposes a Signer API to allow your website to
   * request signatures from the user using Signer functions.
   *
   * @param {*} needSigner - True if you need the signer, default false otherwise
   */
  const getProviderOrSigner = async (needSigner = false) => {
    // Connect to Metamask
    // Since we store `web3Modal` as a reference, we need to access the `current` value to get access to the underlying object
    const provider = await web3ModalRef.current.connect();
    const web3Provider = new providers.Web3Provider(provider);

    // If user is not connected to the Rinkeby network, let them know and throw an error
    const { chainId } = await web3Provider.getNetwork();
    if (chainId !== 4) {
      window.alert("Change the network to Rinkeby");
      throw new Error("Change network to Rinkeby");
    }

    if (needSigner) {
      const signer = web3Provider.getSigner();
      return signer;
    }
    return web3Provider;
  };

  /**
   * addAddressToWhitelist: Adds the current connected address to the whitelist
   */
  const addAddressToWhitelist = async () => {
    try {
      // We need a Signer here since this is a 'write' transaction.
      const signer = await getProviderOrSigner(true);
      // Create a new instance of the Contract with a Signer, which allows
      // update methods
      const whitelistContract = new Contract(
        WHITELIST_CONTRACT_ADDRESS,
        abi,
        signer
      );
      // call the addAddressToWhitelist from the contract
      const tx = await whitelistContract.addAddressToWhitelist();
      setLoading(true);
      // wait for the transaction to get mined
      await tx.wait();
      setLoading(false);
      // get the updated number of addresses in the whitelist
      await getNumberOfWhitelisted();
      setJoinedWhitelist(true);
    } catch (err) {
      console.error(err);
    }
  };

  /**
   * getNumberOfWhitelisted:  gets the number of whitelisted addresses
   */
  const getNumberOfWhitelisted = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // No need for the Signer here, as we are only reading state from the blockchain
      const provider = await getProviderOrSigner();
      // We connect to the Contract using a Provider, so we will only
      // have read-only access to the Contract
      const whitelistContract = new Contract(
        WHITELIST_CONTRACT_ADDRESS,
        abi,
        provider
      );
      // call the numAddressesWhitelisted from the contract
      const _numberOfWhitelisted = await whitelistContract.numAddressesWhitelisted();
      setNumberOfWhitelisted(_numberOfWhitelisted);
    } catch (err) {
      console.error(err);
    }
  };

  /**
   * checkIfAddressInWhitelist: Checks if the address is in whitelist
   */
  const checkIfAddressInWhitelist = async () => {
    try {
      // We will need the signer later to get the user's address
      // Even though it is a read transaction, since Signers are just special kinds of Providers,
      // We can use it in it's place
      const signer = await getProviderOrSigner(true);
      const whitelistContract = new Contract(
        WHITELIST_CONTRACT_ADDRESS,
        abi,
        signer
      );
      // Get the address associated to the signer which is connected to  MetaMask
      const address = await signer.getAddress();
      // call the whitelistedAddresses from the contract
      const _joinedWhitelist = await whitelistContract.whitelistedAddresses(
        address
      );
      setJoinedWhitelist(_joinedWhitelist);
    } catch (err) {
      console.error(err);
    }
  };

  /*
    connectWallet: Connects the MetaMask wallet
  */
  const connectWallet = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // When used for the first time, it prompts the user to connect their wallet
      await getProviderOrSigner();
      setWalletConnected(true);

      checkIfAddressInWhitelist();
      getNumberOfWhitelisted();
    } catch (err) {
      console.error(err);
    }
  };

  /*
    renderButton: Returns a button based on the state of the dapp
  */
  const renderButton = () => {
    if (walletConnected) {
      if (joinedWhitelist) {
        return (
          <div className={styles.description}>
            Thanks for joining the Whitelist!
          </div>
        );
      } else if (loading) {
        return <button className={styles.button}>Loading...</button>;
      } else {
        return (
          <button onClick={addAddressToWhitelist} className={styles.button}>
            Join the Whitelist
          </button>
        );
      }
    } else {
      return (
        <button onClick={connectWallet} className={styles.button}>
          Connect your wallet
        </button>
      );
    }
  };

  // useEffects are used to react to changes in state of the website
  // The array at the end of function call represents what state changes will trigger this effect
  // In this case, whenever the value of `walletConnected` changes - this effect will be called
  useEffect(() => {
    // if wallet is not connected, create a new instance of Web3Modal and connect the MetaMask wallet
    if (!walletConnected) {
      // Assign the Web3Modal class to the reference object by setting it's `current` value
      // The `current` value is persisted throughout as long as this page is open
      web3ModalRef.current = new Web3Modal({
        network: "rinkeby",
        providerOptions: {},
        disableInjectedProvider: false,
      });
      connectWallet();
    }
  }, [walletConnected]);

  return (
    <div>
      <Head>
        <title>Whitelist Dapp</title>
        <meta name="description" content="Whitelist-Dapp" />
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <div className={styles.main}>
        <div>
          <h1 className={styles.title}>Welcome to Crypto Devs!</h1>
          <div className={styles.description}>
            Its an NFT collection for developers in Crypto.
          </div>
          <div className={styles.description}>
            {numberOfWhitelisted} have already joined the Whitelist
          </div>
          {renderButton()}
        </div>
        <div>
          <img className={styles.image} src="./crypto-devs.svg" />
        </div>
      </div>

      <footer className={styles.footer}>
        Made with &#10084; by Crypto Devs
      </footer>
    </div>
  );
}
```

- 现在在 my-app 文件夹下创建一个新文件夹并将其命名为`constants`。
- 在`constants `文件夹中创建一个文件`index.js`然后粘贴以下代码。
- 替换`"YOUR_WHITELIST_CONTRACT_ADDRESS"`为您部署的白名单合约的地址。
- 替换`"YOUR_ABI"`为您的白名单合约的 ABI。要为您的合同获取 ABI，请转到您的`hardhat-tutorial/artifacts/contracts/Whitelist.sol`文件夹并从您的`Whitelist.json`文件中获取标记在`"abi"`密钥下的数组（这将是一个巨大的数组，接近 100 行）。

```
export const abi = YOUR_ABI;
export const WHITELIST_CONTRACT_ADDRESS = "YOUR_WHITELIST_CONTRACT_ADDRESS";
```

- 现在在指向my-app文件夹的终端中，执行

```
npm run dev
```

您的白名单 dapp 现在应该可以正常运行了🚀


### 推送到github

确保在继续之前您已将所有代码[推送到 github](https://medium.com/hackernoon/a-gentle-introduction-to-git-and-github-the-eli5-way-43f0aa64f2e4) :)


## 部署你的 DApp

我们现在将部署您的 dApp，以便每个人都可以看到您的网站，并且您可以与所有 LearnWeb3 DAO 朋友分享它。

- 转到[Vercel](https://vercel.com/)并使用您的 GitHub 登录
- 然后单击按钮，然后选择您的白名单 dApp 存储库`New Project`

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220710143130.png)

- 在配置您的新项目时，Vercel 将允许您自定义您的`Root Directory`
- 单击`Edit`旁边`Root Directory`并将其设置为m`y-app`
- 选择框架为`Next.js`
- 点击`Deploy`
- 现在，您可以通过转到仪表板、选择您的项目并从那里复制 URL 来查看您部署的网站！


## 确认

要验证此级别，请确保您已将合约中的某些地址列入白名单。在智能合约验证框中输入您的合约地址，然后选择您部署的测试网络。


> 原文：[https://www.learnweb3.io/tracks/sophomore/whitelist-dapp](https://www.learnweb3.io/tracks/sophomore/whitelist-dapp)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)

