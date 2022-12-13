---
title: 建立你自己的以太坊 NFT 集合（3）
description: null
author: 李留白
weight: 0
date: 2022-12-13T12:32:51.163Z
lastmod: 2022-12-13T12:35:47.825Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221210103130.png
---

**目录**

```
入门
  我们在建造什么？
铸造你的第一个 NFT
  什么是 NFT
  启动并运行您的本地环境
  在本地铸造 NFT
  创建一个铸造 NFT 的合约
在链上生成 NFT
  什么是链上数据。
  使用静态 SVG 铸造 NFT
  在链上动态生成 SVG NFT
创建 WEB 客户端
  设置 React 应用程序和钱包
  将钱包连接到网络应用程序
  创建一个调用合约和铸造 NFT 的按钮
完成并交付
  Web 应用程序的最后完善
  交付
```

## 在链上生成 NFTS

### 🎨什么是链上数据

#### 🔗 “链上”的含义及其重要性

我们现在的 NFT 有一个大问题。

如果 imgur 出现故障会怎样？好吧——那么我们的“图像”链接就会完全失效，我们的 NFT 将会丢失，我们的 `Sponge baby Nft`也丢失了！更糟糕的是，如果托管 JSON 文件的网站出现故障怎么办？好吧——那么我们的 NFT 就完全崩溃了，因为无法访问元数据。

解决这个问题的一种方法是将我们所有的 NFT 数据存储在“链上”，这意味着数据存在合约本身而不是在第三方手中。这意味着我们的 NFT 将真正永久的存在 :)。在这种情况下，我们丢失 NFT 数据的唯一情况是区块链本身出现故障。如果发生这种情况——那么我们就有更大的问题了！

但是，假设区块链永远存在——我们的 NFT 也将永远存在！这非常有吸引力，因为这也意味着如果您出售 NFT，买家可以确信 NFT 不会损坏。许多蓝筹项目都使用链上数据，[Loot](https://techcrunch.com/2021/09/03/loot-games-the-crypto-world/?utm_source=buildspace.so&utm_medium=buildspace_project) 就是是一个非常著名的例子！

#### 🖼什么是 SVGs

为图像存储 NFT 数据的常用方法是使用SVG。SVG是一个图像，但图像本身是用代码构建的。

例如，这是一个非常简单的 SVG，它呈现一个黑框，中间有一些白色文本。

```html
<svg xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMinYMin meet" viewBox="0 0 350 350">
    <style>.base { fill: white; font-family: serif; font-size: 14px; }</style>
    <rect width="100%" height="100%" fill="black" />
    <text x="50%" y="50%" class="base" dominant-baseline="middle" text-anchor="middle">EpicLordHamburger</text>
</svg>
```

前往 [这个](https://www.svgviewer.dev/?utm_source=buildspace.so&utm_medium=buildspace_project) 网站并粘贴上面的代码以查看它。随意查看。

这真的很酷，因为它让我们可以用代码创建**图像**。

SVG 可以定制**很多。** 您甚至可以为它们制作动画，哈哈。 欢迎在 [此处](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial?utm_source=buildspace.so&utm_medium=buildspace_project) 阅读更多关于它们的信息。

#### 🤘我们要做什么

首先，我们将学习如何让我们所有的 NFT 数据上链。我们的 NFT 只是一个方框，中间有一个**有趣的三词组合**。 就像上面的 SVG 一样。 我们将在“EpicLordHamburger”的合约中对上面的 SVG 进行硬编码。

之后，我们将学习如何在合约上*动态生成*这些 NFT。 因此，**每次有人铸造 NFT 时，他们都会得到一个不同的、搞笑的三词组合**。 例如：

- EpicLordHamburger
- NinjaSandwichBoomerang
- SasukeInterstellarSwift

这将是史诗级的工作:)。 我们开始吧！

### 🤖使用静态 SVG 铸造 NFT

#### 🤘创建SVG

这是我们的黑框 SVG。

```html
<svg xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMinYMin meet" viewBox="0 0 350 350">
    <style>.base { fill: white; font-family: serif; font-size: 14px; }</style>
    <rect width="100%" height="100%" fill="black" />
    <text x="50%" y="50%" class="base" dominant-baseline="middle" text-anchor="middle">EpicLordHamburger</text>
</svg>
```

接下来，我们想要一种方法来以某种方式在我们的 NFT 中获取这些数据，而无需将其托管在像 imgur 这样的地方（它随时可能出现故障或消失！）。 前往 [这个](https://www.utilities-online.info/base64?utm_source=buildspace.so&utm_medium=buildspace_project) 网站，粘贴上面的完整 SVG 代码，然后单击“encode”以获取 base64 编码的 SVG。 现在，准备好施展魔法了吗？ 打开一个新浏览器窗口。在 URL 栏中粘贴：

```plaintext
data:image/svg+xml;base64,INSERT_YOUR_BASE64_ENCODED_SVG_HERE
```

例如，我的看起来像这样：

```plaintext
data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHByZXNlcnZlQXNwZWN0UmF0aW89InhNaW5ZTWluIG1lZXQiIHZpZXdCb3g9IjAgMCAzNTAgMzUwIj4NCiAgICA8c3R5bGU+LmJhc2UgeyBmaWxsOiB3aGl0ZTsgZm9udC1mYW1pbHk6IHNlcmlmOyBmb250LXNpemU6IDE0cHg7IH08L3N0eWxlPg0KICAgIDxyZWN0IHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIGZpbGw9ImJsYWNrIiAvPg0KICAgIDx0ZXh0IHg9IjUwJSIgeT0iNTAlIiBjbGFzcz0iYmFzZSIgZG9taW5hbnQtYmFzZWxpbmU9Im1pZGRsZSIgdGV4dC1hbmNob3I9Im1pZGRsZSI+RXBpY0xvcmRIYW1idXJnZXI8L3RleHQ+DQo8L3N2Zz4=
```

我们将 SVG 代码转换成一个漂亮的字符串 :)， base64 基本上是将数据编码为字符串的公认标准。 所以当我们说 `data:image/svg+xml;base64` 时，它基本上是在说，“嘿，我给你 base64 编码的数据，请将它转换成 SVG ，谢谢！”。

将整个字符串 `data:image/svg+xml;base64,INSERT_YOUR_BASE64_ENCODED_SVG_HERE` 粘贴到浏览器的地址栏中，然后你就会看到 SVG！ 注意：如果出现错误，请仔细检查是否正确执行了所有步骤,这很容易搞砸:)。

