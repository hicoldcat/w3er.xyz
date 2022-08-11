---
title: Web3系列教程之高级篇---12：使用元交易支付其他人的交易燃料费
description: null
author: 李留白
weight: 0
date: 2022-08-11T13:37:41.543Z
lastmod: 2022-08-11T14:53:37.175Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/R3RYera.png
---

有些时候，你希望你的DApp用户有一个无燃料费的体验，或者也许在没有真正把东西放在链上的情况下进行交易。这些类型的交易被称为元交易，在这个层面上，我们将深入探讨如何设计元交易，以及如果不仔细设计，它们如何被利用。

对于那些使用过OpenSea的人，有没有注意到OpenSea是如何让你免费制作NFT的？无论您想以什么价格出售NFT，它都不会在初始NFT批准交易之外收取天然气费用？答案是元交易。

元交易也常用于无燃料费的交易体验，例如要求用户签署一个信息来索取NFT，而不是支付气体来发送交易来索取NFT。

也有其他的用例，例如让用户用任何代币，甚至是法币来支付汽油费，而不需要转换为加密货币。Multisig钱包也只要求最后一个签名者为正在从multisig钱包进行的交易支付汽油费，其他用户只是签署一些信息。

这是不是真的很酷？🎉

## 它是如何工作的？

元交易只是一个花哨的词，即由第三方（中继者）代表用户支付交易的气体。用户只需要签署包含他们想要执行的交易信息的消息（而不是发送交易），并把它交给中继者。然后，中继者负责使用这些数据创建有效的交易，并自己支付燃气费。

中继者可以是，例如，你的DApp中的代码，让你的用户体验无气交易，也可以是你用法币支付的第三方公司，在以太坊上执行交易，等等。

在这一层次，我们将做两件事--学习元交易以及如何构建一个支持元交易的简单智能合约，同时学习我们构建的第一个合约中的一个安全漏洞以及如何修复它。一石二鸟😎。

## 数字签名

在前面的IPFS级别中，我们已经解释了什么是散列。在继续之前，我们需要了解密码学的第二个基本概念--数字签名。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220811221450.png)

数字签名是一种验证信息真实性的数学方法。给定一些数据，一个私钥可以用来签署该数据并产生一个有签名的信息。

然后，其他认为自己拥有原始输入数据的人可以将其与签名的信息进行比较，以检索出签名信息的公钥。由于发送者的公钥必须事先知道，他们可以保证数据没有被篡改，其完整性得到维护。

所以在上面的例子中，Alice用她的私钥签署了`Hello Bob！`这个消息并把它发送给Bob。Bob已经知道内容是`Hello Bob！`现在他想验证该信息确实是由Alice发送的，所以他试图从签署的信息中检索出公钥。如果公钥是Alice的公钥，那么该信息就没有被篡改，是安全传输的。

### 数字签名的使用案例

数字签名通常用于想要安全通信和确保信息内容在通信过程中没有被操纵的情况。你可以发送原始明文信息，以及来自已知发送者的签名信息，接收者可以通过比较明文信息和签名信息得出签名者的公钥来验证信息的完整性。如果签名者的公钥与预期的发送者的公钥相匹配，那么该信息就被安全地传送了。

### 以太坊上的数字签名

在以太坊的情况下，用户钱包可以签署消息，然后原始输入数据可以与签署的消息进行验证，以检索签署消息的用户的地址。签名验证既可以在链外进行，也可以在Solidity内部进行。这里在这种情况下，用户的地址就是公钥。

## 了解概念

假设我们想建立一个DApp，用户可以将代币转移到其他地址，而不需要支付汽油，只有一次批准的机会。由于用户自己不会发送交易，我们将为他们支付汽油🤑.。

这种设计与OpenSea的工作方式非常相似，一旦你支付气体提供对ERC-721或ERC-1155集合的批准，OpenSea上的列表和销售对卖家来说是免费的。OpenSea只要求卖家在挂牌时签署交易，而当买家出现时，卖家的签名会与买家的交易一起提交，从而将NFT转移给买家，并将ETH转移给卖家--而买家则为这一切支付所有的气体。

