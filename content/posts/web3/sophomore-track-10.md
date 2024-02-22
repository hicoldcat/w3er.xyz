---
title: Web3系列教程之入门篇---10. ICO（首次代币发行）
description: null
author: 李留白
weight: 0
date: 2022-07-10T08:22:55.581Z
lastmod: 2022-10-12T02:11:22.609Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703215340.png
---

现在是时候让你为Crypto Devs推出一个代币了。让我们把这个代币称为` "Crypto Dev Token"`。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220710155754.png)

## 要求

- 最多应该有10,000个CD代币。
- 每个Crypto Dev NFT持有人都应该免费获得10个代币，但他们必须支付汽油费。
- 在ICO的时候，一个CD的价格应该是 ` 0.001 ether`。
- 应该有一个用户可以访问的ICO网站。

让我们开始建设🚀

## 先决条件

- 你必须已经完成了[NFT-Collection的教程](https://w3er.xyz/posts/web3/sophomore-track-9/)

## 理论

- 什么是ERC20？
  - ERC-20是一个技术标准；它用于以太坊区块链上所有智能合约的代币实施，并提供了一个所有基于以太坊的代币必须遵循的规则清单。
  - 在前进之前，请查看所有ERC20的[functions ](https://docs.openzeppelin.com/contracts/2.x/api/token/erc20)

## 智能合约

为了构建智能合约，我们将使用[Hardhat](https://hardhat.org/).Hardhat是一个Ethereum开发环境和框架，为Solidity的全栈开发而设计。简单地说，你可以编写你的智能合约，部署它们，运行测试，并调试你的代码。

- 要设置一个Hardhat项目，请打开终端并执行这些命令

```
mkdir ICO
cd ICO
mkdir hardhat-tutorial
cd hardhat-tutorial
npm init --yes
npm install --save-dev hardhat
```

- 在你安装Hardhat的同一目录下运行。

```
npx hardhat
```


- 选择`Create a basic sample project`
- 按回车键指定`Hardhat Project root`
- 如果您想添加一个问题，请按 Enter 键.gitignore
- 按回车键`Do you want to install this sample project's dependencies with npm (@nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers)?`

现在你有一个Hardhat项目准备好了！

如果您不在 Mac 上，请执行此额外步骤并安装这些库：)

```
npm install --save-dev @nomiclabs/hardhat-waffle ethereum-waffle chai @nomiclabs/hardhat-ethers ethers
```

- 在同一个终端中，现在安装@openzeppelin/contracts，因为我们将在我们的`CryptoDevToken`合约中导入[Openzeppelin的ERC20合约](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol)和[Openzeppelin的可拥有合约](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol)。

```
npm install @openzeppelin/contracts
```

- 我们将需要调用你为上一级别部署的`CryptoDevs Contract`，以检查CryptoDev NFT的所有者。由于我们只需要调用`tokenOfOwnerByIndex`和`balanceOf`方法，我们可以为`CryptoDevs contract`创建一个只有这两个函数的接口。这样我们就可以节省气体，因为我们不需要继承和部署整个`CryptoDevs contract`，只需要继承和部署其中的一部分。

- 在`contracts`目录内创建一个新的文件，将其称为`ICryptoDevs.sol`，并添加以下几行

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.10;

interface ICryptoDevs {
    /**
     * @dev Returns a token ID owned by `owner` at a given `index` of its token list.
     * Use along with {balanceOf} to enumerate all of ``owner``'s tokens.
     */
    function tokenOfOwnerByIndex(address owner, uint256 index)
        external
        view
        returns (uint256 tokenId);

    /**
     * @dev Returns the number of tokens in ``owner``'s account.
     */
    function balanceOf(address owner) external view returns (uint256 balance);
}

```

- 在`contracts`目录下创建一个新的文件，将其称为`CryptoDevToken.sol`，并添加以下几行

```
 // SPDX-License-Identifier: MIT
  pragma solidity ^0.8.10;

  import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
  import "@openzeppelin/contracts/access/Ownable.sol";
  import "./ICryptoDevs.sol";

  contract CryptoDevToken is ERC20, Ownable {
      // Price of one Crypto Dev token
      uint256 public constant tokenPrice = 0.001 ether;
      // Each NFT would give the user 10 tokens
      // It needs to be represented as 10 * (10 ** 18) as ERC20 tokens are represented by the smallest denomination possible for the token
      // By default, ERC20 tokens have the smallest denomination of 10^(-18). This means, having a balance of (1)
      // is actually equal to (10 ^ -18) tokens.
      // Owning 1 full token is equivalent to owning (10^18) tokens when you account for the decimal places.
      // More information on this can be found in the Freshman Track Cryptocurrency tutorial.
      uint256 public constant tokensPerNFT = 10 * 10**18;
      // the max total supply is 10000 for Crypto Dev Tokens
      uint256 public constant maxTotalSupply = 10000 * 10**18;
      // CryptoDevsNFT contract instance
      ICryptoDevs CryptoDevsNFT;
      // Mapping to keep track of which tokenIds have been claimed
      mapping(uint256 => bool) public tokenIdsClaimed;

      constructor(address _cryptoDevsContract) ERC20("Crypto Dev Token", "CD") {
          CryptoDevsNFT = ICryptoDevs(_cryptoDevsContract);
      }

      /**
       * @dev Mints `amount` number of CryptoDevTokens
       * Requirements:
       * - `msg.value` should be equal or greater than the tokenPrice * amount
       */
      function mint(uint256 amount) public payable {
          // the value of ether that should be equal or greater than tokenPrice * amount;
          uint256 _requiredAmount = tokenPrice * amount;
          require(msg.value >= _requiredAmount, "Ether sent is incorrect");
          // total tokens + amount <= 10000, otherwise revert the transaction
          uint256 amountWithDecimals = amount * 10**18;
          require(
              (totalSupply() + amountWithDecimals) <= maxTotalSupply,
              "Exceeds the max total supply available."
          );
          // call the internal function from Openzeppelin's ERC20 contract
          _mint(msg.sender, amountWithDecimals);
      }

      /**
       * @dev Mints tokens based on the number of NFT's held by the sender
       * Requirements:
       * balance of Crypto Dev NFT's owned by the sender should be greater than 0
       * Tokens should have not been claimed for all the NFTs owned by the sender
       */
      function claim() public {
          address sender = msg.sender;
          // Get the number of CryptoDev NFT's held by a given sender address
          uint256 balance = CryptoDevsNFT.balanceOf(sender);
          // If the balance is zero, revert the transaction
          require(balance > 0, "You dont own any Crypto Dev NFT's");
          // amount keeps track of number of unclaimed tokenIds
          uint256 amount = 0;
          // loop over the balance and get the token ID owned by `sender` at a given `index` of its token list.
          for (uint256 i = 0; i < balance; i++) {
              uint256 tokenId = CryptoDevsNFT.tokenOfOwnerByIndex(sender, i);
              // if the tokenId has not been claimed, increase the amount
              if (!tokenIdsClaimed[tokenId]) {
                  amount += 1;
                  tokenIdsClaimed[tokenId] = true;
              }
          }
          // If all the token Ids have been claimed, revert the transaction;
          require(amount > 0, "You have already claimed all the tokens");
          // call the internal function from Openzeppelin's ERC20 contract
          // Mint (amount * 10) tokens for each NFT
          _mint(msg.sender, amount * tokensPerNFT);
      }

      /**
        * @dev withdraws all ETH and tokens sent to the contract
        * Requirements: 
        * wallet connected must be owner's address
        */
      function withdraw() public onlyOwner {
        address _owner = owner();
        uint256 amount = address(this).balance;
        (bool sent, ) = _owner.call{value: amount}("");
        require(sent, "Failed to send Ether");
      }

      // Function to receive Ether. msg.data must be empty
      receive() external payable {}

      // Fallback function is called when msg.data is not empty
      fallback() external payable {}
  }

```

- 现在我们将安装`dotenv`包，以便能够导入env文件并在我们的配置中使用它。打开一个终端，指向`thardhat-tutorial`目录，执行以下命令

```
npm install dotenv
```

- 现在在`hardhat-tutorial`文件夹中创建一个`.env`文件，并添加以下几行，使用注释中的说明来获得你的`Alchemy API Key URL`和`RINKEBY Private Key`。确保你获得`RINKEBY Private Key`的账户有Rinkeby的以太币资金。

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

- 让我们把合同部署到rinkeby网络。在`scripts`文件夹下创建一个名为`deploy.js`的新文件

- 现在我们要写一些代码，在`deploy.js`文件中部署合同。

```
const { ethers } = require("hardhat");
require("dotenv").config({ path: ".env" });
const { CRYPTO_DEVS_NFT_CONTRACT_ADDRESS } = require("../constants");

async function main() {
  // Address of the Crypto Devs NFT contract that you deployed in the previous module
  const cryptoDevsNFTContract = CRYPTO_DEVS_NFT_CONTRACT_ADDRESS;

  /*
    A ContractFactory in ethers.js is an abstraction used to deploy new smart contracts,
    so cryptoDevsTokenContract here is a factory for instances of our CryptoDevToken contract.
    */
  const cryptoDevsTokenContract = await ethers.getContractFactory(
    "CryptoDevToken"
  );

  // deploy the contract
  const deployedCryptoDevsTokenContract = await cryptoDevsTokenContract.deploy(
    cryptoDevsNFTContract
  );

  // print the address of the deployed contract
  console.log(
    "Crypto Devs Token Contract Address:",
    deployedCryptoDevsTokenContract.address
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

- 你会看到，`deploy.js`文件需要一个常量。让我们在`hardhat-tutorial`文件夹下创建一个`constant`文件夹。
- 在`constants `文件夹中创建一个名为`index.js`的新文件，并在其中添加以下几行。
  - 用你在前一个模块(NFT-Collection)中部署的`CryptoDevs.sol`的地址替换 `"address-of-the-nft-contract"`。

```
// Address of the NFT Contract that you deployed
const CRYPTO_DEVS_NFT_CONTRACT_ADDRESS = "address-of-the-nft-contract";

module.exports = { CRYPTO_DEVS_NFT_CONTRACT_ADDRESS };
```

- 现在打开`hardhat.config.js`文件，我们将在这里添加`rinkeby`网络，这样我们就可以把我们的合同部署到rinkeby。将`hardhat.config.js`文件中的所有行替换为下面给出的行

```
require("@nomiclabs/hardhat-waffle");
require("dotenv").config({ path: ".env" });

const ALCHEMY_API_KEY_URL = process.env.ALCHEMY_API_KEY_URL;

const RINKEBY_PRIVATE_KEY = process.env.RINKEBY_PRIVATE_KEY;

module.exports = {
  solidity: "0.8.10",
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

- 将打印在终端上的CryptoDevToken合同地址保存在你的记事本中，你将在接下来的教程中需要它。

## 网站

- 为了开发该网站，我们将使用React和Next Js。React是一个用于制作网站的javascript框架，而Next Js是建立在React之上的。

- 首先，你将需要创建一个新的`next `应用程序。你的文件夹结构应该是这样的

```
    - ICO
       - hardhat-tutorial
       - my-app
```

- 要创建这个`my-app`，在终端指向ICO文件夹并输入

```
    npx create-next-app@latest
```
并对所有问题按回车键

- 现在要运行该应用程序，在终端执行这些命令

```
cd my-app
npm run dev
```

- 现在去`http://localhost:3000`，你的应用程序应该正在运行 🤘。
- 现在让我们安装Web3Modal库（https://github.com/Web3Modal/web3modal）。Web3Modal是一个易于使用的库，帮助开发者通过简单的可定制配置在他们的应用程序中添加对多个供应商的支持。默认情况下，Web3Modal库支持注入的提供者，如（Metamask、Dapper、Gnosis Safe、Frame、Web3 Browsers等），你也可以轻松配置该库以支持Portis、Fortmatic、Squarelink、Torus、Authereum、D'CENT Wallet和Arkane。打开终端，指向`my-app`目录，执行以下命令

```
  npm install web3modal
```

- 在同一终端中也安装`ethers.js`

```
npm install ethers
```

- 在你的公共文件夹中，下载以下图片（https://github.com/LearnWeb3DAO/NFT-Collection/tree/main/my-app/public/cryptodevs/0.svg）。确保下载的图像的名称是0.svg
- 现在去style文件夹，用以下代码替换`Home.modules.css`文件的所有内容，这将给你的dapp添加一些样式。

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

.input {
  width: 200px;
  height: 100%;
  padding: 1%;
  margin-bottom: 2%;
  box-shadow: 0 0 15px 4px rgba(0, 0, 0, 0.06);
  border-radius: 10px;
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
  padding: 5px;
  width: 100px;
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

- 打开page文件夹下的index.js文件，粘贴以下代码，代码的解释可以在评论中找到。

```
import { BigNumber, Contract, providers, utils } from "ethers";
import Head from "next/head";
import React, { useEffect, useRef, useState } from "react";
import Web3Modal from "web3modal";
import {
  NFT_CONTRACT_ABI,
  NFT_CONTRACT_ADDRESS,
  TOKEN_CONTRACT_ABI,
  TOKEN_CONTRACT_ADDRESS,
} from "../constants";
import styles from "../styles/Home.module.css";

export default function Home() {
  // Create a BigNumber `0`
  const zero = BigNumber.from(0);
  // walletConnected keeps track of whether the user's wallet is connected or not
  const [walletConnected, setWalletConnected] = useState(false);
  // loading is set to true when we are waiting for a transaction to get mined
  const [loading, setLoading] = useState(false);
  // tokensToBeClaimed keeps track of the number of tokens that can be claimed
  // based on the Crypto Dev NFT's held by the user for which they havent claimed the tokens
  const [tokensToBeClaimed, setTokensToBeClaimed] = useState(zero);
  // balanceOfCryptoDevTokens keeps track of number of Crypto Dev tokens owned by an address
  const [balanceOfCryptoDevTokens, setBalanceOfCryptoDevTokens] = useState(
    zero
  );
  // amount of the tokens that the user wants to mint
  const [tokenAmount, setTokenAmount] = useState(zero);
  // tokensMinted is the total number of tokens that have been minted till now out of 10000(max total supply)
  const [tokensMinted, setTokensMinted] = useState(zero);
  // isOwner gets the owner of the contract through the signed address
  const [isOwner, setIsOwner] = useState(false);
  // Create a reference to the Web3 Modal (used for connecting to Metamask) which persists as long as the page is open
  const web3ModalRef = useRef();

  /**
   * getTokensToBeClaimed: checks the balance of tokens that can be claimed by the user
   */
  const getTokensToBeClaimed = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // No need for the Signer here, as we are only reading state from the blockchain
      const provider = await getProviderOrSigner();
      // Create an instance of NFT Contract
      const nftContract = new Contract(
        NFT_CONTRACT_ADDRESS,
        NFT_CONTRACT_ABI,
        provider
      );
      // Create an instance of tokenContract
      const tokenContract = new Contract(
        TOKEN_CONTRACT_ADDRESS,
        TOKEN_CONTRACT_ABI,
        provider
      );
      // We will get the signer now to extract the address of the currently connected MetaMask account
      const signer = await getProviderOrSigner(true);
      // Get the address associated to the signer which is connected to  MetaMask
      const address = await signer.getAddress();
      // call the balanceOf from the NFT contract to get the number of NFT's held by the user
      const balance = await nftContract.balanceOf(address);
      // balance is a Big number and thus we would compare it with Big number `zero`
      if (balance === zero) {
        setTokensToBeClaimed(zero);
      } else {
        // amount keeps track of the number of unclaimed tokens
        var amount = 0;
        // For all the NFT's, check if the tokens have already been claimed
        // Only increase the amount if the tokens have not been claimed
        // for a an NFT(for a given tokenId)
        for (var i = 0; i < balance; i++) {
          const tokenId = await nftContract.tokenOfOwnerByIndex(address, i);
          const claimed = await tokenContract.tokenIdsClaimed(tokenId);
          if (!claimed) {
            amount++;
          }
        }
        //tokensToBeClaimed has been initialized to a Big Number, thus we would convert amount
        // to a big number and then set its value
        setTokensToBeClaimed(BigNumber.from(amount));
      }
    } catch (err) {
      console.error(err);
      setTokensToBeClaimed(zero);
    }
  };

  /**
   * getBalanceOfCryptoDevTokens: checks the balance of Crypto Dev Tokens's held by an address
   */
  const getBalanceOfCryptoDevTokens = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // No need for the Signer here, as we are only reading state from the blockchain
      const provider = await getProviderOrSigner();
      // Create an instace of token contract
      const tokenContract = new Contract(
        TOKEN_CONTRACT_ADDRESS,
        TOKEN_CONTRACT_ABI,
        provider
      );
      // We will get the signer now to extract the address of the currently connected MetaMask account
      const signer = await getProviderOrSigner(true);
      // Get the address associated to the signer which is connected to  MetaMask
      const address = await signer.getAddress();
      // call the balanceOf from the token contract to get the number of tokens held by the user
      const balance = await tokenContract.balanceOf(address);
      // balance is already a big number, so we dont need to convert it before setting it
      setBalanceOfCryptoDevTokens(balance);
    } catch (err) {
      console.error(err);
      setBalanceOfCryptoDevTokens(zero);
    }
  };

  /**
   * mintCryptoDevToken: mints `amount` number of tokens to a given address
   */
  const mintCryptoDevToken = async (amount) => {
    try {
      // We need a Signer here since this is a 'write' transaction.
      // Create an instance of tokenContract
      const signer = await getProviderOrSigner(true);
      // Create an instance of tokenContract
      const tokenContract = new Contract(
        TOKEN_CONTRACT_ADDRESS,
        TOKEN_CONTRACT_ABI,
        signer
      );
      // Each token is of `0.001 ether`. The value we need to send is `0.001 * amount`
      const value = 0.001 * amount;
      const tx = await tokenContract.mint(amount, {
        // value signifies the cost of one crypto dev token which is "0.001" eth.
        // We are parsing `0.001` string to ether using the utils library from ethers.js
        value: utils.parseEther(value.toString()),
      });
      setLoading(true);
      // wait for the transaction to get mined
      await tx.wait();
      setLoading(false);
      window.alert("Sucessfully minted Crypto Dev Tokens");
      await getBalanceOfCryptoDevTokens();
      await getTotalTokensMinted();
      await getTokensToBeClaimed();
    } catch (err) {
      console.error(err);
    }
  };

  /**
   * claimCryptoDevTokens: Helps the user claim Crypto Dev Tokens
   */
  const claimCryptoDevTokens = async () => {
    try {
      // We need a Signer here since this is a 'write' transaction.
      // Create an instance of tokenContract
      const signer = await getProviderOrSigner(true);
      // Create an instance of tokenContract
      const tokenContract = new Contract(
        TOKEN_CONTRACT_ADDRESS,
        TOKEN_CONTRACT_ABI,
        signer
      );
      const tx = await tokenContract.claim();
      setLoading(true);
      // wait for the transaction to get mined
      await tx.wait();
      setLoading(false);
      window.alert("Sucessfully claimed Crypto Dev Tokens");
      await getBalanceOfCryptoDevTokens();
      await getTotalTokensMinted();
      await getTokensToBeClaimed();
    } catch (err) {
      console.error(err);
    }
  };

  /**
   * getTotalTokensMinted: Retrieves how many tokens have been minted till now
   * out of the total supply
   */
  const getTotalTokensMinted = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // No need for the Signer here, as we are only reading state from the blockchain
      const provider = await getProviderOrSigner();
      // Create an instance of token contract
      const tokenContract = new Contract(
        TOKEN_CONTRACT_ADDRESS,
        TOKEN_CONTRACT_ABI,
        provider
      );
      // Get all the tokens that have been minted
      const _tokensMinted = await tokenContract.totalSupply();
      setTokensMinted(_tokensMinted);
    } catch (err) {
      console.error(err);
    }
  };

  /**
   * getOwner: gets the contract owner by connected address
   */
  const getOwner = async () => {
    try {
      const provider = await getProviderOrSigner();
      const nftContract = new Contract(TOKEN_CONTRACT_ADDRESS, TOKEN_CONTRACT_ABI, provider);
      // call the owner function from the contract
      const _owner = await tokenContract.owner();
      // we get signer to extract address of currently connected Metamask account
      const signer = await getProviderOrSigner(true);
      // Get the address associated to signer which is connected to Metamask
      const address = await signer.getAddress();
      if (address.toLowerCase() === _owner.toLowerCase()) {
        setIsOwner(true);
      }
    } catch (err) {
      console.error(err.message);
    }
  };

  /**
   * withdrawCoins: withdraws ether and tokens by calling 
   * the withdraw function in the contract
   */
  const withdrawCoins = async () => {
    try {
      const signer = await getProviderOrSigner(true);
      const tokenContract = new Contract(
        TOKEN_CONTRACT_ADDRESS,
        TOKEN_CONTRACT_ABI,
        signer
      );

      const tx = await tokenContract.withdraw();
      setLoading(true);
      await tx.wait();
      setLoading(false);
      await getOwner();
    } catch (err) {
      console.error(err);
    }
  }

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

  /*
        connectWallet: Connects the MetaMask wallet
      */
  const connectWallet = async () => {
    try {
      // Get the provider from web3Modal, which in our case is MetaMask
      // When used for the first time, it prompts the user to connect their wallet
      await getProviderOrSigner();
      setWalletConnected(true);
    } catch (err) {
      console.error(err);
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
      getTotalTokensMinted();
      getBalanceOfCryptoDevTokens();
      getTokensToBeClaimed();
      withdrawCoins();
    }
  }, [walletConnected]);

  /*
        renderButton: Returns a button based on the state of the dapp
      */
  const renderButton = () => {
    // If we are currently waiting for something, return a loading button
    if (loading) {
      return (
        <div>
          <button className={styles.button}>Loading...</button>
        </div>
      );
    }
    // if owner is connected, withdrawCoins() is called
    if (walletConnected && isOwner) {
      return (
        <div>
          <button className={styles.button1} onClick={withdrawCoins}>
            Withdraw Coins
          </button>
        </div>
      );
    }
    // If tokens to be claimed are greater than 0, Return a claim button
    if (tokensToBeClaimed > 0) {
      return (
        <div>
          <div className={styles.description}>
            {tokensToBeClaimed * 10} Tokens can be claimed!
          </div>
          <button className={styles.button} onClick={claimCryptoDevTokens}>
            Claim Tokens
          </button>
        </div>
      );
    }
    // If user doesn't have any tokens to claim, show the mint button
    return (
      <div style={{ display: "flex-col" }}>
        <div>
          <input
            type="number"
            placeholder="Amount of Tokens"
            // BigNumber.from converts the `e.target.value` to a BigNumber
            onChange={(e) => setTokenAmount(BigNumber.from(e.target.value))}
            className={styles.input}
          />
        </div>

        <button
          className={styles.button}
          disabled={!(tokenAmount > 0)}
          onClick={() => mintCryptoDevToken(tokenAmount)}
        >
          Mint Tokens
        </button>
      </div>
    );
  };

  return (
    <div>
      <Head>
        <title>Crypto Devs</title>
        <meta name="description" content="ICO-Dapp" />
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <div className={styles.main}>
        <div>
          <h1 className={styles.title}>Welcome to Crypto Devs ICO!</h1>
          <div className={styles.description}>
            You can claim or mint Crypto Dev tokens here
          </div>
          {walletConnected ? (
            <div>
              <div className={styles.description}>
                {/* Format Ether helps us in converting a BigNumber to string */}
                You have minted {utils.formatEther(balanceOfCryptoDevTokens)} Crypto
                Dev Tokens
              </div>
              <div className={styles.description}>
                {/* Format Ether helps us in converting a BigNumber to string */}
                Overall {utils.formatEther(tokensMinted)}/10000 have been minted!!!
              </div>
              {renderButton()}
            </div>
          ) : (
            <button onClick={connectWallet} className={styles.button}>
              Connect your wallet
            </button>
          )}
        </div>
        <div>
          <img className={styles.image} src="./0.svg" />
        </div>
      </div>

      <footer className={styles.footer}>
        Made with &#10084; by Crypto Devs
      </footer>
    </div>
  );
}
```

- 现在在my-app文件夹下创建一个新的文件夹，并将其命名为`constants`。
- 在常量文件夹中创建一个文件，`index.js`并粘贴以下代码。
  - 将 `"abi-of-your-nft-contract "`替换为你在上一个教程中部署的NFT合同的abi。
  - 将 `"address-of-your-nft-contract "`替换为你在之前教程中部署的NFT合同的地址。
  - 将 `"abi-of-your-token-contract "`替换为代币合约的abi。要获得代币合约的abi，请进入`hardhat-tutorial/artifacts/contracts/CryptoDevToken.sol`，然后从`CryptoDevToken.json`文件中获得 `"abi "`键下标记的数组。
  - 将 `"address-of-your-token-contract"`替换为你在教程早期保存在记事本上的代币合约地址。

```
export const NFT_CONTRACT_ABI = "abi-of-your-nft-contract";
export const NFT_CONTRACT_ADDRESS = "address-of-your-nft-contract";
export const TOKEN_CONTRACT_ABI = "abi-of-your-token-contract";
export const TOKEN_CONTRACT_ADDRESS = "address-of-your-token-contract";
```

- 现在，在你的终端，也就是指向my-app文件夹，执行

```
npm run dev
```

你的ICO dapp现在应该没有错误地工作了🚀

## 推送到Github

确保在进行下一步之前，[将所有这些代码推送到Github](https://medium.com/hackernoon/a-gentle-introduction-to-git-and-github-the-eli5-way-43f0aa64f2e4)。

## 部署你的DApp

现在我们将部署你的DApp，这样每个人都可以看到你的网站，你可以与所有LearnWeb3 DAO的朋友分享它。

- 进入https://vercel.com/，用你的GitHub登录
- 然后点击新项目按钮，然后选择您的ICO dApp repo
- 在配置你的新项目时，Vercel将允许你定制你的根目录
- 点击根目录旁边的编辑，并将其设置为我的应用程序
- 选择框架预设为Next.js

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220710162130.png)

- 单击 "部署"。
- 现在，你可以通过进入你的仪表板，选择你的项目，并从那里复制URL，看到你部署的网站

## 祝贺你!你们都完成了!

希望你喜欢这个教程。不要忘了分享你的ICO网站：D

> 原文：[https://www.learnweb3.io/tracks/sophomore/initial-coin-offering](https://www.learnweb3.io/tracks/sophomore/initial-coin-offering)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
