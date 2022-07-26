---
title: Web3系列教程之进阶篇---9. 去中心化的数据网络Ceramic
description: null
author: 李留白
weight: 0
date: 2022-07-26T13:14:27.649Z
lastmod: 2022-07-26T14:10:51.411Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/Dcx1zWU(1).png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220726211454.png)

Ceramic是一个去中心化的数据网络，它允许建立可组合的Web3应用程序。由于Ceramic将应用数据库分散化，应用开发者可以在不同的应用中重复使用数据，并使它们自动具有互通性。

在本文中，我们将深入挖掘这些说法的实际含义，以及为什么去中心化的数据首先是重要的。

## Web3 和数据

在过去的几年里，我们看到了Web3的趋势，如DeFi、NFT，以及最近的DAO的爆发。像以太坊这样的智能合约平台已经向我们表明，像乐高积木一样，可以与其他dApps一起组成全新的dApps的dApps有很大的价值。这一点在建立在其他代币之上的代币、利用其他DeFi协议的DeFi协议等方面尤为突出。

Ceramic 允许您为 Internet 上的数据带来相同类型的可组合性。这可以是任何类型的数据。个人资料、社交关系、博客文章、身份、声誉、游戏资产等。

这是一个抽象的概念，试图将Ceramic Network定义为一个单一的东西是有点棘手的。与以太坊类似，以太坊本身就很难定义（它甚至意味着成为一个智能合约平台？），如果我们看一下Ceramic可以实现的具体用例和未来愿景的例子，就更容易理解。如果我们看一下DeFi协议、NFT、DAO等确切的例子，Ethereum就更容易理解了--在试图理解Ceramic时也可以应用同样的思维。

## Web3 中缺少数据

现在的web3市场主要是由金融应用组成的。与代币和数字资产有关的东西。这在某种程度上是由于设计。智能合约在可以存储多少数据方面有内在的限制，而且可以建立的数据功能也有限。

随着我们的进步和dApps的成熟，市场对构建更多数据丰富的应用程序的需求正在增加。像去中心化的社交媒体、去中心化的信誉平台、去中心化的博客等趋势正在兴起，很多技术方法正在被采用，但在某一方面有所欠缺。

在 Web2 中，这些平台被构建为数据孤岛。您的 Twitter 帖子和社交联系被锁定在 Twitter 平台中 - 您无法将 Twitter 上的社交联系转移到 Facebook，您无法将 Twitter 帖子转移到 Facebook 等。它们都是作为孤岛构建的。

这将改变。Ceramic 押注于可互操作的应用程序和可互操作的生态系统能够胜过这些孤立的平台。

## 那么......Ceramic 是做什么的？

Ceramic 正在构建：

1. 一个通用的数据协议
2. 其中数据只能由所有者修改
3. 大批量的数据处理
4. 具有全球数据的可用性和一致性
5. 支持快速查询
6. 具有跨应用程序的可互操作数据
7. 和社区管理

这是很多的，这也是为什么Ceramic 本身很难定义的原因。毕竟，就像以太坊（Ethereum）一样，它是一种通用协议，尽管是针对数据的。

为了实现Ceramic的真正规模和愿景，需要实现大量的突破。如果Ceramic要成为网络的去中心化数据库，它需要能够大规模地扩展--比今天任何中心化的数据库都要大，因为它们都没有存储整个互联网的数据。

数据还需要在全球范围内可用，并且需要确保 Ceramic 节点运行器将这些数据提供给世界其他地方并且不会劫持它。

此外，不仅仅是存储，Ceramic 还需要快速查询和检索数据。用户通常读取的数据多于写入的数据，因此快速读取和查询对于 Ceramic 大规模工作非常重要。

所有这一切同时保持数据的高度安全性和隐私性，并确保它不会有一天崩溃。

看看今天Ceramic可以实现的具体用例，以及拥有一个可变的、去中心化的、通用的数据协议的好处是很有帮助的。

## Ceramic 使用案例

### 去中心化的信誉

信誉与一个人的身份高度相关。在Twitter上，它是粉丝和喜欢。在Instagram上，它是人心。在Reddit，它是Karma，而在StackOverflow，它是积分。

