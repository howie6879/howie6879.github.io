---
title: "ChatGPT 从注册到自建应用"
date: 2023-03-30T21:39:47+08:00
lastmod: 2023-03-30T20:39:47+08:00
keywords: ["chatgpt", "chatgpt-plus", "chatgpt 注册",'chatgpt 登录','chatgpt 信用卡', 'Depay']
description: "ChatGPT 从注册到自建应用"
tags: [ChatGPT]
categories: [ChatGPT,教程]
author: "howie.hu"
---

> 这会是一个关于 ChatGPT 的系列文章，主要记录日常使用 ChatGPT 的感想和相关信息以及有趣的开源项目，然后这些信息我都会汇总到一个 ChatGPT 信息群，有兴趣的朋友可以文末加入🥳

## 介绍

`ChatGPT` 是由 `OpenAI` 开发的一种大型自然语言处理模型，可以生成人类般的自然语言响应，让对话更加自然、流畅和智能。相较于传统的聊天机器人，`ChatGPT` 的应用场景更为广泛，不仅可以进行简单的问答交互，还支持更加复杂的对话环境，提供更加深入的信息和建议。

## 注册

如果您想在中国地区注册并使用 `ChatGPT`，可以按照以下步骤操作：

1.  访问 `OpenAI` 的[平台官网](https://platform.openai.com/)。
2.  点击页面中间偏右下的 `Sign Up` 按钮，进入注册页面。
3.  填写个人信息，包括邮箱地址和密码，并同意《Terms of Service》和《Privacy Policy》。
4.  完成验证邮件的收取和验证，即可开始使用 `ChatGPT`。

需要注意的是，由于某些原因，`OpenAI` 当前无法在中国地区提供完全的服务。

接下来将详细说下如何注册使用 `ChatGPT`，请先做好如下准备工作：

- 代理：推荐美日韩新加坡印度即可，不要用香港和台湾
- 国外手机号：推荐使用 [sms-activate.org](/Applications/Joplin.app/Contents/Resources/app.asar/sms-activate.org "sms-activate.org")
- 邮箱账号：推荐 `gmail`

准备好代理并打开全局模式，可以在 **[百度搜我的ip](https://www.baidu.com/s?wd=%E6%88%91%E7%9A%84ip)** 进行验证：

![jGRWOq](https://images-1252557999.file.myqcloud.com/uPic/jGRWOq.png)
成功后直接访问 `OpenAI` 的[平台官网](https://platform.openai.com/)，建议使用谷歌浏览器的无痕模式。

进入后点击页面中间偏右下的 `Sign Up` 按钮，进入注册页面，填写邮箱，设置密码，会提示发给你一封邮件，去邮箱查看即可：

![gpt_mail](https://images-1252557999.file.myqcloud.com/uPic/gpt_mail.jpg)
接下来填写 `OpenAI` 要求的姓名信息，然后进行手机号验证，此时打开 [sms-activate.org](/Applications/Joplin.app/Contents/Resources/app.asar/sms-activate.org "sms-activate.org") 进行国外手机号验证（第一次用请右上角点击充值2$即可，支持支付宝）：

![sms_demo](https://images-1252557999.file.myqcloud.com/uPic/sms_demo.png)

点击左侧买入，将电话号码填入 `OpenAI` 的电话输入框，稍等一会在 **sms-activate** 就能收到验证码：

![sms_valid](https://images-1252557999.file.myqcloud.com/uPic/sms_valid.jpg)
输入验证码，此时你就会注册成功并自动跳转到平台首页，点击右上角可查看到官方送了我们 5$(老用户有 18) 额度来使用 `API`：

![openai_usage](https://images-1252557999.file.myqcloud.com/uPic/openai_usage.jpg)

恭喜你，现在已经成功有了自己的 `OpenAI` 账号，直接进入 https://chat.openai.com/ 就可以体验 `ChatGPT` 了：

![chat_demo](https://images-1252557999.file.myqcloud.com/uPic/chat_demo.jpg)

## 虚拟信用卡

为什么需要虚拟信用卡：

- 使用 `API` 进行开发绑定信用卡会活的更好的体验，请求次数有更宽松的权限（ `OpenAI` 不支持国内的信用卡）
- `ChatGPT Plus` 版本购买需要美国信用卡，可以有更快的速度以及体验最新的模型（比如现在的 ChatGPT 4）

接下来我将根据 `Depay` 来申请国外信用卡，据我所知有两种方法，`Depay` 方法弄完我才了解到一个更方便的方法，有兴趣的直接公众号加我好友私聊，这里就不写了。

使用前的概念介绍：

- KYC认证：Depay申请后，要做的就是KYC认证（也就是实名认证），国内用户可以用身份证和护照，反之添加大概$10即可
- 充值：Depay只支持 USDT 加密货币，所以需要自行从第三方购买币种

使用前请做好以下准备：

- 下载欧易并[注册](https://cnouyi.care/join/36959806)，先进行 `USDT` 充值（根据你的需要买入，买后会冻结 24h）：https://cnouyi.care/join/36959806
- 下载 `Depay`，苹果需要美国账号去 `APP Store` 下载，用安卓设备会比较方便
- PC、手机端都能翻墙

`Depay` 操作流程：

用手机号登录：

![depay_01](https://images-1252557999.file.myqcloud.com/uPic/depay_01.jpg)

然后进行手机和邮箱验证即可，登录后，你有两个选择：

- 进入**Me-KYC** 进行身份认证
- 直接在首页申请开卡（多交 10$）

![depay_02](https://images-1252557999.file.myqcloud.com/uPic/depay_02.jpg)

直接进入，填写相关信息，进行 `USDT` 转账，记得选择 `USDT-TRC20` 网络（这里我使用的是欧易转账）。

## 绑定 Open AI

恭喜你，现在你成功拥有了一张专属的海外信用卡，接下来你可以：

- 购买 Plug 体验 ChatGPT-4：登录进入 https://chat.openai.com 点击左下角的 Upgrade to Plus 即可
- 绑定 OpenAI信用卡：登录进入 [platform.openai.com](https://platform.openai.com/account/billing/payment-methods) 选择 Payment methods 填写信用卡即可

操作过程中，请一定要遵循以下建议：

- 一定要自己专属的上网梯子
- 使用浏览器隐私模式

![gpt-4-demo](https://images-1252557999.file.myqcloud.com/uPic/gpt-4-demo.jpg)

目标达成，开了会员正式体验 GTP-4 吧！

拿  `API Keys` 步骤：

- [进入 api-keys](https://platform.openai.com/account/api-keys)
- 点击 Create new secret key 即可获取私有的秘钥，可以构建自己的应用

## 使用 API

`ChatGPT` 的能力非常强，每天都有很多有想法的工程师基于 `ChatGPT` 贡献自己的项目，我这边列举几个：

- [chatgpt-web](https://github.com/Chanzhaoyu/chatgpt-web)：用 `Express` 和 `Vue3` 搭建的 `ChatGPT` 演示网页
- [chatgpt-mirror](https://github.com/yuezk/chatgpt-mirror)：基于 `gpt-3.5-turbo` 的 ChatGPT 镜像网站
- [bob-plugin-openai-translator](https://github.com/yetone/bob-plugin-openai-translator)：基于 ChatGPT API 的文本翻译、文本润色、语法纠错 Bob 插件，让我们一起迎接不需要巴别塔的新时代！
- [openai-translator](https://github.com/yetone/openai-translator)：基于 ChatGPT  API` 的划词翻译浏览器插件和跨平台桌面端应用
-  [bilingual\_book\_maker](https://github.com/yihong0618/bilingual_book_maker)：`bilingual_book_maker` 是一个 `AI` 翻译工具，使用 ChatGPT 帮助用户制作多语言版本的 `epub` 文件和图书。该工具仅适用于翻译进入公共版权领域的 `epub` 图书，不适用于有版权的书籍
- [周报生成器](https://weeklyreport.avemaria.fun/zh)：简单描述工作内容帮你生成完整周报
- [README 生成器](https://readme.rustc.cloud/zh)：帮你生成完整 Github README
- [邮件生成器](https://email-helper.vercel.app/)：几秒钟内生成多语言商务邮件
- [Teach Anything](https://www.teach-anything.com/)：几秒钟内得到想要的答案
- [聊天简化器](https://chat-simplifier.imzbb.cc/zh)：简化聊天记录内容
- [SiteExplainer](https://siteexplainer.vercel.app/)：输入网址，快速总结网站内容
- [Dear Aibby](https://www.dearaibby.com/)：来自新机器灵魂的衷心建议
- [TextSummarizer](https://text-summarizer-seven.vercel.app/)：在几秒钟内从文本生成摘要
- [chatgpt-vscode](https://github.com/mpociot/chatgpt-vscode)：支持  ChatGPT  的  `Visual Studio Code`  扩展，可以与  `ChatGPT`  配对编程
- [editGPT](https://chrome.google.com/webstore/detail/editgpt/mognjodfeldknhobgbnkoomipkmlnnhk)：利用 ChatGPT 做文案修改编辑
- [chatgpt-google-extension](https://github.com/wong2/chatgpt-google-extension)：显示  `ChatGPT`  响应和  `Google`  搜索结果的浏览器扩展
- [chatgpt-chrome-extension](https://github.com/gragland/chatgpt-chrome-extension)：将  `ChatGPT`  集成到互联网上的每个文本框中

上述工具全部都是基于 `ChatGPT` 的能力构建的上层应用，只要你有 `openai keys` 都可以玩转，我以 [chatgpt-web](https://github.com/Chanzhaoyu/chatgpt-web) 项目为例，先确保安装好 `Docker` 环境，这样让我们可以更方便地尝试任何应用，直接使用：

```shell
git clone https://github.com/Chanzhaoyu/chatgpt-web.git

docker build -t chatgpt-web .

docker run --name chatgpt-web --rm -it -p 127.0.0.1:3002:3002 --env OPENAI_API_KEY=your_api_key chatgpt-web
```

成品图：

![ChatGPT_demo](https://images-1252557999.file.myqcloud.com/uPic/ChatGPT_demo.jpg)

## 说明

感谢你阅读到这里，希望这篇文章能帮你创建自己的 `ChatGPT` 账号并绑定信用卡，如果操作过程中遇到任何问题，可以直接在公众号内部加我个人微信私聊我，我非常乐意解答你的问题。

熟悉我这个号的朋友应该都知道我写了一年多的周刊，因此我经常会浏览相关信息，如果你也对 `ChatGPT` 感兴趣，希望及时接收到 `ChatGPT` 相关信息如：

- 最新进展
- 使用指南
- 各种有趣的项目

那么可以加入我刚创建的 `ChatGPT` 交流群，虽然可能比较技术向，但是欢迎任何人加入交流：

![howie_chatgpt](https://images-1252557999.file.myqcloud.com/uPic/yTPBlU.png)

发文前我在一些群发了下看看一键，十分钟不到就一百多人了，看样子大家还是很感兴趣，欢迎加入，如果二维码过期欢迎加我好友进群。

各位，祝好！

