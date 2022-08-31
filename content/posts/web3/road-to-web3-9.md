---
title: Web3之路——如何用Lens协议创建一个去中心化的Twitter
description: null
author: 李留白
weight: 0
date: 2022-08-31T13:39:39.538Z
lastmod: 2022-08-31T14:50:22.198Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220814204307.png
---

最近有很多关于Facebook和Twitter等社交媒体巨头有多大控制权的讨论。不管你对它的看法如何，我想和你分享一个超级令人兴奋的实验。

今年，Aave团队的朋友们推出了一个名为Lens Protocol的项目。这是最近进入web3生态系统的最令人兴奋的技术之一，因为它使用区块链技术，将数据的权力和控制权交还给产生数据的用户。

所以我想，为什么我们不一起探索它呢？这将是一个合适的最后一课，把你到目前为止所学的一切结合起来🙂。

在这一课中，你将学习。

- 如何用Apollo GraphQL客户端设置一个Next.js应用
- 如何使用Lens协议API来获取Polygon区块链上存储的资料、帖子和其他数据
- 介绍MintKudos API -- 这样你就可以在你的应用程序中整合你的PoK token了
- 对Lit协议的介绍 -- 如果你想对某些帖子进行加密，只向不同的社区成员展示。
- 如何使用Repl.it部署你的去中心化的社交媒体应用前端网站
- 多个挑战选项来扩展这个项目!

这里有一个[Github Repo](https://github.com/thatguyintech/road-to-lens)，里面有一个已完成项目的例子，以防你需要它来跟随。

让我们开始吧!

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831214321.png)

### 第0步：参观Lens协议的生态系统

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831214339.png)

https://lens.xyz/

让我们先来看看Lens协议提供了什么吧!

如果你访问[Lens网站](https://lens.xyz/)，你会立即看到两个链接。

- 开发者园地 - 带你到文档和指南
- 加入 Discord - 带你到社区聊天服务器

我们将在一秒钟内访问开发者花园，但在此之前，让我们检查一下右上方的菜单栏选项。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831214943.png)

*你可以查看已经由社区建立的应用程序*

在这里，我将提请你注意两件事。

1. **Claim Handle**  - 截至目前（7/13/2022），Lens协议仍处于开放测试阶段，所以你需要获得允许列表，以便为你的钱包地址申领一个handle 。幸运的是，如果你一直在关注Road To Web3社区计划，并在过去获得了知识证明NFT，那么你很可能已经被列入了允许名单！如果没有，请到阿尔及尔大学来。如果没有，请进入 [Alchemy Discord](http://alchemy.com/discord) ，在[#week-10](https://discord.com/channels/735965332958871634/996103339416768593/996103993564610661)频道中询问此事。一旦你得到你的handle，请查看Lenster上的[Road To Web3社区 ](https://lenster.xyz/communities/0x25c4-0x0c)🔥
2. 应用程序 - 这个链接会带你到一个社区建立的网络应用列表，包括像Lensfrens、Lenster、Phaver、Alps Finance、Refract等。

https://twitter.com/thatguyintech/status/1546561239756615680

我想用一个简短的时间来说明Lens协议的力量。

如果你访问lensfrens的这个个人资料页面--https://www.lensfrens.xyz/thatguyintech.lens，你会看到我的账户，它应该是这样的。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831215318.png)

现在，如果你访问这个完全不同的网络应用Lenster - https://lenster.xyz/u/thatguyintech.lens - 你会再次看到我，有完全相同的个人资料数据，只是体验完全不同，甚至包括其他个人资料的帖子/评论/反应。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831215401.png)

到目前为止，没有什么超级震撼的东西，但在这里坚持我的观点。

看看其他的应用程序。

- Phaver - 支持Lens的社交移动应用，ALSO让你 "入股 "来策划其他人的帖子，从而让人们为他们的内容赚钱。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831222109.png)

- Refract - 它就像Hacker News，只是这里分享的所有链接和帖子都由Lens Protocol提供。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831222127.png)

这些应用都是由不同的人、不同的团队建立的，有着不同的用户体验和产品目标，但所有的底层数据都是一样的，就好像他们都共享同一个数据库和API。

这怎么可能呢？

事实证明，共享数据库和公共API正是Lens Protocol的基本思想。这就是为什么这项技术有如此大的潜力。

每一块数据都是一个NFT。

每个帖子，每个评论，每个反应，每个FOLLOW。这些数据中的每一个都被存储为由你这个创造者创建和控制的不可伪造的令牌。

