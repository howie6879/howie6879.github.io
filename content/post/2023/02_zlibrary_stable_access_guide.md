---
title: "Z-library 稳定访问指南"
date: 2023-02-20T21:39:47+08:00
lastmod: 2023-02-28T20:39:47+08:00
keywords: ["zlib", "Z-library", "访问",'Z-library 下载','电子书下载']
description: "Z-library 稳定访问指南"
tags: [效率]
categories: [工具,教程]
author: "howie.hu"
---

> Z-library 是一个影子图书馆，用户可在此网站上下载期刊以及各种类型的书籍。

![zlib](https://images-1252557999.file.myqcloud.com/uPic/zlib.jpg)

近期，常用 `Z-library` 的各位应该都知晓，`Z-library` 近期被美国执法机构查封，基本上之前的访问地址都被停掉了（现在也算回归了，但是门槛高了起来，有必要探索更多的访问形式）。

我花了点时间了解了下如何更方便的访问 `Z-library` ，最后总结了以下方式分享出来以供大家参考（只推荐个人认为稳定方便的方式），也欢迎评论区补充。

## 官方

- [singlelogin.me](https://singlelogin.me/)：
	- 直接打开官方网址（可能需要用科学上网才能访问），使用你之前注册的 `Z-library` 账号登录：

	- ![singlelogin](https://images-1252557999.file.myqcloud.com/uPic/singlelogin.png)

	- 点击继续，就会跳转到你的专属域名，但是不要分享出去。
- Tor 浏览器：
	- 下载 [Tor 浏览器](https://www.torproject.org/zh-CN/download/)，打开建立网桥（最好科学上网，保证访问速度）
	- 直接访问[对应地址](http://loginzlib2vrak5zzpcocc3ouizykn6k5qecgj2tzlnab5wcbqhembyd.onion/)就可以
- 自建 TG 机器人：官方的 Telegram bot 被封禁地很快，所以官方出了自建 TG 机器人的教程，自建后绑定 Z-Library，独立使用非常方便
	- 基于第一个[singlelogin.me](https://singlelogin.me/)方式获取专属域名，打开网址下滑到网页底部，右侧有个 TG 标识，点击就可以看到教程了，就几个命令，非常方便
	- ![zlib_tg](https://images-1252557999.file.myqcloud.com/uPic/zlib_tg.jpg)
- 安卓官方客户端，下载见：蓝奏云盘（A 姐分享）https://abskoop.lanzoue.com/imBOP0fz7l1a 密码：2c8w

## 第三方

- [annas-archive](https://zh.annas-archive.org/)：安娜的档案，⭐️ `Z-Library、Library Genesis、Sci-Hub` 影子图书馆搜索引擎：书籍、论文、漫画、杂志
	- ![annas-archive](https://images-1252557999.file.myqcloud.com/uPic/annas-archive.jpg)
- [search.yibook.org](https://search.yibook.org/)：中文友好的电子书搜索引擎
	- ![s_yibook](https://images-1252557999.file.myqcloud.com/uPic/s_yibook.jpg)
- [mirror.yibook.org](https://mirror.yibook.org/)：镜像网站
	- ![yibook](https://images-1252557999.file.myqcloud.com/uPic/yibook.jpg)
- [ooopn.com/tool/zlibrary](https://www.ooopn.com/tool/zlibrary/)：镜像网站
	- ![ooopn](https://images-1252557999.file.myqcloud.com/uPic/ooopn.jpg)
- [clibrary.cn](https://clibrary.cn/)：2022年9月建立的中文数字图书馆,图书来自 `Z-Library`，具有不错的UI
	- ![clibrary](https://images-1252557999.file.myqcloud.com/uPic/clibrary.jpg)

## 开源

>2022 年底，Z-library 被封
>2023 年初，Z-library 回归

被封到回归这段时间，开源界为我们折腾出了 `31T` 且永不下线的电子图书馆。

是的，每个人都能用！这里需要感谢一个叫 `Anna` 的网友爬取并分享了 `Z-library` 上的所有资源（31T），这就让我们有了免费的书籍数据库，最具代表性的开源项目就是👉 [book-searcher](https://github.com/book-searcher-org/book-searcher)，核心逻辑就是：

>  基于 Anna's Archive 数据库做成的索引，通过搜索获得 IPFS CID，最后通过 IPFS 网关下载

总之**Anna's Archive 数据库索引 + book-searcher**已经满足一切搜索需求

更详细的来龙去脉建议各位阅读这份文档：[Awesome-Zlibrary](https://github.com/runningcheese/Awesome-Zlibrary)。
