---
title: 将WalletConnect集成到Vue.js DApps中
description: null
author: 李留白
weight: 0
date: 2022-12-01T08:22:44.664Z
lastmod: 2022-12-01T08:39:03.617Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202212011626641.png
---

去中心化应用程序（DApps）的主要功能之一是连接钱包的能力，这反过来又允许用户与DApp上的交易互动。它抽象了一些功能，如切换网络，提供签名者，以及其他为用户提供一种认证形式的功能。连接钱包也作为一个网关，允许用户通过DApp在区块链上进行和读取操作，使用他们的钱包地址作为授权身份。

WalletConnect是一个免费的开源协议，使我们的DApp与多个钱包连接成为可能，包括MetaMask、Trust Wallet、Rainbow和其他钱包。该协议通过在DApp和钱包之间建立连接来抽象这个过程，使它们在整个会话中保持同步。

在这篇文章中，我们将使用WalletConnect将我们的钱包应用与我们的DApp连接起来，在前端使用Vue.js。需要注意的是，WalletConnect可以用在任何兼容WalletConnect的DApp、链和钱包（托管和非托管）上。

你可以在这里找到本教程的源代码，以及我们将建立的应用程序的演示。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202212011638491.gif)

## 开始使用Vue.js应用程序

首先，让我们使用Vue CLI来启动这个项目。如果你的系统中已经安装了Vue CLI，你可以直接创建Vue项目。

你可以用这个命令全局安装它。

```shell
npm install -g @vue/cli
```

现在我们可以使用Vue CLI来创建我们的项目。用这个命令创建一个新项目。

```shell
vue create vue-wallet-connect
```

你将需要挑选一个预设。选择手动选择功能，然后选择如下所示的选项。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202212011628316.png)

在项目被创建后，导航到新的项目文件夹。

```shell
cd vue-wallet-connect
```

我们将在我们的Vue应用程序中使用Ethers.js，在连接我们的钱包时直接与区块链互动。

```shell
npm i ethers
```

在这里，我们将WalletConnect库安装到你的项目中。

```shell
npm install --save web3 @walletconnect/web3-provider
```

接下来，为了在Vue 3中直接使用WalletConnect库，我们需要安装node-polyfill-webpack-plugin。

```shell
npm i node-polyfill-webpack-plugin
```

我们安装它是因为我们的项目使用webpack v5，其中polyfill Node核心模块被删除。所以，我们安装它是为了在项目中访问这些模块。

现在，打开vue.config.js文件，用这段代码替换它。

```javascript
const { defineConfig } = require("@vue/cli-service");
const NodePolyfillPlugin = require("node-polyfill-webpack-plugin");
module.exports = defineConfig({
  transpileDependencies: true,
  configureWebpack: {
    plugins: [new NodePolyfillPlugin()],
    optimization: {
      splitChunks: {
        chunks: "all",
      },
    },
  },
});
```

一旦完成，你就可以启动服务器了。

```shell
npm run serve
```

## 构建用户界面

让我们进入组件文件夹，创建一个名为StatusContainer.vue的新文件。这个组件包含我们的主页面。

它有我们的欢迎词，帮助我们连接的连接钱包按钮，以及断开我们与钱包连接的断开按钮。最后，当我们成功连接到一个钱包时，显示Connected按钮。

```javascript
<template>
  <div class="hello">
    <h1>Welcome to Your Vue.js Dapp</h1>
    <div >
       <button class="button">Connected</button>
       <button class="disconnect__button">Disconnect</button>
    </div>

    <button class="button"> Connect Wallet</button>
  </div>
</template>
<script>
export default {
  name: 'StatusContainer'
}
</script>
```

一旦完成，打开App.vue文件，像这样导入StatusContainer组件。

```javascript
<template>
  <status-container/>
</template>
<script>

import StatusContainer from './components/StatusContainer.vue'
export default {
  name: 'App',
  components: {
    StatusContainer
  }
}
</script>
<style>
@import url('https://fonts.googleapis.com/css2?family=Sora:wght@100&display=swap');
#app {
  font-family: 'Sora', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.button {
      background-color: #1c82ff;
    border: none;
    color: #ffffff;
    font-family: "Sora";
    border-radius: 3rem;
    padding: 2rem 3rem;
    font-weight: 600;
    font-size: 2rem;
    margin: 1rem 1rem 1rem auto;
    width: 40%;
}
.disconnect__button {
     background-color: red;
    border: none;
    color: #ffffff;
    font-family: "Sora";
    border-radius: 3rem;
    padding: 1rem 1.3rem;
    font-weight: 600;
    font-size: 1rem;
    margin: 8rem 1rem 1rem auto;
    width: 20%;
}
</style>
```