在我们的案例中，我们将只是有一个中继者，让你在初始批准后将ERC-20代币转移到其他地址 🎉。

让我们想一想，为实现这一目标需要哪些所有的信息。智能合约需要知道关于代币转移的所有信息，所以我们至少需要把4个东西纳入到签署的消息中。
- `sender`地址
- `recipient`地址
- `amount`要转让的代币数量
- `tokenContract`ERC-20智能合约地址。

流程将看起来像这样:

- 用户首先批准TokenSender合约，以进行无限的代币转移（使用ERC20批准功能）。
- 用户签署包含上述消息的信息
- 中继器调用智能合约，传递已签署的消息，并支付汽油费。
- 智能合约验证签名和解码信息数据，并将代币从发送方转移到接收方

值得庆幸的是，`ethers.js`带有一个名为`signMessage`的函数，可以让我们轻松地签署信息。然而，为了避免产生任意长度的消息，我们将首先对所需的数据进行散列，然后进行签名。在智能合约中也是如此，我们将首先对给定的参数进行散列，然后尝试用散列来验证签名。

我们将用代码写一个Hardhat测试，所以首先，我们需要编写智能合约。

## 构建

让我们建立一个例子，在这个例子中你可以体验到元交易的作用，稍后我们将扩展这个测试，以显示漏洞。

- 要设置一个Hardhat项目，请打开终端，在一个新的文件夹中执行这些命令

```bash
npm init --yes
npm install --save-dev hardhat
```

- 如果你是在Windows上，请做这个额外的步骤，也安装这些库 :)

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

让我们也在同一终端安装OpenZeppelin合同。

```
npm install @openzeppelin/contracts
```

我们将创建两个智能合约。第一个是一个超级简单的ERC-20实现，第二个是我们的`TokenSender`合约。为了简单起见，我们将在同一个`.sol`文件中创建这两个合约。

让我们在`contracts/`目录下创建一个名为`MetaTokenSender.sol`的Solidity文件，代码如下:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

contract RandomToken is ERC20 {
    constructor() ERC20("", "") {}

    function freeMint(uint amount) public {
        _mint(msg.sender, amount);
    }
}

contract TokenSender {

    using ECDSA for bytes32;

    function transfer(address sender, uint amount, address recipient, address tokenContract, bytes memory signature) public {
        bytes32 messageHash = getHash(sender, amount, recipient, tokenContract);
        bytes32 signedMessageHash = messageHash.toEthSignedMessageHash();

        address signer = signedMessageHash.recover(signature);

        require(signer == sender, "Signature does not come from sender");

        bool sent = ERC20(tokenContract).transferFrom(sender, recipient, amount);
        require(sent, "Transfer failed");
    }

    function getHash(address sender, uint amount, address recipient, address tokenContract) public pure returns (bytes32) {
        return keccak256(abi.encodePacked(sender, amount, recipient, tokenContract));
    }
}
```

让我们来看看发生了什么。首先，让我们看一下导入。

### 导入

`ERC20.sol`导入是为了继承OpenZeppelin的基本ERC-20合约实现，我们在以前的课程上已经了解了很多。

`ECDSA`是`Elliptic Curve Digital Signature Algorithm`的缩写--它是Ethereum使用的签名算法，`ECDSA.sol`的OpenZeppelin库包含一些用于Solidity中数字签名的辅助函数。

### 函数

ERC-20合约是不言自明的，因为它所做的就是让你铸造一个任意数量的免费代币。

对于`TokenSender`，这里有两个函数。让我们先看看辅助函数--`getHash`--它接收`sender`地址、代币`amount`、`recipient`地址和`tokenContract`地址，并返回它们打包在一起的`keccak256`哈希值。`abi.encodePacked`将所有指定的值转换为字节，中间不留任何填充，并将其传递给keccak256，这是一个Ethereum使用的散列函数。这是一个`pure`的函数，所以我们也将通过Javascript在客户端使用它，以避免在Javascript中处理keccak散列和打包编码，这可能会有点烦人。

`transfer`函数是一个有趣的函数，它接收上述四个参数和一个签名。它使用`getHash`帮助器计算哈希值。之后，根据[EIP-191](https://eips.ethereum.org/EIPS/eip-191)，消息哈希值被转换为`Ethereum Signed Message Hash`。调用这个函数可以将`messageHash`转换成这样的格式 `"\x19Ethereum Signed Message:\n" + len(message) + message)`。遵守标准以实现互操作性是很重要的。

在这样做之后，你调用`recover`方法，在该方法中你传递`signature`，这只不过是你用发件人的私钥签名的`Ethereum Signed Message`，你将其与你生成的`Ethereum Signed Message` - `signedMessageHash`进行比较，以恢复公钥，这应该是发件人的地址。

如果签名人地址与传入的`sender`地址相同，那么我们就将ERC-20代币从`sender `转移到`recipient`。

### Hardhat Test

让我们创建一个Hardhat测试来演示这将如何工作。在`test`目录下创建一个名为`metatxn-test.js`的新文件，代码如下

```
const { expect } = require("chai");
const { BigNumber } = require("ethers");
const { arrayify, parseEther } = require("ethers/lib/utils");
const { ethers } = require("hardhat");

