---
title: Web3之路——如何创建一个NFT画廊
description: null
author: 李留白
weight: 0
date: 2022-08-21T12:58:44.576Z
lastmod: 2022-08-21T12:58:54.810Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814204307.png
---

在本教程中，你将学习如何开发一个NFT画廊，按钱包地址和智能合约地址显示NFT。

将公共数据存储在区块链上并不能使其容易被访问。查询区块链实际上是对开发人员最大的提升之一，他们需要从链的原点开始，收集他们需要的数据，以连接点和回答他们的问题。

比方说，一个开发者想回答以下问题。"一个钱包拥有哪些NFTs？"。

这听起来是一个简单的询问，但答案却远非如此简单。

在这种情况下，开发人员需要找到所有在给定链上铸造的NFT，然后跟踪所有的转移功能，以了解谁是目前拥有NFT的。

这项任务可能需要开发人员花费数周时间。不过幸运的是，有一个解决方案--通过一个免费的Alchemy账户和Alchemy NFT API，允许开发者在几毫秒内从区块链上获取NFT，而不是几周。

因为Alchemy已经查询了完整的区块链并对其数据进行了索引，所以NFT API使您能够完全访问这些信息。

## 如何创建一个NFT图库教程

在本教程中，您将学习如何使用Alchemy NFT API来建立一个NFT库，能够根据三件事来获取NFT

- 钱包地址
- 合约地址
- 钱包地址+合约地址

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821145356.png)

## 所需工具

我们将使用Alchemy NFT API调用来获取NFT及其元数据，并显示图像、描述和ID，在我们的NFT画廊中使用。

- Next JS
- Tailwind CSS
- Alchemy NFT API
- [Alchemy Account](https://alchemy.com/)

## 1. 创建项目设置

你需要做的第一件事是创建Next JS项目的模板，并安装TailwindCSS，它将负责你的样式。

打开你的终端，编写以下代码。

```
npx create-next-app -e with-tailwindcss nameoftheproject
```

导航到你的项目并启动VSCode。

```
cd nameoftheproject && code .
```

现在你的项目模板已经创建完毕，测试一下一切是否正常。

在你的项目目录中运行以下代码。

```
npm run dev
```

浏览到localhost:3000应该会呈现以下页面。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821145611.png)

因为我们不需要它，你可以删除pages>index.tsx中<div>标签内的所有代码。

你在index.tsx文件中的代码现在应该是这样的。

```
// Some code
```

因为本教程将使用Javascript而不是Typescript，在编写你的代码之前，将你的index.tsx和_app.tsx文件从.tsx转换成.jsx文件。

- 将两个文件的扩展名都改为.jsx

- 删除两个文件顶部导入的类型

## 2. 创建一个主页

创建可搜索的NFT画廊的第一步是创建文本输入，在这里你将添加钱包地址和用于搜索NFT的收藏地址。

在index.jsx页面，添加以下代码。

```
const Home = () => {
 
  return (
    <div className="flex flex-col items-center justify-center py-8 gap-y-3">
      <div className="flex flex-col w-full justify-center items-center gap-y-2">
        <input type={"text"} placeholder="Add your wallet address"></input>
        <input type={"text"} placeholder="Add the collection address"></input>
      </div>
    </div>
  )
}

export default Home

```