在我们的样式标签中，我们现在为我们先前创建的按钮添加样式：.button和.disconnect__button。另外，我们从Google Fonts导入Sora自定义字体，并将其作为我们的字体家族。

## 实例化WalletConnect

我们将需要一个RPC提供者来实例化我们的WalletConnect库。在这个例子中，我们将使用Infura。打开Infura，创建一个新的项目，并获取项目ID。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202212011629804.png)

现在，在src文件夹下创建一个新的walletConnect文件夹：src/walletConnect。在这个文件夹中，让我们创建一个provider.js文件。在这里，我们导入我们的WalletConnect库，使用我们的Infura ID将其实例化，并导出它以便在其他文件中使用。

src/walletConnect/provider.js将看起来像这样。

```javascript
import WalletConnectProvider from "@walletconnect/web3-provider";
export const provider = new WalletConnectProvider({
  infuraId: process.env.VUE_APP_INFURA_ID,
});
```

Infura的ID应该作为环境变量使用。所以在你的.env文件中添加以下内容。

```shell
VUE_APP_INFURA_ID={{INFURA__ID}}
```

## 使用可合成物添加功能

在创建我们的接口并成功实例化我们的库之后，下一步是实现我们的功能。为了做到这一点，我们将使用Vue composables，因为它允许我们在应用中的任何组件中使用我们的状态和动作，类似于我们在Pinea和Vuex中的情况。

### 创建一个可组合的

在src文件夹内，添加src/composables/connect。在connect文件夹内，让我们创建一个index.js文件。

在这里，我们导入reactive和watch，我们将在这个文件中使用它们。让我们创建一个叫做defaultState的状态对象。

```javascript
import { reactive, watch } from "vue";

const defaultState = {
  address: "",
  chainId: "",
  status: false,
};

const state = defaultState
```

为了保持状态的一致性，我们将状态与本地存储中的一个项目进行同步。让我们把这个项目命名为 "userState"，并把它分配给一个叫做STATE_NAME的变量。这样做是为了避免在多个地方重复 "userState "时犯错误。

```javascript
const STATE_NAME = "userState";
```

现在我们使用watch来更新我们的本地存储，一旦我们的状态有任何变化。

```javascript
watch(
  () => state,
  () => {
    localStorage.setItem(STATE_NAME, JSON.stringify(state));
  },
  { deep: true }
);
```

接下来，我们创建一个getDefaultState函数，检查我们本地存储中的STATE_NAME项是否存在，并将本地存储项分配给状态。如果我们的本地存储项不存在，它就将默认状态分配给state。

现在，我们可以删除 const state = defaultState 并使用 reactive 来分配 const state = reactive(getDefaultState());。

```javascript
const getDefaultState = () => {
  if (localStorage.getItem(STATE_NAME) !== null) {
    return JSON.parse(localStorage.getItem(STATE_NAME));
  }
  return defaultState;
};
const state = reactive(getDefaultState());
```

最后，我们导出我们的状态。我们还添加了一个if语句，检查我们的本地存储项是否不存在。如果不存在，它就创建这个项目并将状态分配给本地存储。

```javascript
 export default () => {
  if (localStorage.getItem(STATE_NAME) === null) {
    localStorage.setItem(STATE_NAME, JSON.stringify(state));
  }
  return {
    state,
  };
};
```

现在，我们的状态总是与本地存储同步，确保一致性。

我们来看看 src/composables/connect/index.js。

```javascript
import { reactive, watch } from "vue";

const defaultState = {
  address: "",
  chainId: "",
  status: false,
};

const STATE_NAME = "userState";
const getDefaultState = () => {
  if (localStorage.getItem(STATE_NAME) !== null) {
    return JSON.parse(localStorage.getItem(STATE_NAME));
  }
  return defaultState;
};
const state = reactive(getDefaultState());

watch(
  () => state,
  () => {
    localStorage.setItem(STATE_NAME, JSON.stringify(state));
  },
  { deep: true }
);
export default () => {
  if (localStorage.getItem(STATE_NAME) === null) {
    localStorage.setItem(STATE_NAME, JSON.stringify(state));
  }
  return {
    state,
  };
};
```

