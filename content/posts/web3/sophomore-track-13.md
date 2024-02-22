---
title: Web3系列教程之入门篇---13. 像 Uniswap 一样建立自己的去中心化交易所
description: null
author: 李留白
weight: 0
date: 2022-07-17T12:26:58.103Z
lastmod: 2022-10-12T02:11:16.335Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220703215340.png
---

现在是时候为你的`Crypto Dev`代币启动 DeFi 交易所了

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220717192003.png)

## 要求
- 仅使用一个资产对（Eth / Crypto Dev）建立交易所
- 您的去中心化交易所应收取掉1%费用
- 当用户增加流动性时，应该给他们Crypto Dev LP代币（流动性提供者代币）
- 应根据Ether用户愿意增加流动性的比例给予 CD LP 代币

让我们开始开发🚀

## 先决条件

- 您已完成[ICO 教程](https://w3er.xyz/posts/web3/sophomore-track-10/)
- 您已完成[Defi 交换理论教程](https://w3er.xyz/posts/web3/sophomore-track-12/)
- 您已完成[混合主题教程](https://w3er.xyz/posts/web3/sophomore-track-7/)


## 智能合约

为了构建智能合约，我们将使用[Hardhat](https://hardhat.org/)。Hardhat 是一个以太坊开发环境和框架，专为 Solidity 中的全栈开发而设计。简单来说，您可以编写智能合约、部署它们、运行测试和调试代码。

- 要设置安全帽项目，请打开终端并执行以下命令

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

- 选择`Create a Javascript project`
- 按回车键已指定`Hardhat Project root`
- 如果您想添加一个问题，请按 Enter 键`.gitignore`
- 按回车键`Do you want to install this sample project's dependencies with npm (@nomicfoundation/hardhat-toolbox)?`

- 现在你有一个安全帽项目准备好了！

- 如果您在 Windows 上，请执行此额外步骤并安装这些库：)

```bash
npm install --save-dev @nomicfoundation/hardhat-toolbox
```

- 现在在同一个终端中安装`@openzeppelin/contracts`，因为我们将在我们的合同中导入[Openzeppelin 的 ERC20](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/ERC20.sol)`Exchange`合同

 ```bash
  npm install @openzeppelin/contracts
 ```

- 在目录中创建一个`contracts`名为`Exchange.sol`. 在本教程中，我们将分别介绍合同的每个部分

	- 首先让我们从导入开始`ERC20.sol`
	
	  - 我们导入`ERC20.sol`是因为我们的交易所需要铸造和创建`Crypto Dev LP`代币，这就是它需要继承 ERC20.sol 的原因
	
```go
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract Exchange is ERC20 {
}
```
	
	- `constructor`现在让我们为我们的合约创建一个
	
	  - 它将`_CryptoDevToken`您在教程中部署的地址`ICO`作为输入参数
	  - 然后检查地址是否为空地址
	  - 在所有检查之后，它将值分配给`cryptoDevTokenAddress`变量的输入参数
	  - 构造函数还为我们的令牌设置`name`and`symbol``Crypto Dev LP`
	
```go
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract Exchange is ERC20 {

    address public cryptoDevTokenAddress;

    // Exchange is inheriting ERC20, because our exchange would keep track of Crypto Dev LP tokens
    constructor(address _CryptoDevtoken) ERC20("CryptoDev LP Token", "CDLP") {
        require(_CryptoDevtoken != address(0), "Token address passed is a null address");
        cryptoDevTokenAddress = _CryptoDevtoken;
    }
}
```

- 是时候创建一个函数来获取合约持有的`Eth`和代币的储备了。`Crypto Dev`

  - 众所周知，Eth 储备金将等于合约的余额，并且可以使用`address(this).balance`因此我们创建一个仅用于获取`Crypto Dev`代币储备金的函数
  - 我们知道`Crypto Dev Token`我们部署的合约是 ERC20
  - 所以我们可以调用`balanceOf`来检查`CryptoDev Tokens` 合约`address`持有的余额

```go
/**
* @dev Returns the amount of `Crypto Dev Tokens` held by the contract
*/
function getReserve() public view returns (uint) {
    return ERC20(cryptoDevTokenAddress).balanceOf(address(this));
}
```

- 是时候创建一个以合约形式`addLiquidity`添加的函数了`liquidity``Ether``Crypto Dev tokens`

  - 如果`cryptoDevTokenReserve`为零，则表示这是第一次有人向合约添加`Crypto Dev`代币`Ether`

  - 当用户添加初始流动性时，我们不必保持任何比率，因为我们没有任何流动性。因此，我们接受用户在初始调用中发送的任何数量的令牌

  - 如果`cryptoDevTokenReserve`不是零，那么我们必须确保当有人增加流动性时，它不会影响市场当前的价格

  - 为了确保这一点，我们保持一个必须保持不变的比率

  - 比率是`(cryptoDevTokenAmount user can add/cryptoDevTokenReserve in the contract) = (Eth Sent by the user/Eth Reserve in the contract)`

  - `Crypto Dev`这个比率决定了在给定一定数量的 Eth 的情况下用户可以提供多少代币

  - 当用户增加流动性时，我们需要为他提供一些`LP`代币，因为我们需要跟踪他提供给合约的流动性数量

  - `LP`铸造给用户的代币数量与用户`Eth`提供的代币数量成比例

  - 在初始流动性情况下，当没有流动性时：`LP`将铸造给用户的代币数量等于`Eth`合约的余额（因为余额等于`Eth`用户在`addLiquidity`调用中发送的）

  - 当合约中已经存在流动性时，铸造的`LP`代币数量基于一个比率。

  - 比率是`(LP tokens to be sent to the user (liquidity) / totalSupply of LP tokens in contract) = (Eth sent by the user) / (Eth reserve in the contract)`

   ```go
    /**
    * @dev Adds liquidity to the exchange.
    */
    function addLiquidity(uint _amount) public payable returns (uint) {
        uint liquidity;
        uint ethBalance = address(this).balance;
        uint cryptoDevTokenReserve = getReserve();
        ERC20 cryptoDevToken = ERC20(cryptoDevTokenAddress);
        /*
            If the reserve is empty, intake any user supplied value for
            `Ether` and `Crypto Dev` tokens because there is no ratio currently
        */
        if(cryptoDevTokenReserve == 0) {
            // Transfer the `cryptoDevToken` from the user's account to the contract
            cryptoDevToken.transferFrom(msg.sender, address(this), _amount);
            // Take the current ethBalance and mint `ethBalance` amount of LP tokens to the user.
            // `liquidity` provided is equal to `ethBalance` because this is the first time user
            // is adding `Eth` to the contract, so whatever `Eth` contract has is equal to the one supplied
            // by the user in the current `addLiquidity` call
            // `liquidity` tokens that need to be minted to the user on `addLiquidity` call should always be proportional
            // to the Eth specified by the user
            liquidity = ethBalance;
            _mint(msg.sender, liquidity);
            // _mint is ERC20.sol smart contract function to mint ERC20 tokens
        } else {
            /*
                If the reserve is not empty, intake any user supplied value for
                `Ether` and determine according to the ratio how many `Crypto Dev` tokens
                need to be supplied to prevent any large price impacts because of the additional
                liquidity
            */
            // EthReserve should be the current ethBalance subtracted by the value of ether sent by the user
            // in the current `addLiquidity` call
            uint ethReserve =  ethBalance - msg.value;
            // Ratio should always be maintained so that there are no major price impacts when adding liquidity
            // Ratio here is -> (cryptoDevTokenAmount user can add/cryptoDevTokenReserve in the contract) = (Eth Sent by the user/Eth Reserve in the contract);
            // So doing some maths, (cryptoDevTokenAmount user can add) = (Eth Sent by the user * cryptoDevTokenReserve /Eth Reserve);
            uint cryptoDevTokenAmount = (msg.value * cryptoDevTokenReserve)/(ethReserve);
            require(_amount >= cryptoDevTokenAmount, "Amount of tokens sent is less than the minimum tokens required");
            // transfer only (cryptoDevTokenAmount user can add) amount of `Crypto Dev tokens` from users account
            // to the contract
            cryptoDevToken.transferFrom(msg.sender, address(this), cryptoDevTokenAmount);
            // The amount of LP tokens that would be sent to the user should be propotional to the liquidity of
            // ether added by the user
            // Ratio here to be maintained is ->
            // (LP tokens to be sent to the user (liquidity)/ totalSupply of LP tokens in contract) = (Eth sent by the user)/(Eth reserve in the contract)
            // by some maths -> liquidity =  (totalSupply of LP tokens in contract * (Eth sent by the user))/(Eth reserve in the contract)
            liquidity = (totalSupply() * msg.value)/ ethReserve;
            _mint(msg.sender, liquidity);
        }
         return liquidity;
    }
   ```


- 现在让我们`removing liquidity`从合约中创建一个函数。

  - 将发送回用户的以太币数量将基于一个比率
  - 比率是`(Eth sent back to the user) / (current Eth reserve) = (amount of LP tokens that user wants to withdraw) / (total supply of LP tokens)`
  - 将被发送回用户的 Crypto Dev 代币的数量也将基于一个比率
  - 比率是`(Crypto Dev sent back to the user) / (current Crypto Dev token reserve) = (amount of LP tokens that user wants to withdraw) / (total supply of LP tokens)`
  - `LP`用户用于消除流动性的代币数量将被烧毁

  ```go
  /** 
  * @dev Returns the amount Eth/Crypto Dev tokens that would be returned to the user
  * in the swap
  */
  function removeLiquidity(uint _amount) public returns (uint , uint) {
      require(_amount > 0, "_amount should be greater than zero");
      uint ethReserve = address(this).balance;
      uint _totalSupply = totalSupply();
      // The amount of Eth that would be sent back to the user is based
      // on a ratio
      // Ratio is -> (Eth sent back to the user) / (current Eth reserve)
      // = (amount of LP tokens that user wants to withdraw) / (total supply of LP tokens)
      // Then by some maths -> (Eth sent back to the user)
      // = (current Eth reserve * amount of LP tokens that user wants to withdraw) / (total supply of LP tokens)
      uint ethAmount = (ethReserve * _amount)/ _totalSupply;
      // The amount of Crypto Dev token that would be sent back to the user is based
      // on a ratio
      // Ratio is -> (Crypto Dev sent back to the user) / (current Crypto Dev token reserve)
      // = (amount of LP tokens that user wants to withdraw) / (total supply of LP tokens)
      // Then by some maths -> (Crypto Dev sent back to the user)
      // = (current Crypto Dev token reserve * amount of LP tokens that user wants to withdraw) / (total supply of LP tokens)
      uint cryptoDevTokenAmount = (getReserve() * _amount)/ _totalSupply;
      // Burn the sent LP tokens from the user's wallet because they are already sent to
      // remove liquidity
      _burn(msg.sender, _amount);
      // Transfer `ethAmount` of Eth from user's wallet to the contract
      payable(msg.sender).transfer(ethAmount);
      // Transfer `cryptoDevTokenAmount` of Crypto Dev tokens from the user's wallet to the contract
      ERC20(cryptoDevTokenAddress).transfer(msg.sender, cryptoDevTokenAmount);
      return (ethAmount, cryptoDevTokenAmount);
  }
  ```

- 接下来让我们实现交换功能

- 交换有两种方式。一种方法是`Eth`令牌`Crypto Dev`，另一种方法`Crypto Dev`是`Eth`

- 重要的是我们要确定：给定用户发送`x`的 / 代币数量`Eth`，他将从交换中收到`Crypto Dev`多少`Eth`/代币？`Crypto Dev`

- 所以让我们创建一个计算这个的函数：

  - 我们会收费`1%`的。这意味着带有费用的输入代币数量将等于`Input amount with fees = (input amount - (1*(input amount)/100)) = ((input amount)*99)/100`
  - 我们需要遵循`XY = K`曲线的概念
  - 我们需要确定`(x + Δx) * (y - Δy) = x * y`，所以最终的公式是`Δy = (y * Δx) / (x + Δx)`；
  - `Δy`在我们的例子中是`tokens to be received`, `Δx = ((input amount)*99)/100`, `x`= 输入储备, `y`= 输出储备
  - 输入储备和输出储备将取决于我们正在实施的交换。`Eth`令牌或`Crypto Dev`反之亦然

```go
/**
* @dev Returns the amount Eth/Crypto Dev tokens that would be returned to the user
* in the swap
*/
function getAmountOfTokens(
    uint256 inputAmount,
    uint256 inputReserve,
    uint256 outputReserve
) public pure returns (uint256) {
    require(inputReserve > 0 && outputReserve > 0, "invalid reserves");
    // We are charging a fee of `1%`
    // Input amount with fee = (input amount - (1*(input amount)/100)) = ((input amount)*99)/100
    uint256 inputAmountWithFee = inputAmount * 99;
    // Because we need to follow the concept of `XY = K` curve
    // We need to make sure (x + Δx) * (y - Δy) = x * y
    // So the final formula is Δy = (y * Δx) / (x + Δx)
    // Δy in our case is `tokens to be received`
    // Δx = ((input amount)*99)/100, x = inputReserve, y = outputReserve
    // So by putting the values in the formulae you can get the numerator and denominator
    uint256 numerator = inputAmountWithFee * outputReserve;
    uint256 denominator = (inputReserve * 100) + inputAmountWithFee;
    return numerator / denominator;
}
```

  - `Ether`现在让我们实现一个函数来交换`Crypto Dev`令牌

```go
/** 
* @dev Swaps Eth for CryptoDev Tokens
*/
function ethToCryptoDevToken(uint _minTokens) public payable {
    uint256 tokenReserve = getReserve();
    // call the `getAmountOfTokens` to get the amount of Crypto Dev tokens
    // that would be returned to the user after the swap
    // Notice that the `inputReserve` we are sending is equal to  
    // `address(this).balance - msg.value` instead of just `address(this).balance`
    // because `address(this).balance` already contains the `msg.value` user has sent in the given call
    // so we need to subtract it to get the actual input reserve
    uint256 tokensBought = getAmountOfTokens(
        msg.value,
        address(this).balance - msg.value,
        tokenReserve
    );

    require(tokensBought >= _minTokens, "insufficient output amount");
    // Transfer the `Crypto Dev` tokens to the user
    ERC20(cryptoDevTokenAddress).transfer(msg.sender, tokensBought);
}
```

- 现在让我们实现一个函数来交换`Crypto Dev`令牌`Ether`

```go
/** 
* @dev Swaps CryptoDev Tokens for Eth
*/
function cryptoDevTokenToEth(uint _tokensSold, uint _minEth) public {
    uint256 tokenReserve = getReserve();
    // call the `getAmountOfTokens` to get the amount of Eth
    // that would be returned to the user after the swap
    uint256 ethBought = getAmountOfTokens(
        _tokensSold,
        tokenReserve,
        address(this).balance
    );
    require(ethBought >= _minEth, "insufficient output amount");
    // Transfer `Crypto Dev` tokens from the user's address to the contract
    ERC20(cryptoDevTokenAddress).transferFrom(
        msg.sender,
        address(this),
        _tokensSold
    );
    // send the `ethBought` to the user from the contract
    payable(msg.sender).transfer(ethBought);
}
```

- 您的最终合同应如下所示：

```go
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract Exchange is ERC20 {

    address public cryptoDevTokenAddress;

    // Exchange is inheriting ERC20, because our exchange would keep track of Crypto Dev LP tokens
    constructor(address _CryptoDevtoken) ERC20("CryptoDev LP Token", "CDLP") {
        require(_CryptoDevtoken != address(0), "Token address passed is a null address");
        cryptoDevTokenAddress = _CryptoDevtoken;
    }

    /** 
    * @dev Returns the amount of `Crypto Dev Tokens` held by the contract
    */
    function getReserve() public view returns (uint) {
        return ERC20(cryptoDevTokenAddress).balanceOf(address(this));
    }

    /**
    * @dev Adds liquidity to the exchange.
    */
    function addLiquidity(uint _amount) public payable returns (uint) {
        uint liquidity;
        uint ethBalance = address(this).balance;
        uint cryptoDevTokenReserve = getReserve();
        ERC20 cryptoDevToken = ERC20(cryptoDevTokenAddress);
        /* 
            If the reserve is empty, intake any user supplied value for 
            `Ether` and `Crypto Dev` tokens because there is no ratio currently
        */
        if(cryptoDevTokenReserve == 0) {
            // Transfer the `cryptoDevToken` address from the user's account to the contract
            cryptoDevToken.transferFrom(msg.sender, address(this), _amount);
            // Take the current ethBalance and mint `ethBalance` amount of LP tokens to the user.
            // `liquidity` provided is equal to `ethBalance` because this is the first time user 
            // is adding `Eth` to the contract, so whatever `Eth` contract has is equal to the one supplied 
            // by the user in the current `addLiquidity` call
            // `liquidity` tokens that need to be minted to the user on `addLiquidity` call should always be proportional
            // to the Eth specified by the user
            liquidity = ethBalance;
            _mint(msg.sender, liquidity);
        } else {
            /* 
                If the reserve is not empty, intake any user supplied value for 
                `Ether` and determine according to the ratio how many `Crypto Dev` tokens
                need to be supplied to prevent any large price impacts because of the additional
                liquidity
            */
            // EthReserve should be the current ethBalance subtracted by the value of ether sent by the user
            // in the current `addLiquidity` call
            uint ethReserve =  ethBalance - msg.value;
            // Ratio should always be maintained so that there are no major price impacts when adding liquidity
            // Ration here is -> (cryptoDevTokenAmount user can add/cryptoDevTokenReserve in the contract) = (Eth Sent by the user/Eth Reserve in the contract);
            // So doing some maths, (cryptoDevTokenAmount user can add) = (Eth Sent by the user * cryptoDevTokenReserve /Eth Reserve);
            uint cryptoDevTokenAmount = (msg.value * cryptoDevTokenReserve)/(ethReserve);
            require(_amount >= cryptoDevTokenAmount, "Amount of tokens sent is less than the minimum tokens required");
            // transfer only (cryptoDevTokenAmount user can add) amount of `Crypto Dev tokens` from users account
            // to the contract
            cryptoDevToken.transferFrom(msg.sender, address(this), cryptoDevTokenAmount);
            // The amount of LP tokens that would be sent to the user should be propotional to the liquidity of
            // ether added by the user
            // Ratio here to be maintained is -> 
            // (lp tokens to be sent to the user (liquidity)/ totalSupply of LP tokens in contract) = (Eth sent by the user)/(Eth reserve in the contract)
            // by some maths -> liquidity =  (totalSupply of LP tokens in contract * (Eth sent by the user))/(Eth reserve in the contract)
            liquidity = (totalSupply() * msg.value)/ ethReserve;
            _mint(msg.sender, liquidity);
        }
         return liquidity;
    }

    /** 
    * @dev Returns the amount Eth/Crypto Dev tokens that would be returned to the user
    * in the swap
    */
    function removeLiquidity(uint _amount) public returns (uint , uint) {
        require(_amount > 0, "_amount should be greater than zero");
        uint ethReserve = address(this).balance;
        uint _totalSupply = totalSupply();
        // The amount of Eth that would be sent back to the user is based
        // on a ratio 
        // Ratio is -> (Eth sent back to the user/ Current Eth reserve)  
        // = (amount of LP tokens that user wants to withdraw) / (total supply of LP tokens)
        // Then by some maths -> (Eth sent back to the user) 
        // = (current Eth reserve * amount of LP tokens that user wants to withdraw) / (total supply of LP tokens)
        uint ethAmount = (ethReserve * _amount)/ _totalSupply;
        // The amount of Crypto Dev token that would be sent back to the user is based
        // on a ratio 
        // Ratio is -> (Crypto Dev sent back to the user) / (current Crypto Dev token reserve)
        // = (amount of LP tokens that user wants to withdraw) / (total supply of LP tokens)
        // Then by some maths -> (Crypto Dev sent back to the user) 
        // = (current Crypto Dev token reserve * amount of LP tokens that user wants to withdraw) / (total supply of LP tokens)
        uint cryptoDevTokenAmount = (getReserve() * _amount)/ _totalSupply;
        // Burn the sent `LP` tokens from the user's wallet because they are already sent to 
        // remove liquidity
        _burn(msg.sender, _amount);
        // Transfer `ethAmount` of Eth from user's wallet to the contract
        payable(msg.sender).transfer(ethAmount);
        // Transfer `cryptoDevTokenAmount` of `Crypto Dev` tokens from the user's wallet to the contract 
        ERC20(cryptoDevTokenAddress).transfer(msg.sender, cryptoDevTokenAmount);
        return (ethAmount, cryptoDevTokenAmount);
    }

    /** 
    * @dev Returns the amount Eth/Crypto Dev tokens that would be returned to the user
    * in the swap
    */
    function getAmountOfTokens(
        uint256 inputAmount,
        uint256 inputReserve,
        uint256 outputReserve
    ) public pure returns (uint256) {
        require(inputReserve > 0 && outputReserve > 0, "invalid reserves");
        // We are charging a fee of `1%`
        // Input amount with fee = (input amount - (1*(input amount)/100)) = ((input amount)*99)/100
        uint256 inputAmountWithFee = inputAmount * 99;
        // Because we need to follow the concept of `XY = K` curve
        // We need to make sure (x + Δx) * (y - Δy) = x * y
        // So the final formula is Δy = (y * Δx) / (x + Δx)
        // Δy in our case is `tokens to be received`
        // Δx = ((input amount)*99)/100, x = inputReserve, y = outputReserve
        // So by putting the values in the formulae you can get the numerator and denominator
        uint256 numerator = inputAmountWithFee * outputReserve;
        uint256 denominator = (inputReserve * 100) + inputAmountWithFee;
        return numerator / denominator;
    }

    /** 
    * @dev Swaps Eth for CryptoDev Tokens
    */
    function ethToCryptoDevToken(uint _minTokens) public payable {
        uint256 tokenReserve = getReserve();
        // call the `getAmountOfTokens` to get the amount of Crypto Dev tokens
        // that would be returned to the user after the swap
        // Notice that the `inputReserve` we are sending is equal to  
        // `address(this).balance - msg.value` instead of just `address(this).balance`
        // because `address(this).balance` already contains the `msg.value` user has sent in the given call
        // so we need to subtract it to get the actual input reserve
        uint256 tokensBought = getAmountOfTokens(
            msg.value,
            address(this).balance - msg.value,
            tokenReserve
        );

        require(tokensBought >= _minTokens, "insufficient output amount");
        // Transfer the `Crypto Dev` tokens to the user
        ERC20(cryptoDevTokenAddress).transfer(msg.sender, tokensBought);
    }


    /** 
    * @dev Swaps CryptoDev Tokens for Eth
    */
    function cryptoDevTokenToEth(uint _tokensSold, uint _minEth) public {
        uint256 tokenReserve = getReserve();
        // call the `getAmountOfTokens` to get the amount of Eth
        // that would be returned to the user after the swap
        uint256 ethBought = getAmountOfTokens(
            _tokensSold,
            tokenReserve,
            address(this).balance
        );
        require(ethBought >= _minEth, "insufficient output amount");
        // Transfer `Crypto Dev` tokens from the user's address to the contract
        ERC20(cryptoDevTokenAddress).transferFrom(
            msg.sender,
            address(this),
            _tokensSold
        );
        // send the `ethBought` to the user from the contract
        payable(msg.sender).transfer(ethBought);
    }
}
```

- 现在我们将安装`dotenv`包以便能够导入 env 文件并在我们的配置中使用它。打开指向`hardhat-tutorial`目录的终端并执行此命令

```bash
  npm install dotenv
```

- 现在在文件夹中创建一个`.env`文件`hardhat-tutorial`并添加以下行，使用注释中的说明获取您的 Alchemy API 密钥 URL 和 Rinkeby 私钥。确保您获取 Rinkeby 私钥的账户由 Rinkeby 以太币提供资金。



```bash
// Go to https://www.alchemyapi.io, sign up, create
// a new App in its dashboard and select the network as Rinkeby, and replace "add-the-alchemy-key-url-here" with its key url
ALCHEMY_API_KEY_URL="add-the-alchemy-key-url-here"

// Replace this private key with your RINKEBY account private key
// To export your private key from Metamask, open Metamask and
// go to Account Details > Export Private Key
// Be aware of NEVER putting real Ether into testing accounts
RINKEBY_PRIVATE_KEY="add-the-rinkeby-private-key-here"
```

- 让我们还创建一个常量文件夹来跟踪我们拥有的任何常量。在`hardhat-tutorial`文件夹下创建一个名为的新文件夹`constants`，并在该`constants`文件夹下创建一个新文件`index.js`
- 在`index.js`文件中添加以下代码行。记得替换成你在教程中部署的代币合约`ADDRESS-OF-CRYPTO-DEV-TOKEN`的合约地址`Crypto Dev``ICO`

```js
const CRYPTO_DEV_TOKEN_CONTRACT_ADDRESS = "ADDRESS-OF-CRYPTO-DEV-TOKEN";

module.exports = { CRYPTO_DEV_TOKEN_CONTRACT_ADDRESS };
```

- 让我们将合约部署到`rinkeby`网络。创建一个新文件，或替换现有的默认文件，`deploy.js`在`scripts`文件夹下命名
- `deploy.js`现在我们将编写一些代码来在文件中部署合约。

```js
  const { ethers } = require("hardhat");
  require("dotenv").config({ path: ".env" });
  const { CRYPTO_DEV_TOKEN_CONTRACT_ADDRESS } = require("../constants");
  
  async function main() {
    const cryptoDevTokenAddress = CRYPTO_DEV_TOKEN_CONTRACT_ADDRESS;
    /*
    A ContractFactory in ethers.js is an abstraction used to deploy new smart contracts,
    so exchangeContract here is a factory for instances of our Exchange contract.
    */
    const exchangeContract = await ethers.getContractFactory("Exchange");
  
    // here we deploy the contract
    const deployedExchangeContract = await exchangeContract.deploy(
      cryptoDevTokenAddress
    );
    await deployedExchangeContract.deployed();
  
    // print the address of the deployed contract
    console.log("Exchange Contract Address:", deployedExchangeContract.address);
  }
  
  // Call the main function and catch if there is any error
  main()
    .then(() => process.exit(0))
    .catch((error) => {
      console.error(error);
      process.exit(1);
    });
```

- 现在打开`hardhat.config.js`文件，我们将在`rinkeby`此处添加网络，以便我们可以将合约部署到 rinkeby。`hardhat.config.js`用下面给出的行替换文件中的所有行

```js
require("@nomicfoundation/hardhat-toolbox");
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

- 编译合约，打开一个指向`hardhat-tutorial`目录的终端并执行这个命令

```
npx hardhat compile
```

- 要部署，请打开指向`hardhat-tutorial`目录的终端并执行此命令

```
 npx hardhat run scripts/deploy.js --network rinkeby
```

- 将打印在您的终端上的交易所合约地址保存在记事本中，您将在教程的后面进一步需要它。

## 网站

- 为了开发网站，我们将使用[React](https://reactjs.org/)和[Next Js](https://nextjs.org/)。React 是一个用于制作网站的 javascript 框架，Next Js 构建在 React 之上。
- 首先，您需要创建一个新`next`应用程序。您的文件夹结构应该类似于

```
   - DeFi-Exchange
       - hardhat-tutorial
       - my-app
```

- 要创建这个`my-app`，在终端指向 DeFi-Exchange 文件夹并输入

```
  npx create-next-app@latest
```

  并按下`enter`所有问题

- 现在运行应用程序，在终端中执行这些命令

```
cd my-app
npm run dev
```

- 现在转到`http://localhost:3000`，您的应用程序应该正在运行🤘
- 现在让我们安装 Web3Modal 库（https://github.com/Web3Modal/web3modal）。Web3Modal 是一个易于使用的库，可帮助开发人员通过简单的可自定义配置在其应用程序中添加对多个提供程序的支持。默认情况下，Web3Modal 库支持注入的提供程序，例如（Metamask、Dapper、Gnosis Safe、Frame、Web3 浏览器等），您还可以轻松配置库以支持 Portis、Fortmatic、Squarelink、Torus、Authereum、D'CENT 钱包和 Arkane。打开指向`my-app`目录的终端并执行此命令

```
  npm install web3modal
```

- 在同一个终端也安装`ethers.js`

```
npm i ethers
```

- 在您的公用文件夹中，下载此[图像](https://github.com/LearnWeb3DAO/Defi-Exchange/blob/main/my-app/public/cryptodev.svg)并将其重命名为`cryptodev.svg`.
- 现在转到样式文件夹并用`Home.modules.css`以下代码替换文件的所有内容，这将为您的 dapp 添加一些样式：

```css
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
  margin: 2%;
  box-shadow: 0 0 15px 4px rgba(0, 0, 0, 0.06);
  border-radius: 10px;
}

.title {
  font-size: 2rem;
  margin: 2rem 0;
}

.description {
  line-height: 1;
  margin: 2%;
  font-size: 1.2rem;
}

.button {
  border-radius: 4px;
  background-color: purple;
  border: none;
  color: #ffffff;
  font-size: 15px;
  padding: 5px;
  width: 100px;
  cursor: pointer;
  margin: 2%;
}

.inputDiv {
  width: 200px;
  height: 100%;
  padding: 1%;
  margin: 2%;
  border: lightslategray;
  box-shadow: 0 0 15px 4px rgba(0, 0, 0, 0.06);
  border-radius: 10px;
}
.select {
  border-radius: 4px;
  font-size: 15px;
  padding: 5px;
  width: 175px;
  cursor: pointer;
  margin: 2%;
}

.button1 {
  border-radius: 4px;
  background-color: blue;
  border: none;
  color: #ffffff;
  font-size: 15px;
  padding: 5px;
  width: 100px;
  cursor: pointer;
  margin: 2%;
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

- 现在让我们创建一个常量文件夹来跟踪我们可能拥有的任何常量。`constants`在文件夹下创建一个文件`my-app`夹，并在文件夹内`constants`创建一个文件名 index.js
- 粘贴以下代码。
  - 替换为您在 ICO 教程中部署`ABI-CRYPTO-DEV-TOKEN-CONTRACT`的代币合约的 abi 。`Crypto Dev`
  - 替换为您在 ICO 教程中部署`ADDRESS-OF-CRYPTO-DEV-TOKEN-CONTRACT`的代币合约的地址`Crypto Dev`
  - 替换`ABI-EXCHANGE-CONTRACT`为交易所合约的 abi。要获取您的合同的 abi，请转到您的`hardhat-tutorial/artifacts/contracts/Exchange.sol`文件夹并从您的`Exchange.json`文件中获取标记在`"abi"`密钥下的数组。
  - 替换`ADDRESS-EXCHANGE-CONTRACT`为您在上面部署并保存到记事本的交换合约的地址

```
export const TOKEN_CONTRACT_ABI = "ABI-CRYPTO-DEV-TOKEN-CONTRACT";
export const TOKEN_CONTRACT_ADDRESS =
  "ADDRESS-OF-CRYPTO-DEV-TOKEN-CONTRACT";
export const EXCHANGE_CONTRACT_ABI = "ABI-EXCHANGE-CONTRACT";
export const EXCHANGE_CONTRACT_ADDRESS = "ADDRESS-EXCHANGE-CONTRACT";
```

- 现在我们将创建一些实用程序文件来帮助我们更好地与合约交互。`utils`在文件夹内创建一个文件`my-app`夹，在文件夹内创建 4 个文件：`addLiquidity.js`、`removeLiquidity.js`、`getAmounts.js`和`swap.js`
- 让我们从编写一些代码开始`getAmounts.js`。此文件用于检索资产的余额和准备金

```
import { Contract } from "ethers";
import {
  EXCHANGE_CONTRACT_ABI,
  EXCHANGE_CONTRACT_ADDRESS,
  TOKEN_CONTRACT_ABI,
  TOKEN_CONTRACT_ADDRESS,
} from "../constants";

/**
 * getEtherBalance: Retrieves the ether balance of the user or the contract
 */
export const getEtherBalance = async (
  provider,
  address,
  contract = false
) => {
  try {
    // If the caller has set the `contract` boolean to true, retrieve the balance of
    // ether in the `exchange contract`, if it is set to false, retrieve the balance
    // of the user's address
    if (contract) {
      const balance = await provider.getBalance(EXCHANGE_CONTRACT_ADDRESS);
      return balance;
    } else {
      const balance = await provider.getBalance(address);
      return balance;
    }
  } catch (err) {
    console.error(err);
    return 0;
  }
};

/**
 * getCDTokensBalance: Retrieves the Crypto Dev tokens in the account
 * of the provided `address`
 */
export const getCDTokensBalance = async (provider, address) => {
  try {
    const tokenContract = new Contract(
      TOKEN_CONTRACT_ADDRESS,
      TOKEN_CONTRACT_ABI,
      provider
    );
    const balanceOfCryptoDevTokens = await tokenContract.balanceOf(address);
    return balanceOfCryptoDevTokens;
  } catch (err) {
    console.error(err);
  }
};

/**
 * getLPTokensBalance: Retrieves the amount of LP tokens in the account
 * of the provided `address`
 */
export const getLPTokensBalance = async (provider, address) => {
  try {
    const exchangeContract = new Contract(
      EXCHANGE_CONTRACT_ADDRESS,
      EXCHANGE_CONTRACT_ABI,
      provider
    );
    const balanceOfLPTokens = await exchangeContract.balanceOf(address);
    return balanceOfLPTokens;
  } catch (err) {
    console.error(err);
  }
};

/**
 * getReserveOfCDTokens: Retrieves the amount of CD tokens in the
 * exchange contract address
 */
export const getReserveOfCDTokens = async (provider) => {
  try {
    const exchangeContract = new Contract(
      EXCHANGE_CONTRACT_ADDRESS,
      EXCHANGE_CONTRACT_ABI,
      provider
    );
    const reserve = await exchangeContract.getReserve();
    return reserve;
  } catch (err) {
    console.error(err);
  }
};
```

- 现在让我们为`addLiquidity.js`.
  - `addLiquidity`用于调用`addLiquidity`合约中的函数增加流动性
  - 它还获得`Crypto Dev`用户批准用于合同的代币。代币需要批准的原因`Crypto Dev`是因为它们是 ERC20 代币。合约从用户账户中提取 ERC20 需要用户账户的批准
  - `calculateCD`告诉您对于给定数量的，可以添加`Eth`多少代币到`Crypto Dev``liquidity`
  - 我们通过保持一个比率来计算这一点。我们遵循的比例是`(amount of Crypto Dev tokens to be added) / (Crypto Dev tokens balance) = (Eth that would be added) / (Eth reserve in the contract)`
  - 所以通过数学我们得到`(amount of Crypto Dev tokens to be added) = (Eth that would be added * Crypto Dev tokens balance) / (Eth reserve in the contract)`
  - 需要该比率，以便增加流动性不会在很大程度上影响价格
  - 注意`tx.wait()`表示我们正在等待交易被挖掘

```
import { Contract, utils } from "ethers";
import {
  EXCHANGE_CONTRACT_ABI,
  EXCHANGE_CONTRACT_ADDRESS,
  TOKEN_CONTRACT_ABI,
  TOKEN_CONTRACT_ADDRESS,
} from "../constants";

/**
 * addLiquidity helps add liquidity to the exchange,
 * If the user is adding initial liquidity, user decides the ether and CD tokens he wants to add
 * to the exchange. If he is adding the liquidity after the initial liquidity has already been added
 * then we calculate the Crypto Dev tokens he can add, given the Eth he wants to add by keeping the ratios
 * constant
 */
export const addLiquidity = async (
  signer,
  addCDAmountWei,
  addEtherAmountWei
) => {
  try {
    // create a new instance of the token contract
    const tokenContract = new Contract(
      TOKEN_CONTRACT_ADDRESS,
      TOKEN_CONTRACT_ABI,
      signer
    );
    // create a new instance of the exchange contract
    const exchangeContract = new Contract(
      EXCHANGE_CONTRACT_ADDRESS,
      EXCHANGE_CONTRACT_ABI,
      signer
    );
    // Because CD tokens are an ERC20, user would need to give the contract allowance
    // to take the required number CD tokens out of his contract
    let tx = await tokenContract.approve(
      EXCHANGE_CONTRACT_ADDRESS,
      addCDAmountWei.toString()
    );
    await tx.wait();
    // After the contract has the approval, add the ether and cd tokens in the liquidity
    tx = await exchangeContract.addLiquidity(addCDAmountWei, {
      value: addEtherAmountWei,
    });
    await tx.wait();
  } catch (err) {
    console.error(err);
  }
};

/**
 * calculateCD calculates the CD tokens that need to be added to the liquidity
 * given `_addEtherAmountWei` amount of ether
 */
export const calculateCD = async (
  _addEther = "0",
  etherBalanceContract,
  cdTokenReserve
) => {
  // `_addEther` is a string, we need to convert it to a Bignumber before we can do our calculations
  // We do that using the `parseEther` function from `ethers.js`
  const _addEtherAmountWei = utils.parseEther(_addEther);

  // Ratio needs to be maintained when we add liquidty.
  // We need to let the user know for a specific amount of ether how many `CD` tokens
  // He can add so that the price impact is not large
  // The ratio we follow is (amount of Crypto Dev tokens to be added) / (Crypto Dev tokens balance) = (Eth that would be added) / (Eth reserve in the contract)
  // So by maths we get (amount of Crypto Dev tokens to be added) = (Eth that would be added * Crypto Dev tokens balance) / (Eth reserve in the contract)

  const cryptoDevTokenAmount = _addEtherAmountWei
    .mul(cdTokenReserve)
    .div(etherBalanceContract);
  return cryptoDevTokenAmount;
};
```

- 现在添加一些代码`removeLiquidity.js`
  - 我们这里有两个函数：一个是`removeLiquidity`，另一个是`getTokensAfterRemove`
  - `removeLiquidity`从合约中调用该`removeLiquidity`函数，以移除`LP`用户指定的代币数量
  - `getTokensAfterRemove`计算 从池中删除一定数量的代币`Ether`后将`CD`发送回用户的数量和代币`LP`
  - `Eth`在用户提取代币后将返还给用户的金额`LP`是根据一个比率计算的，
  - 比率是 ->`(amount of Eth that would be sent back to the user / Eth reserve) = (LP tokens withdrawn) / (total supply of LP tokens)`
  - 通过一些数学我们得到 ->`(amount of Eth that would be sent back to the user) = (Eth Reserve * LP tokens withdrawn) / (total supply of LP tokens)`
  - 同样，我们也为`CD`令牌保持一个比率，所以在我们的例子中
  - 比率是 ->`(amount of CD tokens sent back to the user / CD Token reserve) = (LP tokens withdrawn) / (total supply of LP tokens)`
  - 然后`(amount of CD tokens sent back to the user) = (CD token reserve * LP tokens withdrawn) / (total supply of LP tokens)`

```
import { Contract, providers, utils, BigNumber } from "ethers";
import { EXCHANGE_CONTRACT_ABI, EXCHANGE_CONTRACT_ADDRESS } from "../constants";

/**
* removeLiquidity: Removes the `removeLPTokensWei` amount of LP tokens from
* liquidity and also the calculated amount of `ether` and `CD` tokens
*/
export const removeLiquidity = async (signer, removeLPTokensWei) => {
  // Create a new instance of the exchange contract
  const exchangeContract = new Contract(
    EXCHANGE_CONTRACT_ADDRESS,
    EXCHANGE_CONTRACT_ABI,
    signer
  );
  const tx = await exchangeContract.removeLiquidity(removeLPTokensWei);
  await tx.wait();
};

/**
* getTokensAfterRemove: Calculates the amount of `Eth` and `CD` tokens
* that would be returned back to user after he removes `removeLPTokenWei` amount
* of LP tokens from the contract
*/
export const getTokensAfterRemove = async (
  provider,
  removeLPTokenWei,
  _ethBalance,
  cryptoDevTokenReserve
) => {
  try {
    // Create a new instance of the exchange contract
    const exchangeContract = new Contract(
      EXCHANGE_CONTRACT_ADDRESS,
      EXCHANGE_CONTRACT_ABI,
      provider
    );
    // Get the total supply of `Crypto Dev` LP tokens
    const _totalSupply = await exchangeContract.totalSupply();
    // Here we are using the BigNumber methods of multiplication and division
    // The amount of Eth that would be sent back to the user after he withdraws the LP token
    // is calculated based on a ratio,
    // Ratio is -> (amount of Eth that would be sent back to the user / Eth reserve) = (LP tokens withdrawn) / (total supply of LP tokens)
    // By some maths we get -> (amount of Eth that would be sent back to the user) = (Eth Reserve * LP tokens withdrawn) / (total supply of LP tokens)
    // Similarly we also maintain a ratio for the `CD` tokens, so here in our case
    // Ratio is -> (amount of CD tokens sent back to the user / CD Token reserve) = (LP tokens withdrawn) / (total supply of LP tokens)
    // Then (amount of CD tokens sent back to the user) = (CD token reserve * LP tokens withdrawn) / (total supply of LP tokens)
    const _removeEther = _ethBalance.mul(removeLPTokenWei).div(_totalSupply);
    const _removeCD = cryptoDevTokenReserve
      .mul(removeLPTokenWei)
      .div(_totalSupply);
    return {
      _removeEther,
      _removeCD,
    };
  } catch (err) {
    console.error(err);
  }
};
```

- 现在是时候为`swap.js`我们的最后一个`utils`文件编写代码了

  - 它有两个功能`getAmountOfTokenReceivedFromSwap`和`swapTokens`
  - `swapTokens``Eth/Crypto Dev`用代币交换一定数量的代`Crypto Dev/Eth`币
  - 如果`Eth`已经被用户从 UI 中选择，则表示用户有`Eth`并且他想将其换成一定数量的`Crypto Dev`代币
  - 在这种情况下，我们调用该`ethToCryptoDevToken`函数。请注意，这`Eth`是作为函数中的值发送的，因为用户将其支付`Eth`给合约。`Eth`在这种情况下，发送不是输入参数值
  - 另一方面，如果`Eth`未选择，则表示用户希望将`Crypto Dev`代币换成`Eth`
  - 这里我们称`cryptoDevTokenToEth`
  - `getAmountOfTokensReceivedFromSwap`是一个函数，它计算给定一定数量的`Eth/Crypto Dev`令牌，有多少`Eth/Crypto Dev`令牌将被发送回用户
  - 如果`Eth`被选中，它会调用`getAmountOfTokens`从合约中获取`input`储备金和`output`储备金。在这里，输入储备将是`Eth`合约的余额，输出储备将是代`Crypto Dev`币储备。`Eth`如果未选中，则相反

```
import { Contract } from "ethers";
import {
  EXCHANGE_CONTRACT_ABI,
  EXCHANGE_CONTRACT_ADDRESS,
  TOKEN_CONTRACT_ABI,
  TOKEN_CONTRACT_ADDRESS,
} from "../constants";

/*
    getAmountOfTokensReceivedFromSwap:  Returns the number of Eth/Crypto Dev tokens that can be received 
    when the user swaps `_swapAmountWei` amount of Eth/Crypto Dev tokens.
*/
export const getAmountOfTokensReceivedFromSwap = async (
  _swapAmountWei,
  provider,
  ethSelected,
  ethBalance,
  reservedCD
) => {
  // Create a new instance of the exchange contract
  const exchangeContract = new Contract(
    EXCHANGE_CONTRACT_ADDRESS,
    EXCHANGE_CONTRACT_ABI,
    provider
  );
  let amountOfTokens;
  // If `Eth` is selected this means our input value is `Eth` which means our input amount would be
  // `_swapAmountWei`, the input reserve would be the `ethBalance` of the contract and output reserve
  // would be the `Crypto Dev` token reserve
  if (ethSelected) {
    amountOfTokens = await exchangeContract.getAmountOfTokens(
      _swapAmountWei,
      ethBalance,
      reservedCD
    );
  } else {
    // If `Eth` is not selected this means our input value is `Crypto Dev` tokens which means our input amount would be
    // `_swapAmountWei`, the input reserve would be the `Crypto Dev` token reserve of the contract and output reserve
    // would be the `ethBalance`
    amountOfTokens = await exchangeContract.getAmountOfTokens(
      _swapAmountWei,
      reservedCD,
      ethBalance
    );
  }

  return amountOfTokens;
};

/*
  swapTokens: Swaps `swapAmountWei` of Eth/Crypto Dev tokens with `tokenToBeReceivedAfterSwap` amount of Eth/Crypto Dev tokens.
*/
export const swapTokens = async (
  signer,
  swapAmountWei,
  tokenToBeReceivedAfterSwap,
  ethSelected
) => {
  // Create a new instance of the exchange contract
  const exchangeContract = new Contract(
    EXCHANGE_CONTRACT_ADDRESS,
    EXCHANGE_CONTRACT_ABI,
    signer
  );
  const tokenContract = new Contract(
    TOKEN_CONTRACT_ADDRESS,
    TOKEN_CONTRACT_ABI,
    signer
  );
  let tx;
  // If Eth is selected call the `ethToCryptoDevToken` function else
  // call the `cryptoDevTokenToEth` function from the contract
  // As you can see you need to pass the `swapAmount` as a value to the function because
  // it is the ether we are paying to the contract, instead of a value we are passing to the function
  if (ethSelected) {
    tx = await exchangeContract.ethToCryptoDevToken(
      tokenToBeReceivedAfterSwap,
      {
        value: swapAmountWei,
      }
    );
  } else {
    // User has to approve `swapAmountWei` for the contract because `Crypto Dev` token
    // is an ERC20
    tx = await tokenContract.approve(
      EXCHANGE_CONTRACT_ADDRESS,
      swapAmountWei.toString()
    );
    await tx.wait();
    // call cryptoDevTokenToEth function which would take in `swapAmountWei` of `Crypto Dev` tokens and would
    // send back `tokenToBeReceivedAfterSwap` amount of `Eth` to the user
    tx = await exchangeContract.cryptoDevTokenToEth(
      swapAmountWei,
      tokenToBeReceivedAfterSwap
    );
  }
  await tx.wait();
};
```

- 现在是我们应用程序的最后阶段的时候了，让我们将一些代码添加到`pages/index.js`接下来已经提供给您的文件中。将文件的所有内容替换为以下内容

```
import { BigNumber, providers, utils } from "ethers";
import Head from "next/head";
import React, { useEffect, useRef, useState } from "react";
import Web3Modal from "web3modal";
import styles from "../styles/Home.module.css";
import { addLiquidity, calculateCD } from "../utils/addLiquidity";
import {
  getCDTokensBalance,
  getEtherBalance,
  getLPTokensBalance,
  getReserveOfCDTokens,
} from "../utils/getAmounts";
import {
  getTokensAfterRemove,
  removeLiquidity,
} from "../utils/removeLiquidity";
import { swapTokens, getAmountOfTokensReceivedFromSwap } from "../utils/swap";

export default function Home() {
  /** General state variables */
  // loading is set to true when the transaction is mining and set to false when
  // the transaction has mined
  const [loading, setLoading] = useState(false);
  // We have two tabs in this dapp, Liquidity Tab and Swap Tab. This variable
  // keeps track of which Tab the user is on. If it is set to true this means
  // that the user is on `liquidity` tab else he is on `swap` tab
  const [liquidityTab, setLiquidityTab] = useState(true);
  // This variable is the `0` number in form of a BigNumber
  const zero = BigNumber.from(0);
  /** Variables to keep track of amount */
  // `ethBalance` keeps track of the amount of Eth held by the user's account
  const [ethBalance, setEtherBalance] = useState(zero);
  // `reservedCD` keeps track of the Crypto Dev tokens Reserve balance in the Exchange contract
  const [reservedCD, setReservedCD] = useState(zero);
  // Keeps track of the ether balance in the contract
  const [etherBalanceContract, setEtherBalanceContract] = useState(zero);
  // cdBalance is the amount of `CD` tokens help by the users account
  const [cdBalance, setCDBalance] = useState(zero);
  // `lpBalance` is the amount of LP tokens held by the users account
  const [lpBalance, setLPBalance] = useState(zero);
  /** Variables to keep track of liquidity to be added or removed */
  // addEther is the amount of Ether that the user wants to add to the liquidity
  const [addEther, setAddEther] = useState(zero);
  // addCDTokens keeps track of the amount of CD tokens that the user wants to add to the liquidity
  // in case when there is no initial liquidity and after liquidity gets added it keeps track of the
  // CD tokens that the user can add given a certain amount of ether
  const [addCDTokens, setAddCDTokens] = useState(zero);
  // removeEther is the amount of `Ether` that would be sent back to the user based on a certain number of `LP` tokens
  const [removeEther, setRemoveEther] = useState(zero);
  // removeCD is the amount of `Crypto Dev` tokens that would be sent back to the user based on a certain number of `LP` tokens
  // that he wants to withdraw
  const [removeCD, setRemoveCD] = useState(zero);
  // amount of LP tokens that the user wants to remove from liquidity
  const [removeLPTokens, setRemoveLPTokens] = useState("0");
  /** Variables to keep track of swap functionality */
  // Amount that the user wants to swap
  const [swapAmount, setSwapAmount] = useState("");
  // This keeps track of the number of tokens that the user would receive after a swap completes
  const [tokenToBeReceivedAfterSwap, settokenToBeReceivedAfterSwap] = useState(
    zero
  );
  // Keeps track of whether  `Eth` or `Crypto Dev` token is selected. If `Eth` is selected it means that the user
  // wants to swap some `Eth` for some `Crypto Dev` tokens and vice versa if `Eth` is not selected
  const [ethSelected, setEthSelected] = useState(true);
  /** Wallet connection */
  // Create a reference to the Web3 Modal (used for connecting to Metamask) which persists as long as the page is open
  const web3ModalRef = useRef();
  // walletConnected keep track of whether the user's wallet is connected or not
  const [walletConnected, setWalletConnected] = useState(false);

  /**
   * getAmounts call various functions to retrive amounts for ethbalance,
   * LP tokens etc
   */
  const getAmounts = async () => {
    try {
      const provider = await getProviderOrSigner(false);
      const signer = await getProviderOrSigner(true);
      const address = await signer.getAddress();
      // get the amount of eth in the user's account
      const _ethBalance = await getEtherBalance(provider, address);
      // get the amount of `Crypto Dev` tokens held by the user
      const _cdBalance = await getCDTokensBalance(provider, address);
      // get the amount of `Crypto Dev` LP tokens held by the user
      const _lpBalance = await getLPTokensBalance(provider, address);
      // gets the amount of `CD` tokens that are present in the reserve of the `Exchange contract`
      const _reservedCD = await getReserveOfCDTokens(provider);
      // Get the ether reserves in the contract
      const _ethBalanceContract = await getEtherBalance(provider, null, true);
      setEtherBalance(_ethBalance);
      setCDBalance(_cdBalance);
      setLPBalance(_lpBalance);
      setReservedCD(_reservedCD);
      setReservedCD(_reservedCD);
      setEtherBalanceContract(_ethBalanceContract);
    } catch (err) {
      console.error(err);
    }
  };

  /**** SWAP FUNCTIONS ****/

  /**
   * swapTokens: Swaps  `swapAmountWei` of Eth/Crypto Dev tokens with `tokenToBeReceivedAfterSwap` amount of Eth/Crypto Dev tokens.
   */
  const _swapTokens = async () => {
    try {
      // Convert the amount entered by the user to a BigNumber using the `parseEther` library from `ethers.js`
      const swapAmountWei = utils.parseEther(swapAmount);
      // Check if the user entered zero
      // We are here using the `eq` method from BigNumber class in `ethers.js`
      if (!swapAmountWei.eq(zero)) {
        const signer = await getProviderOrSigner(true);
        setLoading(true);
        // Call the swapTokens function from the `utils` folder
        await swapTokens(
          signer,
          swapAmountWei,
          tokenToBeReceivedAfterSwap,
          ethSelected
        );
        setLoading(false);
        // Get all the updated amounts after the swap
        await getAmounts();
        setSwapAmount("");
      }
    } catch (err) {
      console.error(err);
      setLoading(false);
      setSwapAmount("");
    }
  };

  /**
   * _getAmountOfTokensReceivedFromSwap:  Returns the number of Eth/Crypto Dev tokens that can be received 
   * when the user swaps `_swapAmountWEI` amount of Eth/Crypto Dev tokens.
   */
  const _getAmountOfTokensReceivedFromSwap = async (_swapAmount) => {
    try {
      // Convert the amount entered by the user to a BigNumber using the `parseEther` library from `ethers.js`
      const _swapAmountWEI = utils.parseEther(_swapAmount.toString());
      // Check if the user entered zero
      // We are here using the `eq` method from BigNumber class in `ethers.js`
      if (!_swapAmountWEI.eq(zero)) {
        const provider = await getProviderOrSigner();
        // Get the amount of ether in the contract
        const _ethBalance = await getEtherBalance(provider, null, true);
        // Call the `getAmountOfTokensReceivedFromSwap` from the utils folder
        const amountOfTokens = await getAmountOfTokensReceivedFromSwap(
          _swapAmountWEI,
          provider,
          ethSelected,
          _ethBalance,
          reservedCD
        );
        settokenToBeReceivedAfterSwap(amountOfTokens);
      } else {
        settokenToBeReceivedAfterSwap(zero);
      }
    } catch (err) {
      console.error(err);
    }
  };

  /*** END ***/

  /**** ADD LIQUIDITY FUNCTIONS ****/

  /**
   * _addLiquidity helps add liquidity to the exchange,
   * If the user is adding initial liquidity, user decides the ether and CD tokens he wants to add
   * to the exchange. If he is adding the liquidity after the initial liquidity has already been added
   * then we calculate the crypto dev tokens he can add, given the Eth he wants to add by keeping the ratios
   * constant
   */
  const _addLiquidity = async () => {
    try {
      // Convert the ether amount entered by the user to Bignumber
      const addEtherWei = utils.parseEther(addEther.toString());
      // Check if the values are zero
      if (!addCDTokens.eq(zero) && !addEtherWei.eq(zero)) {
        const signer = await getProviderOrSigner(true);
        setLoading(true);
        // call the addLiquidity function from the utils folder
        await addLiquidity(signer, addCDTokens, addEtherWei);
        setLoading(false);
        // Reinitialize the CD tokens
        setAddCDTokens(zero);
        // Get amounts for all values after the liquidity has been added
        await getAmounts();
      } else {
        setAddCDTokens(zero);
      }
    } catch (err) {
      console.error(err);
      setLoading(false);
      setAddCDTokens(zero);
    }
  };

  /**** END ****/

  /**** REMOVE LIQUIDITY FUNCTIONS ****/

  /**
   * _removeLiquidity: Removes the `removeLPTokensWei` amount of LP tokens from
   * liquidity and also the calculated amount of `ether` and `CD` tokens
   */
  const _removeLiquidity = async () => {
    try {
      const signer = await getProviderOrSigner(true);
      // Convert the LP tokens entered by the user to a BigNumber
      const removeLPTokensWei = utils.parseEther(removeLPTokens);
      setLoading(true);
      // Call the removeLiquidity function from the `utils` folder
      await removeLiquidity(signer, removeLPTokensWei);
      setLoading(false);
      await getAmounts();
      setRemoveCD(zero);
      setRemoveEther(zero);
    } catch (err) {
      console.error(err);
      setLoading(false);
      setRemoveCD(zero);
      setRemoveEther(zero);
    }
  };

  /**
   * _getTokensAfterRemove: Calculates the amount of `Ether` and `CD` tokens
   * that would be returned back to user after he removes `removeLPTokenWei` amount
   * of LP tokens from the contract
   */
  const _getTokensAfterRemove = async (_removeLPTokens) => {
    try {
      const provider = await getProviderOrSigner();
      // Convert the LP tokens entered by the user to a BigNumber
      const removeLPTokenWei = utils.parseEther(_removeLPTokens);
      // Get the Eth reserves within the exchange contract
      const _ethBalance = await getEtherBalance(provider, null, true);
      // get the crypto dev token reserves from the contract
      const cryptoDevTokenReserve = await getReserveOfCDTokens(provider);
      // call the getTokensAfterRemove from the utils folder
      const { _removeEther, _removeCD } = await getTokensAfterRemove(
        provider,
        removeLPTokenWei,
        _ethBalance,
        cryptoDevTokenReserve
      );
      setRemoveEther(_removeEther);
      setRemoveCD(_removeCD);
    } catch (err) {
      console.error(err);
    }
  };

  /**** END ****/

  /**
   * connectWallet: Connects the MetaMask wallet
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

  /**
   * Returns a Provider or Signer object representing the Ethereum RPC with or
   * without the signing capabilities of Metamask attached
   *
   * A `Provider` is needed to interact with the blockchain - reading
   * transactions, reading balances, reading state, etc.
   *
   * A `Signer` is a special type of Provider used in case a `write` transaction
   * needs to be made to the blockchain, which involves the connected account
   * needing to make a digital signature to authorize the transaction being
   * sent. Metamask exposes a Signer API to allow your website to request
   * signatures from the user using Signer functions.
   *
   * @param {*} needSigner - True if you need the signer, default false
   * otherwise
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
      getAmounts();
    }
  }, [walletConnected]);

  /*
      renderButton: Returns a button based on the state of the dapp
  */
  const renderButton = () => {
    // If wallet is not connected, return a button which allows them to connect their wllet
    if (!walletConnected) {
      return (
        <button onClick={connectWallet} className={styles.button}>
          Connect your wallet
        </button>
      );
    }

    // If we are currently waiting for something, return a loading button
    if (loading) {
      return <button className={styles.button}>Loading...</button>;
    }

    if (liquidityTab) {
      return (
        <div>
          <div className={styles.description}>
            You have:
            <br />
            {/* Convert the BigNumber to string using the formatEther function from ethers.js */}
            {utils.formatEther(cdBalance)} Crypto Dev Tokens
            <br />
            {utils.formatEther(ethBalance)} Ether
            <br />
            {utils.formatEther(lpBalance)} Crypto Dev LP tokens
          </div>
          <div>
            {/* If reserved CD is zero, render the state for liquidity zero where we ask the user
            how much initial liquidity he wants to add else just render the state where liquidity is not zero and
            we calculate based on the `Eth` amount specified by the user how much `CD` tokens can be added */}
            {utils.parseEther(reservedCD.toString()).eq(zero) ? (
              <div>
                <input
                  type="number"
                  placeholder="Amount of Ether"
                  onChange={(e) => setAddEther(e.target.value || "0")}
                  className={styles.input}
                />
                <input
                  type="number"
                  placeholder="Amount of CryptoDev tokens"
                  onChange={(e) =>
                    setAddCDTokens(
                      BigNumber.from(utils.parseEther(e.target.value || "0"))
                    )
                  }
                  className={styles.input}
                />
                <button className={styles.button1} onClick={_addLiquidity}>
                  Add
                </button>
              </div>
            ) : (
              <div>
                <input
                  type="number"
                  placeholder="Amount of Ether"
                  onChange={async (e) => {
                    setAddEther(e.target.value || "0");
                    // calculate the number of CD tokens that
                    // can be added given  `e.target.value` amount of Eth
                    const _addCDTokens = await calculateCD(
                      e.target.value || "0",
                      etherBalanceContract,
                      reservedCD
                    );
                    setAddCDTokens(_addCDTokens);
                  }}
                  className={styles.input}
                />
                <div className={styles.inputDiv}>
                  {/* Convert the BigNumber to string using the formatEther function from ethers.js */}
                  {`You will need ${utils.formatEther(addCDTokens)} Crypto Dev
                  Tokens`}
                </div>
                <button className={styles.button1} onClick={_addLiquidity}>
                  Add
                </button>
              </div>
            )}
            <div>
              <input
                type="number"
                placeholder="Amount of LP Tokens"
                onChange={async (e) => {
                  setRemoveLPTokens(e.target.value || "0");
                  // Calculate the amount of Ether and CD tokens that the user would receive
                  // After he removes `e.target.value` amount of `LP` tokens
                  await _getTokensAfterRemove(e.target.value || "0");
                }}
                className={styles.input}
              />
              <div className={styles.inputDiv}>
                {/* Convert the BigNumber to string using the formatEther function from ethers.js */}
                {`You will get ${utils.formatEther(removeCD)} Crypto
              Dev Tokens and ${utils.formatEther(removeEther)} Eth`}
              </div>
              <button className={styles.button1} onClick={_removeLiquidity}>
                Remove
              </button>
            </div>
          </div>
        </div>
      );
    } else {
      return (
        <div>
          <input
            type="number"
            placeholder="Amount"
            onChange={async (e) => {
              setSwapAmount(e.target.value || "");
              // Calculate the amount of tokens user would receive after the swap
              await _getAmountOfTokensReceivedFromSwap(e.target.value || "0");
            }}
            className={styles.input}
            value={swapAmount}
          />
          <select
            className={styles.select}
            name="dropdown"
            id="dropdown"
            onChange={async () => {
              setEthSelected(!ethSelected);
              // Initialize the values back to zero
              await _getAmountOfTokensReceivedFromSwap(0);
              setSwapAmount("");
            }}
          >
            <option value="eth">Ethereum</option>
            <option value="cryptoDevToken">Crypto Dev Token</option>
          </select>
          <br />
          <div className={styles.inputDiv}>
            {/* Convert the BigNumber to string using the formatEther function from ethers.js */}
            {ethSelected
              ? `You will get ${utils.formatEther(
                  tokenToBeReceivedAfterSwap
                )} Crypto Dev Tokens`
              : `You will get ${utils.formatEther(
                  tokenToBeReceivedAfterSwap
                )} Eth`}
          </div>
          <button className={styles.button1} onClick={_swapTokens}>
            Swap
          </button>
        </div>
      );
    }
  };

  return (
    <div>
      <Head>
        <title>Crypto Devs</title>
        <meta name="description" content="Whitelist-Dapp" />
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <div className={styles.main}>
        <div>
          <h1 className={styles.title}>Welcome to Crypto Devs Exchange!</h1>
          <div className={styles.description}>
            Exchange Ethereum &#60;&#62; Crypto Dev Tokens
          </div>
          <div>
            <button
              className={styles.button}
              onClick={() => {
                setLiquidityTab(true);
              }}
            >
              Liquidity
            </button>
            <button
              className={styles.button}
              onClick={() => {
                setLiquidityTab(false);
              }}
            >
              Swap
            </button>
          </div>
          {renderButton()}
        </div>
        <div>
          <img className={styles.image} src="./cryptodev.svg" />
        </div>
      </div>

      <footer className={styles.footer}>
        Made with &#10084; by Crypto Devs
      </footer>
    </div>
  );
}
```

- 现在在指向`my-app`文件夹的终端中，执行

```
npm run dev
```

您的 Exchange dapp 现在应该可以正常运行了🚀

## 将您的代码推送到 Github

确保在进行下一步部署之前将所有代码推送到 github

## 部署你的 dApp

我们现在将部署您的 dApp，以便每个人都可以看到您的网站，并且您可以与所有 LearnWeb3 DAO 朋友分享它。

- 转到 https://vercel.com/ 并使用您的 GitHub 登录
- 然后单击`New Project`按钮，然后选择您的 Defi-Exchange dApp 存储库

- ![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220717202436.png)

- 在配置您的新项目时，Vercel 将允许您自定义您的`Root Directory`
- 单击`Edit`旁边`Root Directory`并将其设置为`my-app`
- 点击`Deploy`
- 现在，您可以通过转到仪表板、选择您的项目并从那里复制 URL 来查看您部署的网站！



> 原文[https://www.learnweb3.io/tracks/sophomore/defi-amm-dapp](https://www.learnweb3.io/tracks/sophomore/defi-amm-dapp)


![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