你可以注意到，我们已经在输入的className属性内实现了Tailwind CSS类。我们不会去讨论TailwindCSS如何工作的细节，如果你想了解更多，你可以参考[官方文档](https://tailwindcss.com/docs/installation)。

### 创建两个变量来存储钱包和集合地址

接下来，我们需要使用useState()React钩子创建两个变量来存储我们将插入输入字段的钱包地址和收藏品地址。

从 "react "导入 "useState "钩子，并在 "Home "组件的返回语句上方添加以下代码。

```
  import { useState } from 'react'

  const Home = () => {
  const [wallet, setWalletAddress] = useState("");
  const [collection, setCollectionAddress] = useState("");
  
  return (
    <div className="flex flex-col items-center justify-center py-8 gap-y-3">
      <div className="flex flex-col w-full justify-center items-center gap-y-2">
        <input type={"text"} placeholder="Add your wallet address"></input>
        <input type={"text"} placeholder="Add the collection address"></input>
      </div>
    </div>
  )
}

export default Home
 
```

要在 "钱包 "和 "集合 "变量内存储你的文本输入的值，请使用 "onChange "事件处理程序。

每次你改变你的输入字段的值时，"onChange "事件处理程序将被触发，使用setWallet和setCollectionAddress函数获取输入并将其存储在各自的变量中。

你也要反映你的 "钱包 "和 "集合 "变量的变化，分配它们的值显示在你的输入中。

在文本输入标签中，添加以下代码。

```
import { useState } from 'react'

const Home = () => {
  const [wallet, setWalletAddress] = useState("");
  const [collection, setCollectionAddress] = useState("");
  
  return (
    <div className="flex flex-col items-center justify-center py-8 gap-y-3">
      <div className="flex flex-col w-full justify-center items-center gap-y-2">
        <input onChange={(e)=>{setWalletAddress(e.target.value)}} value={wallet} type={"text"} placeholder="Add your wallet address"></input>
        <input onChange={(e)=>{setCollectionAddress(e.target.value)}} value={collection} type={"text"} placeholder="Add the collection address"></input>
      </div>
    </div>
  )
}

export default Home

```

你的输入现在将在各自的变量中存储我们将写在里面的地址。

### 检查一切都在你的浏览器的 "React开发者工具 "中运行

我们将在Chrome浏览器上完成安装过程，但所有这些也适用于Mozilla Firefox。

- 进入浏览器扩展商店
- 搜索React开发者工具
- 点击 "添加到Chrome"。

一旦下载并安装，你就能在Chrome开发者工具的 "检查 "视图中看到React开发工具标签。

回到终端，在你的应用程序文件夹内，再次启动你的应用程序编写。

```
npx run dev
```

- 转到你的应用程序页面（localhost:3000）。
- 右键单击页面上的任何地方
- 点击 "检查
- 点击">>"符号
- 转到 "组件"。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821205440.png)

在 "组件 "视图中，我们将能够看到我们页面上包含的所有组件的列表，在右边，是保存在钩子中的不同状态和关于应用程序的其他信息。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821205457.png)

在安装了React开发者工具后，你可以通过写进你的输入并检查你的状态值是否被更新来测试useState()钩。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821205512.png)

现在你已经能够存储钱包和收款地址，让我们完成主页。

### 完成主页

接下来，您需要添加一个按钮，以触发从Alchemy NFT API中获取NFT的功能，并添加一个切换按钮，以决定您是否要按钱包地址、收藏或两者进行搜索。

- 在集合文本输入下添加一个新的 "复选框 "类型的输入
- 把它包在一个``函数中，添加一些文本
- 创建按钮

```
 import { useState } from 'react'

const Home = () => {
  const [wallet, setWalletAddress] = useState("");
  const [collection, setCollectionAddress] = useState("");
  
  return (
    <div className="flex flex-col items-center justify-center py-8 gap-y-3">
      <div className="flex flex-col w-full justify-center items-center gap-y-2">
        <input type={"text"} placeholder="Add your wallet address"></input>
        <input type={"text"} placeholder="Add the collection address"></input>
        <label className="text-gray-600 "><input type={"checkbox"} className="mr-2"></input>Fetch for collection</label>
        <button className={"disabled:bg-slate-500 text-white bg-blue-400 px-4 py-2 mt-3 rounded-sm w-1/5"}>Let's go! </button>
      </div>
    </div>
  )
}

export default Home
```

现在你有了你的按钮，添加一个onClick()处理程序，它将触发你的fetchNFTs()函数，根据拥有的钱包获取NFTs。

