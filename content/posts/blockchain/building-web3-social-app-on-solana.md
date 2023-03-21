---
title: 在 Solana 上构建 web3 社交应用
description: null
author: 李留白
weight: 0
date: 2023-03-21T16:06:45.395Z
lastmod: 2023-03-21T16:43:17.203Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230322000701.png
---

Web3很有趣，当你可以使用你最喜欢的Web2应用程序的Web3版本时，它就更令人愉快了。2023年1月，Solana Sandstorm黑客马拉松开始了，有 50 多条赛道可供构建应用程序。我选择参加 Web3 社交应用程序赛道，特别是[Spling 协议](https://www.splinglabs.com/)。经过头脑风暴，我决定使用 Spling 协议构建一个结合 Instagram 和 Pinterest 功能的社交媒体平台。Spling 是一种建立在 Solana 区块链上的社交层协议，它简化了构建 Web3 社交应用程序的过程，而不需要 Rust 中的复杂智能合约。这意味着开发人员可以使用熟悉的 Web2 技术栈在 Solana 上构建 Web3 社交应用程序。

在我第一次尝试使用 Spling SDK 期间，我遇到了几个问题。然而，感谢 Spling Discord 提供的支持，我能够快速获得技术帮助并解决我的问题。

我项目的决定性技术堆栈是**React**和**TailwindCSS**，尽管我建议使用NextJS来进行服务器端渲染，并对用户生成的内容进行更好的SEO。现在让我们深入研究事物的技术方面，并开始接触 Spling。确保从Github 上 [Solpin ](https://github.com/ItsAditya-xyz/solpin)开源存储库中获取参考。这个博客是关于 Spling 协议的，而不是关于 React 和其他技术的，所以如果你在你的应用程序中遇到一些非 Spling 错误，请确保从代码库中获得帮助。

## 使用 Solana 钱包登录设置入门React应用程序：

我已经为ReactJS应用程序创建了一个启动模板，其中已经有Solana登录功能。克隆[这个repo ](https://github.com/ItsAditya-xyz/solana-reactJS-starter)并运行它。对于后面的教程，我将使用相同的模板。这就是它一开始的样子：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202303220007657.png)

在入门模板中要注意的一件事是它使用`react-app-rewired`（请参阅`package.json`中的“scripts” ），因为我们在项目的根目录中有一个自定义文件`config-overrides.js`来修复一些依赖性问题。

## 将 Spling SDK 安装到启动程序中

对于 Solpin，我使用了`"@spling/social-protocol": "^1.5.4"`Spling SDK 的版本。确保安装带有`--no-optional`标志的Spling SDK ，如`npm install @spling/social-protocol --no-optional`，以只包括React所需的文件。

## 关于Spling和Spling SDK

Spling 建立在 Solana 区块链之上，并使用[Shadow Drive](https://www.shadow.cloud/)来存储内容。Spling SDK 使开发人员能够使用 web2 技术在 Solana 上构建社交应用程序。Spling Protocol 上的每个活动都是区块链交易，因此需要 gas 费。gas 费可以由最终用户支付，也可以由应用程序本身支付，从而使最终用户的一切都无需 gas。Spling SDK 允许两种选择。在本教程中，我们将只采用非 gas 方式，用户自己将为所有交易支付 gas。只有那个人必须确保用户钱包中有足够的 $SOL 和 $SHDW 代币。人们可以[在这里轻松地交换一些 $SHDW 代币](https://jup.ag/swap/SOL-SHDW)

## 使用和构建 Spling应用程序时的工作流程

用户要使用 Spling，他们**必须**在 Spling 协议上拥有一个帐户。前端应用程序应该有一个逻辑，应用程序检查 Solana 钱包是否已经有一个 spling 帐户。如果用户没有 Spling 帐户，应用程序应路由到`/sign-up`，用户可以创建其个人资料的位置。对 Spling 的所有写操作都必须有一个配置文件。

如果用户有一个 Spling 帐户，他们可以执行所有的写操作，如`createPost()`等。所有帖子只能在组内发布。群组（或`groupID`）通常由开发人员自己创建，表示用户可以在其中发布帖子的 appID。通过`groupID`，人们可以阅读所有的帖子和内容。目前，一个钱包只能创建一个群组。Spling 上的所有内容都可以使用删除功能隐藏，使其对前端不可见。这就是删除在 Spling 中的工作方式。[您可以在此处](https://www.docs.splinglabs.com/)阅读 Spling 的开发人员文档。现在让我们深入了解制作完整的 Spling 应用程序的步骤。

## 在 Spling 上制作我们的第一个应用程序

### 1.创建`/sign-up`页面

在前面提到的入门模板中，在`/src`创建一个 utils 文件夹，其中包含两个名为`constants.js`和`functions.js`的文件。

Constant.js 有 Spling SDK 的配置，它看起来像这样：

```js
export const protocolOptions = {
  useIndexer: true,
  rpcUrl:
    "YOUR RPC PROVIDER",
};
```

[您可以在helius.xyz](https://helius.xyz/)免费获取您的 RPC 提供程序 URL 。

functions.js 有一些实用函数，我们稍后会在我们的应用程序中使用它们。function.js：

```js
export const convertBase64 = (file) => {
  return new Promise((resolve, reject) => {
    const fileReader = new FileReader();
    fileReader.readAsDataURL(file);
    fileReader.onload = () => {
      resolve(fileReader.result);
    };
    fileReader.onerror = (error) => {
      reject(error);
    };
  });
};

export function timeStampToTimeAgo(p_timeStampNanoSeconds) {
  const milliseconds = p_timeStampNanoSeconds / 1000000;
  const durationUntilNowInMilliseconds = new Date().getTime() - milliseconds;
  const durationInMinutes = durationUntilNowInMilliseconds / 1000 / 60;
  if (durationInMinutes < 60) return Math.floor(durationInMinutes) + "m";
  const durationInHours = durationInMinutes / 60;
  if (durationInHours < 24) return Math.floor(durationInHours) + "h";
  const durationInDays = durationInHours / 24;
  return Math.floor(durationInDays) + "d";
}

```

现在在`/src/pages/`下创建一个名为 Signup 的新文件夹。在 Signup 文件夹中创建两个名为`ProfileForm.jsx`和`SignUp.jsx`的文件。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202303220008331.png)

`ProfileForm.jsx`应该是这样的：

```jsx
import React, { useState, useEffect } from "react";
import toast, { Toaster } from "react-hot-toast";
import { useNavigate } from "react-router-dom";
import { BiUpload } from "react-icons/bi";
import default_profile_pic from "../../assets/default_profile_pic.png";
import { useWallet } from "@solana/wallet-adapter-react";
import { SocialProtocol } from "@spling/social-protocol";
import { convertBase64 } from "../../utils/functions";
import { protocolOptions } from "../../utils/constants";
import banner from "../../assets/banner.jpg";
export default function ProfileForm() {
  const navigate = useNavigate();
  const [profileImage, setProfileImage] = useState(default_profile_pic);
  const [profileImageFile, setProfileImageFile] = useState(null);
  const [loading, setLoading] = useState(false);
  const [username, setUsername] = useState("");
  const [profileDescription, setProfileDescription] = useState("");
  const [isUploadingProfilePic, setIsUploadingProfilePic] = useState(false);
  const [socialProtocol, setSocialProtocol] = useState(null);
  const fileInput = React.useRef(null);
  const wallet = useWallet();
  useEffect(() => {
    async function initApp() {
      const socialProtocolVal = await new SocialProtocol(
        wallet,
        null,
        protocolOptions
      ).init();
      setSocialProtocol(socialProtocolVal);
    }
    if (wallet?.publicKey && typeof wallet !== "undefined") {
      initApp();
    }
  }, [wallet]);
  const updateProfile = async () => {
    if (!wallet || typeof wallet == "undefined")
      return toast.error("Wallet not connected");
    if (!username) return toast.error("Please enter a username");
    if (!profileImageFile) return toast.error("Please upload a profile image");
    if (!profileDescription)
      return toast.error("Please enter a profile description");

    const toastID = toast.loading(
      "Creating your profile. This may take a while..."
    );
    try {
      let bs64 = await convertBase64(profileImageFile);
      let finalObj = {
        base64: bs64,
        size: profileImageFile.size,
        type: profileImageFile.type,
      };
      const user = await socialProtocol.createUser(
        username,
        finalObj,
        profileDescription
      );
      if (user) {
        toast.dismiss(toastID);
        toast.success("Profile created successfully");
        //after 2 seconds redirect to home
        setTimeout(() => {
          navigate("/");
        }, 2000);
      } else {
        toast.dismiss(toastID);
        toast.error("Something went wrong");
      }
    } catch (err) {
      console.log(err);
      toast.dismiss(toastID);
      toast.error("Something went wrong. Please Try again..");
    }
  };
  const handleUsernameChange = (e) => {
    setUsername(e.target.value);
  };
  const handleProfilePicUpload = async () => {
    //store the file in the state
    const file = fileInput.current.files[0];
    if (!file) return toast.error("Please select a file");
    if (file.size > 1000000) return toast.error("File size too large");
    if (!file.type.includes("image"))
      return toast.error("Please upload an image file");
    setProfileImage(URL.createObjectURL(file));
    console.log(file);
    setProfileImageFile(file);
  };
  return (
    <div className='flex   mx-auto  justify-center items-start w-full md:w-2/3 mb-24'>
      <Toaster />
      <div className='flex mx-auto  w-full space-y-6 md:flex-row md:space-x-10 md:space-y-0'>
        <div className='flex mx-auto flex-col w-full md:w-3/4 secondaryBg border secondaryBorder rounded-xl p-4'>
          <div
            style={{
              backgroundImage: `url(${banner})`,
              backgroundPosition: "center",
              backgroundSize: "cover",
              backgroundRepeat: "no-repeat",
            }}
            className='rounded-lg w-full h-40 md:h-64 relative flex justify-center items-center dark:border-[#2D2D33] border-gray-100 border z-20 '>
            <div className='flex  -bottom-12 left-auto absolute items-center'>
              <div
                className='w-24 h-24 my-2 group rounded-full relative z-20 flex items-center justify-center dark:border-[#2D2D33] border-white border-2'
                id='profilePicOnSignUp'
                style={{
                  backgroundImage: `url(${profileImage})`,
                  backgroundPosition: "center",
                  backgroundSize: "cover",
                  backgroundRepeat: "no-repeat",
                }}>
                <button
                  className='bg-white/[.7]  group-hover:flex rounded-full px-2 py-2 hover:bg-white/[.9]'
                  onClick={() => {
                    fileInput.current.click();
                  }}>
                  <input
                    ref={fileInput}
                    type='file'
                    accept='image/*'
                    onChange={handleProfilePicUpload}
                    style={{ display: "none" }}
                  />
                  <BiUpload size={24} />
                </button>
              </div>
            </div>
          </div>
          <div className='flex flex-col mt-10 pt-5 w-full space-y-4 items-center'>
            <div className='w-full md:w-3/5'>
              <p className='font-semibold mb-2 primaryTextColor'>Username</p>
              <input
                type='text'
                value={username}
                onChange={handleUsernameChange}
                placeholder='GavinBelson'
                className='search rounded-full darkenBg darkenBorder border darkenHoverBg px-3 py-2 w-full outline-none focus:shadow transition delay-50 placeholder:text-gray-400 dark:placeholder:text-gray-500'
              />
            </div>
            <div className='w-full md:w-3/5'>
              <p className='font-semibold mb-2 primaryTextColor'>Description</p>
              <textarea
                placeholder='CEO OF HOOLI'
                className='search rounded-xl darkenBg darkenBorder border darkenHoverBg h-32 px-3 py-2 w-full outline-none focus:shadow transition delay-50 placeholder:text-gray-400 dark:placeholder:text-gray-500'
                value={profileDescription}
                onChange={(e) =>
                  setProfileDescription(e.target.value)
                }></textarea>
            </div>

            <div className='mx-auto'>
              <button
                onClick={() => updateProfile()}
                className={` flex items-center justify-center space-x-2 font-medium text-white px-6 py-3 leading-none rounded-full buttonBG my-2 ${
                  loading ? "cursor-not-allowed bg-opacity-50" : ""
                }`}>
                <span>Create Profile</span>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}
```

上面的代码可能看起来让人不知所措，但您不必担心，因为其中大部分代码都可以很好地处理异常和错误。只知道我们从用户那里获取了三样东西：

- 用户名
- 配置文件说明
- 头像照片

并使用这三个实体，我们在 Spling 上创建用户配置文件。`useEffect()`函数在用户使用 Solana 钱包登录并在`constants.js` 中定义的 `wallet`和`protocolOptions`初始化SDK。函数`updateProfile()`是一切发生的地方。我们首先需要使用我们在`functions.js`文件中定义的`convertBase64()`函数将选定的用户图像转换为`base64`格式。在获得一切之后，我们只需调用spling 对象的`createUser()`函数。您还可以传递`Metadata`，其中包括更多关于配置文件的信息。

现在让我们在`SignUp.jsx`导入`ProfileForm.jsx`组件并在`App.jsx`创建一个新的路由来测试一下。

`SignUp.jsx`如下：

```jsx
import React from "react";
import Navbar from "../../components/Navbar";
import ProfileForm from "./ProfileForm";
export default function SignUp() {
  return (
    <div>
      <Navbar shouldShowWallet={true} />
      <div className='flex justify-center mx-auto'>
        <div>
          <div className='relative text-3xl md:py-10 text-gray-800 text-center font-extrabold  sm:text-5xl lg:text-4xl  rounded-full sm:w-[70%] flex justify-center mx-auto px-2 '>
            <span className='brandGradientBg blur-2xl filter opacity-10 w-full h-full absolute inset-0 rounded-full leading-snug'></span>
            <span className='md:px-5 leading-snug'>
              Create your profile to get started.
            </span>
          </div>
          <div className='flex justify-center mx-auto px-2 '>
            <div className='bg-yellow-200 px-4 py-1 border-l-2 mb-4 mt-3 border-yellow-500'>
              Make sure you have enough $SOL and $SHDW tokens in your wallet to
              cover the gas fee.
              <br></br>
              You can buy some $SHDW tokens from{" "}
              <a
                href='https://jup.ag/swap/SOL-SHDW'
                target={"_blank"}
                className='text-blue-400 underline'>
                here
              </a>
            </div>
          </div>
        </div>
      </div>
      <div className='mb-10'>
        <ProfileForm />
      </div>
    </div>
  );
}
```

创建`/sign-up`路由后，`App.jsx`看起来的样子：

```jsx
import React from "react";
import "./App.css";
import "./styles/styles.css";
import { BrowserRouter, Route, Routes } from "react-router-dom";
import WalletContextProvider from "./components/walletContextProvider";
import LandingPage from "./pages/Landing/LandingPage";
import SignUp from "./pages/Signup/SignUp";
function App() {
  return (
    <WalletContextProvider>
      <BrowserRouter>
        <Routes>
          <Route path='/' element={<LandingPage />} />
          <Route path='/sign-up' element={<SignUp />} />
        </Routes>
      </BrowserRouter>
    </WalletContextProvider>
  );
}

export default App;
```

编译所有内容，运行并访问http://localhost:3000/sign-up以查看这个漂亮的注册页面

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202303220009890.png)

让我们测试注册页面，看看我们的个人资料是否已创建。填写报名表并点击“创建个人资料”按钮后，事实证明一切都按预期进行！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202303220009510.png)

恭喜你🥳🥳🥳。您刚刚在您自己的 web3 社交 Dapp 中完成了您的第一个 Spling 交易。

### 2.制作`/create-group`页面以创建我们的应用程序组

如前所述，应用程序需要一个群组，以便用户可以发帖。让我们创建一个简单的 UI，以便开发人员可以创建和删除组以进行快速测试。

请注意，用户与组创建无关。为用户创建组是开发人员的工作。

在`/pages/Group/`目录中创建`CreateGroup.jsx`文件

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202303220010398.png)

这是`CreateGroup.jsx`文件的样子：

```jsx
import React, { useState, useEffect } from "react";
import toast, { Toaster } from "react-hot-toast";
import { useWallet } from "@solana/wallet-adapter-react";
import { SocialProtocol } from "@spling/social-protocol";
import Navbar from "../../components/Navbar";
import { protocolOptions } from "../../utils/constants";
function CreateGroup() {
  const [socialProtocol, setSocialProtocol] = useState(null);
  const [group, setGroup] = useState(null);
  const wallet = useWallet();
  useEffect(() => {
    async function initApp() {
      const socialProtocolVal = await new SocialProtocol(
        wallet,
        null,
        protocolOptions
      ).init();
      setSocialProtocol(socialProtocolVal);
    }
    if (wallet?.publicKey && typeof wallet !== "undefined") {
      initApp();
    }
  }, [wallet]);

  const createGroup = async () => {
    if (!wallet || typeof wallet == "undefined")
      return toast.error("Wallet not connected");
    if (!socialProtocol) return toast.error("Wallet not connected");
    const loadingToast = toast.loading("Creating Group");
    const group = await socialProtocol.createGroup(
      "Test Group",
      "We are just testing out Spling",
      null
    );
    toast.dismiss(loadingToast);
    const groupID = group.groupId;
    setGroup(groupID);
    toast.success("Group Created");
    console.log(group);
  };

  const deleteGroup = async () => {
    if (!wallet || typeof wallet == "undefined")
      return toast.error("Wallet not connected");
    if (!socialProtocol) return toast.error("Wallet not connected");
    const loadingToast = toast.loading("Deleting Group");
    const group = await socialProtocol.deleteGroup();
    toast.dismiss(loadingToast);
    toast.success("Group Deleted");
    console.log(group);
  };

  return (
    <div>
      <Navbar shouldShowWallet={true} />
      <div className='flex   mx-auto  justify-center items-start w-full md:w-2/3 mb-24'>
        <Toaster />
        <div className='flex mx-auto  w-full space-y-6 md:flex-row md:space-x-10 md:space-y-0 my-28'>
          <div className='mx-auto flex justify-center items-center flex-col'>
            <h1 className='text-xl text-red-400'>
              This will create a test group
            </h1>

            <h1 className='text-xl text-green-400'>
              {group ? `Group ID: ${group}` : null}
            </h1>
            <button
              onClick={() => createGroup()}
              className=' flex items-center justify-center space-x-2 font-medium text-white px-6 py-3 leading-none rounded-full buttonBG my-2'>
              <span>Create Group</span>
            </button>
            <button
              onClick={() => deleteGroup()}
              className='flex items-center justify-center space-x-2 font-medium text-white px-6 py-3 leading-none rounded-full buttonBG my-2'>
              <span>Delete Group</span>
            </button>
          </div>
        </div>
      </div>
    </div>
  );
}

export default CreateGroup;
```

请注意，除了创建组的功能外，代码还具有删除组的功能，以便于测试。SDK 初始化和登录过程与之前解释的相同。Spling SDK 的`createGroup()`函数有 4 个参数：

- 团体名称
- 团体说明
- 团体的个人资料图片（在我们的例子中我们将其设置为空）
- 元数据（如果需要，用于额外数据）

我们小组的名称是`Test Group`，它的描述是`We are just testing out Spling`。`deleteGroup()`函数会自动检测并删除与您的钱包关联的组，因为一个钱包只能创建一个组。

让我们在`App.jsx`中添加`/admin`路由并测试一下。

`App.jsx`然后看起来像：

```jsx
import React from "react";
import "./App.css";
import "./styles/styles.css";
import { BrowserRouter, Route, Routes } from "react-router-dom";
import WalletContextProvider from "./components/walletContextProvider";
import LandingPage from "./pages/Landing/LandingPage";
import SignUp from "./pages/Signup/SignUp";
function App() {
  return (
    <WalletContextProvider>
      <BrowserRouter>
        <Routes>
          <Route path='/' element={<LandingPage />} />
          <Route path='/sign-up' element={<SignUp />} />
          <Route path='/admin' element={<SignUp />} />
        </Routes>
      </BrowserRouter>
    </WalletContextProvider>
  );
}

export default App;
```

我们现在可以在http://localhost:3000/admin看到管理面板，它看起来像这样：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202303220010497.png)

让我们点击创建组，看看会发生什么。

如您所见，我们已经成功地在 Spling 上创建了我们的组，它的 ID 是 31。记下这个 ID 号，因为稍后在制作和获取帖子时会用到它。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202303220010490.png)

如果您想玩，可以删除该组并尝试重新创建。

### 3. 制作`/create`页面用来在我们的应用程序组中发帖

`/create`页面是用户可以在我们的平台上发布图片的地方。在`/src/pages/Create/`中创建一个名为`Create.jsx`的新文件

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202303220011339.png)

