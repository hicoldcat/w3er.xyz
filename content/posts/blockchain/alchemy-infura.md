---
title: Alchemy vs. Infura：哪个是最好的区块链节点服务商？
description: null
author: 李留白
weight: 0
date: 2022-05-15T01:59:10.831Z
lastmod: 2022-07-01T16:47:00.599Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/alchemy-vs-infura-which-node-provider-best.png
---

当我们开始开发DApp，托管智能合约需要你建立一个你想托管它们的区块链节点，但这个过程不仅对刚刚开始Web3之旅的人来说很复杂；它也可能很耗时，需要大量的处理能力来完成。

这就是Infura和Alchemy这样的平台所要解决的问题。从本质上讲，它们是节点提供者；也就是说，它们提供对区块链服务的远程访问，所以你不必运行自己的节点。

在这篇文章中，我们将比较Infura和Alchemy这两个最受欢迎的节点供应商。我们的比较将基于每个平台支持的网络类型、它们的界面、API、文档、在这些平台上加入团队成员可能有多容易或多复杂，以及它们都提供的任何其他服务。

在文章的最后还提供了一个更简洁的表格，以便快速回顾。下面是我们要介绍的内容。

## 什么是Alchemy？
> [https://www.alchemy.com/](https://www.alchemy.com/)

Alchemy是一个区块链扩展平台，允许开发者安全地创建、测试和监控他们的去中心化应用程序（DApps）。该平台提供可靠的网络连接和节点管理端点。

他们简化了去中心化的开发，并超越了仅仅提供远程节点的功能，如Notify —— 它允许开发人员根据区块链活动向用户发送实时推送通知的关键事件，以及他们的NFT API —— 它提供了一套服务，允许你在多个区块链上即时查找、验证和显示任何NFT。

### 支持的网络

Alchemy平台支持在以太坊第一层主网以及Rinkeby、Goerli、Kovan和Ropsten网络等测试网开发DApp。

此外，Alchemy支持Polygon、Arbitrum网络和Optimism，这些都是第二层网络。第2层网络是在以太坊（第1层）之上创建的不同的链，作为智能合约，允许更快的交易速度和更便宜的气体价格，同时也提高了合约的速度和可扩展性。

Alchemy支持Polygon主网及其测试网（Polygon Mumbai），此外还支持Arbitrum和Optimism主网和测试网。

### Alchemy 界面

你可以从它的仪表板上即时访问你的所有应用程序，以及快速访问关于每个应用程序的简明指标，如总的请求和响应，无效请求的数量，以及更多的信息。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220515101616.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220515101548.png)

查看每个应用程序的额外细节，使你可以选择正确配置每个应用程序，以及更好的分析视图，使你可以按日期、类型、甚至国家过滤请求。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220515101722.png)

还值得注意的是，你可以访问Alchemy的 composer，这个平台允许你直接使用网络表格配置和发送区块链请求。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220515101748.png)

### API、文档和DX

Alchemy的所有服务和集成都可以作为顶级的API，可以通过HTTP或WebSockets访问，他们为所有的服务和网络连接提供了大量的文档以及实践教程。此外，该团队开发了一个围绕Web3.js的包装器，以便在开发DApps时更容易集成和改进对各种API的访问。

Alchemy还为您的应用程序提供了安全功能。提供的预防措施包括白名单地址，以便只有这些地址可以与您的合同互动。另一个安全解决方案是添加白名单的域和IP，以便只能从这些媒介使用您的API密钥进行请求。

最后，你可以轻松地将团队成员纳入你的项目。你可以直接从用户设置页面邀请新成员加入你的项目，也可以为首选用户提供管理权限。

### 定价

Alchemy提供了一个慷慨的免费计划，没有每日请求限制，每月最多有3亿个计算单元。此外，通过这种免费订阅，你可以构建多达五个不同的应用程序，并将它们连接到平台的主网和测试网。你还可以免费使用其他 alchemy 解决方案，包括Supernode、Build、Monitor和Notify。

