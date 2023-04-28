---
title: "Web LLM👉让你在浏览器中体验基于 LLM 的聊天机器人"
date: 2023-04-16T21:39:47+08:00
lastmod: 2023-04-16T20:39:47+08:00
keywords: ["chatgpt", "chatgpt 开源项目",'chatgpt 自建','web LLM']
description: "Web LLM👉让你在浏览器中体验基于 LLM 的聊天机器人"
tags: [ChatGPT]
categories: [ChatGPT,教程]
author: "howie.hu"
---

> 这会是一个关于 ChatGPT 的系列文章，主要记录日常使用 ChatGPT 的感想和相关信息以及有趣的开源项目，然后这些信息我都会汇总到一个 ChatGPT 信息群，有兴趣的朋友可以文末加入 🥳。

`Web LLM` 将大型语言模型和基于 `LLM` 的聊天机器人引入 `Web` 浏览器。让一切都在浏览器内运行而无需服务器支持（使用 `WebGPU` 加速）。

这无疑产生了许多有趣的机会，这样做可以为每个人构建 `AI` 助手，还可以在享受 `GPU` 加速的同时实现隐私保护。项目相关信息如下：

- 开源地址：[https://github.com/mlc-ai/web-llm](https://github.com/mlc-ai/web-llm "https://github.com/mlc-ai/web-llm")
- 支持模型：[lmsys/vicuna-7b-delta-v0](https://huggingface.co/lmsys/vicuna-7b-delta-v0 "lmsys/vicuna-7b-delta-v0")（微调 `LLaMA`，号称能达到 `GPT-4` 的 90%性能）

这个项目 `04-14` 才开源，很多地方还没有很完善，如文档或者运行示例等，但是在浏览器中运行实在是吸引人，正好手头有一台 `M1` 的 `MacOS`，所以赶紧来体验一波。

## 在线体验

官网直接给了一个 `Apple` 芯片的 `Mac` 电脑本地使用的例子，步骤简单，如下：

- 下载 [Chrome Canary](https://mlc.ai/web-llm/ "Chrome Canary")，目的是为了体验最新版的 `WebGPU` 功能（也可以试用最新的 Chrome 113）
- 安装好之后，命令行启动 - 记得启动前设置好代理，方便下载模型参数 - `/Applications/Google\ Chrome\ Canary.app/Contents/MacOS/Google\ Chrome\ Canary --enable-dawn-features=disable_robustness`
- 开始体验！

![web-llm-demo](https://images-1252557999.file.myqcloud.com/uPic/web-llm-demo.jpg)

等待下载完毕，就可以直接使用了：

![llm-online-demo-01](https://images-1252557999.file.myqcloud.com/uPic/llm-online-demo-01.jpg)

可以看到，明目张胆地胡乱介绍我们的李白，测了下写代码还是能行的。

## 本地体验

这块官方也还没有说怎么本地启动运行，我是个人觉得因为是基于浏览器，所以本地运行应该就是启动一个网站。

测试也很方便，我就直接把相关源码 clone 下来，然后启动，发现果然可行，看过程如下：

```shell
git clone https://github.com/mlc-ai/web-llm

# switch branch
cd web-llm
git checkout -b gh-pages origin/gh-pages
cd docs

# start
docker run --restart always  --name docker-web-llm -p 8060:80 -d -v "`pwd`:/usr/share/nginx/html" nginx
```

启动浏览器：

```shell
/Applications/Google\ Chrome\ Canary.app/Contents/MacOS/Google\ Chrome\ Canary --enable-dawn-features=disable_robustness
```

在浏览器输入：`http://127.0.0.1:8060/`， 即可在你的本地体验 `vicuna-7b` 模型了，相当简单：

![llm-local](https://images-1252557999.file.myqcloud.com/uPic/llm-local.jpg)

## 说明

至此，线上线下体验 `Web LLM` 至此结束，有兴趣的欢迎来尝试交流。也感谢你阅读到这里，如果此文对你有帮助，欢迎转发点赞。

👬🏻 朋友，都看到这了，确定不关注一下么 👇

熟悉我这个号的朋友应该都知道我写了一年多的周刊，因此我经常会浏览相关信息，如果你也对 ChatGPT 感兴趣，希望及时接收到 ChatGPT 相关信息如：

- 最新进展
- 使用指南
- 各种有趣的项目

那么可以加入我创建的 `ChatGPT` 交流群，虽然可能比较技术向，但是欢迎任何人加入交流（加我微信申请加入）