这意味着，我们作为用户创建的数字内容和关系是由我们自己拥有的，并且可以被带到任何建立在协议之上的应用中去

现在让我们开始行动吧!

### 第1步：设置Next.js应用程序并安装Apollo

打开一个命令行。使用create-next-app来启动一个项目，该项目将被命名为road-to-lens

```bash
npx create-next-app road-to-lens
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831222218.png)


生成的 repo 将被称为 road-to-lens，你应该有以下目录结构。

```
thatguyintech@albert road-to-lens % tree -L 1
.
├── README.md
├── next.config.js
├── node_modules
├── package.json
├── pages
├── public
├── styles
└── yarn.lock
```

让我们在这里也安装我们的graphql客户端。我们将使用Apollo来查询Lens协议的数据。

```
npm install @apollo/client graphql
```

完成这些后，我们可以通过启动本地服务器并加载网页来进行理智检查。

```javascript
thatguyintech@albert road-to-lens % npm run dev

> road-to-lens@0.1.0 dev
> next dev

ready - started server on 0.0.0.0:3000, url: http://localhost:3000
info  - SWC minify release candidate enabled. https://nextjs.link/swcmin
event - compiled client and server successfully in 4.2s (169 modules)
```

加载http://localhost:3000，应该会得到一个基本的模板页面，看起来像这样。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831222230.png)

###  第2步：在index.js页面上试用Apollo GraphQL与Lens的推荐配置文件

让我们通过在主页上加载Lens推荐的配置文件来熟悉一下Apollo和GraphQL。

首先，设置Apollo提供者来包装我们的整个应用，这样我们就可以在以后访问useQuery和useMutation等方法。

在顶层目录中创建一个文件，名为apollo-client.js

```
thatguyintech@albert road-to-lens % touch apollo-client.js
```

我们将在这里初始化一个客户端，其基本网址指向Lens Matic Mainnet API。

```javascript
// ./apollo-client.js

import { ApolloClient, InMemoryCache } from "@apollo/client";

const client = new ApolloClient({
    uri: "https://api.lens.dev",
    cache: new InMemoryCache(),
});

export default client;
```

有了这个GraphQL客户端的初始化，我们可以在我们的/pages/_app.js文件中导入它，用它来包装我们的全局应用程序组件。

```javascript
// pages/_app.js

import '../styles/globals.css'
import { ApolloProvider } from "@apollo/client";
import client from "../apollo-client";

function MyApp({ Component, pageProps }) {
  return (
    <ApolloProvider client={client}>
      <Component {...pageProps} />
    </ApolloProvider>
  );
}

export default MyApp
```

你可以看到，我们在这里添加了<ApolloProvider client={client}>作为包装器。这让我们的整个应用具有了超能力--在其他地方，我们都可以使用useQuery和useMutation这样的实用方法来从Lens API中获取数据，并且也可以发送更新。

在我们回到localhost检查之前，还有最后一次更新。让我们更新/pages/index.js，从Lens获取推荐档案的查询。

```jsx
import { useQuery, gql } from "@apollo/client";

const recommendProfiles = gql`
  query RecommendedProfiles {
    recommendedProfiles {
          id
        name
        bio
        attributes {
          displayType
          traitType
          key
          value
        }
          followNftAddress
        metadata
        isDefault
        picture {
          ... on NftImage {
            contractAddress
            tokenId
            uri
            verified
          }
          ... on MediaSet {
            original {
              url
              mimeType
            }
          }
          __typename
        }
        handle
        coverPicture {
          ... on NftImage {
            contractAddress
            tokenId
            uri
            verified
          }
          ... on MediaSet {
            original {
              url
              mimeType
            }
          }
          __typename
        }
        ownedBy
        dispatcher {
          address
          canUseRelay
        }
        stats {
          totalFollowers
          totalFollowing
          totalPosts
          totalComments
          totalMirrors
          totalPublications
          totalCollects
        }
        followModule {
          ... on FeeFollowModuleSettings {
            type
            amount {
              asset {
                symbol
                name
                decimals
                address
              }
              value
            }
            recipient
          }
          ... on ProfileFollowModuleSettings {
          type
          }
          ... on RevertFollowModuleSettings {
          type
          }
        }
    }
  }
`;

