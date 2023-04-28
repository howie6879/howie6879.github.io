---
title: "基于 Azure OpenAI 免费注册使用 ChatGPT 教程"
date: 2023-04-18T21:39:47+08:00
lastmod: 2023-04-18T20:39:47+08:00
keywords: ["chatgpt", "chatgpt-plus", "chatgpt 注册",'chatgpt 登录','chatgpt 信用卡', 'Azure Openai', '微软 ChatGPT']
description: "基于 Azure OpenAI 免费注册使用 ChatGPT 教程"
tags: [ChatGPT]
categories: [ChatGPT,教程]
author: "howie.hu"
---

> 这会是一个关于 ChatGPT 的系列文章，主要记录老胡日常使用 ChatGPT 的思考和一些有趣的开源项目，然后这些信息我都会汇总到一个 ChatGPT 信息群(免费，目的是为了交流)，有兴趣的朋友可以联系我进群 🥳。

目前，大部分朋友使用 ChatGPT 还是挺麻烦的，需要原生 IP 代理注册、扫码、搭建代理服务让国内可以访问等等（详细可以看之前的文章：ChatGPT 从注册到自建应用），还要小心翼翼防止被封，确实比较麻烦。

今天和大家介绍下我新摸索出来的 ChatGPT 使用方式：**基于 Azure OpenAI 免费注册使用 ChatGPT 教程**。

`Azure OpenAI` 是由微软和 OpenAI 联合开发的一项人工智能业务，其可以在 `Azure` 云平台上使用并对外提供了针对语言、视觉、知识等多个方面的人工智能 API。

