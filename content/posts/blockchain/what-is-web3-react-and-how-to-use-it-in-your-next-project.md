---
title: 什么是 Web3-React 以及如何在您的下一个项目中使用它？
description: null
author: 李留白
weight: 0
date: 2022-10-15T13:24:18.443Z
lastmod: 2022-10-15T13:40:08.993Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210152125283.png
---

到目前为止，您可能已经听说过区块链和 Web3，并且可能想要制作一些去[中心化应用程序 (dApps)](https://w3er.xyz/posts/blockchain/what-are-dapps-a-comprehensive-guide-to-dapps)来展示您的朋友和社区。

与普通的 Web 应用程序类似，dApp 也有后端和前端。

在本文中，我们将讨论 dApp 的前端，主要使用 web3-react。

Web3-react 是由 Noah Zinsmeister 制作的 web3 框架，旨在帮助区块链开发人员使用 React 架构制作现代以太坊 dApp。

2021 年，web3-react 在构建 dApp 的区块链社区中获得了很大的关注。

让我们看看为什么以及如何使用它。

## 什么是 Web3-react？

Web3-react 是一个易于使用、简单、可扩展的 web3 框架，用于在以太坊区块链网络上构建 dApp。

简而言之，web3-react 是一个状态机，它存储与你的 dApp 相关的某些基本数据位是最新的。

默认情况下，它支持注入的提供者，如[Meta Mask](https://metamask.io/)、[Gnosis Safe](https://gnosis-safe.io/)、[Frame](https://frame.sh/)和[WalletConnect](https://walletconnect.com/)。

您可以轻松地将其配置为使用其他钱包，例如[Portis](https://www.portis.io/)、[Squarelink](https://squarelink.com/)和 Arkane。

> *web3-react 中最受欢迎的支持钱包👇*
>
> ![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210152127431.png)

如前所述，web3-react 使用 React 架构来构建 dApp 的前端。

由于这些原因，Web3-react 是许多开发人员的选择；

- 构建现代网络应用程序。
- 完全响应和可定制。
- 简单易用。
- 更快的发展。
- 庞大且不断发展的社区。

## 为什么 Web3-react 是一个不错的选择？

Web3-react 将自己确立为 dApp 开发人员的可行选择。

创建可定制的、功能齐全的连接器来管理 dApp 的以太坊区块链和用户帐户连接的各个方面的能力是一个巨大的优势。

它也是一个对开发人员友好的环境，其中包含一个实例化的 ethers.js 或 web3.js 实例、当前帐户和网络 ID，以及在您的去中心化应用程序中全局可用的其他信息。

Web3-react 是一个非常适合 dApp 开发的框架，因为：

- 您可以以编程方式将插件写入区块链。
- 没有传统的数据架构。
- 您可以制作系统化、强大且可扩展的 dApp。

您会发现 web3-react 的另一个超级重要功能是能够轻松连接到区块链上的节点。

节点是在云中某处运行的软件片段，它们连接到其他节点，也可以从区块链中读取，然后广播回区块链。

#### 步骤 1 ： 安装yarn。

Yarn 是 Facebook 创建的一个包管理器，用于替代 NPM 客户端。

它使团队能够创建 JavaScript 代码并通过软件包进行分发。

检查[yarn的安装指南](https://classic.yarnpkg.com/lang/en/docs/install/)。

安装完成后，查看安装的 Yarn 包的版本。确保它是最新的。

运行此命令以检查安装的yarn版本。

```
yarn --version
```

> ***注意：*** npm也可以代替yarn，但不推荐。

####  步骤 2： 安装`react`和`react-dom`版本

确保您已安装最新版本的 React 和 React-dom。这些包是运行 Web3-react 所必需的。

Ethers.js 是 JavaScript 和 TypeScript 中完整的以太坊钱包实现和实用程序。[您可以在GitHub 上](https://github.com/ethers-io/ethers.js/)了解有关 Ethers.js 的更多信息。

它是 Web3-react 的必需包，因为它是对库的内部依赖。

```
yarn add ethers
```

#### 可选 ： 安装`web3.js`

Web3.js 是一组库，可让您使用 HTTP、IPC 或 Web Socket 与本地或远程以太坊节点进行通信。

*它是 Web3 react 的可选包。*

```
yarn add web3
```

#### 步骤 4 ： 安装`web3-react`

最后，您需要将 Web3-react 安装到您的项目中。

您可以通过运行此命令将*最新版本*的 web3-react 下载到您的项目中。

```
yarn add web3-react@unstable
```

或者您可以通过运行此命令来安装它的*核心版本*。

```
yarn add @web3-react/core
```

恭喜🥳 你终于在你的项目中安装了 web3-react。现在您已准备好制作 dApp 的前端。那不是很简单吗？

到目前为止，您的**dApp**组件应该如下所示。

```react
import { Web3ReactProvider } from '@web3-react/core'
import { Web3Provider } from "@ethersproject/providers";

function getLibrary(provider, connector) {
  return new Web3Provider(provider);
}

const App => () {
  return (
    <Web3ReactProvider getLibrary={getLibrary}>
      <YourAwesomeComponent />
    </Web3ReactProvider>
  )
}
```

> *基于 Web3-react 文档。*

简而言之，就是导入一个提供者，然后在`getLibrary`函数中实例化并返回它。

## Web3-react 示例

> 查看 Web3-react 的一些最新统计数据🤩

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202210152134419.png)

[Uniswap](https://en.wikipedia.org/wiki/Uniswap)等公司使用 web3-react[构建](https://web3.hashnode.com/what-are-dapps-a-comprehensive-guide-to-dapps#heading-what-are-dapps-used-for)dApp 的前端。这绝对显示了这个框架的能力。

现在让我们看看一些 web3-react 教程和示例。

### 使用 Meta Mask 连接您的 dApp

Meta Mask 是一个通过软件与以太坊区块链交互的加密货币钱包。它是世界上最受欢迎的钱包之一，拥有超过 2100 万用户。

它是用于 dApp 的主要钱包，因为它有很多好处；

- 非常安全和快速
- 便于使用
- 与几乎任何类型的基于以太坊区块链的 DApp 交互。

现在让我们看看如何使用 Web3-react 将 Meta Mask 与我们的前端连接起来。

#### 1、将`Connector`文件夹*添加到您的项目中。*

您将在此文件夹中存储与钱包的所有连接和交互代码。从长远来看，这将最终管理您的代码。

#### 2、在`Connector`文件夹中，添加一个`Connectors.js`文件。

开发人员使用 connector.js 文件编写与所需钱包交互和设置协议的代码。

#### 3、假设您已在项目中安装`Web3-react`，您现在应该安装允许您连接到 Meta Mask 的依赖项。

```
yarn add @web3-react/injected-connector
```

#### 4、*将 Injected-Connector 导入您的`connectors.js`文件。*

```react
import { InjectedConnector } from '@web3-react/injected-connector'

export const injected = new InjectedConnector({
  supportedChainIds: [1, 3, 4, 5, 42],
})
```

#### 5、*您需要一个前端来从 Meta Mask 中导出该数据。*

[Youtube 上](https://www.youtube.com/watch?v=DCA53Go5ON8&t=65s)的这个视频 应该可以帮助您使用前端从 Meta Mask 导出数据。

恭喜。💃您已准备好在您的项目中使用 Meta Mask。

### 将您的应用程序与任何其他钱包连接

Web3-react 还支持许多钱包，例如 Wallet Connect 等。大型 dApp 项目提供了 Meta Mask 以外的更多选项来与应用程序交互。

就像我们为 Meta Mask 安装的`injected-connector`一样。我们需要为 Wallet Connect 安装`Walletconnect-connector`。

```
yarn add @web3-react/walletconnect-connector
```

还有其他选项，例如 Portis，可以使用`portis-connector`连接器连接。

```
yarn add @web3react/portis-connector
```

将钱包中的数据导入和导出到您的 dApp 几乎相同。[您可以在文档](https://github.com/NoahZinsmeister/web3-react/tree/v6/packages)中找到有关它的更多信息。

### 断开钱包

停用和关闭是在 web3-react 上断开钱包的两个选项。

停用：当您使用此功能时，钱包信息和交互从 dApp 中被破坏。用户可能必须重新授权应用程序才能连接钱包。

关闭：使用关闭功能时，用户与钱包断开连接，但信息仍存储在 dApp 中。用户可以轻松地将其再次连接到钱包并留下所有信息。

不幸的是，有时很难知道正在执行哪个功能。

Meta Mask 和注入的连接器缺少一个`close`功能，如果使用会导致错误，并且某些钱包不会从 DOM 中删除，除非使用 close，这意味着 Deactivate 无效。

因此，您可能希望创建一个通用`disconnectWallet`函数以确保在钱包完全断开连接时运行适当的函数。

## 结论

我们已经讨论了什么是 web3-react，为什么 web3-react 是一个不错的选择，如何在你的项目中安装 web3-react，以及如何开始使用不同的钱包。

总之，web3-react 是您开发 dApp 前端的一个新的但值得的选择。

它有很多优点，文章中对此进行了讨论。

在早期阶段学习和构建 dApp 可能很困难，但您不应因此而气馁。

现在学习区块链和 Web3 就像在 2000 年代学习如何使用互联网和 Web 开发一样。

让我知道你对 Web3 未来的看法，以及你是否认为 web3-react 是你应该为你的技术栈考虑的东西。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)