describe("MetaTokenTransfer", function () {
  it("Should let user transfer tokens through a relayer", async function () {
    // Deploy the contracts
    const RandomTokenFactory = await ethers.getContractFactory("RandomToken");
    const randomTokenContract = await RandomTokenFactory.deploy();
    await randomTokenContract.deployed();

    const MetaTokenSenderFactory = await ethers.getContractFactory(
      "TokenSender"
    );
    const tokenSenderContract = await MetaTokenSenderFactory.deploy();
    await tokenSenderContract.deployed();

    // Get three addresses, treat one as the user address
    // one as the relayer address, and one as a recipient address
    const [_, userAddress, relayerAddress, recipientAddress] =
      await ethers.getSigners();

    // Mint 10,000 tokens to user address (for testing)
    const tenThousandTokensWithDecimals = parseEther("10000");
    const userTokenContractInstance = randomTokenContract.connect(userAddress);
    const mintTxn = await userTokenContractInstance.freeMint(
      tenThousandTokensWithDecimals
    );
    await mintTxn.wait();

    // Have user infinite approve the token sender contract for transferring 'RandomToken'
    const approveTxn = await userTokenContractInstance.approve(
      tokenSenderContract.address,
      BigNumber.from(
        // This is uint256's max value (2^256 - 1) in hex
        // Fun Fact: There are 64 f's in here.
        // In hexadecimal, each digit can represent 4 bits
        // f is the largest digit in hexadecimal (1111 in binary)
        // 4 + 4 = 8 i.e. two hex digits = 1 byte
        // 64 digits = 32 bytes
        // 32 bytes = 256 bits = uint256
        "0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff"
      )
    );
    await approveTxn.wait();

    // Have user sign message to transfer 10 tokens to recipient
    const transferAmountOfTokens = parseEther("10");
    const messageHash = await tokenSenderContract.getHash(
      userAddress.address,
      transferAmountOfTokens,
      recipientAddress.address,
      randomTokenContract.address
    );
    const signature = await userAddress.signMessage(arrayify(messageHash));

    // Have the relayer execute the transaction on behalf of the user
    const relayerSenderContractInstance =
      tokenSenderContract.connect(relayerAddress);
    const metaTxn = await relayerSenderContractInstance.transfer(
      userAddress.address,
      transferAmountOfTokens,
      recipientAddress.address,
      randomTokenContract.address,
      signature
    );
    await metaTxn.wait();

    // Check the user's balance decreased, and recipient got 10 tokens
    const userBalance = await randomTokenContract.balanceOf(
      userAddress.address
    );
    const recipientBalance = await randomTokenContract.balanceOf(
      recipientAddress.address
    );

    expect(userBalance.lt(tenThousandTokensWithDecimals)).to.be.true;
    expect(recipientBalance.gt(BigNumber.from(0))).to.be.true;
  });
});
```

代码在评论里有解释，但让我们试着运行测试。你可以在终端输入`npx hardhat test`来运行测试，如果你做的一切正确，应该会通过

这意味着，在初步批准后，发送方能够向接收方转移10个代币，而不需要自己支付汽油。你可以进一步轻松地扩展这个测试，看看这甚至会对多次转账起作用，只要你不超过用户的10000个代币的余额。

## 安全漏洞

不过你能猜到我们刚才写的代码有什么问题吗？🤔

由于签名包含必要的信息，中继者可以不断地将签名发送给合同，从而不断地将代币从`sender's`的账户转移到`recipient's`的账户。

虽然在这个具体的例子中，这似乎不是什么大问题，但如果这个合同是负责处理主网的资金问题呢？如果同样的签名可以被反复使用，用户将失去他们所有的代币

相反，只有当用户明确提供第二个签名时，交易才会被执行（当然是在智能合约的规则内）。

这种攻击被称为签名重放--因为，你猜对了，你重放了一个签名。

## 解决签名回放的问题

对于更简单的合同，你可以通过在合同中设置一些（嵌套）的映射来解决这个问题。但是每次转账有4个变量需要跟踪--`sender`、`amount`、`recipient`和`tokenContract`。在Solidity中，创建一个这么深的嵌套映射可能是相当昂贵的。

而且，每一种智能合约的 "种类 "都是不同的--因为你并不总是在处理相同的用例。一个更通用的解决方案是创建一个从参数哈希到布尔值的单一映射，其中`true`表示这个元交易已经被执行，`false`表示没有。

类似于`mapping(bytes32 => bool)`。

不过这也有一个问题。在目前的参数设置下，如果Alice向Bob发送了10个令牌，它将在第一次通过，并且映射将被更新以反映这一点。然而，如果Alice真的想在第二次向Bob再发送10个代币呢？

由于数字签名是确定性的，也就是说，对于同一组密钥来说，相同的输入将得到相同的输出，这意味着Alice将永远无法再向Bob发送10个令牌了

为了避免这种情况，我们引入了第五个参数，即`nonce`。

`nonce`只是一个随机数值，可以由用户选择，也可以由合同选择，还可以随机生成，这都不重要--只要用户的签名包括这个nonce。由于完全相同的交易但使用不同的nonce会产生不同的签名，所以上述问题就解决了!

让我们来实施这个🚀

### 智能合约的变化

我们需要在三个地方更新智能合约的代码。

首先，我们将添加一个`mapping(bytes32 => bool)`来跟踪哪些签名已经被执行。

第二，我们将更新我们的辅助函数`getHash`，以接受一个`nonce`参数，并将其纳入哈希值。

第三，我们将更新我们的`transfer`函数，以便在验证签名时也接收`nonce`并将其传递给`getHash`。它也会在签名验证后更新映射。

下面是更新后的代码。

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.4;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/utils/cryptography/ECDSA.sol";

contract RandomToken is ERC20 {
    constructor() ERC20("", "") {}

    function freeMint(uint amount) public {
        _mint(msg.sender, amount);
    }
}

contract TokenSender {

    using ECDSA for bytes32;

    // New mapping
    mapping(bytes32 => bool) executed;

    // Add the nonce parameter here
    function transfer(address sender, uint amount, address recipient, address tokenContract, uint nonce, bytes memory signature) public {
        // Pass ahead the nonce
        bytes32 messageHash = getHash(sender, amount, recipient, tokenContract, nonce);
        bytes32 signedMessageHash = messageHash.toEthSignedMessageHash();

        // Require that this signature hasn't already been executed
        require(!executed[signedMessageHash], "Already executed!");

        address signer = signedMessageHash.recover(signature);

        require(signer == sender, "Signature does not come from sender");

        // Mark this signature as having been executed now
        executed[signedMessageHash] = true;
        bool sent = ERC20(tokenContract).transferFrom(sender, recipient, amount);
        require(sent, "Transfer failed");
    }

    // Add the nonce parameter here
    function getHash(address sender, uint amount, address recipient, address tokenContract, uint nonce) public pure returns (bytes32) {
        return keccak256(abi.encodePacked(sender, amount, recipient, tokenContract, nonce));
    }
}
```