在今天的web3生态系统中，dApps很难做到比上述例子中的集中式信誉系统更好，每个平台都有自己的信誉系统。这主要是因为存储大量可能随时间变化的去中心化数据是不可行的。而且，即使Ethereum Layer 2的要大量减少存储成本，那么非Ethereum链呢？当你切换到NEAR或Flow或Solana时，你的声誉会发生什么？

进入Ceramic。

dApps可以使用Ceramic的数据协议来建立标准化的多链信誉系统。一个用户可以将多个钱包连接到他们的去中心化身份，属于不同的区块链，数据可以被写入和更新自用户在Ceramic上的去中心化数据存储。

因此，无论用户使用什么链和DApp，他们都可以随身携带他们的信誉系统。

### 社交图谱

与声誉类似，你的社交图谱在当今世界也是严重集中化的。你的Twitter粉丝、Facebook朋友、LinkedIn连接和Snapchat好友都是不同程度的孤岛。

随着集中式社交媒体的审查制度随着时间的推移而增加，而且由于社交媒体公司是世界上最大的公司，其中一些公司有足够的力量操纵整个国家的选举，因此对去中心化的社交媒体的需求从未如此强烈。

然而，如果我们要建立去中心化的社交媒体，让我们试着比旧系统做得更好。与其把用户锁定在一个平台上，我们实际上可以允许互操作性和选择性。

dApps可以遵循标准化的数据模型来存储帖子和社交图。这些社交图由用户携带到他们想使用的任何dApp。这意味着产品的竞争在于谁能提供最好的体验，而不是谁拥有最多的厂商锁定。

这也让拥有更好产品的小公司和初创公司更容易实现市场渗透。他们可以利用已经存在的底层数据，当用户在他们的平台上注册时，他们的所有数据都会随之而来。对于较大的参与者来说，为该数据模型提供大量的数据可以有经济奖励。

### 多钱包和多链身份

您可以推断去中心化声誉用例，为用户实现通用的多钱包和多链身份。无需将数据绑定在智能合约中或基于钱包地址的链下，数据可以绑定到用户的去中心化身份，该身份可以由跨多个链的多个钱包控制。

通过这种方式，多链 dApp 可以为用户数据提供去中心化但单一的真实来源。

## Ceramic 的多链架构

在最底层，有一个去中心化的身份。在Ceramic上，最常见的去中心化身份的方法是一种叫做3ID（Three ID）的东西--以Ceramic Network背后的团队3Box Labs命名。

一个用户可以将多个链上的多个钱包链接到一个3ID上。目前，Ceramic支持10多个区块链，并在不断增加对更多区块链的支持。

3ID 可以拥有 Ceramic Network 上的数据。Ceramic 上的数据称为**Streams**。因此，每个流都有一个所有者（或多个所有者）。

流具有唯一的**StreamID**，它们在流的生命周期内保持不变。3ID 可以修改和更新他们拥有所有权的 Stream 的内容。

流有一个**创世**状态，它是创建流的初始数据。在创世状态之后，用户可以在 Streams 上创建**提交，这表示对数据的修改。**Stream 的最新状态可以通过从创世状态开始并一一应用所有提交来计算。最新状态也称为流的**尖端**。

## 使用Ceramic

Ceramic提供了一套高级和低级的库和SDK，可以根据使用情况进行操作。

对于常见的用例，开发人员可以使用高级别的SDK--Self.ID--它抽象出使用3ID和流的大部分复杂情况。

对于复杂或定制的用例，开发人员可以使用较底层级别的Ceramic HTTP客户端，连接到他们可以在自己的（或公共节点）上运行的Ceramic节点，并手动管理3IDs和流数据。

在本教程中，我们将坚持使用Self.ID高级SDK，否则本教程将变得非常庞大。如果你有兴趣深入研究，请查看下面的推荐资源部分。

### Self.ID

Self.ID是一个单一的、高水平的库，它封装了3ID账户、创建和设置3ID、对Ceramic节点的底层调用，所有这些都在一个单一的软件包中进行了优化，以便在浏览器中工作。

