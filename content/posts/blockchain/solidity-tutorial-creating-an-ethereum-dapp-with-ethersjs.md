---
title: Solidity教程：使用 Ethers.js 创建以太坊 dApp
description: null
author: 李留白
weight: 0
date: 2022-09-14T13:45:53.205Z
lastmod: 2022-09-14T13:47:45.561Z
tags: []
categories:
  - 区块链
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914210500.png
---

在本教程中，我们将学习如何使用 Ethers.js 库构建一个带有与 Solidity 智能合约交互的前端的基本以太坊 dApp。

本教程是Solidity 教程系列的延续。如果您不熟悉编写 Solidity 智能合约，请在继续阅读本文之前查看[如何构建您的第一个智能合约。](https://mp.weixin.qq.com/s/hRxfMoPy0oAMFGQRtLA3_A)

## 介绍

智能合约是仅在满足特定条件时才在区块链上部署和执行的功能。我们可以通过多种方式与我们在区块链上部署的智能合约进行交互。

一种方法是使用 Ethers.js 库将我们的 dApp 前端连接到作为后端的智能合约。

在本文中，我们将编写和部署一个接受宠物姓名、主人姓名和年龄的智能合约。我们还将使用前端项目中的 getter 函数从智能合约中检索宠物的详细信息。

## 演示

这是我们将在本教程结束时构建的宠物 dApp 的演示视频：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/ha0DIUdbX.gif)

## 先决条件

在继续本教程之前，您应该具备：