```
import { useState } from 'react'

const Home = () => {
  const [wallet, setWalletAddress] = useState("");
  const [collection, setCollectionAddress] = useState("");
  
  return (
    <div className="flex flex-col items-center justify-center py-8 gap-y-3">
      <div className="flex flex-col w-full justify-center items-center gap-y-2">
        <input type={"text"} placeholder="Add your wallet address"></input>
        <input type={"text"} placeholder="Add the collection address"></input>
        <label className="text-gray-600 "><input type={"checkbox"} className="mr-2"></input>Fetch for collection</label>
        <button className={"disabled:bg-slate-500 text-white bg-blue-400 px-4 py-2 mt-3 rounded-sm w-1/5"} onClick={
          () => {
        
          }
        }>Let's go! </button>
      </div>
    </div>
  )
}

export default Home
```

现在，创建fetchNFTs()函数，添加到onClick处理程序中，获取你的NFTs。

不过在此之前，你需要创建一个新的应用程序，以获得Alchemy API密钥。

## 3. 创建一个新的Alchemy应用程序

导航到Alchemy并点击 "创建应用程序 "来创建一个新的应用程序。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821205548.png)

以下是要添加的细节。

- 应用程序名称
- 描述
- 链 (Ethereum)
- 网络（主网）

选择Ethereum主网将允许你只从Ethereum获取NFTs。

如果你想在Polygon或其他链上获取NFT，你需要用相应的链创建一个新的应用程序，并改变基本的URL以反映你想使用的链，例如，Polygon的URL将是：https://polygon-mumbai.g.alchemy.com/v2/YOUR-API-KEY

现在我们的Alchemy应用程序已经启动并运行，让我们创建fetchNFTs函数来获取一个地址所拥有的所有NFTs。

## 4. 创建FetchNFTs函数

要获得一个钱包地址所拥有的NFT，请使用Alchemy NFT API的getNFTs端点。

在主页组件中，声明一个新的useState()变量，就像我们之前对钱包和集合地址所做的那样，以存储我们将使用Alchemy NFT API获取的NFTs。

```
import { useState } from 'react'

const Home = () => {
  const [wallet, setWalletAddress] = useState("");
  const [collection, setCollectionAddress] = useState("");
  const [NFTs, setNFTs] = useState([])

  return (
    <div className="flex flex-col items-center justify-center py-8 gap-y-3">
      <div className="flex flex-col w-full justify-center items-center gap-y-2">
        <input type={"text"} placeholder="Add your wallet address"></input>
        <input type={"text"} placeholder="Add the collection address"></input>
        <label className="text-gray-600 "><input type={"checkbox"} className="mr-2"></input>Fetch for collection</label>
        <button className={"disabled:bg-slate-500 text-white bg-blue-400 px-4 py-2 mt-3 rounded-sm w-1/5"} onClick={
          () => {
          }
        }>Let's go! </button>
      </div>
    </div>
  )
}

export default Home
```

现在，总是在主页组件内，添加fetchNFTs()函数。