好吧。这是一种让我们的 NFT 图像数据永久存储的方法。 世界上所有的数据中心都可能被毁坏，但由于我们有这个 base64 编码的字符串，只要我们有一台计算机和一个浏览器，我们总能看到 SVG。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221210111051.png)

#### ☠️摆脱 JSON 托管

还记得我们的 JSON 元数据吗？

好吧，我为我们的三词 NFT 稍微做一点点改动 :)。同样的操作，名称、描述和图像。 但是现在我们不再指向 imgur 链接，而是指向我们的 base64 编码字符串。

```json
{
    "name": "EpicLordHamburger",
    "description": "An NFT from the highly acclaimed square collection",
    "image": "data:image/svg+xml;base64,INSERT_YOUR_BASE64_ENCODED_SVG_HERE"
}
```

注意：不要忘记 `data:image/svg+xml;base64,INSERT_YOUR_BASE64_ENCODED_SVG_HERE` 两边的引号。

例如，我的看起来是这样：

```json
{
    "name": "EpicLordHamburger",
    "description": "An NFT from the highly acclaimed square collection",
    "image": "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHByZXNlcnZlQXNwZWN0UmF0aW89InhNaW5ZTWluIG1lZXQiIHZpZXdCb3g9IjAgMCAzNTAgMzUwIj4NCiAgICA8c3R5bGU+LmJhc2UgeyBmaWxsOiB3aGl0ZTsgZm9udC1mYW1pbHk6IHNlcmlmOyBmb250LXNpemU6IDE0cHg7IH08L3N0eWxlPg0KICAgIDxyZWN0IHdpZHRoPSIxMDAlIiBoZWlnaHQ9IjEwMCUiIGZpbGw9ImJsYWNrIiAvPg0KICAgIDx0ZXh0IHg9IjUwJSIgeT0iNTAlIiBjbGFzcz0iYmFzZSIgZG9taW5hbnQtYmFzZWxpbmU9Im1pZGRsZSIgdGV4dC1hbmNob3I9Im1pZGRsZSI+RXBpY0xvcmRIYW1idXJnZXI8L3RleHQ+DQo8L3N2Zz4="
}
```

