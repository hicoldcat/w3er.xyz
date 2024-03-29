---
title: 什么是多重签名钱包，它们是如何工作的？
description: null
author: 李留白
weight: 0
date: 2022-09-05T14:00:36.766Z
lastmod: 2022-09-05T14:00:41.101Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/bitpanda-academy-expert-19-what-are-multisignature-wallets-header.jpg
---

## 什么是多重签名钱包（Multisig Wallet）？

Multisig表示多重签名，而多重签名是一种特定类型的数字签名，而此类型的签名将允许两个以上用户作为一组来签署文档。因此，多重签名则通过多个单一签名的组合来产生。现在多重签名技术已经应用于加密货币世界，而其实这一原理其实早在比特币诞生之前就存在了。

加密货币背景下的多重签名技术，在2012年第一次被用于比特币地址，而一年之后此次应用则催生出了多重签名钱包。多重签名地址可以在不同环境中使用，但大多情况都是用于与安全问题有关的领域。而本文我们将讨论该技术在加密货币钱包中的使用。

## 如何运行？

做一个简单的类比，设想一个拥有两把锁和两把钥匙的保险箱。一把钥匙是由Alice持有，另一把则由Bob掌管。而打开此保险箱的唯一办法则是这两个人同时提供钥匙，而当只有其中一把钥匙时，该保险箱则是打不开的。

而这也就是说，存储资金的多重签名地址也只能通过使用2个或更多重签名才能访问。因此，多重签名钱包的使用就可以为用户的资金创建额外的安全层。但在进一步研究之前，我们首先对标准比特币地址进行了解，因为它是依赖于单一密钥，而并非多重签名的。

## 单密钥vs 多重签名

通常，比特币都被存储在标准的单密钥地址中，这就意味着任何人只要持有了对应的私钥就可以访问该笔资金。而这同时也意味着，只需一个密钥就可以签署交易，且任何人只要拥有私钥就可以在不得到任何授权的情况下转移地址中的代币。

的确，单密钥地址与多重签名地址相比更便于管理，但其中也呈现出一系列问题，尤其是在安全方面。由于只有单个密钥，所以资金是由单点故障来保护的，而这导致了网络犯罪分子不断开发新的钓鱼技术来窃取加密货币用户的资金。

另外，对于涉及加密货币的企业来说，单密钥地址并不是最好的选择。试想一下，一家大公司将资金存储在一个标准地址中，且该地址只有唯一对应私钥。这就意味着，该私钥要么只交付给一个人掌管，要么同时委托给多个人掌管，但这两种方式显然都并不是最安全且最佳的方式。

而此时，多重签名钱包就为以上的问题提供了解决方案。多重签名完全不同于单密钥，即当资金被存储在多重签名地址时，只有提供了多个签名（通过不同的私钥生成）时才允许转移资该资金。

多重签名地址可设定其所需的密钥组合：最常见的就是三分之二（2/3），此类地址表示只需提供2个起签名就可以访问3签名地址中的资金。其实，还存在许多其他类型，如2/2（二分之二）、3/3（三分之三）、3/4（四分之三）等等。

这项技术具有很多潜在应用。以下则是多重签名加密钱包的一些常见用例。

## 增加安全性

通过使用多重签名钱包，用户就能够避免由于私钥丢失或被盗所引起的安全性问题。因此，即使其中之一的密钥被盗，资金也将是安全的。

假设Alice创建了一个2/3的多重签名钱包，之后将每个私钥存储在不同的地方或设备中（如移动电话、笔记本电脑或平板电脑）。则结果是，即使她的移动设备被盗，窃贼也不可能使用仅有的1个密钥来盗取Alice的资金。同样，钓鱼攻击和恶意软件感染也不太可能成功，因为黑客仅有可能黑入单一设备或盗取单一密钥。

尚不考虑恶意攻击，倘若Alice丢失了一个私钥，则她仍可以使用其他两个密钥来访问她的资金。

## 双因素认证

通过创建一个双密钥的多重签名钱包，Alice就可以为她的资金建立一个双因素认证机制。例如，Alice可以将她的一个私钥存储在笔记本电脑，而另一个存储在她的移动设备上（甚至一张纸上）。这就可以确保，只有当某人可以同时访问这两个密钥时才可以进行资金交易。

然而，请切记，使用多重签名技术来作为双因素认证时也可能存在风险的（特别是2/2多重签名地址）。因为，如果当其中一个密钥丢失时，你将丢失资金的访问权。因此，使用2/3的设置，或使用带有备份代码的第三方2FA（双因素认证）服务会更安全些。对于交易所交易账户，强烈推荐使用Google Authenticator（谷歌身份验证器）。

## 第三方托管交易

创建一个2/3的多重签名钱包则可以允许双方（Alice和Bob）之间进行第三方托管交易，双方之间还存在一个第三方（Charlie）来作为相互信任的仲裁者，以防出现信任危机。

此情况下，Alice首先需要在钱包中存入资金，之后该资金将被锁定且任何用户都不能单独访问该笔资金。随后，如果Bob按照约定提供了相应的产品或服务，则他们俩就可以使用他们的密钥来签署并完成此次交易。

只有出现分歧时，作为仲裁者的Charlie才需介入。在那时，Charlie将使用他的密钥来创建一份签名，并根据他的裁决来将此签名提供给正确的一方（Alice或Bob）。

## 决策制定

公司董事会可以使用多重签名钱包来控制公司的资金。例如，董事会设置一个4/6多重签名钱包，之后每个董事会成员将拥有持有一个密钥。则最后任何单独的董事会成员都不能滥用这笔资金，因为只有大多数董事会成员同意之后，才能执行对资金的访问。

## 劣势

尽管多重签名钱包可以为一些列问题提供解决方案，但其中还是涉及了一些风险和限制。因为在创建多重签名钱包时需要有必要的技术基础，尤其是当你不想依赖于第三方供应商时尤为明显。

此外，由于区块链和多重签名地址都是相对较新的技术，所以如果出现问题可能很难找到适用的法律来解决。在共享钱包中（具有多个密钥持有）存入的资金很难找到合法托管人。

## 总结

尽管多重签名其拿包含些许不足之处，但其还是有众多令人满意的实际应用，它使得比特币和其他加密货币在商业中更具实用性。其由于要求多个签名来完成资金转移，使得多重签名钱包提供了更高的安全性，并允许了互不信任方的第三方托管交易，诸如此类的优势或将使得该技术在未来得到更广泛的应用。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)