## 创建动作

我们的动作由将在我们的应用程序中使用的函数组成。我们将创建三个函数。

- connectWalletConnect，用于触发WalletConnect模式以连接钱包
- autoConnect，在DApp连接后处理WalletConnect会话的一致性，所以当DApp连接后，你刷新页面时，用户的会话仍然是活动的。
- disconnectWallet，断开DApp与钱包的连接并结束用户的会话。

让我们直接跳到代码中

### `ConnectWalletConnect`

还是在我们的connect文件夹（src/composables/connect）中，创建connectWalletConnect文件。首先，我们导入我们的索引文件，来自以太坊的提供者，以及我们之前在 src/walletConnect/provider.js 文件中创建的提供者。

```javascript
import { providers } from "ethers";
import connect from "./index";
import { provider } from "../../walletConnect/provider";

const connectWalletConnect = async () => {
  try {
    const { state } = connect();
    //  Enable session (triggers QR Code modal)
    await provider.enable();
    const web3Provider = new providers.Web3Provider(provider);
    const signer = await web3Provider.getSigner();
    const address = await signer.getAddress();
    state.status = true;
    state.address = address;
    state.chainId = await provider.request({ method: "eth_chainId" });

    provider.on("disconnect", (code, reason) => {
      console.log(code, reason);
      console.log("disconnected");
      state.status = false;
      state.address = "";
      localStorage.removeItem("userState");
    });

    provider.on("accountsChanged", (accounts) => {
       if (accounts.length > 0) {
        state.address = accounts[0];
      }
    });

    provider.on("chainChanged", (chainId) => {
      state.chainId = chainId
    });
  } catch (error) {
    console.log(error);
  }
};
export default connectWalletConnect;
```

接下来，我们有一个try-catch语句。在我们的尝试语句中，我们从connect()中获得我们的状态，并弹出我们的QR模式进行连接。一旦连接，我们就将我们的地址和chainId分配给状态属性，并使我们的state.status读作true。

然后，我们用提供者观察三个事件：断开连接、账户变更和链式连接。

- 一旦用户直接从他们的钱包断开连接，就会触发断开连接。
- accountsChanged是在用户切换他们钱包中的账户时触发的。如果账户数组的长度大于0，我们将state.address分配给数组中的第一个地址（accounts[0]），也就是当前地址。
- 如果用户切换了他们的链/网络，就会触发 chainChainged。例如，如果他们将他们的链从Ethereum mainnet切换到rinkeby testnet，我们的应用程序将state.chainId从1变为4。

然后，我们的catch语句只是将任何错误记录到控制台。

回到connect文件夹中的index.js文件，导入connectWalletConnect动作。在这里，我们创建一个动作对象，并将其与我们的状态一起导出。

```javascript
import { reactive, watch } from "vue";
import connectWalletConnect from "./connectWalletConnect";

const STATE_NAME = "userState";
const defaultState = {
  address: "",
  chainId: "",
  status: false,
};
const getDefaultState = () => {
  if (localStorage.getItem(STATE_NAME) !== null) {
    return JSON.parse(localStorage.getItem(STATE_NAME));
  }
  return defaultState;
};
const state = reactive(getDefaultState());
const actions = {
  connectWalletConnect,
};
watch(
  () => state,
  () => {
    localStorage.setItem(STATE_NAME, JSON.stringify(state));
  },
  { deep: true }
);
export default () => {
  if (localStorage.getItem(STATE_NAME) === null) {
    localStorage.setItem(STATE_NAME, JSON.stringify(state));
  }
  return {
    state,
    ...actions,
  };
};
```

### `autoConnect`

让我们继续看autoConnect.js，以及我们的动作。与 connectWalletConnect 类似，创建一个 autoConnect.js 文件。我们导入索引文件并对其进行解构，以获得我们的状态，并使用connect()进行connectWalletConnect。

```javascript
import connect from "./index";

const autoConnect = () => {
  const { state, connectWalletConnect } = connect();
  if (state.status) {
    if (localStorage.getItem("walletconnect") == null) {
      console.log("disconnected");
      console.log("disconnected");
      state.status = false;
      state.address = "";
      localStorage.removeItem("userState");
    }
    if (localStorage.getItem("walletconnect")) {
      (async () => {
        console.log("start");
        connectWalletConnect();
      })();
    }
  }
};
export default autoConnect;
```

