---
title: "三分钟将文集转成在线电子书"
date: 2021-03-22T20:35:47+08:00
lastmod: 2021-03-22T20:35:47+08:00
keywords: []
description: "不知不觉，确实有了一些产出"
tags: ["效率"]
categories: ["工具"]
author: "howie.hu"
image: /images/thumbs/h_64.jpg
---

下午对自己这几年写的一些博客做了些整理工作，整理完毕惊喜地发现，自己针对一些主题确实已经有了一些产出。

但是由于时间线的原因，一些主题文章的连贯性被破坏了，所以我有了将他们整理成在线电子书的想法。

题图就是我的最终成果，如果你也有类似需求，那你可以按照我的方法玩一玩。

## 要求

目前我可以产出以下三个比较明确的主题：

- k8s 学习笔记（最近投入在这块）
- 机器学习读书笔记
- Sanic 小书

说下我个人对在线电子书的要求：

- 首要自然是可以随时随地在线访问
- 可快速搭建，颜值在线
- 方便更新、交流等

我很容易地联想到了自己的博客，我的[博客](https://www.howie6879.cn/ "博客")是我大学时期（2016）年搭建的，期间从`github page`到`hexo`再到`hugo`：

![https://www.howie6879.cn/](https://images-1252557999.file.myqcloud.com/uPic/Xnip2021-03-21_22-54-50.jpg)

我完全可以使用我的博客用时间线将我的一些文章抽出来，然后单独再选择一个适合作为电子书的主题嵌到我的博客里面去，比如针对`Sanic`的小书，就对应`https://www.howie6879.cn/sanic_book`这样来映射。

## 搭建

我的博客目前一直使用的是[hugo](https://gohugo.io/getting-started/quick-start/ "hugo")进行搭建：

```shell
brew install hugo
```

安装及其简单，我现在要做的无非是选择一个电子书主题，然后将`sanic`文集集中起来，具体操作如下：

```shell
hugo new site sanic_book
```

此时会生成一个文件夹，如下：

```shell
tree -L 1
.
├── archetypes
├── config.toml
├── content
├── data
├── layouts
├── public
├── resources
├── static
└── themes
```

接下来要做的事找到一个迁移非常简便的`hugo`主题，我选择的是这款[hugo-book](https://themes.gohugo.io/hugo-book/ "hugo-book")，成本极低：

```shell
git init
git submodule add https://github.com/alex-shpak/hugo-book themes/book
cp -R themes/book/exampleSite/content .
```

复制的目录不用管太多，只需要关注`doc`即可：

```shell
├── _index.md
├── docs
│   ├── 01_skill
│   └── 02_appendix
└── menu
    └── index.md
```

这里我将`Sanic`文集分成两部分：

- 技巧
- 附录

接下来要做的就是讲以前的博文`md`文件复制过来就可以了，极其简单。最后，改动一下配置文件`config.toml`：

```shell
baseURL = "https://www.howie6879.cn/sanic_book"
languageCode = "en-us"
title = "Sanic-For-Pythoneer"
theme = 'book'

# Book configuration
disablePathToLower = true

[menu]
[[menu.before]]
  name = "老胡的储物柜"
  url = "https://www.howie6879.cn/"
  weight = 10

[[menu.before]]
  name = "微信公众号"
  url = "https://cdn.jsdelivr.net/gh/howie6879/oss/uPic/qrcode_for_gh_3f02ace79dfb_258.jpg"
  weight = 20

[[menu.before]]
  name = "Github"
  url = "https://github.com/howie6879"
  weight = 30

[params]
  BookComments = true
```

启动：

```
cd sanic_book
hugo server

# 输出
Environment: "development"
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at http://localhost:1313/sanic_book/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```

很方便也很简单，于是我一次性整了三本在线电子书的网页版。

### K8s

![k8s](https://images-1252557999.file.myqcloud.com/uPic/Xnip2021-03-21_23-13-14.jpg)

在接下来的云原生时代，`k8s`是必须要掌握的，我对`k8s`感兴趣来自对一站式机器学习云开发平台的调研，目前已经在这上面花了不少时间，我将会记录自己在`k8s`上所有的学习心得，从简单到深入，目前这块已经将相关文章开源形成了一个项目，详见[k8s_note](https://github.com/howie6879/k8s_note "k8s_note")。

### 机器学习

![ml_note](https://images-1252557999.file.myqcloud.com/uPic/Xnip2021-03-21_23-13-50.jpg)

这块是我目前的工作方向，主要工作就是带领一个团队解决游戏中的风控问题，比如外挂、小号、广告等。刚毕业时候做了一年多后端，但在实际工作中一些问题涉及到机器学习，于是在解决问题的过程中慢慢地喜欢上了机器学习，于是就开始学习这块。我目前更多地还是将机器学习一些思路引入到实际问题中，这个还需要持续更新。

### Sanic

成果如下：

![sanic](https://images-1252557999.file.myqcloud.com/uPic/Xnip2021-03-21_23-11-10.jpg)

这是一本`sanic`开源小书，应该是国内第一本。我 17 年那时候特喜欢这框架，源码也读了一遍，不出意外也贡献了几个`PR`，于是就结合工作实践写了这本开源小书。

## 说明

三本电子书在线访问地址如下：

- sanic_book：[https://www.howie6879.cn/sanic_book/](https://www.howie6879.cn/sanic_book/ "https://www.howie6879.cn/sanic_book/")
- ml_book：[https://www.howie6879.cn/ml_book/](https://www.howie6879.cn/ml_book/ "https://www.howie6879.cn/ml_book/")
- k8s_note：[https://www.howie6879.cn/k8s/](https://www.howie6879.cn/k8s/ "https://www.howie6879.cn/k8s/")

第一本已经完结，`ml`和`k8s`希望今年可以完结（好像整了个 flag??），加油吧。 

<div align=center><img width="20%" src="https://cdn.jsdelivr.net/gh/howie6879/oss/uPic/qrcode_for_gh_3f02ace79dfb_258.jpg" /></div>