让我们也来更新我们的测试以反映这一点。我们将有两个测试--一个是证明签名不能再被重放，另一个是证明两个不同的签名与不同的nonces仍然可以工作，即使其他东西都是一样的。

以下是最新的测试

```
const { expect } = require("chai");
const { BigNumber } = require("ethers");
const { arrayify, parseEther } = require("ethers/lib/utils");
const { ethers } = require("hardhat");

describe("MetaTokenTransfer", function () {
  it("Should let user transfer tokens through a relayer with different nonces", async function () {
    // Deploy the contracts
    const RandomTokenFactory = await ethers.getContractFactory("RandomToken");
    const randomTokenContract = await RandomTokenFactory.deploy();
    await randomTokenContract.deployed();

    const MetaTokenSenderFactory = await ethers.getContractFactory(
      "TokenSender"
    );
    const tokenSenderContract = await MetaTokenSenderFactory.deploy();
    await tokenSenderContract.deployed();

    // Get three addresses, treat one as the user address
    // one as the relayer address, and one as a recipient address
    const [_, userAddress, relayerAddress, recipientAddress] =
      await ethers.getSigners();

    // Mint 10,000 tokens to user address (for testing)
    const tenThousandTokensWithDecimals = parseEther("10000");
    const userTokenContractInstance = randomTokenContract.connect(userAddress);
    const mintTxn = await userTokenContractInstance.freeMint(
      tenThousandTokensWithDecimals
    );
    await mintTxn.wait();

    // Have user infinite approve the token sender contract for transferring 'RandomToken'
    const approveTxn = await userTokenContractInstance.approve(
      tokenSenderContract.address,
      BigNumber.from(
        // This is uint256's max value (2^256 - 1) in hex
        "0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff"
      )
    );
    await approveTxn.wait();

    // Have user sign message to transfer 10 tokens to recipient
    let nonce = 1;

    const transferAmountOfTokens = parseEther("10");
    const messageHash = await tokenSenderContract.getHash(
      userAddress.address,
      transferAmountOfTokens,
      recipientAddress.address,
      randomTokenContract.address,
      nonce
    );
    const signature = await userAddress.signMessage(arrayify(messageHash));

    // Have the relayer execute the transaction on behalf of the user
    const relayerSenderContractInstance =
      tokenSenderContract.connect(relayerAddress);
    const metaTxn = await relayerSenderContractInstance.transfer(
      userAddress.address,
      transferAmountOfTokens,
      recipientAddress.address,
      randomTokenContract.address,
      nonce,
      signature
    );
    await metaTxn.wait();

    // Check the user's balance decreased, and recipient got 10 tokens
    let userBalance = await randomTokenContract.balanceOf(userAddress.address);
    let recipientBalance = await randomTokenContract.balanceOf(
      recipientAddress.address
    );

    expect(userBalance.eq(parseEther("9990"))).to.be.true;
    expect(recipientBalance.eq(parseEther("10"))).to.be.true;

    // Increment the nonce
    nonce++;

    // Have user sign a second message, with a different nonce, to transfer 10 more tokens
    const messageHash2 = await tokenSenderContract.getHash(
      userAddress.address,
      transferAmountOfTokens,
      recipientAddress.address,
      randomTokenContract.address,
      nonce
    );
    const signature2 = await userAddress.signMessage(arrayify(messageHash2));
    // Have the relayer execute the transaction on behalf of the user
    const metaTxn2 = await relayerSenderContractInstance.transfer(
      userAddress.address,
      transferAmountOfTokens,
      recipientAddress.address,
      randomTokenContract.address,
      nonce,
      signature2
    );
    await metaTxn2.wait();

    // Check the user's balance decreased, and recipient got 10 tokens
    userBalance = await randomTokenContract.balanceOf(userAddress.address);
    recipientBalance = await randomTokenContract.balanceOf(
      recipientAddress.address
    );

    expect(userBalance.eq(parseEther("9980"))).to.be.true;
    expect(recipientBalance.eq(parseEther("20"))).to.be.true;
  });

  it("Should not let signature replay happen", async function () {
    // Deploy the contracts
    const RandomTokenFactory = await ethers.getContractFactory("RandomToken");
    const randomTokenContract = await RandomTokenFactory.deploy();
    await randomTokenContract.deployed();

    const MetaTokenSenderFactory = await ethers.getContractFactory(
      "TokenSender"
    );
    const tokenSenderContract = await MetaTokenSenderFactory.deploy();
    await tokenSenderContract.deployed();

    // Get three addresses, treat one as the user address
    // one as the relayer address, and one as a recipient address
    const [_, userAddress, relayerAddress, recipientAddress] =
      await ethers.getSigners();

    // Mint 10,000 tokens to user address (for testing)
    const tenThousandTokensWithDecimals = parseEther("10000");
    const userTokenContractInstance = randomTokenContract.connect(userAddress);
    const mintTxn = await userTokenContractInstance.freeMint(
      tenThousandTokensWithDecimals
    );
    await mintTxn.wait();

    // Have user infinite approve the token sender contract for transferring 'RandomToken'
    const approveTxn = await userTokenContractInstance.approve(
      tokenSenderContract.address,
      BigNumber.from(
        // This is uint256's max value (2^256 - 1) in hex
        "0xffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff"
      )
    );
    await approveTxn.wait();

    // Have user sign message to transfer 10 tokens to recipient
    let nonce = 1;

    const transferAmountOfTokens = parseEther("10");
    const messageHash = await tokenSenderContract.getHash(
      userAddress.address,
      transferAmountOfTokens,
      recipientAddress.address,
      randomTokenContract.address,
      nonce
    );
    const signature = await userAddress.signMessage(arrayify(messageHash));

    // Have the relayer execute the transaction on behalf of the user
    const relayerSenderContractInstance =
      tokenSenderContract.connect(relayerAddress);
    const metaTxn = await relayerSenderContractInstance.transfer(
      userAddress.address,
      transferAmountOfTokens,
      recipientAddress.address,
      randomTokenContract.address,
      nonce,
      signature
    );
    await metaTxn.wait();

    // Have the relayer attempt to execute the same transaction again with the same signature
    // This time, we expect the transaction to be reverted because the signature has already been used.
    expect(relayerSenderContractInstance.transfer(
      userAddress.address,
      transferAmountOfTokens,
      recipientAddress.address,
      randomTokenContract.address,
      nonce,
      signature
    )).to.be.revertedWith('Already executed!');
  });
});
```

这里的第一个测试是让用户用两个不同的nonces签署两个不同的签名，并且中继器都执行了这两个签名。然而，在第二个测试中，中继器试图执行同一个签名两次。在中继器第二次试图使用相同的签名时，我们希望交易能被还原。

如果你在这里运行`npx hardhat test`，并且所有测试都成功了，这意味着带有重放攻击的第二个交易被还原了。

这表明签名重放不再发生，该漏洞已被保护起来!🥳🥳

## 总结

这是个比我预期的要长的教程，但我希望你能学到一些很酷的东西!元交易是一个伟大的实用工具，但要确保你正确地使用它们。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
