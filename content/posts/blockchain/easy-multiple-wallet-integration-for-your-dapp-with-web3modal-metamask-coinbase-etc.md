---
title: 通过 web3Modal（Metamask、Coinbase 等）为您的 dApp 轻松集成多个钱包
description: 使用 web3Modal 在 dApp 中集成多个钱包
author: 李留白
weight: 0
date: 2023-11-19T13:44:28.659Z
lastmod: 2023-11-19T13:48:04.783Z
tags: []
categories:
    - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231119214439.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231119214439.png)

我们将实现多个钱包，如 Metamask、coinbase、Wallet Connect、trust wallet 等，

## 介绍

我们正在使用 web3Modal、wagmi 和 viem 实现多个钱包

- **wagmi**：它是 React Hooks 的集合，包含开始使用以太坊所需的一切，您可以将其与其他 React Web3 框架（如 web3-react）[进行比较。](https://wagmi.sh/react/comparison)查看[文档](https://wagmi.sh/react/getting-started)以获取更多信息。
- **viem**：它是以太坊的 TypeScript 接口，提供用于与以太坊交互的低级无状态原语。[出于明显的包大小原因](https://viem.sh/docs/introduction.html#bundle-size)，我们使用 viem 作为 ethers.js 的替代品。
- **web3Modal**：Web3Modal SDK 允许您轻松地将 Web3 dapp 与钱包连接。它为 dapp 提供了一个简单直观的界面来请求操作，例如签署交易以及与区块链上的智能合约交互。查看[文档](https://docs.walletconnect.com/web3modal/about)以获取更多信息。

## 先决条件

1. 系统上安装了 Node.js 和 npm
2. React.js 知识
3. web3.js 或 ethers.js 知识

## 设置钱包连接项目

做到这一点的步骤，

1. 登录https://walletconnect.com/

2. 单击[https://cloud.walletconnect.com/app上的“创建”按钮创建一个新项目](https://cloud.walletconnect.com/app)

   命名项目并选择类型**应用程序**

   ![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231119214615.png)

3. 转到创建的项目并获取将来需要的项目 ID。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231119214623.png)

## 设置 React 项目

我们正在建立一个 React JS 项目来玩。

```bash
npm create vite@latest
```

它将询问您一些配置问题，如下所示：

✔ 项目名称：web3-modal-setup

✔ 选择一个框架： › React

✔ 选择一个变体： › JavaScript

```bash
cd web3-modal-setup
npm install
npm run dev
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231119214641.png)

## **安装包**

安装所需的包以实现所需的 web3 功能和钱包组件包。

```bash
npm install @web3modal/wagmi wagmi viem
```

## 配置wagmi

**wagmi**是 React Hooks 的集合，包含开始使用以太坊所需的一切。wagmi 可以轻松地“连接钱包”、显示 ENS 和余额信息、签署消息、与合约交互等等 - 所有这些都具有缓存、请求重复数据删除和持久性。

### 导入包

```javascript
import { createWeb3Modal, defaultWagmiConfig } from '@web3modal/wagmi/react'

import { WagmiConfig } from 'wagmi'
import { arbitrum, mainnet } from 'viem/chains'
```

### **创建**wagmiConfig 和 web3Modal

建议在 React 应用程序组件之外编写此代码，这样它就不会出现任何不必要的渲染。

这里`projectId`是我们首先创建的钱包连接项目的项目ID。

```javascript
// 1. Get projectId
const projectId = '57c3edxxxxxxxxxxxxxxxxxxxxxee'

// 2. Create wagmiConfig
const metadata = {
  name: 'web3-modal-setup',
  description: 'Web3 Modal Example',
}

const chains = [mainnet, arbitrum]
const wagmiConfig = defaultWagmiConfig({ chains, projectId, metadata })

// 3. Create modal
createWeb3Modal({ wagmiConfig, projectId, chains })
```

### 将应用程序包装为`WagmiConfig`

最后，用组件包装您的应用程序[`WagmiConfig`](https://wagmi.sh/react/WagmiConfig)，并将其传递`config`给它。

```javascript
function App() {
  return (
    <>
      <WagmiConfig config={wagmiConfig}>
        {/* other components.. */}
      </WagmiConfig>
    </>
  )
}
```

## 实现Web3Modal和按钮

组件内部`WagmiConfig`，实施按钮`<w3m-network-button />`和`<w3m-button />`

- `<w3m-network-button />`：这是用于更改网络的按钮
- `<w3m-button />`：这个按钮提供了许多钱包选项来连接您的 dApp。

这里，为了让按钮值得一看，编写了一些 CSS，顺便说一句，这是不必要的。

```javascript
function App() {
  const [isNetworkSwitchHighlighted, setIsNetworkSwitchHighlighted] =
    useState(false);
  const [isConnectHighlighted, setIsConnectHighlighted] = useState(false);

  const closeAll = () => {
    setIsNetworkSwitchHighlighted(false);
    setIsConnectHighlighted(false);
  };

  return (
    <>
      <WagmiConfig config={wagmiConfig}>
        <header>
          <div
            className={styles.backdrop}
            style={{
              opacity:
                isConnectHighlighted || isNetworkSwitchHighlighted
                  ? 1
                  : 0,
            }}
          />
          <div className={styles.header}>
            <div className={styles.buttons}>
              <div
                onClick={closeAll}
                className={`${styles.highlight} ${isNetworkSwitchHighlighted
                  ? styles.highlightSelected
                  : ``
                  }`}
              >
                <w3m-network-button />
              </div>
              <div
                onClick={closeAll}
                className={`${styles.highlight} ${isConnectHighlighted
                  ? styles.highlightSelected
                  : ``
                  }`}
              >
                <w3m-button />
              </div>
            </div>
          </div>
        </header>
      </WagmiConfig>
    </>
  )
}
```

## **你可以走了！**

运行。

```bash
npm run dev
```

干得好，

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231119214725.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231119214733.png)


查看[Github仓库](https://github.com/siddharth9903/web3-modal-setup)，无论如何，它是需要的。


> 原文： https://jarvisdev.hashnode.dev/easy-multiple-wallet-integration-for-your-dapp-with-web3modal-metamask-coinbase-etc