但是等等——我们神奇的 JSON 文件会去哪里？ 现在，我们将其托管在 [这个](https://jsonkeeper.com/?utm_source=buildspace.so&utm_medium=buildspace_project) 随机网站上。 如果该网站出现故障，我们漂亮的 NFT 将永远消失！这就现在是我们要做的。 **我们将对整个 JSON 文件进行 base64 编码。** 就像我们对 SVG 进行编码一样。

再次前往 [这个](https://www.utilities-online.info/base64?utm_source=buildspace.so&utm_medium=buildspace_project) 网站，使用 base64 编码的 SVG 粘贴完整的 JSON 元数据（应该看起来像我上面的那样），然后单击“encode”以获取编码的 JSON。

在浏览器中打开一个新标签页，然后在 URL 栏粘贴这个:

```plaintext
data:application/json;base64,INSERT_YOUR_BASE64_ENCODED_JSON_HERE
```

比如，我的看起来是这样：

```plaintext
data:application/json;base64,ewogICAgIm5hbWUiOiAiRXBpY0xvcmRIYW1idXJnZXIiLAogICAgImRlc2NyaXB0aW9uIjogIkFuIE5GVCBmcm9tIHRoZSBoaWdobHkgYWNjbGFpbWVkIHNxdWFyZSBjb2xsZWN0aW9uIiwKICAgICJpbWFnZSI6ICJkYXRhOmltYWdlL3N2Zyt4bWw7YmFzZTY0LFBITjJaeUI0Yld4dWN6MGlhSFIwY0RvdkwzZDNkeTUzTXk1dmNtY3ZNakF3TUM5emRtY2lJSEJ5WlhObGNuWmxRWE53WldOMFVtRjBhVzg5SW5oTmFXNVpUV2x1SUcxbFpYUWlJSFpwWlhkQ2IzZzlJakFnTUNBek5UQWdNelV3SWo0TkNpQWdJQ0E4YzNSNWJHVStMbUpoYzJVZ2V5Qm1hV3hzT2lCM2FHbDBaVHNnWm05dWRDMW1ZVzFwYkhrNklITmxjbWxtT3lCbWIyNTBMWE5wZW1VNklERTBjSGc3SUgwOEwzTjBlV3hsUGcwS0lDQWdJRHh5WldOMElIZHBaSFJvUFNJeE1EQWxJaUJvWldsbmFIUTlJakV3TUNVaUlHWnBiR3c5SW1Kc1lXTnJJaUF2UGcwS0lDQWdJRHgwWlhoMElIZzlJalV3SlNJZ2VUMGlOVEFsSWlCamJHRnpjejBpWW1GelpTSWdaRzl0YVc1aGJuUXRZbUZ6Wld4cGJtVTlJbTFwWkdSc1pTSWdkR1Y0ZEMxaGJtTm9iM0k5SW0xcFpHUnNaU0krUlhCcFkweHZjbVJJWVcxaWRYSm5aWEk4TDNSbGVIUStEUW84TDNOMlp6ND0iCn0=
```

当您将该完整的 URI 粘贴到浏览器地址栏时，您将看到完整的 JSON。 **BOOOOOM！** 现在我们有办法让我们的 JSON 元数据永久保存。

这是我的屏幕截图：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221210111108.png)

*注意：当你encode+复制粘贴这些时，这里**很容易**搞砸。 所以，要非常小心！ 并仔细检查一切是否正常， 如果出现问题，请再次执行所有步骤！*

#### 🚀 修改我们的合约代码，然后部署

好的，太棒了，我们有了这个神器的 base64 编码的 JSON 文件。 我们要怎样把它写进合约里？ 只需转到`MyEpicNFT.sol`文件，然后将整个字符串复制粘贴到我们的合约中。

我们只需要修改 `tokenURI()` 中的一行代码：

~~~solidity
return string(
    abi.encodePacked(
        "data:application/json;base64,",
        "INSERT_BASE_64_ENCODED_JSON_HERE"
    )
)

例如，看起来像我这样的：
```solidity
return string(
    abi.encodePacked(
        "data:application/json;base64,",
        "ewogICAgIm5hbWUiOiAiRXBpY0xvcmRIYW1idXJnZXIiLAogICAgImRlc2NyaXB0aW9uIjogIkFuIE5GVCBmcm9tIHRoZSBoaWdobHkgYWNjbGFpbWVkIHNxdWFyZSBjb2xsZWN0aW9uIiwKICAgICJpbWFnZSI6ICJkYXRhOmltYWdlL3N2Zyt4bWw7YmFzZTY0LFBITjJaeUI0Yld4dWN6MGlhSFIwY0RvdkwzZDNkeTUzTXk1dmNtY3ZNakF3TUM5emRtY2lJSEJ5WlhObGNuWmxRWE53WldOMFVtRjBhVzg5SW5oTmFXNVpUV2x1SUcxbFpYUWlJSFpwWlhkQ2IzZzlJakFnTUNBek5UQWdNelV3SWo0TkNpQWdJQ0E4YzNSNWJHVStMbUpoYzJVZ2V5Qm1hV3hzT2lCM2FHbDBaVHNnWm05dWRDMW1ZVzFwYkhrNklITmxjbWxtT3lCbWIyNTBMWE5wZW1VNklERTBjSGc3SUgwOEwzTjBlV3hsUGcwS0lDQWdJRHh5WldOMElIZHBaSFJvUFNJeE1EQWxJaUJvWldsbmFIUTlJakV3TUNVaUlHWnBiR3c5SW1Kc1lXTnJJaUF2UGcwS0lDQWdJRHgwWlhoMElIZzlJalV3SlNJZ2VUMGlOVEFsSWlCamJHRnpjejBpWW1GelpTSWdaRzl0YVc1aGJuUXRZbUZ6Wld4cGJtVTlJbTFwWkdSc1pTSWdkR1Y0ZEMxaGJtTm9iM0k5SW0xcFpHUnNaU0krUlhCcFkweHZjbVJJWVcxaWRYSm5aWEk4TDNSbGVIUStEUW84TDNOMlp6ND0iCn0="
    )
)
~~~

最后，让我们部署更新后的合约并铸造 NFT，以及确保它能在 OpenSea 上正常显示！ 使用相同的命令进行部署， 我稍微修改了我的部署脚本，只铸造一个 NFT 而不是两个，你也可以随意更改代码已凸显其个性化！

```bash
npx hardhat run scripts/deploy.js --network goerli
```

然后和以前一样，等待一两分钟，直到输出合约地址，在 [https://testnets.opensea.io](https://testnets.opensea.io/?utm_source=buildspace.so&utm_medium=buildspace_project) 上搜索该合约地址，你应该会在那里看到你的 NFT :) 。同样，搜索时不要单击“Enter”——当NFT系列在搜索栏中弹出时，您需要实际点击击该系列。

*注意：请记得使用 `https://testnets.opensea.io/assets/goerli/INSERT_DEPLOY_CONTRACT_ADDRESS_HERE/TOKEN_ID`。*

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221210111127.png)