`Create.jsx`文件如下所示：

```jsx
import React, { useEffect, useRef, useState, useContext } from "react";
import { BiImageAdd, BiRocket } from "react-icons/bi";
import { BsTrash } from "react-icons/bs";
import toast, { Toaster } from "react-hot-toast";

import { useWallet } from "@solana/wallet-adapter-react";
import { SocialProtocol } from "@spling/social-protocol";
import { convertBase64 } from "../../utils/functions";
import { Link } from "react-router-dom";
import Navbar from "../../components/Navbar";
import { protocolOptions } from "../../utils/constants";
export default function Create() {
  const [socialProtocol, setSocialProtocol] = useState(null);
  const { publicKey } = useWallet();
  const [postBody, setPostBody] = useState("");
  const fileInput = React.useRef(null);
  const [imageURL, setImageURL] = useState("");
  const [imageFile, setImageFile] = useState(null);
  const [postCreationDone, setPostCreationDone] = useState(false);
  const wallet = useWallet();
  useEffect(() => {
    async function initApp() {
      const socialProtocolVal = await new SocialProtocol(
        wallet,
        null,
        protocolOptions
      ).init();
      setSocialProtocol(socialProtocolVal);
    }
    if (wallet?.publicKey && typeof wallet !== "undefined") initApp();
  }, [wallet]);

  const handleImageUpload = async (file) => {
    try {
      if (!wallet?.publicKey) {
        toast.error("Please connect wallet first");
        fileInput.current.value = null;
        return;
      }
      const file = fileInput.current.files[0];
      if (file.size > 10000000) return toast.error("File size too large");
      if (!file.type.includes("image"))
        return toast.error("Please upload an image file");
      setImageURL(URL.createObjectURL(file));
      setImageFile(file);
    } catch (e) {
      toast.error("Error uploading Image");
      console.log(e);
    }
  };
  const submitPost = async () => {
    if (!wallet?.publicKey || typeof wallet == "undefined")
      return toast.error("Wallet not connected");
    if (!imageFile) return toast.error("Please upload an image file");
    if (!postBody) return toast.error("Please enter a Image Details.");

    const toastID = toast.loading(
      "Creating Post...Please approve transactions"
    );
    try {
      let bs64 = await convertBase64(imageFile);
      let finalObj = {
        base64: bs64,
        size: imageFile.size,
        type: imageFile.type,
      };

      const post = await socialProtocol.createPost(31, postBody, "", [
        finalObj,
      ]);
      if (post) {
        toast.dismiss(toastID);
        toast.success("Post created successfully");
        setPostCreationDone(true);
        setImageFile(null);
        setImageURL("");
        setPostBody("");

        return;
      } else {
        toast.dismiss(toastID);
        toast.error("Error creating post");
        return;
      }
    } catch (e) {
      if (e.message.includes("Account does not exist")) {
        toast.dismiss(toastID);
        toast.success("Post created successfully");
        setImageFile(null);
        setImageURL("");
        setPostBody("");
        return;
      }
      toast.dismiss(toastID);
      toast.error("Error creating post");
      console.log(e);
      return;
    }
  };

  return (
    <div>
      <Navbar shouldShowWallet={true} />
      <Toaster />
      <div className='flex justify-center mx-auto'>
        <div>
          <div className='relative text-3xl md:py-10 text-gray-800 text-center font-extrabold  sm:text-5xl lg:text-4xl  rounded-full sm:w-[70%] flex justify-center mx-auto px-2 '>
            <span className='brandGradientBg blur-2xl filter opacity-10 w-full h-full absolute inset-0 rounded-full leading-snug'></span>
            <span className='md:px-5 leading-snug mt-3'>
              <span className='text-transparent bg-clip-text brandGradientBg'>
                {" "}
                Create
              </span>{" "}
              a high-performing post to get your photos across!
            </span>
          </div>
          <div className='flex justify-center mx-auto px-2 '>
            <div className='bg-yellow-200 px-4 py-1 border-l-2 mb-4 mt-3 border-yellow-500'>
              Make sure you have enough $SOL and $SHDW tokens in your wallet to
              cover the gas fee.
              <br></br>
              You can buy some $SHDW tokens from{" "}
              <a
                href='https://jup.ag/swap/SOL-SHDW'
                target={"_blank"}
                className='text-blue-400 underline'>
                here
              </a>
              . We are working to make everything gasless
            </div>
          </div>

          {postCreationDone && (
            <div className='flex justify-center mx-auto px-2 my-2'>
              <div className='bg-green-500 px-4 py-1 border-l-2 mb-4 mt-3 border-green-700 text-white'>
                View your Posts
                <Link
                  to={`/`}
                  className='text-blue-100 underline'>
                  {" "}
                  here
                </Link>
              </div>
            </div>
          )}
        </div>
      </div>
      <div className='sm:w-3/4 md:w-3/5 lg:w-1/2 mx-auto my-3 '>
        <textarea
          className='focus:ring-0 h-28 focus:outline-none outline-none darkenBg darkenHoverBg border dark:border-[#2D2D33] hover:dark:border-[#43434d] border-gray-200 hover:border-gray-200 resize-none w-full rounded-lg heading px-4 py-2'
          placeholder='About the photo...'
          value={postBody}
          onChange={(e) => setPostBody(e.target.value)}
        />

        <div className='flex items-center space-x-1'>
          <button
            className={`mx-1 flex items-center justify-center space-x-2 font-medium text-gray-800 px-4 py-3 leading-none rounded-full bg-gray-200 hover:bg-gray-300 my-2`}
            onClick={() => {
              fileInput.current.click();
            }}>
            <input
              ref={fileInput}
              type='file'
              accept='image/*'
              onChange={handleImageUpload}
              style={{ display: "none" }}
            />
            <BiImageAdd size={21} className='text-gray-900' />
            <p className='text-gray-900'>Upload Image</p>
          </button>
          <button
            className={`mx-1 flex items-center justify-center space-x-2 font-medium text-white px-8 py-3 leading-none rounded-full buttonBG my-2`}
            onClick={() => {
              submitPost();
            }}>
            <BiRocket size={21} className='text-gray-100' />
            <p className='text-gray-100'>Post</p>
          </button>
        </div>
        <div className={`${!imageURL ? "hidden" : "flex"}`}>
          <img src={imageURL} className='w-3/4 sm:w-3/5  mx-auto rounded-md' />
        </div>
        <div
          className={`${
            !imageURL
              ? " hw-3/4 sm:w-3/5 h-[60vh] bg-gray-300 mx-auto rouned-md flex justify-center items-center"
              : "hidden"
          }`}>
          <p className='text-gray-600'>
            Your Image will Appear Here after Upload
          </p>
        </div>
      </div>
    </div>
  );
}
```