03-09 号，[ChatGPT is now available in Azure OpenAI Service](https://azure.microsoft.com/en-in/blog/chatgpt-is-now-available-in-azure-openai-service/ "ChatGPT is now available in Azure OpenAI Service") 这篇博客宣布 ChatGPT 在 Azure OpenAI 服务中提供预览版，针对中国区一样可以申请试用：

- 中国区用户直接访问&申请 `ChatGPT` 服务
- 注册 Azure 即送 200 刀
- 一年常用服务的免费使用期（这块我不清楚我有没有弄到 Azure OpenAI 的免费使用，不过不管了，价格也和 OpenAI 一样，但是省事）

开始前看下我最终自建的 GPT 机器人使用效果图（基于开源项目-ChatGPT-Next-Web）：

![ChatGPT-Next-Web](https://images-1252557999.file.myqcloud.com/uPic/Xnip2023-04-20_00-31-26.jpg)

## 注册

开始前请做好以下准备：

- 一个微软账号
- 一张 visa 信用卡，Azure 要求你绑定信用卡
- 相关公司信息如：公司邮箱、地址、电话等信息

先进行注册，打开 [Azure 云平台官网](https://azure.microsoft.com/zh-cn/ "Azure 云平台官网")，点击**免费试用**：

![](https://images-1252557999.file.myqcloud.com/uPic/reg_azure.jpg)
点击后你需要绑定微软账户然后填写个人资料：

![](https://images-1252557999.file.myqcloud.com/uPic/ziliao.jpg)

填写完资料就是绑定银行卡，这块照着填就行了，然后会告诉你送 200\$，进入控制台，右上角可以看到额度：

![](https://images-1252557999.file.myqcloud.com/uPic/azure_200.jpg)

## 申请 OpenAI

先进入 [azure 控制台](https://portal.azure.com/?quickstart=true#home "azure 控制台") 搜索订阅：

![](https://images-1252557999.file.myqcloud.com/uPic/XvXGAW.png)

点击进入后如果有免费试用，就直接复制免费使用的订阅 ID，如果没有（我就没有，不清楚是现在没有了还是我的账户是老账号）就自己新增一个订阅，如下：

![](https://images-1252557999.file.myqcloud.com/uPic/sub.jpg)

请复制好保存好这个订阅 ID，后面申请需要。接下来让我们申请 `OpenAI` 服务，一样在顶部进行搜索：

![](https://images-1252557999.file.myqcloud.com/uPic/openai_search.jpg)

因为这个服务是需要申请的，所以直接点击提示的链接进行申请：

![](https://images-1252557999.file.myqcloud.com/uPic/sub_openai.jpg)

也就是这个链接：[https://aka.ms/oai/access](https://aka.ms/oai/access "https://aka.ms/oai/access")：

![](https://images-1252557999.file.myqcloud.com/uPic/akaoai.jpg)

耐心将这 25 个问题回答完毕即可，需要注意的点：

- 使用公司邮箱
- 填公司信息尽量准确
- 第四个问题一定要用订阅 ID，我就是填错了导致申请两次

![](https://images-1252557999.file.myqcloud.com/uPic/q4.jpg)

好，填写成功后，一般一两天就会收到验证邮件：

![](https://images-1252557999.file.myqcloud.com/uPic/Snipaste_2023-04-19_18-25-29.jpg)
点击验证邮箱即可，验证成功后再等两三天就能收到 `Onboarding` 邮件，代表申请通过，然后就可以使用 `Chatgpt3.5& Dalle-2`：

![](https://images-1252557999.file.myqcloud.com/uPic/Snipaste_2023-04-19_18-27-04.jpg)
至此，恭喜你，成功申请了微软的 `OpenAI` 服务资格。

## 配置 OpenAI

有了资格就可以直接创建 Azure OpenAI 服务了，进入[OpenAI 配置页面](https://portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesOpenAI "OpenAI 配置页面")：

![](https://images-1252557999.file.myqcloud.com/uPic/Snipaste_2023-04-19_18-28-24.jpg)

一路确认往下就开启成功了，然后在控制台主页就能看到开启的服务：

![](https://images-1252557999.file.myqcloud.com/uPic/Snipaste_2023-04-19_18-31-47.jpg)
点击进入 `OpenAI` 服务，确认服务创建成功之后，选择 **模型部署(model deployments)**，即可配置要用的模型：

![](https://images-1252557999.file.myqcloud.com/uPic/Snipaste_2023-04-19_18-34-42.jpg)

申请好了就可以直接点击可以到 [ChatGPT 操场(预览版)](https://oai.azure.com/portal/chat "ChatGPT 操场(预览版)") 体验使用了：

![](https://images-1252557999.file.myqcloud.com/uPic/Snipaste_2023-04-19_18-37-17.jpg)

## 申请 API Key

从 `Azure` 面板中点击我们的 `OpenAI` 资源，点击 `manage keys`：

![](https://images-1252557999.file.myqcloud.com/uPic/Snipaste_2023-04-19_18-41-47.jpg)

点击即可获取**密钥 1**，接下来就是利用 `API` 开发自己的应用了。

我们先简单调用一下看能不能行：

![](https://images-1252557999.file.myqcloud.com/uPic/Xnip2023-04-19_23-34-35.jpg)

可以看到成功发起了请求，命令行也可，打开终端：

```shell
curl https://YOUR_RESOURCE_NAME.openai.azure.com/openai/deployments/YOUR_DEPLOYMENT_NAME/chat/completions?api-version=2023-03-15-preview \
  -H "Content-Type: application/json" \
  -H "api-key: YOUR_API_KEY" \
  -d '{"messages":[{"role": "system", "content": "You are a helpful assistant."},{"role": "user", "content": "hello"}]}'
```

不出意外就可以在终端得到你想要的返回结果了。

## 代理

目前开源社区基于 `ChatGPT API` 衍生出了很多有意思的项目，但有个问题是大部分的项目是不支持 `Azure OpenAI` 访问形式的。

仔细看上一段我给的使用介绍，可以看出请求方式上是有些差别的，所以在使用其他应用前，我们需要做一个代理转换，很多乐于助人的朋友已经将这个事情给做了，相关项目如下：

- [stulzq/azure-openai-proxy](https://github.com/stulzq/azure-openai-proxy "stulzq/azure-openai-proxy")：Azure OpenAI 服务代理，将 OpenAI 官方 API 请求转换为 Azure OpenAI API 请求，支持所有型号，支持 GPT-4。
- [diemus/azure-openai-proxy](https://github.com/diemus/azure-openai-proxy "diemus/azure-openai-proxy")：一个 Azure OpenAI API 的代理工具，可以将一个 OpenAI 请求转化为 Azure OpenAI 请求，方便作为各类开源 ChatGPT 的后端使用。同时也支持作为单纯的 OpenAI 接口代理使用，用来解决 OpenAI 接口在部分地区的被限制使用的问题。
- [cf-openai-azure-proxy](https://github.com/haibbo/cf-openai-azure-proxy "cf-openai-azure-proxy")：基于 Cloudflare 代理 OpenAI 的请求到 Azure OpenAI Serivce

诸位只需要根据各自需求来选择上述项目做代理即可，这里我选第一个试试看，项目 README 里面都说好了如何使用（要装好 Docker）：

```shell
docker run -d -p 8010:8080 --name=azure-openai-proxy \
  --env AZURE_OPENAI_ENDPOINT=your_azure_endpoint \
  --env AZURE_OPENAI_API_VER=2023-03-15-preview \
  --env AZURE_OPENAI_MODEL_MAPPER==gpt-3.5-turbo=your_model_id \
  stulzq/azure-openai-proxy:latest
```

启动成功后，就可以用 OpenAI 接口代理的形式使用 Azure OpenAI Serivce 了，请求一个看看效果：

```shell
curl --location --request POST 'localhost:8010/v1/chat/completions' \
-H 'Authorization: Bearer <Azure OpenAI Key>'' \
-H 'Content-Type: application/json' \
-d '{
    "max_tokens": 1000,
    "model": "gpt-3.5-turbo",
    "temperature": 0.8,
    "top_p": 1,
    "presence_penalty": 1,
    "messages": [
        {
            "role": "user",
            "content": "Hello"
        }
    ],
    "stream": true
}'
```

还支持流式输出呢，非常棒。

## 部署应用

至此，我们已经有了中国区随意使用的 Azure OpenAI 服务，并且基于一些开源项目兼容了 OpenAI 原本的请求形式，也就是说我们申请的服务可以直接应用于任何 ChatGPT 开源项目 🥳

先基于 [ChatGPT-Next-Web](https://github.com/Yidadaa/ChatGPT-Next-Web "ChatGPT-Next-Web") 搭建一个私人 ChatGPT 网页应用吧：

```shell
docker run -d -p 3000:3000 \
   -e BASE_URL="http://127.0.0.1:8010" \
   -e OPENAI_API_KEY="sk-xxxx" \
   -e CODE="页面访问密码" \
   --net=host \
   yidadaa/chatgpt-next-web
```

最后看效果：

![ChatGPT-Next-Web](https://images-1252557999.file.myqcloud.com/uPic/Xnip2023-04-20_00-31-26.jpg)

## 说明

熟悉我这个号的朋友应该都知道我写了一年多的周刊，因此我经常会浏览相关信息，如果你也对 ChatGPT 感兴趣，希望及时接收到 ChatGPT 相关信息如：

- 最新进展
- 使用指南
- 各种有趣的项目

那么可以加入我创建的 `ChatGPT` 交流群，虽然可能比较技术向，但是欢迎任何人加入交流（加我微信申请加入）