- 对 HTML 和 CSS 有基本的了解，
- 了解 JavaScript 中的函数和 DOM，
- [您可以在此处](https://web3.hashnode.com/solidity-tutorial-how-to-build-your-first-smart-contract)找到对 Solidity 的基本了解。

本教程中使用的其他技术包括：Ethers.js 库、Remix IDE 和 Metamask。

## 构建 dApp

我们的项目分为两部分：**后端**，我们将在 Goerli 测试网上编写和部署我们的 Solidity 智能合约；**前端**，我们将在这里使用 HTML 和 CSS 构建我们的 dApp 界面，并使用带有 Ethers.js 的 JavaScript 与我们部署的智能合约进行交互。

## 构建后端

在这一部分中，我们将使用 Remix IDE 和 Metamask 在 Goerli 测试网上编写和部署我们的 Solidity 智能合约。

### 第 1 步 - Solidity IDE（Remix）

Remix IDE 是一个基于 Web 的 Solidity 编译器。它允许我们直接从浏览器编写、测试和部署我们的 Solidity 智能合约，无需任何配置或设置。

我们将使用 Remix IDE 来编写和部署我们的宠物智能合约。

单击[此处](https://remix.ethereum.org/)在浏览器上启动 Remix IDE：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914211447.png)

### 第 2 步 - 编写智能合约

找到Remix 的“文件资源管理器”下的`contracts`文件夹并创建一个名为`Pet_Contract.sol`的新文件：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914211744.png)

在`Pet_Contract.sol`文件中复制并粘贴下面的 Solidity 智能合约：

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^ 0.8.13;

// 1. Creating a new pet contract
contract Pet_Contract{

    // 2. Declaring our smart contract state variables
    string public petName;
    string public petOwner;
    string public petAge;

    // 3. Creating a set pet function
   function setPet( 
       string memory newPetName, 
       string memory newPetOwner, 
       string memory newPetAge
    ) public {
        petName = newPetName;
        petOwner = newPetOwner;
        petAge = newPetAge;
    }

    // 4. Creating a fetch pet function
    function getPet() public view returns (
        string memory, 
        string memory, 
        string memory
    ){
        return (petAge, petName, petOwner);
    }
}
```

上面的智能合约是我们在[这里](https://web3.hashnode.com/solidity-tutorial-how-to-build-your-first-smart-contract)编写的第一个智能合约的修改。我们正在创建一个`setPet`函数，该函数接受三个参数：`petName`、`petOwner`和`petAge`，并在调用`setPet`函数时将它们存储在内存中。

`getPet`函数将返回我们智能合约内存中的`petAge`、`petName`和`petOwner`状态的当前值。

可以在[此处](https://web3.hashnode.com/solidity-tutorial-how-to-build-your-first-smart-contract)找到智能合约的完整细分说明。

### 第 3 步 - 编译智能合约

请按照以下步骤在 Remix IDE 上编译您的 Solidity 智能合约：

- 确保将源文件保存为`ctrl + s`.
- 然后，导航到“Solidity Compiler”部分：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212322.png)

- 选择与我们的智能合约中指定的编译器版本相匹配的编译器版本（如果您不这样做，绿色检查将变为红色）：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212502.png)

- 接下来，确保保存文件并单击“编译”按钮：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212519.png)

### 第 4 步 - 获取 Goerli Testnet Token

现在，我们要在 Goerli 测试网络上部署我们的宠物智能合约，我们需要一些假 ETH 来支付 gas 费。

请按照以下步骤为您的 Metamask 钱包获取免费的 Goerli Testnet 代币：

- 如果您还没有安装[Metamask 。](https://metamask.io/)
- 接下来，导航到[faucets.chain.link](https://faucets.chain.link/)。
- 连接您的 Metamask 钱包：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212532.png)

- 确保在您的 Metamask 上切换到 Goerli 测试网络：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212544.png)

- 解决验证码，然后点击“发送 0.1 测试 ETH”按钮：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212559.png)

- 等待交易确认：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212611.png)

- 检查您的 Metamask 是否有新余额：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212622.png)

[你可以在这里](https://web3.hashnode.com/link-to-5-places-to-get-testnet-tokens)查看其他地方以获得免费的 ETH 用于 dApp 开发。

### 第 5 步 - 部署智能合约

现在我们已经成功编译了我们的智能合约*（参见步骤 3）*，并且我们的 Metamask 钱包中有一些 Goerli Testnet 代币，我们将在 Goerli 测试网络上部署我们的智能合约。

- 导航到“部署和运行事务”部分：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212654.png)

- 选择“Injected Web3”作为环境：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212706.png)

- 点击“部署”按钮，在 Goerli 测试网上部署我们的智能合约：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212717.png)

- 系统将提示您确认合约部署 gas 费用：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212728.png)

- 如果部署成功，您将在“已部署的合约”部分下看到我们的智能合约名称，如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212742.png)

### 第 6 步 - 测试智能合约

在这一步中，我们将在 Remix IDE 上测试我们的智能合约并与之交互。

- 点击`setPet`函数中的下拉箭头，展开输入框：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212831.png)

- 在输入框中填写您宠物的详细信息，然后单击“交易”按钮：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212846.png)

- 确认交易gas费用：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212858.png)

- 等待交易确认：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212908.png)

- 点击`getPet`函数：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212926.png)

- 如上所示，该`getPet`函数应该返回一个包含宠物姓名、主人姓名和年龄的数组。
- 接下来，单击所有“getter”按钮。每个 getter 函数都应该从它们各自的状态变量中返回值，如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914212941.png)

我们的 Solidity 智能合约按预期运行。您可以继续使用不同的宠物名称、所有者姓名和年龄来测试智能合约。

## 构建前端

在这一部分中，我们将构建 dApp 的前端，它使用 Ethers.js 与我们的智能合约进行交互。

### 什么是 Ethers.js

[Ethers.js](https://docs.ethers.io/v5/getting-started/)是[web3.js](https://web3js.readthedocs.io/en/v1.7.3/getting-started.html)库的替代品。它是一个 JavaScript 库，允许开发人员与以太坊区块链进行交互。

### 第 1 步 - 安装 Ethers.js

在我们的项目中使用 Ethers.js 的最快方法是通过[Ethers.js CDN](https://docs.ethers.io/v5/getting-started/#importing)。

- `index.html`使用以下内容创建一个新文件：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <!-- Importing css styles -->
    <link rel="stylesheet" href="./index.css" />
    <title>Pet dApp</title>
  </head>
  <body>



    <!-- Importing Ethers.js script -->
    <script
      src="https://cdn.ethers.io/lib/ethers-5.2.umd.min.js"
      type="application/javascript"
    ></script>

    <!-- Importing javascript file -->
    <script src="./index.js"></script>
  </body>
</html>

```

在上面的代码中，我们导入了 Ethers.js CDN 脚本、我们的 CSS 以及我们稍后将创建的 JavaScript 文件。

> 您的 JavaScript 文件应在 Ethers.js CDN 脚本**之后导入。**

### 第 2 步 - 构建*宠物表格*

在这一步中，我们将创建一个表单，该表单将接受宠物的姓名、主人的姓名和年龄，以及一个“提交”按钮，以将详细信息发送到我们部署的智能合约。

在`index.html`文件的 body 标记中添加以下代码行：

```html
<section class="pet-form-section">
    <section class="section-header">
    <h1>Pet Form</h1>
    </section>
    <!-- Form -->
    <form>
    <label for="pet-name">Pet Name</label>
    <input type="text" id="pet-name" />
    <label for="pet-owner">Pet Owner</label>
    <input type="text" id="pet-owner" />
    <label for="pet-age">Pet Age</label>
    <input type="number" id="pet-age" />
    <input type="button" value="Submit" id="set-new-pet" />
    </form>
</section>
```

接下来，创建一个新`index.css`文件，并添加以下代码：

```css
/* Reset browser styles */
* {
  margin: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial;
  line-height: 1.5;
  color: #333333;
  display: flex;
  justify-content: center;
  flex-direction: column;
  min-height: 100vh;
  max-width: 500px;
  margin:  0 auto;
  padding: 0 20px;
}

/* Header */
.section-header{
    font-size: 1.5rem;
    font-weight: bold;
    margin-bottom: 1rem;
    background-color: #333333;
    color: #ffffff;
    padding: 0.5rem 1rem;
    border-radius: 0.25rem;
    text-align: center;
}

/* Pet Form Section */
form {
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
}

label {
  font-size: 14px;
  display: block;
  margin-bottom: 5px;
}

input {
  width: 100%;
  border: 1px solid #cccccc;
  border-radius: 5px;
  padding: 10px;
  margin-bottom: 10px;
}

input[type=button]{
    border: 1px solid #333333;
    background-color: #333333;
    color: #fff;
    cursor: pointer;
}
```

我们的宠物表单在浏览器中应该是这样的：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914213243.png)