```
const fetchNFTs = async() => {
  let nfts; 
  console.log("fetching nfts");
  const api_key = "A8A1Oo_UTB9IN5oNHfAc2tAxdR4UVwfM"
  const baseURL = `https://eth-mainnet.alchemyapi.io/v2/${api_key}/getNFTs/`;
  var requestOptions = {
      method: 'GET'
    };
   
  if (!collection.length) {
  
    const fetchURL = `${baseURL}?owner=${wallet}`;

    nfts = await fetch(fetchURL, requestOptions).then(data => data.json())
  } else {
    console.log("fetching nfts for collection owned by address")
    const fetchURL = `${baseURL}?owner=${wallet}&contractAddresses%5B%5D=${collection}`;
    nfts= await fetch(fetchURL, requestOptions).then(data => data.json())
  }

  if (nfts) {
    console.log("nfts:", nfts)
    setNFTs(nfts.ownedNfts)
  }
}
```

关于你的fetchNFTs()函数，首先要注意的是async关键字，这将允许你在不阻断整个应用程序的情况下获取数据。

> 阅读更多关于async await的JavaScript工作流程。

然后，你声明了一个新的变量，你将用来存储你要获取的NFT，然后创建基本的URL，你将在你的fetch()中输入以获取NFT。

根据Alchemy NFT API文档，基本URL由以下部分组成。

- 应用程序基本URL
- 您的API密钥
- 业主地址
- 收集地址 - 可选

如果提供了收藏地址，NFT API将按收藏过滤获取的NFT，如果没有提供，API将检索提供的钱包地址所拥有的所有NFT。

正因为如此，你需要一种方法来了解是否已经提供了收集地址，因此：如果你想通过收集来过滤。

要做到这一点，你可以添加一个 "if "语句，检查 "collection "变量是否为空。

如果它是空的，那么你就只提供钱包地址参数。

```
const fetchURL = `${baseURL}?owner=${wallet}`;
```

如果不是，那么你就获取一个钱包所拥有的所有NFT，并通过集合将它们过滤掉。

```
const fetchURL = `${baseURL}?owner=${wallet}&contractAddresses%5B%5D=${collection}`;
```

在 "contractAddresses "参数后面的 "5B%5D "字符串指定 "contractAddresses "参数是一个数组而不是一个简单的字符串。这是因为你实际上可以通过多个 "contractAddresses "来过滤，而不仅仅是一个。

在这两种情况下，无论是否按集合过滤，你都要把你的 "baseURL "输入到fetch()函数中，等待结果，并使用.json()函数将其转换为JSON。

打印由我们的fetch()函数获取的数据，将记录以下对象。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821205636.png)


在获取的对象中，你有更多的信息，因为你只需要包含我们提供的钱包地址所拥有的NFT的数组。

这就是为什么在setNFTs()函数中，你要输入nfts.ownednfts而不只是nfts的原因，因为你只需要存储ownednfts数组，以便以后使用它来显示你的NFTs。

现在你的fetchNFTs()函数已经准备好了，你需要实现一个新的函数，在没有NFT所有者的情况下按集合获取NFT。

## 5. 创建FetchNFTs by Collection函数

为了按集合获取NFT，您可以使用getNFTsForCollection Alchemy端点。

getNFTsForCollection端点将需要两个参数。

- contractAddress - NFT集合的合同地址[字符串] 。
- withMetadata - （可选）****，如果设置为true，返回NFT元数据；否则将只返回tokenIds。默认为false [布尔值] 。

第一个参数是指定你想要获取的集合的合同地址。

第二个参数向NFT API指定你是否还想获取集合中包含的NFT的元数据（如标题、图像、描述、属性）或只获取其ID。

你可以阅读更多关于Alchemy NFT API的信息。

正如我们之前所做的那样，让我们首先复制代码并了解它的作用。

```
const fetchNFTsForCollection = async () => {
  if (collection.length) {
    var requestOptions = {
      method: 'GET'
    };
    const api_key = "A8A1Oo_UTB9IN5oNHfAc2tAxdR4UVwfM"
    const baseURL = `https://eth-mainnet.alchemyapi.io/v2/${api_key}/getNFTsForCollection/`;
    const fetchURL = `${baseURL}?contractAddress=${collection}&withMetadata=${"true"}`;
    const nfts = await fetch(fetchURL, requestOptions).then(data => data.json())
    if (nfts) {
      console.log("NFTs in collection:", nfts)
      setNFTs(nfts.nfts)
    }
  }
}
```

它与我们之前构建的fetchNFTs()函数非常相似，有两个主要区别。

- 使用的端点
- 存储在NFTs状态变量中的值

下面是正在发生的事情。

1. 你首先验证了集合地址不是空的
2. 然后你声明requestOptions，告诉fetch()你的HTTP请求将是一个 "GET "请求。
3. 最后，你建立了一个baseURL，将采集地址的值作为contractAddress参数，并将withMetadata参数设为true。

最后，使用async await工作流+json()函数将获取的数据转换为JSON。

如果我们把包含获取的NFTs的JSON对象进行console.log，我们会发现它包含两个属性。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821205655.png)

- 
  下一个令牌
- nfts

在这种情况下，你只需要将 "nfts.nfts "传入setNFTs()函数，就可以得到包含你的NFTs数组的 "nfts "属性。
在这一点上，你的代码应该如下所示。

```
import { useState } from 'react'

