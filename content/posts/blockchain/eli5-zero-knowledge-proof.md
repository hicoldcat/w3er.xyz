---
title: 零知识证明：像5岁孩子一样解释它（万圣节版）
description: null
author: 李留白
weight: 0
date: 2023-01-09T12:29:44.672Z
lastmod: 2023-01-09T13:07:31.005Z
tags: []
categories:
  - 区块链
  - WEB3.0
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230109204447.png
---

*解释加密技术很难，用简单的话解释加密技术更难。向孩子解释零知识证明？很简单! 所以，在这里你可以看到--用一些万圣节的糖果来解释ZKP。*

此前该系列文章：[Explain Like I’m 5: End-to-end Encryption](https://medium.com/@cossacklabs/eli5-end-to-end-encryption-ae46821db74f?ref=hackernoon.com)

## 零知识协议

零知识协议（或零知识密码证明，ZKP）是一种不交换密码的认证方式，这意味着它们不能被盗。这很酷，因为它使你的通信非常安全和受保护，没有人可以发现你在通信的内容或你在相互分享什么文件。

ZKP允许你向通信另一 "端 "的某人证明你知道一些秘密（或许多秘密），而实际上却没有透露。术语 "零知识 "起源于这样一个事实：关于秘密的任何（"零"）信息都没有透露，但第二方（称为 "验证者"）（正确地）相信第一方（称为 "证明者"）知道有关的秘密。为什么你需要证明你知道这个秘密而不告诉它？当你不信任对方，但仍然需要说服对方你知道这个秘密。那么这个过程是什么样子的呢？

## 糖果棒和百万富翁

让我们在 Bob 和 Alice 的帮助下进行说明，他们为万圣节准备了一些巧克力棒。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230109205459.png)

他们想知道他们是否收到了相同数量的糖果，但没有透露他们的巧克力数量，因为他们不想分享。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230109205546.png)

假设他们的“不给糖就捣蛋”袋子里正好有 10、20、30 或 40 块巧克力。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230109205614.png)

为了比较他们得到的巧克力块的数量而不分享实际数量，Bob 得到了 4 个可上锁的盒子，并在每个盒子里贴上标签，上面写着 10、20、30 或 40（巧克力棒）。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230109205747.png)



然后 Bob 扔掉除了盒子的钥匙对应于他得到的巧克力棒的数量（假设他有 20 块巧克力棒）所有钥匙并离开。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230109205842.png)

Alice拿了 4 张小纸片，在其中一张纸上写上“+”，在其他纸上写上“-”。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230109205906.png)

然后，她将“+”部分从一个槽中滑入盒子，盒子中的数字与她得到的糖果数量相对应（假设她有 30 块糖果），然后将带有“-”的纸片滑入其余部分盒子和树叶。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230109205938.png)

Bob 返回并打开他仍然有钥匙的那个盒子——那个盒子与他得到的糖果数量相对应——然后看它是否包含“+”或“-”。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230109210006.png)

如果是“加号”，则Alice的包里有相同数量的巧克力棒。如果纸条上写着“-”，则表示他们有不同数量的糖果（但仍然不会互相分享）。

我们知道 Bob 的包里有 20 块巧克力，而 Alice 的包里有 30 块巧克力。通过打开盒子并找到上面有“减号”的那张纸，Bob 得知他和 Alice 的糖果数量不同。但他无法查明Alice的巧克力块是多了还是少了。

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230109210027.png)

Alice也回来了，看到Bob有一张纸，上面有一个“减号”。所以他有不同数量的糖果。但是Alice和Bob仍然不知道他们每个人有多少块巧克力。他们只知道他们没有相同的数量。

这个例子的形式略有不同，被广泛称为[Yao’s Millionaire’s Problem](https://en.wikipedia.org/wiki/Yao's_Millionaires'_Problem?ref=hackernoon.com)，其中两个百万富翁想知道他们是否拥有相同数量的钱，但没有透露确切数量。这是 ZKP 如何工作的一个简单示例。


> 原文：https://hackernoon.com/eli5-zero-knowledge-proof-78a276db9eff

![](https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/profile.jpg)
