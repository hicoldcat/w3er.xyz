---
title: 如何使用 Thirdweb 添加 web3 登录
description: null
author: 李留白
weight: 0
date: 2023-12-13T02:58:04.207Z
lastmod: 2023-12-13T03:01:45.412Z
tags: []
categories:
    - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213105817.png
---

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20231213105817.png)

## 介绍

### 为什么使用 web3 登录？

使用以太坊登录可以让您使用钱包安全登录并在后端验证钱包！我们将使用 Thirdweb Auth，它使用非常流行的 JWT 标准！JSON Web Token (JWT) 是一种开放标准，它定义了一种紧凑且独立的方式，用于在各方之间以 JSON 对象的形式安全地传输信息。

## 设置

### 创建 Next.js 应用程序

我将在本指南中使用[Next typescript 入门模板](https://github.com/thirdweb-example/next-typescript-starter)。

如果您按照指南进行操作，则可以 使用[thirdweb CLI使用](https://portal.thirdweb.com/cli?utm_source=hashnode&utm_medium=crosspost&utm_campaign=on-demand-pass)[Next TypeScript 模板](https://github.com/thirdweb-example/next-typescript-starter)创建项目：

```
npx thirdweb create --next --ts
```

如果您已经有 Next.js 应用程序，您只需按照以下步骤即可开始使用：

- 安装`@thirdweb-dev/react`和`@thirdweb-dev/sdk`和`ethers`
- 将 MetaMask 身份验证添加到站点。您可以按照本[指南](https://portal.thirdweb.com/guides/add-connectwallet-to-your-website?utm_source=hashnode&utm_medium=crosspost&utm_campaign=tw-auth-next)来执行此操作。

### 设置 ThirdWeb 身份验证

首先，我们需要安装thirdweb auth包：

```
npm i @thirdweb-dev/auth # npm

yarn add @thirdweb-dev/auth # yarn
```

`auth.config.ts`现在，创建一个名为以下内容的文件：

```js
import { ThirdwebAuth } from "@thirdweb-dev/auth/next";

export const { ThirdwebAuthHandler, getUser } = ThirdwebAuth({
  privateKey: process.env.PRIVATE_KEY as string,
  domain: "example.org",
});
```

使用您的网站 URL 和私钥更新域，创建一个新`.env.local`文件并添加一个名为 的新变量`PRIVATE_KEY`。了解如何从钱包[导出私钥。](https://portal.thirdweb.com/guides/create-a-metamask-wallet?utm_source=hashnode&utm_medium=crosspost&utm_campaign=on-demand-pass#export-your-private-key)

要配置 auth api，请在其中创建一个`pages/api`名为 auth 的新文件夹并`[...thirdweb].ts`在其中创建文件！这里我们需要导出我们创建的thirdwebHandler！

```js
import { ThirdwebAuthHandler } from "../../../auth.config";

export default ThirdwebAuthHandler();
```

最后，在`_app.tsx`文件内，将 authConfig 属性添加到`ThirdwebProvider`：

```js
  <ThirdwebProvider
      desiredChainId={activeChainId}
      authConfig={{
        authUrl: "/api/auth",
        domain: "example.org",
        loginRedirect: "/",
      }}
    >
      <Component {...pageProps} />
    </ThirdwebProvider>
```

## 构建前端

在里面`pages/index.tsx`用以下内容更新 return 语句：

```js
return (
    <div>
      {address ? (
        <button onClick={() => login()}>
          Sign in with Ethereum
        </button>
      ) : (
        <ConnectWallet />
      )}
    </div>
  );
```

我们将使用`useAddress`和`useLogin`钩子来获取登录函数和用户地址：

```js
  const address = useAddress();
  const login = useLogin();
```

这将为我们的网站添加以太坊登录！现在我们需要检查用户是否存在，因此为此从钩子中获取用户，`useUser`如下所示：

```js
  const user = useUser();
```

我们将检查用户是否存在，如果存在我们将返回：

```js
if (user.user) {
  return (
    <div>
      <p>You are signed in as {user.user.address}</p>
      <button>Validate user</button>
    </div>
  );
}
```

## 创建用于验证的 api

现在让我们创建一个 api 来获取后端的用户详细信息（地址）！因此，创建一个名为`validate.ts`inside的新文件`pages/api`并添加以下内容：

```js
import type { NextApiRequest, NextApiResponse } from "next";
import { getUser } from "../../auth.config";

const handler = async (req: NextApiRequest, res: NextApiResponse) => {
  if (req.method === "POST") {
    const thirdwebUser = await getUser(req);

    if (thirdwebUser?.address) {
      return res.status(200).json({
        message: `You are signed in as ${thirdwebUser.address}`,
      });
    }
    return res.status(401).json({ message: "Account not validated" });
  }
  return res.status(405).json({ message: "Method not allowed" });
};

export default handler;
```

在这里，我们使用 ThirdWeb 的 getUser 方法来获取用户的地址，如果存在，我们会发送一条消息“您已作为地址登录”。

## 调用前端API

创建一个新函数，如下`handleClick`所示`pages/index.tsx`：

```js
  const handleClick = async () => {
    try {
      const response = await fetch("/api/validate", {
        method: "POST",
      });

      const data = await response.json();
      alert(data.message);
    } catch (error) {
      console.log(error);
    }
  };
```

并将此函数附加到验证按钮的 onClick 上：

```js
<button onClick={handleClick}>Validate user</button>
```

演示视频：

https://www.loom.com/share/7f637c53db6b4cf6a380ff8e113bf765?sid=7bcbcf04-de92-4770-840d-bf17c46f3dd0

## 结论

希望您通过本指南学会了如何将 web3 登录添加到您令人惊叹的 Dapp 中！

### 有用的链接

[GitHub 仓库](https://github.com/avneesh0612/thirdweb-auth-demo)

[Thirdweb Auth](https://thirdweb.com/auth)

>原文：https://blog.avneesh.tech/how-to-add-web3-sign-in-with-thirdweb