const Home = () => {
  const [wallet, setWalletAddress] = useState("");
  const [collection, setCollectionAddress] = useState("");
  const [NFTs, setNFTs] = useState([])

  const fetchNFTs = async() => {
    let nfts; 
    console.log("fetching nfts");
    const api_key = "A8A1Oo_UTB9IN5oNHfAc2tAxdR4UVwfM"
    const baseURL = `https://eth-mainnet.alchemyapi.io/v2/${api_key}/getNFTs/`;
    var requestOptions = {
        method: 'GET'
      };
     
    if (!collection.length) {
    
      const fetchURL = `${baseURL}?owner=${wallet}`;
  
      nfts = await fetch(fetchURL, requestOptions).then(data => data.json())
    } else {
      console.log("fetching nfts for collection owned by address")
      const fetchURL = `${baseURL}?owner=${wallet}&contractAddresses%5B%5D=${collection}`;
      nfts= await fetch(fetchURL, requestOptions).then(data => data.json())
    }
  
    if (nfts) {
      console.log("nfts:", nfts)
      setNFTs(nfts.ownedNfts)
    }
  }
  
  const fetchNFTsForCollection = async () => {
    if (collection.length) {
      var requestOptions = {
        method: 'GET'
      };
      const api_key = "A8A1Oo_UTB9IN5oNHfAc2tAxdR4UVwfM"
      const baseURL = `https://eth-mainnet.alchemyapi.io/v2/${api_key}/getNFTsForCollection/`;
      const fetchURL = `${baseURL}?contractAddress=${collection}&withMetadata=${"true"}`;
      const nfts = await fetch(fetchURL, requestOptions).then(data => data.json())
      if (nfts) {
        console.log("NFTs in collection:", nfts)
        setNFTs(nfts.nfts)
      }
    }
  }
  return (
    <div className="flex flex-col items-center justify-center py-8 gap-y-3">
      <div className="flex flex-col w-full justify-center items-center gap-y-2">
        <input type={"text"} placeholder="Add your wallet address"></input>
        <input type={"text"} placeholder="Add the collection address"></input>
        <label className="text-gray-600 "><input type={"checkbox"} className="mr-2"></input>Fetch for collection</label>
        <button className={"disabled:bg-slate-500 text-white bg-blue-400 px-4 py-2 mt-3 rounded-sm w-1/5"} onClick={
          () => {
          }
        }>Let's go! </button>
      </div>
    </div>
  )
}

export default Home
```

现在你获取NFT的函数已经工作了，你需要把它们附加到你在前面几节创建的按钮的 "onClick "触发器上。

## 6. 触发FetchNFTs和FetchNFTsByCollection函数

你需要做的第一件事是添加一个名为 "fetchForCollection "的新状态变量，它将检查你是否想通过集合或钱包地址进行搜索。

```
import { useState } from 'react'

const Home = () => {
  const [wallet, setWalletAddress] = useState("");
  const [collection, setCollectionAddress] = useState("");
  const [NFTs, setNFTs] = useState([])
  const [fetchForCollection, setFetchForCollection]=useState(false)


  return (
    <div className="flex flex-col items-center justify-center py-8 gap-y-3">
      <div className="flex flex-col w-full justify-center items-center gap-y-2">
        <input type={"text"} placeholder="Add your wallet address"></input>
        <input type={"text"} placeholder="Add the collection address"></input>
        <label className="text-gray-600 "><input type={"checkbox"} className="mr-2"></input>Fetch for collection</label>
        <button className={"disabled:bg-slate-500 text-white bg-blue-400 px-4 py-2 mt-3 rounded-sm w-1/5"} onClick={
          () => {
         
          }
        }>Let's go! </button>
      </div>
    </div>
  )
}

export default Home
```

这个变量将由我们创建的复选框输入来处理，根据复选框是否被选中来更新其值。

- 勾选：我们通过收集来获取 - 状态变量将为真
- 未勾选：我们通过钱包地址来获取--状态变量将为假。

要做到这一点，你需要在输入中添加另一个 "onChange "处理程序，但这次使用 "e.target.checked"，值作为状态输入的值，而不是e.target.value。

```
import { useState } from 'react'