再次重申，不要因为看到上面的代码而不知所措。其中大部分用于正确的错误处理和用户响应。让我们分解一下。

我们从用户那里获取两件事来发帖：

- 帖子描述
- 图像

用户登录钱包后，我们已经在`useEffect()`中初始化了 Spling SDK，就像我们在前面的页面中所做的那样。`Create.jsx`文件中最重要的函数是`submitPost()`，调用SplingSDK的`createPost()`函数。我们为`createPost()`函数提供了三个参数，它们是：

- `groupID`，在我们的例子中是 31
- 帖子文本
- 帖子图片列表

请注意，我们首先必须使用我们在`functions.js`中定义的函数将帖子图像转换为 base64 格式

在`App.jsx`中添加`/create`页面路由，然后将如下所示：

```jsx
import React from "react";
import "./App.css";
import "./styles/styles.css";
import { BrowserRouter, Route, Routes } from "react-router-dom";
import WalletContextProvider from "./components/walletContextProvider";
import LandingPage from "./pages/Landing/LandingPage";
import SignUp from "./pages/Signup/SignUp";
import CreateGroup from "./pages/Group/CreateGroup";
import Create from "./pages/Create/Create";
function App() {
  return (
    <WalletContextProvider>
      <BrowserRouter>
        <Routes>
          <Route path='/' element={<LandingPage />} />
          <Route path='/sign-up' element={<SignUp />} />
          <Route path='/admin' element={<CreateGroup />} />
          <Route path='/create' element={<Create />} />
        </Routes>
      </BrowserRouter>
    </WalletContextProvider>
  );
}

export default App;
```