该SDK还包括流行的用例，如内置的多链用户档案，这使得开发人员非常容易检索和存储与3ID相关的多链数据。

## 构建

我们将创建一个简单的Next.js应用程序，它使用Self.ID。它将允许用户使用他们选择的钱包登录到网站，这将与他们的3ID相联系。然后，我们将允许用户将一些数据写入他们的去中心化配置文件，并能够从Ceramic 网络中检索到这些数据。

为了验证这个级别，我们会要求你在最后输入你的个人资料的StreamID。

- 让我们从创建一个新的`next`应用开始。运行以下命令在名为的文件夹中创建一个新的 Next.js 应用程序`ceramic-tutorial`

```bash
npx create-next-app@latest ceramic-tutorial
```

并按下`Enter`所有问题提示。这应该创建`ceramic-tutorial`文件夹并`next`在其中设置应用程序。它还将初始化一个 git 存储库，您可以在进行更改后推送到 GitHub。

- 现在让我们安装 Self.ID npm 包和依赖库以开始使用。从`ceramic-tutorial`文件夹内部，在终端中运行以下命令

```
npm install @self.id/react @self.id/web key-did-provider-ed25519
```

- 让我们也安装我们将用于支持钱包连接`ethers`的包`web3modal`

```
npm install ethers web3modal
```

- 在您选择的文本编辑器中打开`ceramic-tutorial`文件夹，让我们开始编码。
- 我们需要做的第一件事就是将 Self.ID 的 Provider 添加到应用程序中。SDK 公开了一个`Provider`需要添加到 Web 应用程序根目录的组件。这会初始化 Self.ID 实例，连接到 Ceramic 网络，并使 Ceramic 相关的功能在您的应用程序中可用。

- 为此，请注意在您的`pages`文件夹 Next.js 中自动创建了一个名为`_app.js`. 这是您的网络应用程序的根目录，您创建的所有其他页面都根据此文件中的配置呈现。默认情况下，它没有什么特别的，只是直接呈现您的页面。在我们的例子中，我们想用 Self.ID 提供者包装我们的每个组件。

- 首先，让我们`Provider`从 Self.ID导入。在顶部添加以下行`_app.js`

```
import { Provider } from '@self.id/react';
```

- 接下来，改变该文件中的MyApp函数，以返回包裹在`Provider`中的`Component`

```js
function MyApp({ Component, pageProps }) {
  return (
    <Provider client={{ ceramic: "testnet-clay" }}>
      <Component {...pageProps} />;
    </Provider>
  );
}
```

我们还为提供者指定了一个配置选项。具体来说，我们说，我们希望提供者连接到Ceramic的Clay测试网络。

- 让我们确定到目前为止一切都在正常工作。在你的终端运行以下程序

```
npm run dev
```

你的网站应该在http://localhost:3000，并运行起来。

- 最后，在我们开始为实际应用编写代码之前，将`style/Home.module.css`中的CSS替换为以下内容。

```
.main {
  min-height: 100vh;
}

.navbar {
  height: 10%;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  padding-top: 1%;
  padding-bottom: 1%;
  padding-left: 2%;
  padding-right: 2%;
  background-color: orange;
}

.content {
  height: 80%;
  width: 100%;
  padding-left: 5%;
  padding-right: 5%;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.flexCol {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.connection {
  margin-top: 2%;
}

.mt2 {
  margin-top: 2%;
}

.subtitle {
  font-size: 20px;
  font-weight: 400;
}

.title {
  font-size: 24px;
  font-weight: 500;
}

.button {
  background-color: azure;
  padding: 0.5%;
  border-radius: 10%;
  border: 0px;
  font-size: 16px;
  cursor: pointer;
}

.button:hover {
  background-color: beige;
}
```

由于这不是一个CSS教程，我们不会深入研究这些CSS属性，不过如果你想深入研究，你应该能够理解这些属性的含义。

- 好了，打开`pages/index.js`，删除当前存在于`function Home() {...}`内的所有内容。我们将用我们的代码替换该函数的内容。

- 我们将首先初始化Web3Modal的相关代码，因为连接一个钱包是第一步。