export default function Home() {
  const {loading, error, data} = useQuery(recommendProfiles);

  if (loading) return 'Loading..';
  if (error) return `Error! ${error.message}`;

  return (
    <div>
      Hello
      {data.recommendedProfiles.map((profile, index) => {
        console.log(`Profile ${index}:`, profile);
        return (
          <div>
            <h1>{profile.name}</h1>
            <p>{profile.bio}</p>
            <div>{profile.attributes.map((attr, idx) => {
              if (attr.key === "website") {
                return <div><a href={`${attr.value}`}>{attr.value}</a><br/></div>
              } else if (attr.key === "twitter") {
                return <div><a href={`https://twitter.com/${attr.value}`}>@{attr.value}</a><br/></div>;
              }
              return(<div>{attr.value}</div>);
            })}</div>
          </div>
        );
      })}
    </div>
  )
}
```

我们在这个变化中做了几件关键的事情。

1. 定义一个名为 RecommendedProfiles 的 GraphQL 查询。
2. 通过调用带有 RecommendedProfiles 查询的 useQuery 来获取配置文件列表 -> 该列表将在数据变量中返回。
3. 显示一些资料信息，如data.profile.name、data.profile.bio和data.profile.attribute。

回到http://localhost:3000/（确保你的本地服务器正在运行），然后voilá，你应该看到这个非常简单的配置文件列表

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831222253.png)

丑陋，但很酷吧？

如果你想在这里暂停，做一些更多的探索，你可以在你的/pages/index.js文件中添加一个console.log语句，以查看更多从GraphQL查询中返回的数据。

例如，通过添加console.log(data)，你就可以在浏览器上拉出开发者控制台，看到个人资料数据。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831222306.png)

查看控制台中的RecommendedProfiles响应，或在开发者文档中查看


我们以后会更多地查看这些数据，并清理页面设计 🙂

提醒一下：所有这些数据都以NFT的形式存储在Polygon区块链上。

Lens团队用他们的API所做的只是对所有的链上数据进行索引，这样开发者就可以更容易地使用NFTs来获取和构建了

在这一点上，快速的理智检查。

你的目录结构应该是这样的。

```bash
thatguyintech@albert road-to-lens % tree -L 2
.
├── README.md
├── apollo-client.js      <- we created this
├── next.config.js
├── node_modules
├── package-lock.json
├── package.json
├── pages
│   ├── api
│   ├── _app.js           <- we modified this
│   └── index.js          <- we modified this
├── public
│   ├── favicon.ico
│   └── vercel.svg
├── styles
│   ├── Home.module.css
│   └── globals.css
└── yarn.lock
```

### 第3步：让我们使配置文件列表看起来更漂亮

在这一部分，我们将重构我们的代码，使其更容易浏览，我们还将设计我们的组件，使用户界面看起来更干净。

从重构开始。

这是我们新的/pages/index.js

```javascript
import { useQuery } from "@apollo/client";
import recommendedProfilesQuery from '../queries/recommendedProfilesQuery.js';
import Profile from '../components/Profile.js';

export default function Home() {
  const {loading, error, data} = useQuery(recommendedProfilesQuery);

  if (loading) return 'Loading..';
  if (error) return `Error! ${error.message}`;

  return (
    <div>
      {data.recommendedProfilesQuery.map((profile, index) => {
        console.log(`Profile ${index}:`, profile);
        return <Profile key={profile.id} profile={profile} displayFullProfile={false} />;
      })}
    </div>
  )
}
```

我们要把 recommendProfilesQuery 移到一个单独的 Graphql 文档中，放在一个你需要创建的名为 queries 的新文件夹中。

```bash
mkdir queries
touch queries/recommendedProfilesQuery.js
```

然后把 RecommendedProfiles 文档复制到这个文件中。

```javascript
// queries/recommendedProfilesQuery.js

import {gql} from '@apollo/client';

export default gql`
  query RecommendedProfiles {
    recommendedProfiles {
          id
        name
        bio
        attributes {
          displayType
          traitType
          key
          value
        }
          followNftAddress
        metadata
        isDefault
        picture {
          ... on NftImage {
            contractAddress
            tokenId
            uri
            verified
          }
          ... on MediaSet {
            original {
              url
              mimeType
            }
          }
          __typename
        }
        handle
        coverPicture {
          ... on NftImage {
            contractAddress
            tokenId
            uri
            verified
          }
          ... on MediaSet {
            original {
              url
              mimeType
            }
          }
          __typename
        }
        ownedBy
        dispatcher {
          address
          canUseRelay
        }
        stats {
          totalFollowers
          totalFollowing
          totalPosts
          totalComments
          totalMirrors
          totalPublications
          totalCollects
        }
        followModule {
          ... on FeeFollowModuleSettings {
            type
            amount {
              asset {
                symbol
                name
                decimals
                address
              }
              value
            }
            recipient
          }
          ... on ProfileFollowModuleSettings {
          type
          }
          ... on RevertFollowModuleSettings {
          type
          }
        }
    }
  }
`;
```

然后让我们也创建我们在上面的index.js页面中介绍的Profile组件（ import Profile from '.../components/Profile.js';)

我们应该把它组织到一个新的目录中，称为组件。

```bash
mkdir components
touch components/Profile.js
```

然后把这个结构复制进去就可以了。

```javascript
// components/Profile.js