### 第 3 步 - 构建*宠物详细信息*部分

在这一步中，我们将创建*宠物详细信息*部分，该部分将显示存储在我们的智能合约内存中的当前宠物信息。

在*宠物表格*部分之后，在您的`index.html`文件中添加以下代码：

```html
 <!-- Pet details section -->
<section class="pet-detail-section">
      <section class="section-header">
        <h1>Pet Details</h1>
      </section>
      <section class="pet-details">
        <h3 class="pet-detail-heading">
            Pet Name:
            <span class="pet-data pet-detail-name"></span>
        </h3>
        <h3 class="pet-detail-heading">
            Pet Owner:
            <span class="pet-data pet-detail-owner"></span>
        </h3>
        <h3 class="pet-detail-heading">
            Pet Age:
            <span class="pet-data pet-detail-age"></span>
        </h3>
      </section>
      <section class="section-footer">
        <button class="show-pet-form-btn">Set New Pet</button>
        <button class="refresh-pet-details-btn">Refresh</button>
      </section>
</section>

```

接下来，使用以下代码更新您的`index.css`文件：

```css

/* Pet details section */
.pet-detail-section{
  display: none; /* hidden by default */
}

.pet-details {
  margin-bottom: 10px;
  padding: 20px;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
}

.pet-detail-heading {
  font-size: 16px;
  margin-bottom: 10px;
  font-weight: 500;
  letter-spacing: 0.5px;
}

.section-footer{
  display: flex;
  gap: 12px;
}

.show-pet-form-btn, .refresh-pet-details-btn{
    width: 50%;
    padding: 10px;
    border: none;
    border-radius: 5px;
    color: #fff;
    cursor: pointer;
}

.show-pet-form-btn{
  background-color: #4CAF50;
}

.refresh-pet-details-btn{
  background-color: #00a8ff;
}

```

在上面的代码中，*宠物详细信息*部分将默认为`hidden`；只有在我们的智能合约上设置了宠物详细信息时，它才会可见。我们将在下一步中实现逻辑。

我们的*宠物详细信息*部分将如下所示：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914213404.png)

- “设置新宠物”按钮将使用户返回到我们之前构建的*宠物表单部分。*
- Goerli 交易确认可能需要几秒钟的时间。我们可以通过点击“刷新”按钮来刷新宠物的详细信息。

### 第 4 步 - 创建签名者逻辑

在这一步中，我们将提示用户将他们的 Metamask 钱包地址与我们的 dApp 连接，然后他们才能与我们的智能合约进行交互。然后，用户将被识别为签名者，使用他们的钱包地址。

- 使用以下内容创建一个`index.js`新文件：

```js
// 1. Declare global variable to store the web3 instance
let PetContract;

// 2. Set contract address and ABI
const Pet_Contract_Address = "";
const Pet_Contract_ABI = [];

/* 3. Prompt user to sign in to MetaMask */
const provider = new ethers.providers.Web3Provider(window.ethereum, "goerli");
provider.send("eth_requestAccounts", []).then(() => {
  provider.listAccounts().then((accounts) => {
    const signer = provider.getSigner(accounts[0]);

    /* 3.1 Create instance of pet smart contract */
    PetContract = new ethers.Contract(
      Pet_Contract_Address,
      Pet_Contract_ABI,
      signer
    );
  });
});

```

在上面的代码中：

#### 1. 我们将`PetContract`变量设为全局变量，因为我们将在其他函数中重用它。

#### 2. 在`Pet_Contract_Address`和`Pet_Contract_ABI`中提供你的智能合约**地址**和**ABI**。