你可以将你的应用程序升级到增长计划，每月花费49美元。这将每月的计算单元增加到4亿，应用程序的数量增加到15个。欲了解更多信息，你可以在这里查看他们的[定价页面](https://www.alchemy.com/pricing)。


## 什么是 Infura ?
> [https://infura.io/](https://infura.io/)

Infura的存在时间比Alchemy长很多。最初成立于2016年，Infura后来被Metamask的母公司ConsenSys收购。

与Alchemy一样，Infura为你的以太坊项目提供了一个简单而可靠的基础设施。但他们也通过他们的IPFS API更进一步，这是一个在区块链上存储和访问文件的去中心化的协议。

### 支持的网络

Infura还与以太坊第一层主网和Rinkeby、Goerli、Kovan和Ropsten网络等测试网合作。此外，infura支持Palm网络，这是一个专门用于出售、购买和交易NFT的以太坊侧链。

第二层网络，如Polygon、Arbitrum和Optimism也被支持，但它们是作为附加组件配置的，需要你在激活它们的免费计划之前输入你的信用卡信息。

### Infura 界面

Infura也有一个直观的界面。从仪表板上，你可以看到你所有的应用程序，它们的状态，以及它们在一天中的请求数量。查看每个应用程序的更多细节将显示关于该应用程序的更多分析。

你还可以访问Infura Explorer，它允许你管理所有的IPFS项目，分析上传的数据以及实时数据。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220515102321.png)

### API、文档和DX

与Alchemy一样，Infura的服务以顶级API的形式提供，也可以通过HTTP和WebSockets访问，他们还提供全面的文档，指导开发者使用他们的API。此外，与Alchemy不同，Infura有一个专门的[社区](https://community.infura.io/)来讨论Infura的所有事情。

Infura提供了更多的选项来帮助你保护你的应用程序。其中之一是将API的使用限制在包含有效JWT（JSON网络令牌）的请求。你也可以指定每秒钟或每天向你的应用程序发送的请求总数。

还值得注意的是，Infura目前不允许你邀请团队成员加入和管理你的应用程序。

### 定价

Infura还提供了一个可观的免费计划，每天有多达100,000个请求，并且能够创建多达三个独立的应用程序。你也可能将你的应用程序升级到所提供的计划之一，这些计划从每月50美元到1000美元不等。

对于其IPFS API，你可以得到5GB的免费存储空间，用于存储和管理数据，这也可以作为一个附加功能，需要你的信用卡信息。因此，你可以通过Infura Plus计划将你的IPFS存储空间升级到无限，该计划的存储上限为0.08美元/GB，数据传输上限为0.12美元/GB。

## 总结

Infura和Alchemy都是很好的产品，但虽然都是节点提供商，但它们都与众不同地提供一些其他服务，以使你的DApp获得成功。在这篇文章中，我们仔细比较了这两个平台，以帮助你轻松决定为你的下一个项目选择哪个。

在Alchemy和Infura之间的选择没有一个放之四海而皆准的答案，因为两者都能很好地满足不同的场景。然而，除了价格方面的考虑和所有这些，当与队友合作时，Alchemy可能是一个更好的选择，另外，你可以获得所有你需要的服务，而无需添加你的信用卡信息。而Infura可能被认为是个人项目的更好选择。

为了快速回顾，下面还提供了一个更简洁的表格。

<table>
  <thead>
    <tr>
        <td></td> 
        <td>Alchemy</td> 
        <td>Infura</td>
   </tr>
  </thead>
    <tr>
        <td rowspan="2">支持的第1层网络</td>    
        <td >以太坊主网</td>  
        <td >以太坊主网</td> 
    </tr>
    <tr>
        <td >测试网 - Rinkeby, Goerli, Kovan, 和Ropsten网络</td>  
        <td >测试网 - Rinkeby, Goerli, Kovan, 和Ropsten网络</td>  
    </tr>
    <tr>
        <td >支持的第2层网络</td>    
        <td >Polygon, Arbitrum network, and Optimism</td>  
        <td >Polygon, Arbitrum, Optimism, and the Palm network</td> 
    </tr>
     <tr>
        <td >额外服务</td>    
        <td >Alchemy Notify, NFT API	</td>  
        <td >IPFS API</td> 
    </tr>
    <tr>
        <td >免费计划</td>    
        <td >每月3亿个计算单元，最多可用于5个应用程序</td>  
        <td >每天有100,000个请求，最多有3个不同的应用程序。免费的5GB IPFS存储空间。</td> 
    </tr>
    <tr>
        <td >付费计划</td>    
        <td >每月49美元的增长计划</td>  
        <td >每月50美元至1000美元不等</td> 
    </tr>
</table>

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)