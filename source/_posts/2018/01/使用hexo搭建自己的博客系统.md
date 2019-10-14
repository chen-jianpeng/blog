---
title: 使用hexo搭建自己的博客系统
date: 2018-01-10 11:52:28
tags: [hexo]
categories: [阶段总结, 项目开发]
toc: true
thumbnail: images/使用hexo搭建自己的博客系统.jpg
---

找工作的时候经常会看到这样一句话，“请附上博客、github 地址”。所以我下定决心要整一个博客玩玩。给自己加点料。自己写一个网站成本比较高，要去买服务器，买域名等等，太麻烦。在前辈的推荐下，学习了使用 hexo 搭建博客，感觉很方便。作为我个人微博的开幕首秀，就先写写 hexo 的上手使用吧。

<!-- more -->

首先先来看一下 hexo 官网的介绍

![hexo官网截图](hexo.jpg)

快速、简洁、高效。直击痛点，学起来。

## 一. 所需环境

首先得安装 node、git，从官网上下载直接安装，一直下一步就好了，没啥可说的。

## 二. 安装 hexo

命令行执行如下命令，全局安装 hexo，这样你就可以使用 hexo 命令了

```shell
npm install hexo-cli -g
```

## 三. 初始化建站

在一个空文件夹下执行下面的命令，完成建站。注意一定是空文件夹，不然会报错。

```shell
hexo init
```

或者也可以执行下面的命令，folder 就是你的项目文件夹。

```shell
hexo init <folder>
```

然后进入项目根目录，安装依赖的模块

```shell
npm install
```

## 四. 参数配置

配置项在项目根目录下的\_config.yml 文件中，主要是对博客的基本信息等进行配置。配置信息很多,具体内容你可以看一下[官网](https://hexo.io/zh-cn/docs/configuration.html)的说明，我只讲一下我用到的几点吧

### 主题配置

你可以到[官网](https://hexo.io/themes/)下载你想要的主题，然后将下载好的文件解压后放到根目录的 themes 文件夹下，然后修改配置文件的 theme，就配置好主题了。

```yml
theme: hexo-theme-Anisina-master
```

### 部署配置

首先你得到你的 github 上新建一个仓库，项目名字为[yourName].github.io。yourName 是你的 github 名字。

_注意项目名称一定要这样命名，不然就无法访问了。_

然后配置文件里按照如下配置。repository 是你的仓库地址

```yml
deploy:
  type: git
  repository: https://github.com/123sky/123sky.github.io.git
  branch: master
```

然后你需要安装 hexo-deployer-git

```shell
npm install hexo-deployer-git -S
```

### 图片配置

写文章的时候不免需要引用图片，所以安装 hexo-asset-image，然后当你新建文章的时候，就会自动生成一个资源文件夹，把你的图片放到文件夹中，然后就可以的 md 文件中引用了。

首先确保你安装了 hexo-asset-image 依赖

```shell
npm install hexo-asset-image -S
```

然后新建文章后，文件结构变成了如下

![文章文件夹结构](structure.png)

你会发现每个文章都会有一个资源文件夹。然后再你的文章中通过如下方式进行图片的引用

```md
![hexo官网截图](hexo.jpg)
```

## 五. 开始你的写作表演

首先要创建文章,layout 表示你的文章布局，title 就是你的文章文件的名字（文章的名字是在文件中定义的。）。

```shell
  hexo new [layout] <title>
```

然后就可以在 source/\_posts/的相应文件夹中找到刚刚创建的文件进行文章的编辑了。

## 六. 本地服务

你可以先起一个本地服务来预览效果。确保安装了 hexo-server 依赖。

```shell
npm install hexo-server --save
```

然后通过下面的命令启动服务，就可以进行预览了

```shell
hexo server
或
hexo s
```

## 七. 发布部署

预览发现没有问题之后就可以发布部署了，执行如下命令

```shell
hexo generate
hexo deploy
或
hexo g -d
```

控制台提示成功之后，你就可以访问 123sky.github.io 来访问你的线上博客了。（123sky 是博主的 github 名字）

## 八. 主题文件修改

从网上下载的主题有时候可能不满足我们的需求，我们也可以自己修改，但修改的时候一定要注意需要 hexo clean 一下，然后重新 hexo g -d，不然线上将不会生效。

## 九. 新建导航页面

使用下面的命令创建导航页面,source 目录下面会增加一个叫 NAME 的文件夹，给里面的 index.md 文件中的 layout 增加一个布局，然后就可以在导航的布局文件中，通过 site.pages 获取相应的导航信息了。

```shell
hexo new page "NAME"
```

## 十. 评论系统

评论系统我选择的是“来必力”（来自韩国的），首先要注册账号，然后到管理页面-代码管理将代码粘贴到项目文件中。

![来必力](laibili.png)

### 结束

开启博客之旅吧