const Home = () => {
  const [wallet, setWalletAddress] = useState("");
  const [collection, setCollectionAddress] = useState("");
  const [NFTs, setNFTs] = useState([])
  const [fetchForCollection, setFetchForCollection]=useState(false)


  return (
    <div className="flex flex-col items-center justify-center py-8 gap-y-3">
      <div className="flex flex-col w-full justify-center items-center gap-y-2">
        <input type={"text"} placeholder="Add your wallet address"></input>
        <input type={"text"} placeholder="Add the collection address"></input>
        <label className="text-gray-600 "><input onChange={(e)=>{setFetchForCollection(e.target.checked)}} type={"checkbox"} className="mr-2"></input>Fetch for collection</label>
        <button className={"disabled:bg-slate-500 text-white bg-blue-400 px-4 py-2 mt-3 rounded-sm w-1/5"} onClick={
          () => {
          }
        }>Let's go! </button>
      </div>
    </div>
  )
}

export default Home
```

我们可以回过头来，用React开发工具检查这个变量是否被正确更新。

现在你知道你是按钱包还是按集合寻找NFT，确保你的按钮能够根据我们的 "fetchForCollection "变量启动正确的函数。

```
import { useState } from 'react'

const Home = () => {
  const [wallet, setWalletAddress] = useState("");
  const [collection, setCollectionAddress] = useState("");
  const [NFTs, setNFTs] = useState([])
  const [fetchForCollection, setFetchForCollection]=useState(false)


  return (
    <div className="flex flex-col items-center justify-center py-8 gap-y-3">
      <div className="flex flex-col w-full justify-center items-center gap-y-2">
        <input type={"text"} placeholder="Add your wallet address"></input>
        <input type={"text"} placeholder="Add the collection address"></input>
        <label className="text-gray-600 "><input onChange={(e)=>{setFetchForCollection(e.target.checked)}} type={"checkbox"} className="mr-2"></input>Fetch for collection</label>
        <button className={"disabled:bg-slate-500 text-white bg-blue-400 px-4 py-2 mt-3 rounded-sm w-1/5"} onClick={
           () => {
            if (fetchForCollection) {
              fetchNFTsForCollection()
            }else fetchNFTs()
          }
        }>Let's go! </button>
      </div>
    </div>
  )
}

export default Home
```

这里我们基本上是在告诉我们的按钮。

- 如果 "fetchForCollection "为真
- 则运行fetchNFTsForCollection()函数，如果不是，则直接fetchNFTs。

为了确保你在根据集合寻找NFT时不在钱包地址输入中添加钱包地址，你可以在钱包输入中添加 "disabled "属性，并在fetchForCollection为真时禁用它。

```
import { useState } from 'react'

const Home = () => {
  const [wallet, setWalletAddress] = useState("");
  const [collection, setCollectionAddress] = useState("");
  const [NFTs, setNFTs] = useState([])
  const [fetchForCollection, setFetchForCollection]=useState(false)


  return (
    <div className="flex flex-col items-center justify-center py-8 gap-y-3">
      <div className="flex flex-col w-full justify-center items-center gap-y-2">
        <input disabled={fetchForCollection} type={"text"} placeholder="Add your wallet address"></input>
        <input type={"text"} placeholder="Add the collection address"></input>
        <label className="text-gray-600 "><input onChange={(e)=>{setFetchForCollection(e.target.checked)}} type={"checkbox"} className="mr-2"></input>Fetch for collection</label>
        <button className={"disabled:bg-slate-500 text-white bg-blue-400 px-4 py-2 mt-3 rounded-sm w-1/5"} onClick={
           () => {
            if (fetchForCollection) {
              fetchNFTsForCollection()
            }else fetchNFTs()
          }
        }>Let's go! </button>
      </div>
    </div>
  )
}