import Link from "next/link";
export default function Profile(props) {
  const profile = props.profile;

  // When displayFullProfile is true, we show more info.
  const displayFullProfile = props.displayFullProfile;

  return (
    <div className="p-8">
      <Link href={`/profile/${profile.id}`}>
        <div className="max-w-md mx-auto bg-white rounded-xl shadow-md overflow-hidden md:max-w-2xl">
          <div className="md:flex">
            <div className="md:shrink-0">
              {profile.picture ? (
                <img
                  src={
                    profile.picture.original
                      ? profile.picture.original.url
                      : profile.picture.uri
                  }
                  className="h-48 w-full object-cover md:h-full md:w-48"
                />
              ) : (
                <div
                  style={{
                    backgrondColor: "gray",
                  }}
                  className="h-48 w-full object-cover md:h-full md:w-48"
                />
              )}
            </div>
            <div className="p-8">
              <div className="uppercase tracking-wide text-sm text-indigo-500 font-semibold">
                {profile.handle}
                {displayFullProfile &&
                  profile.name &&
                  " (" + profile.name + ")"}
              </div>
              <div className="block mt-1 text-sm leading-tight font-medium text-black hover:underline">
                {profile.bio}
              </div>
              <div className="mt-2 text-sm text-slate-900">{profile.ownedBy}</div>
              <p className="mt-2 text-xs text-slate-500">
                following: {profile.stats.totalFollowing} followers:{" "}
                {profile.stats.totalFollowers}
              </p>
            </div>
          </div>
        </div>
      </Link>
    </div>
  );
}
```

这个Profile组件接受props作为输入，并希望props对象有一个profile字段，其中包括我们在渲染组件时可能想要显示的所有信息。

当我们在这里的时候，我们也添加了个人资料的图片和追随者/关注者的数量!

现在，让我们再一次检查我们的工作。经常检查我们的工作是一个好习惯，因为它可以让我们迅速抓住错误

确保你的服务器正在运行。

```bash
thatguyintech@albert road-to-lens % npm run dev

> road-to-lens@0.1.0 dev
> next dev

ready - started server on 0.0.0.0:3000, url: http://localhost:3000
```

然后去访问localhost: http://localhost:3000/

你应该看到像这样的东西。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831222408.png)


它仍然不是那么好看，但是，嘿，我们有照片！"。

在我们完成这一步之前，让我们利用我们包含在简介组件中的CSS的优势。

如果你以前从未使用过Tailwind的CSS，这些标签。

```
大写字母 tracking-wide text-sm text-indigo-500 font-semibold
```

都来自于Tailwind设计的实用性优先的基本原则（[见文档](https://tailwindcss.com/docs/responsive-design)）。

因此，实际上我们在这里要做的就是按照他们的[安装说明](https://tailwindcss.com/docs/guides/nextjs)，安装Tailwind。

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

复制这些配置，以便Tailwind知道在我们的项目中要加载哪些路径的样式。我们希望页面文件夹和组件文件夹中的所有内容都被覆盖，所以我们使用这些路径。

```javascript
// tailwind.config.js