- 让我们首先从`ethers`导入`Web3Provider`。

```
import { Web3Provider } from "@ethersproject/providers"
```

- 然后从react中导入这些react hooks，从`web3modal`中导入`Web3Modal`。

```jsx
import { useEffect, useRef, useState } from "react"
import Web3Modal from "web3modal"
```

- 像以前一样，让我们在`Home`函数中使用useRef  react hook 创建一个指向Web3Modal实例的引用，并创建一个辅助函数来获取提供者。

```jsx
const web3ModalRef = useRef();

const getProvider = async () => {
    const provider = await web3ModalRef.current.connect();
    const wrappedProvider = new Web3Provider(provider);
    return wrappedProvider;
}
```

这个函数将提示用户连接他们的Ethereum钱包，如果尚未连接，然后返回一个Web3Provider。然而，如果你现在尝试运行它，它将会失败，因为我们还没有初始化web3Modal。

- 在我们初始化Web3Modal之前，我们将设置一个由Self.ID SDK提供的React Hook。Self.ID提供了一个名为useViewerConnection的钩子，它为我们提供了一个简单的方法来连接和断开与Ceramic 网络的连接。添加以下导入

```jsx
import { useViewerConnection } from "@self.id/react";
```

现在在你的`Home`函数中，做以下工作来初始化hook

```
const [connection, connect, disconnect] = useViewerConnection();
```

- 现在我们有了这个，我们可以初始化Web3Modal。在你的`Home`函数中添加以下useEffect React Hook。

```jsx
useEffect(() => {
    if (connection.status !== "connected") {
      web3ModalRef.current = new Web3Modal({
        network: "rinkeby",
        providerOptions: {},
        disableInjectedProvider: false,
      });
    }
}, [connection.status]);
```

这段代码我们以前已经看过很多次了。唯一不同的是这里的条件。我们正在检查，如果用户还没有连接到Ceramic，我们将初始化web3Modal。

- 我们需要的最后一样东西是一个叫做`EthereumAuthProvider`的东西，以便能够连接到Ceramic 网络。它是由Self.ID SDK导出的一个类，它接受一个Ethereum提供者和一个地址作为参数，并使用它来连接你的Ethereum钱包和你的3ID。

- 要设置这一点，让我们首先导入它

```
import { EthereumAuthProvider } from "@self.id/web";
```

然后添加以下辅助函数

```jsx
const connectToSelfID = async () => {
    const ethereumAuthProvider = await getEthereumAuthProvider();
    connect(ethereumAuthProvider);
};

const getEthereumAuthProvider = async () => {
    const wrappedProvider = await getProvider();
    const signer = wrappedProvider.getSigner();
    const address = await signer.getAddress();
    return new EthereumAuthProvider(wrappedProvider.provider, address);
};
```

`getEthereumAuthProvider`创建`EthereumAuthProvider`的一个实例。你可能想知道为什么我们要把 `wrappedProvider.provider `传给它，而不是直接传给 `wrappedProvider`。这是因为`ethers`用辅助函数抽象了底层的提供者调用，所以它更容易被开发人员使用，但由于不是每个人都使用`ethers.js`，Self.ID维护了一个实际提供者规范的通用接口，而不是ethers包装的版本。我们可以通过`wrappedProvider`上的`provider`属性来访问实际的提供者实例。 `connectToSelfID`使用这个`Ethereum Auth Provider`，并调用我们从`useViewerConnection`钩子得到的connect函数，它为我们处理了其他一切。

- 现在，让我们来了解一下前台的情况。

- 在你的`Home`函数的末尾添加以下代码

```jsx
return (
    <div className={styles.main}>
      <div className={styles.navbar}>
        <span className={styles.title}>Ceramic Demo</span>
        {connection.status === "connected" ? (
          <span className={styles.subtitle}>Connected</span>
        ) : (
          <button
            onClick={connectToSelfID}
            className={styles.button}
            disabled={connection.status === "connecting"}
          >
            Connect
          </button>
        )}
      </div>

      <div className={styles.content}>
        <div className={styles.connection}>
          {connection.status === "connected" ? (
            <div>
              <span className={styles.subtitle}>
                Your 3ID is {connection.selfID.id}
              </span>
              <RecordSetter />
            </div>
          ) : (
            <span className={styles.subtitle}>
              Connect with your wallet to access your 3ID
            </span>
          )}
        </div>
      </div>
    </div>
)
```