export default Home
```

令人惊讶的是，下一步是将你的NFTs可视化。要做到这一点，你需要创建NFTCard组件。

## 7. 创建NFT卡片组件

在你项目的根文件夹中，创建一个新的文件夹，并将其称为 "组件"。

在这个文件夹中创建一个新的文件并命名为 "nftCard.jsx"。

你的NFT卡片将把一个NFT作为一个道具（在ReactJS文档中了解更多关于道具的信息），并将获得它的元数据以在卡片中显示，这将看起来像下面这样。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821205723.png)

要做到这一点，在你刚刚创建的文件中，添加以下代码。

```
export const NFTCard = ({ nft }) => {

    return (
        <div className="w-1/4 flex flex-col ">
        <div className="rounded-md">
            <img className="object-cover h-128 w-full rounded-t-md" src={nft.media[0].gateway} ></img>
        </div>
        <div className="flex flex-col y-gap-2 px-2 py-3 bg-slate-100 rounded-b-md h-110 ">
            <div className="">
                <h2 className="text-xl text-gray-800">{nft.title}</h2>
                <p className="text-gray-600">Id: {nft.id.tokenId}</p>
                <p className="text-gray-600" >{nft.contract.address}</p>
            </div>

            <div className="flex-grow mt-2">
                <p className="text-gray-600">{nft.description}</p>
            </div>
        </div>

    </div>
    )
}
```

你可以注意到，我们正在显示5个属性。

- 图片
- 标题
- TokenId
- 合同地址
- 描写

为了访问这些属性，我们可以再看一下NFT对象。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220821205736.png)

下面是获取NFT图像的方法。

- 访问媒体对象的第一个索引
- 访问其中的gateway属性以获得图像URL
- 在上面的代码 "media[0].gateway "中把图像URL分配给img标签。

要访问NFT标题，你只需要访问NFT对象本身的标题属性。

用你的NFT卡，去你的home.jsx文件，导入它来创建NFT画廊。

## 8. 创建NFT图库

在page>index.js文件中，就在你的按钮下面，导入以下代码。

```
 import { NFTCard } from "./components/nftCard"
 import { useState } from 'react'

const Home = () => {
  const [wallet, setWalletAddress] = useState("");
  const [collection, setCollectionAddress] = useState("");
  const [NFTs, setNFTs] = useState([])
  const [fetchForCollection, setFetchForCollection]=useState(false)


  return (
    <div className="flex flex-col items-center justify-center py-8 gap-y-3">
      <div className="flex flex-col w-full justify-center items-center gap-y-2">
        <input disabled={fetchForCollection} type={"text"} placeholder="Add your wallet address"></input>
        <input type={"text"} placeholder="Add the collection address"></input>
        <label className="text-gray-600 "><input onChange={(e)=>{setFetchForCollection(e.target.checked)}} type={"checkbox"} className="mr-2"></input>Fetch for collection</label>
        <button className={"disabled:bg-slate-500 text-white bg-blue-400 px-4 py-2 mt-3 rounded-sm w-1/5"} onClick={
           () => {
            if (fetchForCollection) {
              fetchNFTsForCollection()
            }else fetchNFTs()
          }
        }>Let's go! </button>
      </div>
      <div className='flex flex-wrap gap-y-12 mt-4 w-5/6 gap-x-2 justify-center'>
        {
          NFTs.length && NFTs.map(nft => {
            return (
              <NFTCard nft={nft}></NFTCard>
            )
          })
        }
      </div>
    </div>
  )
}

export default Home
```

以下是这段代码中发生的情况。

1. NFTCard在你的文件顶部被导入。
2. 在主页组件中，创建了一个新的div，我们打开了大括号，并使用条件渲染检查我们的状态变量中是否有NFTs。
3. 我们使用map函数遍历NFTs数组，并为每个NFT返回一个NFTCard，将NFT本身作为NFTCard的一个道具传递。

令人惊讶的是，每次我们都会获取NFTs，并在NFTs状态变量中存储一个数组

接下来将为每个NFT返回一个NFT卡，显示其信息。

然后你就可以了! 你现在有了一个工作的NFT画廊 :)

现在，让我们添加一些有趣的挑战，让你在提交你的项目之前自己尝试一下。

1. 在NFT地址旁边添加一个图标，使浏览你网站的人很容易复制合同地址。
2. 通过使用getNFTs端点的pageKey参数，添加一个分页系统来查看100多个NFT。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)