你应该知道的一件事是，一旦WalletConnect成功连接到一个DApp，所有关于该钱包的信息（包括地址和链ID）都在本地存储中，在一个名为walletconnect的项目下。一旦会话被断开，它就会被自动删除。

autoConnect检查我们的state.status是否为true。如果是，我们就检查本地存储中是否有一个walletConnect项目。如果它不在本地存储中，我们就删除我们的状态中的所有现有数据和本地存储中的userState项。

然而，如果walletconnect存在于你的本地存储中，我们有一个异步函数，通过启动connectWalletConnect();为我们的DApp "重新激活 "现有会话。因此，如果我们刷新页面，连接仍然是活跃的，并且可以监听我们的提供者事件。

### `断开钱包`

让我们来看看最后一个动作：disconnectWallet。这个动作允许我们从DApp本身结束会话。

首先，我们导入我们的提供者和状态。然后，我们使用provider.disconnect();来断开会话，之后我们将状态重置为默认值，并删除本地存储中的 "userState "项目。

```javascript
import { provider } from "../../walletConnect/provider";
import connect from "./index";
const disconnectWallet = async () => {
    const { state } = connect();
    await provider.disconnect();
    state.status = false;
    state.address = "";
    localStorage.removeItem("userState");
  }
export default disconnectWallet;
```

现在我们可以回到我们的src/composables/connect/index.js中，像这样更新动作对象。

```javascript
const actions = {
  connectWalletConnect,
  autoConnect,
  disconnectWallet
};
```

## 在我们的组件中实现逻辑

让我们打开我们的StatusContainer组件，将我们的可组合文件中的逻辑连接到接口上。像往常一样，导入你的可组合文件并对其进行解构，以获得动作（连接和断开）和我们的状态。

```javascript
<script>
import connect from '../composables/connect/index';
export default {
  name: 'StatusContainer',
  setup: () => {
    const { connectWalletConnect, disconnectWallet, state } = connect();
    const connectUserWallet = async () => {
      await connectWalletConnect();
    };

    const disconnectUser = async() => {
      await disconnectWallet()
    }
    return {
      connectUserWallet,
      disconnectUser,
      state
    }
  }
}
</script>
```

然后我们返回函数（disconnectUser，connectUserWallet）和状态，以便在模板中使用。

```javascript
 <template>
  <div class="hello">
    <h1>Welcome to Your Vue.js Dapp</h1>
    <div v-if="state.status">
       <button  @click="connectUserWallet" class="button">Connected</button>
       <h3>Address: {{state.address}}</h3>
       <h3>ChainId: {{state.chainId}}</h3>
       <button  @click="disconnectUser" class="disconnect__button">Disconnect</button>
    </div>

    <button v-else @click="connectUserWallet" class="button"> Connect Wallet</button>
  </div>
</template>
```

首先，我们使用v-if来有条件地显示东西，使用state.status。如果我们连接了并且state.status为真，我们就会显示Connected按钮、用户地址和chainId。同时，我们会显示一个断开连接的按钮，触发我们的disconnectUser函数。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202212011631817.gif)

如果用户没有连接，且state.status为false，我们只显示连接钱包按钮，点击后会触发我们的connectUserWallet函数。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202212011632698.gif)

## 添加自动连接

让我们进入App.vue组件，将自动连接逻辑添加到该组件中。与我们之前所做的类似，我们导入我们的composable，并对其进行解构以获得autoConnect动作。使用Vue的onMounted，我们将启动autoConnect()函数。如前所述，这使我们能够监听来自钱包的实时事件，即使我们刷新页面。

```javascript
<script>
import StatusContainer from './components/StatusContainer.vue'
import connect from './composables/connect/index';
import {onMounted} from "vue";
export default {
  name: 'App',
  components: {
    StatusContainer
  },
  setup: () => {
    const { autoConnect} = connect();
     onMounted(async () => {
      await autoConnect()
    })
  }
}
</script>
```

## 结论

如果你一路走到这里，那就恭喜你了!

在这篇文章中，我们介绍了在你的Vue DApps中实现WalletConnect的逐步细节。从用正确的配置设置我们的项目和构建我们的界面，到编写必要的逻辑以确保我们的应用程序始终与钱包保持同步。

> 原文链接：https://blog.logrocket.com/integrating-walletconnect-vue-js-dapps/

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)