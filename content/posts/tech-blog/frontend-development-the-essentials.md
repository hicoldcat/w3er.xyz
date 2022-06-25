---
title: 前端开发要点
description: 在开始前端开发的职业生涯之前你应该知道的事情
author: 李留白
weight: 0
date: 2022-06-25T11:02:38.327Z
lastmod: 2022-06-25T12:59:34.187Z
tags: []
categories:
  - 技术分享
featuredImage: https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/0_bMfANTYZ3J3rSmn1.jpg
---

>原文：https://betterprogramming.pub/frontend-development-the-essentials-1c1290b43590 <br/>
> 翻译：李留白

这是一篇为任何想开始前端开发而没有编程背景的人写的文章。最终目标是帮助爱好者了解软件开发的蓬勃世界，并使他们能够在这个拥挤的生态系统中找到自己的方向。其结果是一个开始学习过程的路线图。

## 介绍

前段时间，有几个朋友问我如何成为一名前端开发人员。我向他们介绍了要走的路，还给了他们一些关键字，例如Git、HTML、CSS等。但后来我发现，对于没有任何编程背景的人来说，进入这个行业是有障碍的。他们应该首先熟悉生态系统。

他们很快就会被大量的教育材料所淹没，对于每个关键字，谷歌都会返回大量的结果。就像你想买你的第一辆车，有很多不同的车型，有不同的选择，价格相同。如果你没有得到任何关于你的目标规格的建议，你很快就会感到困惑，要么选择坏的，要么离开商店。

在本文中，我将尝试为您提供软件开发生态系统的观点和概述。我还将为您提供一些有用的关键字，以及一些指向学习材料和博客文章的链接。最后，你会带着一张学习路线图离开。

在开始之前，我想再次提醒一下，前端开发是最拥挤的开发者社区之一，所以不要害怕大量奇怪的名称、库和技术。目标是使您免于被人群淹没，并为您提供可以帮助您找到方向的知识。

## 网络基础
让我们从网页由什么组成的问题开始。

将网站想象成一本包含一些页面的书，一本高级书，其中每一页都由图像、文本、视频、音频和一个名为链接的特殊元素组成。在网页上，我们可以将所有这些元素放在一起，将它们设置为带有字体和颜色的书页，也可以将它们放在不同的布局中。

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220625191336.png)

正是链接使网页不同于一本书。链接的作用是帮助您以非顺序的顺序在页面之间导航。链接可以将访问者带到网站的任意页面。您可以根据每页上的链接在页面之间跳转，甚至可以跳转到其他书籍的页面。

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220625205757.png)

### 网页元素
你在网页上看到的几乎所有其他东西都是这些基本元素的组合：

- 文本
- 图片
- 声音
- 视频
- 链接（导航元素）

网站的目的是提供信息以及特定于业务的用户体验。

## 网页开发技术
支持 Web 开发的主要技术有以下三种：

- HTML（超文本标记语言）
- CSS（级联样式表）
- JavaScript（浏览器理解的动态编程语言）

### HTML

>“超文本标记语言 (HTML) 是设计用于在Web 浏览器中显示的文档的标准标记语言。它可以通过级联样式表(CSS) 等技术和 JavaScript 等脚本语言来辅助。” —维基百科

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220625191659.png)

HTML 是一种标记语言。这意味着您通过称为标签的 HTML 元素创建页面内容。每个标签都有自己的特定属性和行为。

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220625191721.png)

这是[HTML 标记](https://www.w3schools.com/TAGS/default.ASP)列表。

### CSS

HTML 用于构建页面内容。在 CSS 的帮助下，可以根据布局、颜色、字体和样式设置页面样式。使用 CSS，我们可以制作可维护和可扩展的样式。

### JavaScript

JavaScript 是一种浏览器可以理解的动态脚本语言。在 JavaScript 的帮助下，网页可以进行交互，例如当您单击按钮时，您可以向用户显示特定数据。想想你以前见过的所有登录按钮。JavaScript 支持它们以启用它们的功能和交互。

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220625191827.png)