为了解释，这里是发生了什么。在页面的右上方，我们有条件地呈现一个连接按钮，将你连接到SelfID，或者如果你已经连接了，它只显示连接了。然后，在页面的主体中，如果你已经连接了，我们显示你的3ID，并渲染一个我们还没有创建的名为RecordSetter的组件（我们很快就会创建）。如果你没有连接，我们会呈现一些文字，要求你先连接你的钱包。

- 到目前为止，我们可以通过 Self.ID 连接到 Ceramic Network，但是在 Ceramic 上实际存储和检索数据呢？

- 我们将考虑在 Ceramic 上构建分散配置文件的用例。值得庆幸的是，Self.ID 内置支持创建和编辑您的个人资料，这是一个如此常见的用例。出于本教程的目的，我们只会为您的 3ID 设置一个名称并对其进行更新，但您可以将其扩展为包括各种其他属性，例如头像图像、您的社交媒体链接、描述、您的年龄、性别、等等

- 为了可读性，我们将把它分成第二个 React 组件。这是`RecordSetter`我们上面使用的组件。

- 首先在同一个文件中创建一个新组件。在你的`Home`函数之外，创建一个像这样的新函数

```jsx
function RecordSetter() {
    
}
```

- 我们将使用 Self.ID 提供给我们的另一个钩子，`useViewerRecord`它允许在 Ceramic Network 上存储和检索配置文件信息。我们先导入它

```
import { useViewerRecord } from "@self.id/react";
```

- `RecordSetter`现在，让我们在组件中使用这个钩子。

```jsx
const record = useViewerRecord("basicProfile");

 const updateRecordName = async (name) => {
    await record.merge({
      name: name,
    });
  };
```

- 我们还创建了一个辅助函数来更新存储在我们记录中的名称（Ceramic 上的数据）。它接受一个参数`name`并更新记录。

- 最后，让我们创建一个状态变量`name`，您可以输入它来更新您的记录

```
const [name, setName] = useState("");
```

- 对于前端部分，在`RecordSetter`函数末尾添加以下内容

```jsx
return (
    <div className={styles.content}>
      <div className={styles.mt2}>
        {record.content ? (
          <div className={styles.flexCol}>
            <span className={styles.subtitle}>
              Hello {record.content.name}!
            </span>

            <span>
              The above name was loaded from Ceramic Network. Try updating it
              below.
            </span>
          </div>
        ) : (
          <span>
            You do not have a profile record attached to your 3ID. Create a
            basic profile by setting a name below.
          </span>
        )}
      </div>

      <input
        type="text"
        placeholder="Name"
        value={name}
        onChange={(e) => setName(e.target.value)}
        className={styles.mt2}
      />
      <button onClick={() => updateRecordName(name)}>Update</button>
    </div>
  );
```

- 如果您在 Ceramic 配置文件记录上设置了名称，则此代码基本上会呈现一条消息`Hi ${name}`，否则它会告诉您尚未设置配置文件，您可以创建一个。您可以通过在文本框中输入文本并单击更新按钮来创建或更新配置文件

- 在这一点上我们应该很高兴！运行起来

```bash
npm run dev
```

- 在您的终端中，或者刷新您的网页（如果您已经运行它），您应该能够通过您的 Metamask 钱包连接到 Ceramic Network 并在您的去中心化配置文件中设置名称，并更新它！

要查看我们构建的最终代码，您可以访问[Github](https://github.com/LearnWeb3DAO/Ceramic)。

希望这篇文章能让您先睹为快，了解如何使用 Ceramic，并帮助您了解 Ceramic 为何如此重要的原因。

> 原文： [https://www.learnweb3.io/tracks/junior/ceramic-network](https://www.learnweb3.io/tracks/junior/ceramic-network)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)