运行应用程序并转到http://localhost:3000/create，您将看到这个帖子创建页面：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202303220011414.png)

是时候在我们自己的 web3 社交应用程序中发布我们的第一篇文章了🚀

在测试中，我们的帖子似乎已经发布到区块链上了！

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202303220011664.png)

现在我们已经成功地创建了我们的帖子，是时候用像这样的漂亮的 Masonry 布局在登陆页面上显示我们应用程序中发布的所有图像👀

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202303220011006.png)

### 4.查看应用群中的所有帖子

对于本教程，我们将简单地以漂亮的 Masonry 布局在登录页面上显示我们应用程序中发布的所有内容，就像 Pinterest 所做的那样。

首先安装`react-responsive-masonry`库以使Masonry视图更容易。。运行npm安装`npm install react-responsive-masonry`

接下来让我们创建一个 PostCard 组件，它将负责在Masonry布局中显示每个帖子。在`/src/pages/Landing`中创建`PostCard.jsx`

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202303220012867.png)

`PostCard.jsx`应该是这样的：

```jsx
import React from "react";
import { Link } from "react-router-dom";
import { timeStampToTimeAgo } from "../../utils/functions";
import { BsHeart, BsHeartFill } from "react-icons/bs";
import defaultPic from "../../assets/default_profile_pic.png";
export default function PostCard({ postValue }) {
  const post = postValue;
  const timeAgo = timeStampToTimeAgo(post.timestamp * 1e9);
  const posterPublicKey = postValue.user.publicKey.toString();
  const totalLikes = postValue.likes.length;
  const isLiked = false;
  return (
    <div className='hover:scale-105 transition-transform duration-300'>
      <div className='flex flex-col w-full shadow-sm border-md  rounded-xl my-1 bg-[#f7f7fa]'>
        <Link to={`/post/${post.postId}`} className='flex flex-col w-full'>
          <img
            src={`${post.media[0].file}`}
            className='w-full rounded-t-xl'
            alt='Post Image'
          />
        </Link>
        <div className='flex w-full py-4 px-2 space-x-1'>
          <Link
            to={`/u/${posterPublicKey}`}
            className='cursor-pointer relative flex items-center justify-center space-x-1'>
            <img
              src={`${post.user.avatar}`}
              onError={(e) => {
                e.target.onerror = null;
                e.target.src = defaultPic;
              }}
              className='w-6 h-6 darkenBg darkenBorder rounded-full'
              alt={`${post.user.nickname}`}
            />
          </Link>
          <div className='flex items-center justify-between w-full'>
            <div className='flex items-center space-x-1'>
              <Link
                to={`/u/${posterPublicKey}`}
                className=' text-base hover:underline'>
                {post.user.nickname}
              </Link>
              <span className='middot' />
              <p className='text-xs text-gray-700'>{timeAgo}</p>
            </div>
            <div className='justify-end flex space-x-1 items-center'>
              <button className='flex items-center justify-center px-2 py-2 rounded-full  border bg-gray-100 border-gray-300 hover:bg-red-200'>
                {isLiked ? (
                  <BsHeartFill size={16} color='red' />
                ) : (
                  <BsHeart size={16} color='red' />
                )}
              </button>

              <p>{totalLikes}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}
```