所以现在我们知道，借助三种技术，我们可以构建网页。我们将在进一步的步骤中研究如何做到这一点。

## 编写代码使其生效的过程

在这一点上，我将为您提供整个软件开发过程的广泛而简单的视角，从您开始编写第一行代码到您将 Web 应用程序上线。这个过程在任何软件开发平台上都有些相同，包括 Android 和 iOS。

为您提供此概述的目的是表明软件开发过程不仅仅是编写代码。在其中，您会听到很多与流程其他部分相关的奇怪和新词。

![软件开发过程非常重要](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220625191856.png)


记住这个事实：整个过程是迭代的。每次将应用程序上线时，您都应该监控用户或利益相关者的请求和反馈，然后计划更改。

### 源代码控制

如果您在团队中工作，甚至单独工作，您需要了解的一件事就是源代码控制。软件开发发生在迭代中，并且在每次迭代中，您都会添加新功能或修复错误。为了跟踪源代码的变化，我们需要一种机制。源代码控制工具是跟踪代码库更改并允许团队中代码库协作的方法。

[Git](https://git-scm.com/) 是开发人员最常用的源代码控制工具之一。正如[Web Development Glossary](https://books.google.ca/books?id=nYjhDwAAQBAJ&pg=PT101&lpg=PT101&dq=s+goals+include+speed,+data+integrity,+and+support+for+distributed,+non-linear+workflows.&source=bl&ots=8iV6gquLBx&sig=ACfU3U2E-OmUydwQiMArK3yDjR7M8-Zj-w&hl=en&sa=X&ved=2ahUKEwib0JWuqM_pAhWRVN8KHYeYBoAQ6AEwAHoECAkQAQ#v=onepage&q=s%20goals%20include%20speed%2C%20data%20integrity%2C%20and%20support%20for%20distributed%2C%20non-linear%20workflows.&f=false)中所述，“Git 是一个分布式版本控制系统，用于在开发过程中跟踪源代码的更改。它是为协调程序员之间的工作而设计的，但它可用于跟踪任何文件集的更改。它的目标包括速度、数据完整性以及对分布式非线性工作流的支持。”

![源代码控制机制](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220625191952.png)


## 软件开发生态系统

这是一个有趣的话题。我们将了解一些重要的平台，这些平台可以帮助您与他人交流、学习和分享您的挑战。技术行业，尤其​​是软件行业的沟通对所有参与者都至关重要。科技行业发展迅速，其中一个原因是该行业的沟通和知识共享精神。

在本主题中，我将为您提供一些指向技术知识中心的链接，尤其是那些用于前端开发的知识中心。

### [W3Schools](https://www.w3schools.com/)

W3Schools 是开始学习 JavaScript、HTML 和 CSS 的良好参考之一。它还涵盖了一些其他主题，例如 React 和 SQL。这是开始使用命名主题的好地方。

### [Stack Overflow](https://stackoverflow.com/)

如果您对特定案例有疑问，可以在 Stack Overflow 中找到答案。这是一个网站，任何在开发过程中遇到他们不知道如何解决的问题的开发人员都会首先查看问题和答案。这是一个知识共享和交流平台。我想其他行业的人会羡慕我们拥有这个了不起的平台。

### [NPM JS](https://www.npmjs.com/)

如果您正在寻找任何 JavaScript 或 TypeScript 库或工具，这里就是将它们保存为存储库的地方。将来，您将经常使用该网站。这是一个参考和存储库，您将使用它来构建自己的解决方案并避免重新发明轮子。

软件开发通过将大问题分解为小问题、使用小块解决方案（代码）并将它们组合起来解决更大的问题来解决问题。

### [GitHub](https://github.com/)

有几个云源控制平台，但这个很特别，因为您可以找到大量流行的开源项目和库。此外，几乎所有的 npm 包源代码都托管在 GitHub 上。将 GitHub 视为共享代码的平台。

### [Udemy ](https://www.udemy.com/)

Udemy 是科技行业的教育平台之一，可帮助开发人员提高技能。这个平台上有大量与前端开发相关的不同课程，您可以浏览和选择。
还有其他平台，如Coursera、Edx、Lynda等，您可以在其中找到与前端开发相关的课程。

### [hackerrank](https://www.hackerrank.com/)

该平台通过引入您需要找到答案的挑战来帮助您提高编程技能。这些挑战帮助你学习编程、算法和数据结构。这是一个非常有用的平台，可以提高您的编程技能。

## 前端开发技术

在这里，我们将看看用于前端开发的不同技术。实际上，最重要的是要学习 JavaScript。其余的是可以简化您的开发的库或框架，但最终，它们是基于浏览器理解的语言 JavaScript 的库和框架。

请记住，JavaScript 是最重要的。学习 JavaScript 极大地帮助您更好地阅读和理解库和框架。

你应该学习的 JavaScript 概念：

- Lexical Structure
- Expressions
- Types
- Variables
- Functions
- this
- Arrow functions
- Loops
- Scopes
- Arrays
- Template literals
- Semicolons
- Strict mode
- ECMAScript 6, 2016, 2017
- JSON
- Nodejs. 它是在服务器或任何独立于浏览器的机器上运行 JavaScript 的环境。
- HTTP 请求和响应模型和 REST API 概念。
- Working with files and machine resources with JavaScript
- async/await and Modules
- npm

接下来要学习的是熟悉[HTML](https://www.w3schools.com/html/)和[CSS3](https://css-tricks.com/)。HTML 和 CSS 组合是您工作的视觉方面。尝试了解它们的结构和要领。其余的是不同类型的实现，如果您了解 HTML 和 CSS，对您来说会很容易。

学习[JQuery](https://jquery.com/)，这是一个 JavaScript 库，旨在简化 HTML DOM 树的遍历和操作，以及事件处理、CSS 动画和 Ajax。它是免费的开源软件。

### 现代前端库和框架

注意：以下定义取自维基百科和其他在线资源。

- AngularJs “AngularJS 是一个基于 JavaScript 的开源前端 Web 框架，主要由 Google 和个人社区维护。它是最流行的前端开发框架之一。” —维基百科。
- ReactJs “ React 是一个用于构建用户界面的 JavaScript 库。它由 Facebook 和个人开发者社区维护。” —维基百科。
ReactJs 也是最受欢迎的之一，周围有一个很大的社区。
- VueJs “Vue 是一个开源的 Model-View-ViewModel JavaScript 框架，用于构建用户界面和单页应用程序。它由 Evan You 创建，由他和来自 Netlify 和 Netguru 等不同公司的其他活跃核心团队成员维护。” —火星媒体

首先，只关注一个。作为一名初级开发人员，你有很多工作机会——但我再次强调 JavaScript，因为它是你知识的核心结构。

要找到一份初级 Web 开发人员的工作，首先，请访问 LinkedIn 等网站。搜索与前端开发相关的工作，看看最需要哪些关键字和库，然后去学习它们。

## 如何选择合适的工具

您需要一些工具来编写、执行和调试代码。以下是我对基本工具的建议。（注：以下定义取自维基百科和其他在线资源。）

### [VS Code](https://code.visualstudio.com/)

“Visual Studio Code 是微软为 Windows、Linux 和 macOS 开发的源代码编辑器。它包括嵌入式 Git 和对调试、语法高亮显示、智能代码完成、片段和代码重构的支持。” —维基百科。

### [ZSH](https://ohmyz.sh/)

“Z shell是一个 Unix shell 或 CLI，可以用作交互式登录 shell 和 shell 脚本的命令解释器。ZSH 支持插件并为您提供了许多有助于轻松使用 CLI 的功能”——维基百科。

### [Postman](https://www.postman.com/)

“Postman 是一个流行的 API 客户端，它使开发人员可以轻松地创建、共享、测试和记录 API。这是通过允许用户创建和保存简单和复杂的 HTTP/s 请求以及读取他们的响应来完成的。” — DZone。

这些是必需品，但肯定有大量的浏览器插件和 VS Code 可以简化您的开发生活。

## 计划和路线图

为了实现一个目标，你应该有一个计划和一个路线图。在本节中，我将尝试为您提供成为前端开发人员的最低路线图。您可以在两个月或一年内完成路线图——这完全取决于您。你在这个职业发展上投入的时间是一个关键因素。

你可以通过开始 Udemy ReactJs 课程来学习前端开发，但是在求职面试中，你会遇到很多新的和未知的问题和关键词。我的目标是帮助你作为一名真正的开发人员从根本上学习前端开发。还要记住，要学习一些东西，你需要做一些事情，所以你需要从零开始编写代码。

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220625205152.png)

## 学习资料

以下是每个主题的顶级课程和教程列表。那里有大量的内容，但这些都是好的开始。只需从每个主题中选择一两个即可。

### HTML + CSS

- https://www.youtube.com/user/DesignCourse
一个很棒的 youtube 频道，用于讨论和教授前端开发
- https://www.udemy.com/course/web-design-for-beginners-real-world-coding-in-html-css/
HTML 和 CSS 的好评初学者课程
- https://egghead.io/playlists/css-fundamentals-238ce697
适合初学者的 CSS
- https://www.coursera.org/specializations/web-design
HTML 和 CSS 的另一门好课程

### JavaScript

- https://www.udemy.com/course/javascript-basics-for-beginners/
- https://www.udemy.com/course/javascript-for-beginners-introduction-learn-fast-easy/
- https://www.udemy.com/course/modern-javascript/
- https://www.coursera.org/learn/javascript-jquery-json
- https://www.edx.org/course/javascript-introduction
- https://www.edx.org/course/programming-for-the-web-with-javascript

### VS Code

- https://egghead.io/browse/tools/vscode
这是您了解更多关于 IDE 和 VsCode 的好地方。

### Git

- https://www.udemy.com/course/git-going-fast/
- https://gist.github.com/nicowilliams/a6e5c9131767364ce2f4b3996549748d
- https://egghead.io/browse/tools/git
- https://www.udemy.com/course/git-complete/

### NodeJS

- https://www.w3schools.com/nodejs/default.asp
- https://nodejs.dev/learn/introduction-to-nodejs
- https://www.youtube.com/watch?v=TlB_eWDSMt4
- https://www.youtube.com/user/programmingwithmosh
- https://www.udemy.com/course/the-complete-nodejs-developer-course-2/

### npm

- https://www.udemy.com/course/understanding-npm/
- https://www.tutorialspoint.com/nodejs/nodejs_npm.htm
- https://www.w3schools.com/nodejs/nodejs_npm.asp

### HTTP 协议
- https://egghead.io/courses/understand-the-basics-of-http

### ReactJS

- https://reactjs.org/tutorial/tutorial.html
这是最好的起点。React 网站有一个很棒的简单教程来教授基础知识。
- https://reactjs.org/community/courses.html
然后看看这个。
- https://www.w3schools.com/react/
- https://egghead.io/courses/the-beginner-s-guide-to-react

### Chrome 开发者工具

- https://egghead.io/browse/tools/chrome-devtools

### GitHub
- https://egghead.io/browse/platforms/github

## 结论

写这篇文章的动机是帮助没有编程背景的热心人学习软件开发，尤其是 Web 前端开发。这里讨论的内容是您在学习任何库或框架之前应该学习的基本知识。在选择任何高级教育材料之前，您需要非常了解 JavaScript + HTML + CSS。然后一切都变得容易多了，你会很快学会任何前端框架。

谢谢阅读！

![](https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/my.png)