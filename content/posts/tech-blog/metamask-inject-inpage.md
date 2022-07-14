---
title: 在自己开发的插件中调用MetaMask插件来实现以太坊账户登录和交互
description: null
author: 李留白
weight: 0
date: 2022-07-14T12:00:10.221Z
lastmod: 2022-07-14T12:12:25.718Z
tags: []
categories:
  - 技术分享
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207142004778.png
---

## 背景

最近想制作一个浏览器新标签页导航器(类似于[infinitytab](https://www.infinitytab.com/))的chrome插件，其中，希望在插件中调用MetaMask来作为账户登录。

在开发插件时，遇到了一个致命的问题！我们都知道，如果浏览器安装了MetaMask插件， 那么会默认在浏览器window环境上注入`window.ethereum`，如下图所示

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141848816.png)

这个是MetaMask提供的默认Provider，可以访问以太坊账户的信息。

但是，在新标签页，MetaMask是不注入这个变量的，也就是在`chrome-extension://`这个地址下面，是没有注入Provider的。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141851489.png)

这就导致了当我们自己开发了一个新标签工具，想要直接调用MetaMask的账户登录时，是没什么反应的。

怎么解决？

## `window.ethereum` 如何注入浏览器的？

首先想到的解决方法，试看一下MetaMask是怎么注入到浏览器中这个`window.ethereum`变量的。因此，我找到了 MetaMask浏览器插件的源码：[https://github.com/MetaMask/metamask-extension](https://github.com/MetaMask/metamask-extension)

## [metamask-extension](https://github.com/MetaMask/metamask-extension)

在这个插件中，我先按照`window.ethereum`搜索了一下，没有任何的发现：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141902598.png)

于是，开始寻找是否给页面注入了js脚本，最终在`app/scripts/contentscript.js`文件中看到了`injectScript`函数：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141906153.png)

从这个文件我们看到使用到了`inpage.js`文件，于是在`app/scripts/inpage.js`文件，看到了`initializeProvider`函数，猜测大概率跟这个函数有关。而这个函数是从`@metamask/providers/dist/initializeInpageProvider`中导入的，于是，我找到了`@metamask/providers`包的源码。

## [https://github.com/MetaMask/providers](https://github.com/MetaMask/providers)

在这个包中，找到了`initializeInpageProvider`的源码，其中用到了`setGlobalProvider`函数

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141911352.png)

在这个文件的下方我们找到了这个`setGlobalProvider`函数，

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141913280.png)

终于，原来是在这里，MetaMask将provider实例注入到了 `window.ethereum`中。

## 如何在自己插件中使用

但是，我们怎么在自己的插件中获得provider的实例并且注入`window.ethereum`呢？毕竟我们又无法更改MetaMask的源码。最终，还是从MetaMask官方插件的Github源码 [https://github.com/MetaMask/metamask-extension](https://github.com/MetaMask/metamask-extension)的issues中，找到了这条回答：[https://github.com/MetaMask/metamask-extension/issues/12444#issuecomment-1029338757](https://github.com/MetaMask/metamask-extension/issues/12444#issuecomment-1029338757)：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141856708.png)

意思是说他们有个[extension-provider](https://github.com/MetaMask/extension-provider)的库也许可以解决这个问题。

那么，就继续解决问题！

## [extension-provider](https://github.com/MetaMask/extension-provider)

跟着源码的README，安装插件后，运行代码发现报了一堆错误：

```
ERROR in ./node_modules/post-message-stream/index.js 2:17-41
Module not found: Error: Can't resolve 'util' in '/Users/mac/Mine/tabverse/node_modules/post-message-stream'

BREAKING CHANGE: webpack < 5 used to include polyfills for node.js core modules by default.
This is no longer the case. Verify if you need this module and configure a polyfill for it.

If you want to include a polyfill, you need to:
	- add a fallback 'resolve.fallback: { "util": require.resolve("util/") }'
	- install 'util'
If you don't want to include a polyfill, you can use an empty module like this:
	resolve.fallback: { "util": false }
```

经过Google才知道，因为我们用的是Webpack5，而Webpack5升级之后，相较于Webpack5，移除了很多内置的无用的库，如报错中的`util`、`path`、`os`等等，那怎么解决呢？

在stackoverflow上找到了答案：[https://stackoverflow.com/a/65556946/9336091](https://stackoverflow.com/a/65556946/9336091):

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141923964.png)

也就是通过`node-polyfill-webpack-plugin`插件去兼容webpack5升级后带来的问题。

安装完成后，不报错了，但是这个库只给我们提供了一个provider，并没有帮我们注入到环境变量中`window.ethereum`：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141937426.png)

## [https://github.com/MetaMask/providers](https://github.com/MetaMask/providers)

最终还是绕回到`@metamask/providers`中，根据readme操作，发现使用到了`LocalMessageDuplexStream`:

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141941176.png)

Google查找了一下发现这个库来自`post-message-stream`

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141944115.png)

因此引入后继续使用，发现`window.ethereum`确实有了，但是`chainId`为空：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141945498.png)

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141946998.png)

继续思考，可能是provider提供的不正确，还是得从`initializeProvider`源码看一下，所以又返回[https://github.com/MetaMask/providers](https://github.com/MetaMask/providers)查看了一下：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141949079.png)

原来实际使用的是`MetaMaskInpageProvider`，而之前我们看[extension-provider](https://github.com/MetaMask/extension-provider)时，已经见过这个provider了：

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141948384.png)

因此，参照`extension-provider`代码，我们使用了MetaMask提供的provider

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141952081.png)

需要注意的是，这里需要连接浏览器的MetaMask插件: `chrome.runtime.connect('nkbihfbeogaeaoehlefnkodbefgpgknn')`，connect里面用到的就是MetaMask在chrome浏览器中的插件id。到这里位置，看起来就合理了。在浏览器里试一下，发现确实提供出了`MetaMaskInpageProvider`，而且也有了chainId。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207141955981.png)

此时，测试发现可以成功调用MetaMask插件了，大功告成~~~

## 最后
最终通过阅读6~7个库的源码才解决了这个问题，但是还是希望MeatMask官方能解决一下无法在其他浏览器插件中注入`window.ethereum`的问题。毕竟也有其他人在这么使用。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/202207142007417.png)

最后，来两个推荐：

- 如果想要开发自己的浏览器插件，可以使用[https://github.com/lxieyang/chrome-extension-boilerplate-react.git](https://github.com/lxieyang/chrome-extension-boilerplate-react.git)这个模板。

- 如果希望开发自己的DApp应用，可以使用[https://usedapp.io/](https://usedapp.io/)这个工具~~~

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/my.png)
