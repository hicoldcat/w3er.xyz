---
title: Jenkins构建Docker并自动更新阿里云ACK（k8s）
date: 2022-04-21T10:04:09.162Z
author: 李留白
weight: 1
categories:
  - 技术文章
  - 运维
featuredImage: https://cdn.jsdelivr.net/gh/hicoldcat/assets@main/img/20220421171254.png
lastmod: 2022-04-21T11:08:04.721Z
---

## Jenkins如何构建Docker镜像，推送到阿里云镜像仓库，并且使用kubectl命令自动更新升级阿里云容器镜像器群kubernetes(k8s) ACK上的镜像

### 1、在Jenkins打包机上安装kubectl


### 2、Jenkins上创建Job


### 3、配置Jenkins打包参数


### 4、生成k8s集群容器yml文件


### 5、增加build.sh(以nest.js项目为例)


### 6、配置kubectl更新命令


### 7、测试效果