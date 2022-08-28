---
title: Lazy minting(懒惰铸造)：一种更实惠、更便捷的 NFT 铸造方式
description: null
author: 李留白
weight: 0
date: 2022-08-28T01:42:00.287Z
lastmod: 2022-08-28T02:06:49.980Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/21_12_How-to-Mint-NFTs-for-Free-Without-Paying-Gas.jpg
---

在区块链主网上铸造 NFT通常会花费一些钱，因为将数据写入区块链需要费用（通常称为*gas*）来支付计算和存储费用。这对于 NFT 创作者来说可能是一个障碍，尤其是那些可能不想在知道自己的作品是否会出售之前先投入大量资金的 NFT 新手。

使用一些先进的技术，可以将铸造 NFT 的成本推迟到它卖给第一个买家的那一刻。铸造的汽油费被计入将 NFT 分配给买方的同一笔交易中，因此 NFT 的创建者永远不必为铸造支付费用。相反，购买价格的一部分只是用于支付创建初始 NFT 记录所需的额外气体。

在购买时“及时”铸造通常被称为*惰性铸造*，它已被[OpenSea 等市场采用](https://opensea.io/blog/announcements/introducing-the-collection-manager/)通过使创建 NFT 成为可能而无需任何前期成本，从而降低 NFT 创建者的进入门槛。

本指南将展示一个以太坊上的惰性铸造示例，使用一些来自[OpenZeppelin的帮助库和基础合约](https://openzeppelin.com/contracts/). 如果您一般不熟悉铸造 NFT，我们的[铸造服务教程](https://nftschool.dev/tutorial/minting-service/)是了解基础知识的好地方。

在整个指南中，我们将引用一个示例项目，该项目位于[NFT School 示例存储库中](https://github.com/ipfs-shipyard/nft-school-examples). 如果您想深入研究，请克隆 repo 并在您喜欢的编辑器中打开示例：

```bash
git clone https://github.com/ipfs-shipyard/nft-school-examples
cd nft-school-examples/lazy-minting
code . # or whichever editor you prefer
```

## 惰性铸造是如何工作的

惰性铸造的基本前提是，NFT 创建者不是通过调用合约函数直接创建 NFT，而是使用其以太坊帐户的私钥准备一些数据的加密签名。

签名数据充当“凭证”或可以兑换 NFT 的票证。凭证包含将进入实际 NFT 的所有信息，并且它可能包含未记录在区块链中的其他数据，正如我们稍后会在讨论价格时看到的那样。签名证明 NFT 创建者授权创建凭证中描述的特定 NFT。

当买家想要购买 NFT 时，他们会调用一个`redeem`函数来兑换签名的凭证。如果签名有效并且属于授权铸造 NFT 的账户，则基于凭证创建新代币并将其转移给买方。

对于我们的示例，我们使用的是[Solidity `struct`](https://docs.soliditylang.org/en/v0.4.24/types.html#structs)代表我们的代金券：

```solidity
struct NFTVoucher {
  uint256 tokenId;
  uint256 minPrice;
  string uri;
  bytes signature;
}
```

凭证包含将记录到区块链中的两条信息：唯一的`tokenId`，以及`uri`用于令牌元数据的。`minPrice`没有记录，但它在我们的功能`redeem`中用于允许创建者设置购买价格。如果`minPrice`大于零，则买方在调用时需要发送至少那么多的以太币`redeem`。

我们结构中的`signature`字段包含由 NFT 创建者准备的签名，如[下一节所述](#创建签名凭证)。

> 小费
>
> 在凭证中设置购买价格并不总是必要的，但您可能需要某种条件。否则，任何拥有代金券的人都可以仅用 gas 费用申请 NFT！
>
> 例如，如果您将 NFT“空投”到特定帐户并预先知道收件人地址，则您的凭证可能包含一个`address recipient`字段而不是 a `minPrice`，并且您的`redeem`函数可以检查以确保`msg.sender == voucher.recipient`.

## 创建签名凭证

使用签名进行授权可能会很棘手，因为狡猾的第三方可能会获取在一个上下文中签名的一些数据并将其呈现在其他地方。例如，他们可能会获取授权在 Ropsten 测试网上创建 NFT 的签名，并将其提交给部署在主网上的合约。除非被签名的数据包含一些上下文信息，否则这种“重放攻击”执行起来相当简单，而且很难防御。

为了解决这些问题并在签名消息时提供更好的用户体验，以太坊社区开发了[EIP-712](https://eips.ethereum.org/EIPS/eip-712)，用于签署类型化结构化数据的标准。使用 EIP-712 创建的签名被“绑定”到在特定网络上运行的智能合约的特定实例。它们还包含类型信息，以便[MetaMask等工具](https://metamask.io/)可以向用户提供有关正在签名的数据的更多详细信息，而不是不透明的十六进制字符串。

我们的示例使用了一个 JavaScript 类，调用该类`LazyMinter`来使用 EIP-712 准备签名凭证。因为签名绑定到特定的合约实例，所以你需要提供部署的合约的地址和一个 ethers.js[`Signer`](https://docs.ethers.io/v5/api/signer/)对于 NFT 创建者的私钥：

```js
const lazyminter = new LazyMinter({ myDeployedContract.address, signerForMinterAccount })
```

以下是创建签名 NFT 凭证的主要`createVoucher`方法：

```js
  async createVoucher(tokenId, uri, minPrice = 0) {
    const voucher = { tokenId, uri, minPrice }
    const domain = await this._signingDomain()
    const types = {
      NFTVoucher: [
        {name: "tokenId", type: "uint256"},
        {name: "minPrice", type: "uint256"},
        {name: "uri", type: "string"},  
      ]
    }
    const signature = await this.signer._signTypedData(domain, types, voucher)
    return {
      ...voucher,
      signature,
    }
  }
```

首先，我们准备我们的未签名`voucher`对象并获取用于 EIP-712的*签名域。*该`types`对象包含我们`NFTVoucher`的字段的类型信息（不包括签名本身）。

为了创建签名，我们调用对象的`_signTypedData`方法`Signer`，传入域、类型定义和未签名的凭证对象。

最后，我们返回包含签名的完整凭证对象，可以在我们的智能合约中兑换。

警告

该`_signTypedData`方法将`signTypedData`在 ethers.js 的未来版本中重命名！有关更多信息，请参阅[ethers 文档](https://docs.ethers.io/v5/api/signer/#Signer-signTypedData).

## 在链上兑换代金券

为了让惰性铸造发挥作用，我们需要一个 NFT 购买者可以调用的智能合约功能，该功能将铸造他们想要的 NFT 并将其分配到他们的账户，所有这些都在一次交易中完成。我们的叫`redeem`：

```solidity
  function redeem(address redeemer, NFTVoucher calldata voucher) public payable returns (uint256) {
    // make sure signature is valid and get the address of the signer
    address signer = _verify(voucher);

    // make sure that the signer is authorized to mint NFTs
    require(hasRole(MINTER_ROLE, signer), "Signature invalid or unauthorized");

    // make sure that the redeemer is paying enough to cover the buyer's cost
    require(msg.value >= voucher.minPrice, "Insufficient funds to redeem");

    // first assign the token to the signer, to establish provenance on-chain
    _mint(signer, voucher.tokenId);
    _setTokenURI(voucher.tokenId, voucher.uri);
    
    // transfer the token to the redeemer
    _transfer(signer, redeemer, voucher.tokenId);

    // record payment to signer's withdrawal balance
    pendingWithdrawals[signer] += msg.value;

    return voucher.tokenId;
  }
```

首先我们调用一个`_verify`辅助函数，它要么返回准备签名的账户地址，要么在签名无效时恢复交易。

一旦我们有了签名者的地址，我们就会检查他们是否有权使用[OpenZeppelin 基于角色的 AccessControl 合约](https://docs.openzeppelin.com/contracts/4.x/access-control)`hasRole`中的函数创建 NFT[](https://docs.openzeppelin.com/contracts/4.x/access-control).

我们还确保买方已发送足够的 ETH 来支付`minPrice`. 如果是这样，我们可以根据凭证中的信息创建一个新的令牌并将其转移到`redeemer`帐户中。

最后，我们将付款放入一个名为 的映射`pendingWithdrawals`中，以便 NFT 创建者稍后可以取出他们的 ETH。

而已！如果您对签名验证感到好奇，请参阅[合约源](https://github.com/ipfs-shipyard/nft-school-examples/blob/main/lazy-minting/contracts/LazyNFT.sol)以及[OpenZeppelin EIP-712 基础合约的文档](https://docs.openzeppelin.com/contracts/4.x/api/utils#EIP712).

## 懒惰铸造有什么好处？

懒惰铸造有几个好处，包括：

- **降低 gas 费的影响**：通过将 gas 费支付转移到 mint NFT 售后，卖家可以将费用纳入他们的定价模型，从而减少高额费用对崭露头角的艺术家的威慑力。
- **降低准入门槛**：如前所述，惰性铸造的主要好处之一是它降低了铸造 NFT 所需的高昂 gas 费用所带来的准入门槛，使艺术家和开发人员更能负担得起这一过程。
- **鼓励流动性**：惰性铸造鼓励[**流动性**](https://phemex.com/academy/measure-liquidity-and-use-it-to-make-trading-decisions)，因为 NFT 只能在出售后转移。这有助于防止卖家留下一堆铸造的、未售出的 NFT，并且买家不会无限期地等待 NFT 转移给他们。

## 懒惰铸造的缺点是什么？

懒惰铸造也有一些缺点，包括：

- **失去控制**：懒惰的铸造消除了前期的gas成本，但放弃了艺术家或开发者的一定程度的控制。通过选择懒惰的薄荷，卖家不再对谁购买他们的作品以及何时转让拥有最终决定权。

- **欺诈**的可能性：如果铸造的 NFT 没有被出售，它仍然由艺术家或开发商拥有，他们被激励出售以支付汽油费。然而，通过提供惰性铸造，如果有人出售 NFT 但从未铸造或转让给买方，则可能存在欺诈的可能性。

## 结论

惰性铸造是不可替代数字资产市场的一个蓬勃发展趋势，对艺术家和开发者有很多好处。它消除了铸造 NFT 的前期成本，使该过程更加经济实惠且易于使用。此外，懒惰铸造的好处并没有保留给艺术家和开发人员，因为只有出售的 NFT 被铸造，从区块链中删除了不必要的耗电交易。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