- 您可以从 Remix “Solidity Compiler”部分获取您的智能合约 ABI 代码：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914213555.png)

- 导航到 Remix “部署和运行交易”部分以复制您的智能合约地址：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914213610.png)

#### 3. 我们提示用户在他们的 MetaMask 钱包上连接到 Goerli 网络，我们的智能合约在该钱包中部署（*参见后端 - 步骤 5*）。

当用户访问我们的*宠物表单*页面时，他们将看到以下屏幕：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20220914213654.png)

连接钱包地址后，我们现在可以通过`PetContract`变量访问智能合约中的`setPet`和`getPet`函数。

### 第 5 步 - 创建`SetNewPet`函数

在这一步中，我们将创建一个`setNewPet`函数，使用我们智能合约中的`PetContract.setPet()`函数将宠物的详细信息从*宠物表单*发送到我们的智能合约（*请参阅后端 - 步骤 2*）。

使用以下代码更新您的`index.js`文件：

```js

// 4. Creating variables for reusable dom elements
const petFormSection = document.querySelector(".pet-form-section");
const showPetFormBtn = document.querySelector(".show-pet-form-btn");
const petSection = document.querySelector(".pet-detail-section");
const setPetButton = document.querySelector("#set-new-pet");
const refreshBtn = document.querySelector(".refresh-pet-details-btn");

/* 5. Function to set pet details */
const setNewPet = () => {
  // update button value
  setPetButton.value = "Setting Pet...";

  /* 5.1 Get inputs from pet form */
  const petNameInput = document.querySelector("#pet-name");
  const petOwnerInput = document.querySelector("#pet-owner");
  const petAgeInput = document.querySelector("#pet-age");

  // 5.2 Getting values from the inputs
  petName = petNameInput.value;
  petOwner = petOwnerInput.value;
  petAge = petAgeInput.value;

  /* 5.3 Set pet details in smart contract */
  PetContract.setPet(petName, petOwner, petAge)
    .then(() => {
      // update button value
      setPetButton.value = "Pet Set...";

      /* 5.4 Reset form */
      petNameInput.value = "";
      petOwnerInput.value = "";
      petAgeInput.value = "";

      // update button value
      setPetButton.value = "Set Pet";

      /* 5.5 Get pet details from smart contract */
      getCurrentPet();
    })
    .catch((err) => {
      // If error occurs, display error message
      setPetButton.value = "Set Pet";
      alert("Error setting pet details" + err.message);
    });
};

/* Function to set pet details on click of button */
setPetButton.addEventListener("click", setNewPet);

```

### 第6步 - 创建getCurrentPet函数

在这一步，我们将创建getCurrentPet函数，使用智能合约中的PetContract.getPet()函数，获取智能合约内存中最后一个宠物的详细信息（见后台--第2步）。

用下面的代码更新你的index.js文件。

```js
/* 6. Function to get pet details */
const getCurrentPet = async () => {
  setPetButton.value = "Getting Pet...";

  /* 6.1 Get pet details from smart contract */
  const pet = await PetContract.getPet();

  /* 6.2 Display the pet details section

   6.2.1 Hide the pet form in DOM */
  petSection.style.display = "block";
  petFormSection.style.display = "none";

  /* 6.3 Pet is an array of 3 strings [petName, petOwner, petAge] */
  const petName = pet[0];
  const petOwner = pet[1];
  const petAge = pet[2];

  /* 6.4 Display pet details in DOM */
  document.querySelector(".pet-detail-name").innerText = petName;
  document.querySelector(".pet-detail-owner").innerText = petOwner;
  document.querySelector(".pet-detail-age").innerText = petAge;
};

```

最后，我们将添加一个函数，让用户返回到宠物表格，并添加另一个函数来刷新宠物的详细信息。

在你的index.js文件中添加以下几行代码。

```js
/* 7. Function to show the pet form on click of button */
showPetFormBtn.addEventListener("click", () => {
  petSection.style.display = "none";
  petFormSection.style.display = "block";
  setPetButton.value = "Submit";
});

/* 8. Function to refresh pet details */
refreshBtn.addEventListener("click", (e) => {
  e.target.innerText = "Refreshing...";
  getCurrentPet().then(() => {
    e.target.innerText = "Refreshed";
    setTimeout(() => {
      e.target.innerText = "Refresh";
    }, 2000);
  });
});

```

## 测试我们的dApp

现在我们的dApp的代码已经准备好了，我们可以继续测试我们的实现，如下所示。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/ha0DIUdbX.gif)

这个项目的完整源代码可以在这个资源库中找到。

## 总结

在本教程中，我们学习了如何使用 Ether.js 从前端应用程序中与 Solidity 智能合约进行交互。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)