现在我们需要从我们的应用组中获取所有帖子并将它们显示在登录页面上。

为此，我们将使用SplingSDK中带有两个参数的`getAllPosts()` 函数：

- GroupID
- 要获取的帖子数

制作我们的应用程序也很好，这样当用户登录到我们的应用程序时，它会检查并显示用户的 Spling 配置文件（如果存在）。否则应用程序应该将用户路由到`/sign-up`页面，用户可以创建他的 Spling 帐户

更新`LandingPage.jsx`为：

```jsx
import React, { useEffect, useContext, useState } from "react";
import { SocialProtocol } from "@spling/social-protocol";

import { Keypair } from "@solana/web3.js";
import PostCard from "./PostCard";
import Masonry, { ResponsiveMasonry } from "react-responsive-masonry";
import toast, { Toaster } from "react-hot-toast";
import { useWallet } from "@solana/wallet-adapter-react";
import Navbar from "../../components/Navbar";
import { protocolOptions } from "../../utils/constants";
import { PublicKey } from "@solana/web3.js";
import { useNavigate, Link } from "react-router-dom";
function LandingPage() {
  const [response, setResponse] = useState(null);
  const [isLoading, setIsLoading] = useState(true);
  const [socialProtocolVal, setSocialProtocolVal] = useState(null);
  const [profileInfo, setProfileInfo] = useState(null);
  const wallet = useWallet();
  const { publicKey } = useWallet();
  const navigate = useNavigate();
  useEffect(() => {
    async function initApp() {
      const socialProtocol = await new SocialProtocol(
        Keypair.generate(),
        null,
        protocolOptions
      ).init();
      setSocialProtocolVal(socialProtocol);
      console.log(socialProtocol);
      try {
        const posts = await socialProtocol.getAllPosts(31, 30);
        const finalResult = [];
        //loop through userPosts and add that post to finalResult only when media's array length is greater than 0
        for (let i = 0; i < posts.length; i++) {
          if (posts[i].media.length > 0) {
            finalResult.push(posts[i]);
          }
        }
        console.log(finalResult);
        setResponse(finalResult);
        setIsLoading(false);
      } catch (err) {
        toast.error(
          `Something went wrong. Please reload the page. ${err.message}`
        );
      }
    }
    if (!response) {
      initApp();
    }
  }, []);

  useEffect(() => {
    async function initApp() {
      const socialProtocolValue = await new SocialProtocol(
        wallet,
        null,
        protocolOptions
      ).init();
      console.log(socialProtocolValue);

      setSocialProtocolVal(socialProtocolVal);
    }
    if (wallet?.publicKey && typeof wallet !== "undefined") {
      initApp();
    }
  }, [wallet]);

  useEffect(() => {
    async function checkUser() {
      const publicKeyObj = new PublicKey(publicKey);
      const userInfo = await socialProtocolVal.getUserByPublicKey(publicKeyObj);
      if (userInfo) {
        setProfileInfo(userInfo);
      } else {
        navigate("/sign-up");
      }
    }
    if (publicKey && socialProtocolVal && !profileInfo) {
      checkUser();
    }
  }, [publicKey]);

  return (
    <div className='w-full'>
      <Navbar shouldShowWallet={true} />
      <Toaster />
      <div className='flex flex-col items-center justify-center'>
        <h1 className='text-4xl font-bold text-gray-800 my-3 '>
          Welcome to My Web3 Social App
        </h1>
      </div>

      <div className='flex flex-col items-center justify-center'>
        {profileInfo && (
          <div className='flex flex-col items-center justify-center'>
            <div className='flex items-center justify-cente space-x-1'>
              <p>Logged In As:</p>
              <div className='ml-2'>
                <div className='text-gray-800 text-sm font-bold'>
                  {profileInfo.nickname}
                </div>
              </div>
              <div className='flex items-center justify-center'>
                <img
                  src={profileInfo.avatar}
                  className='w-10 h-10 rounded-full'
                  alt='profile pic'
                />
              </div>
            </div>

            <Link
              to='/create'
              className='bg-purple-500 text-white px-8 py-2 rounded-md hover:bg-purple-600'>
              Create
            </Link>
          </div>
        )}
      </div>
      <h1 className='text-2xl font-bold text-gray-800 my-10 text-center '>
        Trending Posts
      </h1>
      {isLoading && (
        <div className='flex justify-center items-center'>
          <div className=' ease-linear rounded-full border-4 border-t-4 border-t-blue-500 animate-spin h-12 w-12 mb-4'></div>
        </div>
      )}
      {!isLoading && (
        <div className=''>
          <ResponsiveMasonry
            columnsCountBreakPoints={{ 350: 2, 750: 3, 900: 5 }}>
            <Masonry gutter='10px'>
              {response.map((post, index) => (
                <div className='w-full px-1 mx-auto' key={index}>
                  <PostCard postValue={post} />
                </div>
              ))}
            </Masonry>
          </ResponsiveMasonry>
        </div>
      )}
    </div>
  );
}

export default LandingPage;
```

上面的代码为我们做了一切。在运行它时，您现在将看到在应用程序中以masonry布局发布的所有帖子

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202303220012817.png)

恭喜！您刚刚在 Solana 区块链上制作了您的第一个功能齐全的 web3 社交应用程序🥳

您可以添加其他几个功能和页面，如评论、帖子页面、用户页面、喜欢/不喜欢等。您可以[在此处](https://github.com/ItsAditya-xyz/web3-social-spling)获取教程代码的完整存储库。

> 原文链接：https://itsaditya.hashnode.dev/building-web3-social-app-on-solana

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)