### 🤠在链上动态生成 SVG NFT

#### 🔤 在图像上生成随机词组

太棒了——我们已经创建了一个合约，现在可以在链上铸造 NFT。 但是，它始终是一样的 NFT 啊！！！ 让我们让它动态化。

**我在 [这里](https://gist.github.com/farzaa/b788ba3a8dbaf6f1ef9af57eefa63c27?utm_source=buildspace.so&utm_medium=buildspace_project) 写出了所有代码，它将生成一个包含三个随机单词组合的 SVG。**

我认为这将是人们一次查看所有代码并了解其工作原理的最佳方式。

我还在我代码更改的地方进行了大量注释！ 当您查看此代码时，请尝试并动手将其写出来。 可以通过谷歌搜索那些你不懂的函数！

对其中的一些代码行我会进行解释说明。

#### 📝 挑选词组

```solidity
string[] firstWords = ["YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD"];

string[] secondWords = ["YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD"];

string[] thirdWords = ["YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD"];
```

这些都是我们随机生成的单词！请随意享用，只需确保每个单词没有空格！

词义的选择越有趣越好，哈哈。 我喜欢让每个阵列都有一个特定的主题。 例如，`第一个单词`可以是您最喜欢的动漫角色的名字,然后，`第二个单词` 可能是您喜欢的食物, `第三个单词`可以是随机动物的名字。希望你能尽情享受挑选单词的过程。

这是我的一些单词次住。我喜欢的第一行感觉像是在“描述”某些东西的词！ 
![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221213203542.png)

也许您想随机生成一个乐队名称，也许您想随机为龙与地下城游戏生成角色名称，遵循您内心所想的去做吧。也许您根本不在乎三个单词的组合，只想制作类似于`pixel art penguins`的 SVG。 去吧。做你想做的 ：）。

*注意：我建议每个数组包含 15-20 个单词。 因为我意识大约 10 个通常不够随机。*

#### 🥴 随机数

```solidity
function pickRandomFirstWord
```

这个函数看起来有点新奇。对吧?我们来讨论一下如何从数组中随机抽取元素。

因此，在智能合约中生成随机数是众所周知的一个**难题**。

为什么？ 好吧，想一想一个随机数是如何正常产生的。当您在程序中正常生成随机数时，**它会从您的计算机中获取一堆不同的数字作为随机源**。例如：风扇的速度、CPU 的温度、您在下午 3:52 按下“L”的次数，你的网速，以及其他你难以控制的数量。它采用**所有**这些“随机”数字，并将它们组合到一个算法中，该算法生成一个数字，它认为这是对真正“随机”数字的最佳尝试。这说得通吗？

在区块链上，几乎没有随机性的来源。这是非常确定的，合约看到的一切，公众也看到。正因为如此，有人可以通过观察智能合约，看看它依赖于什么随机性来与系统博弈，然后如果他们想的话，就可以玩弄系统。

```solidity
random(string(abi.encodePacked("FIRST_WORD", Strings.toString(tokenId))));
```

这是在做什么？两件事：字符串“FIRST_WORD”和“tokenId”的字符串化版本。我使用 `abi.encodePacked` 组合这两个字符串，然后组合的字符串就是我用作随机源的内容。

**这其实是伪随机。** 但这是我们目前能得到的最好结果！

还有其他方法可以在区块链上生成随机数（查看 [Chainlink](https://docs.chain.link/docs/chainlink-vrf/?utm_source=buildspace.so&utm_medium=buildspace_project)） 但Solidity并不能给我们带来任何可靠的东西，因为它不能！我们合约可以访问的所有内容都是公开的，并不是真正的随机。

对于像我们这里提到dapp，这可能有点烦人！无论如何，没有人会攻击我们的小dapp，但我希望您在构建拥有数百万用户的 dApp 时了解所有这些知识！

#### ✨ 创建动态 SVG

查看合约上的变量 `string baseSvg`。 这看起来很疯狂哈哈。 基本上，我们的 SVG 唯一改变的部分是三个单词的组合，对吧？ 所以我们所做的是创建一个 `baseSvg` 变量，我们可以在创建新的 NFT 时反复使用它。

然后我们用以下方法将所有内容放在一起：

```
string memory finalSvg = string(abi.encodePacked(baseSvg, first, second, third, "</text></svg>"));
```

`</text></svg>` 是结束标记！所以对于 `finalSvg`，我们说，“嘿 - 结合我的 baseSVG，生成三词组合，然后是我的结束标记。就是这样 :)！它看起来很可怕，但我们所做的只是得到 SVG代码。

#### 😎 运行

词组全部写完后，继续使用`npx hardhat run scripts/run.js`运行。 然后查看`console.log(finalSvg)` 在控制台输出的内容：

```plaintext
This is my NFT contract. Woah!
Contract deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3

--------------------
<svg xmlns='http://www.w3.org/2000/svg' preserveAspectRatio='xMinYMin meet' viewBox='0 0 350 350'><style>.base { fill: white; font-family: serif; font-size: 24px; }</style><rect width='100%' height='100%' fill='black' /><text x='50%' y='50%' class='base' dominant-baseline='middle' text-anchor='middle'>SandwichSakuraNinja</text></svg>
--------------------

An NFT w/ ID 0 has been minted to 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266

--------------------
<svg xmlns='http://www.w3.org/2000/svg' preserveAspectRatio='xMinYMin meet' viewBox='0 0 350 350'><style>.base { fill: white; font-family: serif; font-size: 24px; }</style><rect width='100%' height='100%' fill='black' /><text x='50%' y='50%' class='base' dominant-baseline='middle' text-anchor='middle'>GoatSasukeNinja</text></svg>
--------------------

An NFT w/ ID 1 has been minted to 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266
```

哈哈，这里的内容可真多。继续复制其中一个 SVG 输出结果，并将它粘贴到[这里](https://www.svgviewer.dev/?utm_source=buildspace.so&utm_medium=buildspace_project)，看看您会得到什么。

您将能够看到生成的SVG !这是我的:

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221210111406.png)

我们在合约中随机生成了这个！如果您使用您生成的另一个 SVG，您会发现它也有所不同。这一切都是即时生成的✌️

#### 👩‍💻 生成动态元数据

现在，我们需要实际设置 JSON 元数据！首先，我们需要一些辅助函数。在 `contracts`目录 下创建一个名为 `libraries` 的文件夹。 在`libraries`下创建一个名为 `Base64.sol` 的文件，并将[此处](https://gist.github.com/farzaa/f13f5d9bda13af68cc96b54851345832?utm_source=buildspace.so&utm_medium=buildspace_project) 中的代码复制粘贴到其中。这个文件有一些由其他人创建的辅助函数，可以帮助我们在 Solidity 中将 SVG 和 JSON 转换为 Base64。

好的，现在是我们更新后的合约。

**同样的，我写出了所有的代码并添加了注释在 [这里](https://gist.github.com/farzaa/dc45da3eb91a41913767f3eb4d7830f1?utm_source=buildspace.so&utm_medium=buildspace_project)。**

随意复制粘贴其中的一些片段，并在运行后了解它是如何工作的:)。 有时我喜欢这样做，因为我可以看到代码运行并理解它是如何工作的！！

一旦我现在运行合约，这就是我得到的：

```plaintext
Compilation finished successfully
This is my NFT contract. Woah!
Contract deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3

--------------------
data:application/json;base64,eyJuYW1lIjogIlNhbmR3aWNoU2FrdXJhTmluamEiLCAiZGVzY3JpcHRpb24iOiAiQSBoaWdobHkgYWNjbGFpbWVkIGNvbGxlY3Rpb24gb2Ygc3F1YXJlcy4iLCAiaW1hZ2UiOiAiZGF0YTppbWFnZS9zdmcreG1sO2Jhc2U2NCxQSE4yWnlCNGJXeHVjejBuYUhSMGNEb3ZMM2QzZHk1M015NXZjbWN2TWpBd01DOXpkbWNuSUhCeVpYTmxjblpsUVhOd1pXTjBVbUYwYVc4OUozaE5hVzVaVFdsdUlHMWxaWFFuSUhacFpYZENiM2c5SnpBZ01DQXpOVEFnTXpVd0p6NDhjM1I1YkdVK0xtSmhjMlVnZXlCbWFXeHNPaUIzYUdsMFpUc2dabTl1ZEMxbVlXMXBiSGs2SUhObGNtbG1PeUJtYjI1MExYTnBlbVU2SURJMGNIZzdJSDA4TDNOMGVXeGxQanh5WldOMElIZHBaSFJvUFNjeE1EQWxKeUJvWldsbmFIUTlKekV3TUNVbklHWnBiR3c5SjJKc1lXTnJKeUF2UGp4MFpYaDBJSGc5SnpVd0pTY2dlVDBuTlRBbEp5QmpiR0Z6Y3owblltRnpaU2NnWkc5dGFXNWhiblF0WW1GelpXeHBibVU5SjIxcFpHUnNaU2NnZEdWNGRDMWhibU5vYjNJOUoyMXBaR1JzWlNjK1UyRnVaSGRwWTJoVFlXdDFjbUZPYVc1cVlUd3ZkR1Y0ZEQ0OEwzTjJaejQ9In0=
--------------------

An NFT w/ ID 0 has been minted to 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266

--------------------
data:application/json;base64,eyJuYW1lIjogIkdvYXRTYXN1a2VOaW5qYSIsICJkZXNjcmlwdGlvbiI6ICJBIGhpZ2hseSBhY2NsYWltZWQgY29sbGVjdGlvbiBvZiBzcXVhcmVzLiIsICJpbWFnZSI6ICJkYXRhOmltYWdlL3N2Zyt4bWw7YmFzZTY0LFBITjJaeUI0Yld4dWN6MG5hSFIwY0RvdkwzZDNkeTUzTXk1dmNtY3ZNakF3TUM5emRtY25JSEJ5WlhObGNuWmxRWE53WldOMFVtRjBhVzg5SjNoTmFXNVpUV2x1SUcxbFpYUW5JSFpwWlhkQ2IzZzlKekFnTUNBek5UQWdNelV3Sno0OGMzUjViR1UrTG1KaGMyVWdleUJtYVd4c09pQjNhR2wwWlRzZ1ptOXVkQzFtWVcxcGJIazZJSE5sY21sbU95Qm1iMjUwTFhOcGVtVTZJREkwY0hnN0lIMDhMM04wZVd4bFBqeHlaV04wSUhkcFpIUm9QU2N4TURBbEp5Qm9aV2xuYUhROUp6RXdNQ1VuSUdacGJHdzlKMkpzWVdOckp5QXZQangwWlhoMElIZzlKelV3SlNjZ2VUMG5OVEFsSnlCamJHRnpjejBuWW1GelpTY2daRzl0YVc1aGJuUXRZbUZ6Wld4cGJtVTlKMjFwWkdSc1pTY2dkR1Y0ZEMxaGJtTm9iM0k5SjIxcFpHUnNaU2MrUjI5aGRGTmhjM1ZyWlU1cGJtcGhQQzkwWlhoMFBqd3ZjM1puUGc9PSJ9
--------------------

An NFT w/ ID 1 has been minted to 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266
```

这很传奇。

我们只是在链上完整生成了动态的 NFTs。这是史诗一般的时刻。

如果你将其中一个 `data:application/json;base64`  粘贴到浏览器地址栏中，你会看到所有的json元数据，就像我们之前看到的一样。除了现在，这一切都是在我们的合约上自动完成的:)。

#### 👀 `finalTokenUri` 是如何工作的？

带有 `string memory json = Base64.encode` 的那一大行可能看起来很混乱，是因为所有引号 lol。 我们所做的就是对 JSON 元数据进行 base64 编码！ 但是——这一切都是**在链上**。 因此，所有 JSON 都将存在于合约本身中。

我们还动态添加 `name` 和 base64 编码的 SVG！

最后，我们得到了这个`finalTokenUri`，将它们这样放在一起：

```solidity
abi.encodePacked("data:application/json;base64,", json)
```

这里发生的一切就是我们将这些放在一起并像之前做的一样添加到 `data:application/json;base64` ，然后我们附加 base64 编码的 json！

#### 🛠 调试 `finalTokenUri` 内容

现在您已经设置了 tokenURI，我们怎样知道它是否正确？ 毕竟，这保存了我们 NFT 的所有数据！ 您可以使用像 [NFT Preview](https://nftpreview.0xdev.codes/?utm_source=buildspace.so&utm_medium=buildspace_project) 这样酷的工具来快速预览图像和 json 的内容，而无需在 OpenSea 测试网上一次又一次地部署它。

为了方便起见，您可以像这样将 `tokenURI` 代码作为查询参数传递：

```solidity
string memory finalTokenUri = string(
    abi.encodePacked("data:application/json;base64,", json)
);

console.log("\n--------------------");
console.log(
    string(
        abi.encodePacked(
            "https://nftpreview.0xdev.codes/?code=",
            finalTokenUri
        )
    )
);
console.log("--------------------\n");
```

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20221210111433.png)

#### 🚀 部署到Goerli

最酷的是，我们可以在不修改脚本的情况下重新部署：

```bash
npx hardhat run scripts/deploy.js --network goerli
```

一旦我们重新部署，您将能够在 [https://testnets.opensea.io](https://testnets.opensea.io/?utm_source=buildspace.so&utm_medium=buildspace_project) 上搜索新部署的合约地址后看到您的 NFT。 同样，**不要点击回车**。 OpenSea 很奇怪，所以当它出现时您需要单击该系列名字。

注意：如果您使用的是 Rarible，请记住使用 `https://testnet.rarible.com/token/INSERT_DEPLOY_CONTRACT_ADDRESS_HERE:TOKEN_ID`。

合约是**永久的**。 因此，每当我们重新部署我们的合约时，我们实际上是在创建一个全新的系列。

您应该能够在 OpenSea 上看到新的收藏 :)！

#### 🤖 允许用户铸造

真棒！我们现在可以动态创建NFT，并且我们有一个用户可以调用的函数 `makeAnEpicNFT`。虽然已经取得了如此成就！！但是，对别人来说，没有办法真正随机铸造NFT🤒

我们所需要的是一个让用户自己铸造 NFT 的网站。

所以，让我们构建它 :)！

> 原文链接：https://buildspace.so/p/mint-nft-collection

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