module.exports = {
  content: [
    "./pages/**/*.{js,ts,jsx,tsx}",
    "./components/**/*.{js,ts,jsx,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

现在，我们通过将Tailwind指令添加到我们的CSS文件中来收尾。

```css
/* ./styles/globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

然后重新启动服务器，看看它现在看起来怎么样了

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831222421.png)


它看起来棒极了。甚至还有阴影和一切 🙂

现在......你们中的一些人可能已经注意到，我在简介组件中包含了一些<链接>，然后当你点击其中一个简介时，它会链接到一个新的页面

例如，点击DAVIDEV.LENS的卡片，会把我们带到http://localhost:3000/profile/0x16，并显示一个404错误。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831222432.png)


不用担心，在下一步，让我们建立这个页面，并进行第二个GraphQL查询，以获取Publications（又称 "帖子"，像其他社交媒体平台）。

### 第4步：创建个人简介页面

使用Next.js框架快速构建的最酷的事情之一是路由！对于我们的个人简介页面，我们所做的一切是为了获得更多的信息。对于我们的个人简介页面，我们所要做的就是在pages文件夹下建立一个与我们的简介组件同名的文件夹。然后添加一个[id].js文件，该文件可用于传递到路由中的任何动态id!

这就是我的意思。

```bash
road-to-lens % mkdir pages/profile
road-to-lens % touch pages/profile/\[id\].js
```

我们稍后将填写pages/profile/[id].js文件。但有了这个文件结构，我们的Next.js应用程序将能够为诸如以下URL的请求提供服务。

- http://localhost:3000/profile/0x9752
- http://localhost:3000/profile/0x25c4

因此，让我们通过向Lens API设置一个新的GraphQL请求来获取个人资料信息，然后我们将在[id].js文件中构建组件来完成这个工作。

为了获取个人简介信息，让我们创建一个GraphQL文档，就像我们之前为获取整个推荐简介列表所做的那样。

```
road-to-lens % touch queries/fetchProfileQuery.js
```



```bash
// queries/fetchProfileQuery.js

import { gql } from '@apollo/client';

export default gql`
query($request: SingleProfileQueryRequest!) {
    profile(request: $request) {
        id
        name
        bio
        attributes {
          displayType
          traitType
          key
          value
        }
        followNftAddress
        metadata
        isDefault
        picture {
          ... on NftImage {
            contractAddress
            tokenId
            uri
            verified
          }
          ... on MediaSet {
            original {
              url
              mimeType
            }
          }
          __typename
        }
        handle
        coverPicture {
          ... on NftImage {
            contractAddress
            tokenId
            uri
            verified
          }
          ... on MediaSet {
            original {
              url
              mimeType
            }
          }
          __typename
        }
        ownedBy
        dispatcher {
          address
          canUseRelay
        }
        stats {
          totalFollowers
          totalFollowing
          totalPosts
          totalComments
          totalMirrors
          totalPublications
          totalCollects
        }
        followModule {
          ... on FeeFollowModuleSettings {
            type
            amount {
              asset {
                symbol
                name
                decimals
                address
              }
              value
            }
            recipient
          }
          ... on ProfileFollowModuleSettings {
            type
          }
          ... on RevertFollowModuleSettings {
            type
          }
        }
    }
  }
`;
```

现在，让我们在个人简介页中使用这个查询吧!

```javascript
// pages/profile/[id].js

import { useQuery } from "@apollo/client";
import { useRouter } from "next/router";
import fetchProfileQuery from "../../queries/fetchProfileQuery.js";

import Profile from "../../components/Profile.js";

export default function ProfilePage() {
  const router = useRouter();
  const { id } = router.query;

  console.log("fetching profile for", id);
  const { loading, error, data } = useQuery(fetchProfileQuery, {
    variables: { request: { profileId: id } },
  });

  if (loading) return "Loading..";
  if (error) return `Error! ${error.message}`;

  console.log("on profile page data: ", data);

  return <Profile profile={data.profile} displayFullProfile={true}/>
}
```

如果你看一下我们添加的文件，你会看到一个熟悉的useQuery()调用，它接收了从"././queries/fetchProfileQuery.js "导入的fetchProfileQuery GraphQL文档。

新的是，我们现在还引入了useRouter()钩子。这个钩子允许我们在运行时从URL的查询参数中抓取任何变量[id]值，然后在我们的代码中使用。

这意味着，如果我们有这些URL。

- http://localhost:3000/profile/0x9752
- http://localhost:3000/profile/0x25c4

useRouter()调用将获取以下id为 const { id } = router.query; :

- 0x9752
- 0x25c4

然后我们用这些id传入fetchProfileQuery，这样我们就可以只抓取我们正在浏览的那个人的资料。你可以[在这里找到](https://docs.lens.xyz/docs/get-profile)获取个人资料请求的完整文档。

好的，点击保存。刷新页面! 你看到了什么？

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831222449.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831222454.png)

好了，如果到目前为止你的一切都很顺利 -- 一定要庆祝这个里程碑（如果你被卡住了，也可以请求帮助）。

你已经成功了一半以上!

### 第5步：在简介页上加载用户帖子

让我们通过获取用户所发的一些帖子，使个人资料页变得更有趣。

在Lens协议中，帖子、评论和镜像（又称 "转发"）--都被归入Publications数据模型。

您可以在此处找到[发布端点的文档](https://docs.lens.xyz/docs/publication-1)。我们将专注于[ GET Publication](https://docs.lens.xyz/docs/get-publication) 操作。再一次，让我们遵循同样的顺序。

1. 设置GraphQL查询文档。
2. 更新配置文件页面，以使用新的GraphQL查询。
3. 为 "post "创建一个新的组件定义。
4. 把从Lens API返回的数据传递到新的组件中。
5. 测试和调试页面!

#### 1. 设置GraphQL查询文档

这一次，我们不需要一个全新的文档文件。我们可以修改我们在fetchProfileQuery.js中开始的查询，以便在同一请求中也询问Publications。这就是GraphQL的力量! 与REST API调用不同的是，你可以在一个请求中要求更多或更少。让我们对 queries/fetchProfileQuery.js 进行这一更新。

```graphql
import { gql } from "@apollo/client";

export default gql`
  query (
    $request: SingleProfileQueryRequest!
    $publicationsRequest: PublicationsQueryRequest!
  ) {
    publications( request: $publicationsRequest) {
      items {
        __typename
        ... on Post {
          ...PostFields
        }
        ... on Comment {
          ...CommentFields
        }
        ... on Mirror {
          ...MirrorFields
        }
      }
      pageInfo {
        prev
        next
        totalCount
      }
    }
    profile(request: $request) {
      id
      name
      bio
      attributes {
        displayType
        traitType
        key
        value
      }
      followNftAddress
      metadata
      isDefault
      picture {
        ... on NftImage {
          contractAddress
          tokenId
          uri
          verified
        }
        ... on MediaSet {
          original {
            url
            mimeType
          }
        }
        __typename
      }
      handle
      coverPicture {
        ... on NftImage {
          contractAddress
          tokenId
          uri
          verified
        }
        ... on MediaSet {
          original {
            url
            mimeType
          }
        }
        __typename
      }
      ownedBy
      dispatcher {
        address
        canUseRelay
      }
      stats {
        totalFollowers
        totalFollowing
        totalPosts
        totalComments
        totalMirrors
        totalPublications
        totalCollects
      }
      followModule {
        ... on FeeFollowModuleSettings {
          type
          amount {
            asset {
              symbol
              name
              decimals
              address
            }
            value
          }
          recipient
        }
        ... on ProfileFollowModuleSettings {
          type
        }
        ... on RevertFollowModuleSettings {
          type
        }
      }
    }
  }

  fragment MediaFields on Media {
    url
    mimeType
  }

  fragment ProfileFields on Profile {
    id
    name
    bio
    attributes {
      displayType
      traitType
      key
      value
    }
    isFollowedByMe
    isFollowing(who: null)
    followNftAddress
    metadata
    isDefault
    handle
    picture {
      ... on NftImage {
        contractAddress
        tokenId
        uri
        verified
      }
      ... on MediaSet {
        original {
          ...MediaFields
        }
      }
    }
    coverPicture {
      ... on NftImage {
        contractAddress
        tokenId
        uri
        verified
      }
      ... on MediaSet {
        original {
          ...MediaFields
        }
      }
    }
    ownedBy
    dispatcher {
      address
    }
    stats {
      totalFollowers
      totalFollowing
      totalPosts
      totalComments
      totalMirrors
      totalPublications
      totalCollects
    }
    followModule {
      ... on FeeFollowModuleSettings {
        type
        amount {
          asset {
            name
            symbol
            decimals
            address
          }
          value
        }
        recipient
      }
      ... on ProfileFollowModuleSettings {
        type
      }
      ... on RevertFollowModuleSettings {
        type
      }
    }
  }

  fragment PublicationStatsFields on PublicationStats {
    totalAmountOfMirrors
    totalAmountOfCollects
    totalAmountOfComments
  }

  fragment MetadataOutputFields on MetadataOutput {
    name
    description
    content
    media {
      original {
        ...MediaFields
      }
    }
    attributes {
      displayType
      traitType
      value
    }
  }

  fragment Erc20Fields on Erc20 {
    name
    symbol
    decimals
    address
  }

  fragment CollectModuleFields on CollectModule {
    __typename
    ... on FreeCollectModuleSettings {
      type
      followerOnly
      contractAddress
    }
    ... on FeeCollectModuleSettings {
      type
      amount {
        asset {
          ...Erc20Fields
        }
        value
      }
      recipient
      referralFee
    }
    ... on LimitedFeeCollectModuleSettings {
      type
      collectLimit
      amount {
        asset {
          ...Erc20Fields
        }
        value
      }
      recipient
      referralFee
    }
    ... on LimitedTimedFeeCollectModuleSettings {
      type
      collectLimit
      amount {
        asset {
          ...Erc20Fields
        }
        value
      }
      recipient
      referralFee
      endTimestamp
    }
    ... on RevertCollectModuleSettings {
      type
    }
    ... on TimedFeeCollectModuleSettings {
      type
      amount {
        asset {
          ...Erc20Fields
        }
        value
      }
      recipient
      referralFee
      endTimestamp
    }
  }

  fragment PostFields on Post {
    id
    profile {
      ...ProfileFields
    }
    stats {
      ...PublicationStatsFields
    }
    metadata {
      ...MetadataOutputFields
    }
    createdAt
    collectModule {
      ...CollectModuleFields
    }
    referenceModule {
      ... on FollowOnlyReferenceModuleSettings {
        type
      }
    }
    appId
    hidden
    mirrors(by: null)
    hasCollectedByMe
  }

  fragment MirrorBaseFields on Mirror {
    id
    profile {
      ...ProfileFields
    }
    stats {
      ...PublicationStatsFields
    }
    metadata {
      ...MetadataOutputFields
    }
    createdAt
    collectModule {
      ...CollectModuleFields
    }
    referenceModule {
      ... on FollowOnlyReferenceModuleSettings {
        type
      }
    }
    appId
    hidden
    hasCollectedByMe
  }

  fragment MirrorFields on Mirror {
    ...MirrorBaseFields
    mirrorOf {
      ... on Post {
        ...PostFields
      }
      ... on Comment {
        ...CommentFields
      }
    }
  }

  fragment CommentBaseFields on Comment {
    id
    profile {
      ...ProfileFields
    }
    stats {
      ...PublicationStatsFields
    }
    metadata {
      ...MetadataOutputFields
    }
    createdAt
    collectModule {
      ...CollectModuleFields
    }
    referenceModule {
      ... on FollowOnlyReferenceModuleSettings {
        type
      }
    }
    appId
    hidden
    mirrors(by: null)
    hasCollectedByMe
  }

  fragment CommentFields on Comment {
    ...CommentBaseFields
    mainPost {
      ... on Post {
        ...PostFields
      }
      ... on Mirror {
        ...MirrorBaseFields
        mirrorOf {
          ... on Post {
            ...PostFields
          }
          ... on Comment {
            ...CommentMirrorOfFields
          }
        }
      }
    }
  }

  fragment CommentMirrorOfFields on Comment {
    ...CommentBaseFields
    mainPost {
      ... on Post {
        ...PostFields
      }
      ... on Mirror {
        ...MirrorBaseFields
      }
    }
  }
`;
```

请注意，我们确实添加了一个新的查询输入参数：$publicationsRequest。PublicationsQueryRequest！。这是我们必须确保在启动查询时作为输入参数传入的东西。

#### 2. 更新个人资料页面，以使用新的GraphQL查询。

在简介页上，这一步我们真正需要做的事情是为我们想要的帖子添加发布请求。该请求本身应该是这样的。

```javascript
publicationsRequest: {
  profileId: id,
  publicationTypes: ["POST"], // We really only want POSTs
},
```

因此，让我们把它添加到useQuery输入参数选项中。

```javascript
// pages/profile/[id].js

import { useQuery, useMutation } from "@apollo/client";
import { useRouter } from "next/router";
import fetchProfileQuery from "../../queries/fetchProfileQuery.js";
import Profile from "../../components/Profile.js";

export default function ProfilePage() {
  const router = useRouter();
  const { id } = router.query;

  console.log("fetching profile for", id);
  const { loading, error, data } = useQuery(fetchProfileQuery, {
    variables: {
      request: { profileId: id },
      publicationsRequest: {
        profileId: id,
        publicationTypes: ["POST"], // We really only want POSTs
      },
    },
  });

  if (loading) return "Loading..";
  if (error) return `Error! ${error.message}`;

  console.log("on profile page data: ", data);
  return (
    <div className="flex flex-col p-8 items-center">
      <Profile profile={data.profile} displayFullProfile={true} />
    </div>
  );
}
```

#### 3. 为 "帖子 "创建一个新的组件定义。

我们需要一个新的React组件来显示检索到的关于用户帖子的数据。让我们在 components/Post.js 中创建一个。

```javascript
// components/Post.js
export default function Post(props) {
  const post = props.post;

  return (
    <div className="p-8">
      <div className="max-w-md mx-auto bg-white rounded-xl shadow-md overflow-hidden md:max-w-2xl">
        <div className="md:flex">
          <div className="p-8">
            <p className="mt-2 text-xs text-slate-500 whitespace-pre-line">
              {post.metadata.content}
            </p>
          </div>
        </div>
      </div>
    </div>
  );
}
```

如果你把这个简单的组件和Profile组件进行比较，你会发现它们都遵循一个非常简单的模式，那就是它们接受一个props对象，这个对象有组件所需要的数据，以呈现它的某些部分。

在这种情况下，我们真正需要的只是来自帖子的一个核心信息。

- `post.metadata.content`

鉴于这个元数据字段是我们唯一需要的，你实际上可以在以后回到GraphQL文档中，删除我们在最终功能中没有使用的字段。

这个组件也使用Tailwind的CSS进行样式设计。

#### 4. 将从Lens API返回的数据传递到新的组件中

回到个人资料页面（pages/profile/[id].js）。

现在我们可以将<Post/>组件导入到个人资料页面，并使用它来呈现我们从API返回的所有帖子。

```javascript
import { useQuery, useMutation } from "@apollo/client";
import { useRouter } from "next/router";
import fetchProfileQuery from "../../queries/fetchProfileQuery.js";
import Profile from "../../components/Profile.js";
import Post from "../../components/Post.js";

export default function ProfilePage() {
  const router = useRouter();
  const { id } = router.query;

  console.log("fetching profile for", id);
  const { loading, error, data } = useQuery(fetchProfileQuery, {
    variables: {
      request: { profileId: id },
      publicationsRequest: {
        profileId: id,
        publicationTypes: ["POST"],
      },
    },
  });

  if (loading) return "Loading..";
  if (error) return `Error! ${error.message}`;

  return (
    <div className="flex flex-col p-8 items-center">
      <Profile profile={data.profile} displayFullProfile={true} />
      {data.publications.items.map((post, idx) => {
        return <Post key={idx} post={post}/>;
      })}
    </div>
  );
}
```

请注意，在该代码段的底部，实际上是这几行代码。

```javascript
      {data.publications.items.map((post, idx) => {
        return <Post key={idx} post={post}}/>;
      })}
```

在这一步骤中做了大量的工作。但在添加了它之后，我们应该可以尝试一下我们的帖子功能了。

#### 5. 测试和调试

如果一切顺利的话，你现在应该可以看到在个人资料卡下的帖子了

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220831222539.png)


恭喜你，你已经在成为一个去中心化的社交媒体开发者的路上了 :)

### 其他API和资源

希望到此为止，你已经完成了其他一些Road To Web3的挑战，并且你很享受走到这一步的过程。本周的课程是关于如何使用建立在NFTs和去中心化协议之上的API。

我强烈建议你抽出时间来探索Lens文档，尝试不同的端点，并阅读这个 repo中托管的示例代码。

[https://github.com/aave/lens-api-examples](https://github.com/aave/lens-api-examples)

这里有一些其他前沿的web3 apis。

- [MintKudos PoK Tokens APIs ](https://contributionlabs.notion.site/Public-APIs-054cc0a8110a45a9be326fda382bb6bc#6e2810e0d8984496ad7a8674adf12dfd)- 由Kei @ Contribution Labs创建。
- [Lit Protocol SDK和教程](https://litprotocol.notion.site/Decentralized-Unlockable-Content-21a2a1dadb4a4845a5a77ab2589069a0)--由Deb @ Lit Protocol创建

有了这些工具和数据访问权，我真的很想看看你们都能想出什么办法来 🙂

一个Web3瑜伽社交网络？

仅供Road to Web3 PoK代币持有人使用的独家聚会场所？

一个新时代的在线教室，教师通过你们的dapp直接从学生那里获得内容和教育方面的报酬？

这种可能性是无穷无尽的。

### 挑战

本周的内容很复杂，所以我给你的挑战选项是故意模糊的，以便你可以随意探索。至少从下面的选项中选一个，看看你能做到什么程度。

1. 使用Lens API，查询教程中未涉及的新信息，并在网站上显示。
2. 使用Lens API，弄清楚如何对反应、关注或创建新出版物等行为进行认证和突变。
3. 使用Repl.it或Vercel等服务，将你的应用程序部署到全球网络上。
4. 使用Lit协议SDK对你的Lens协议帖子进行令牌访问，这样只有MintKudos的令牌持有者才能查看它们。

你已经成功地学会了如何使用Lens协议进行导航、查询和构建! 🔥

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)