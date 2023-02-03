---
title: "打造一个干净且个性化的公众号阅读环境"
date: 2021-05-09T20:35:47+08:00
lastmod: 2021-05-09T20:35:47+08:00
keywords: []
description: "构建一个多源（公众号、RSS）、干净、个性化的阅读环境"
tags: [效率,Ruia,Liuli]
categories: [Python,工具,项目]
author: "howie.hu"
image: /images/thumbs/h_68.jpg
---

##  背景

一个月前的下班时间，当时我正在看公众号，准备感受下今天的技术文章，不愉快的事情发生了，竟然一连好几篇文档点进去都是**广告**，至今想起，那种心情仍旧挥散不去。

于是我产生了一个想法，为什么不构建一个干净且个性化的个人阅读环境呢？作为一名微信公众号的重度用户，公众号一直被我设为汲取知识的地方。随着使用程度的增加，现在公众号里面的**广告问题**非常影响我的阅读体验。

假设你关注的公众号有十来个，若一个公众号两周接一次广告，理论上你会面临二十多次广告，实际上会更多，运气不好的话一天刷下来都是广告也不一定。若你关注了二三十个公众号，那很难避免现阶段公众号环境的广告轰炸。

更可恶的是，大部分的广告，无不是**贩卖焦虑，营造消极气氛**，实在无法忍受且严重影响我的心情。但有些公众号写的文章又确实不错，那怎么做可以不看广告只看文章呢？如果你在公众号阅读体验下深切感受到对于广告的无奈，那么这个项目就是你需要的。

我收集了一百多篇（个人力量有限，样本需要更多有志之士一起收集）微信广告文章，大家可以看看里面的关键字感受一下：

![2c_ads_word_cloud](https://images-1252557999.file.myqcloud.com/uPic/2c_ads_word_cloud.jpg)

所以我需要**构建一个多源（公众号、RSS）、干净、个性化的阅读环境**，这个环境满足以下三个核心要求：

- **去广告！**
- 考虑到以公众号为主，阅读环境还是在微信生态里面，可以基于**企业微信号**做文章分发
- 多源，除了微信公众号，还可以定订阅其他市面上优质内容源

最终效果大家可以先感受一下：

<img src="https://images-1252557999.file.myqcloud.com/uPic/m3nJ61.png" alt="img" style="zoom: 25%;" />

如果诸位觉得有用，请继续阅读。

## 思路

至此，已经可以大概看到这个项目的面貌了，接下来要做的就是怎么实现一个满足上述要求的项目。

我的思路很简单，大概流程如下：

![2c_process](https://images-1252557999.file.myqcloud.com/uPic/2c_process.svg)

简单解释一下：

- **采集器**：监控各自关注的公众号或者博客源，最终构建`Feed`流作为输入源；
- **分类器**（广告）：基于历史广告数据，利用机器学习实现一个广告分类器（可自定义规则），然后给每篇文章自动打上标签再持久化到`MongoDB`；
- **分发器**：依靠接口层进行数据请求&响应，为使用者提供个性化配置，然后根据配置自动进行分发，将干净的文章流向微信、钉钉、TG甚至自建网站都行。

这样做就实现了干净阅读环境的构建，衍生一下，还可以实现个人知识库的构建，可以做诸如标签管理、图谱构建等，这些都可以在接口层进行实现。

## 开发

其实有了思路，只要有一定的编程基础，接下来的事情就很清晰了，我在这里不详细说开发过程以及代码怎么写，因为我都开源出来了哈。

![2C](https://images-1252557999.file.myqcloud.com/uPic/image-20210509155537337.png)

项目我取名为[2C](https://github.com/howie6879/2c)，含义是`Claen & Customize Content`，真正的开发时间前后大概花了我两周的业余时间，所以还是付出了我的一些心血，如果大家觉得不错，来个Star呗❤️。

这个项目最大的阻力就是广告样本的收集，这也决定`2C`项目的效果，所以我非常欢迎且期待大家参与进来，提交广告样本，具体请看[Issue_更多的广告样本](https://github.com/howie6879/2c/issues/4)。

## 使用

接下来重点说说怎么使用`2C`，此项目对于一些基础环境是有一点要求的，为了尽可能减少开发者部署使用的复杂度（特别是非Python开发者），因此我计划使用`Docker`进行调度运行，这样对用户的使用来说是比较方便的，请按照顺序执行以下`TODO`：

- [ ] 安装`Docker`：推荐直接使用[Docker 极速下载](https://get.daocloud.io/)
- [ ] 安装`MongoDB`：用于持久化，可手动或使用镜像安装
- [ ] 下载`2C`
- [ ] 配置`2C`
- [ ] 运行`2C`

### MongoDB

`2C`的存储部分依赖`MongoDB`，如果你已经部署好了`MongoDB`，直接在**配置**里面进行数据库配置即可。

如果你没有准备好的`MongoDB`，可以使用`Docker`一键执行：

```shelll
# 数据库路径，开发者可自由设置
mkdir -p /data/db
docker run --name mongodb  --restart=always -p 27017:27017 -e /data/db:/data/db -d mongo:3.6
```

可在`Docker`查询是否成功启动：

```shell
[root@centos ~]# docker ps -a
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                      NAMES
758617a57827   mongo:3.6   "docker-entrypoint.s…"   3 seconds ago   Up 2 seconds   0.0.0.0:27017->27017/tcp   mongodb
```

### 下载2C

安装`2C`前，需要你的系统环境安装有[Python3.6+](https://www.python.org/)环境。如果确认准备好环境，请进入终端，做环境检查，如下命令：

```shell
[~] python --version                                                                 
Python 3.6.8 :: Anaconda, Inc.
[~] pip --version
pip 21.1.1 from /Users/howie6879/.local/share/virtualenvs/2c-BCq66QuF/lib/python3.6/site-packages/pip (python 3.6)
```

本项目使用 [pipenv](https://pipenv.pypa.io/en/latest/) 进行项目管理，安装使用过程如下：

```shell
# 确保有Python3.6+环境
git clone https://github.com/howie6879/2c.git
cd 2c

# 创建基础环境
pipenv install --python={your_python3.6+_path}  --skip-lock --dev
```

搭建好基础环境后，就需要对项目进行配置，具体参考如下**2C配置**部分。

### 配置2C

项目的配置文件位于路径`src/config/config.py`下，使用者可以进行以下配置：

- 配置需要订阅的公众号
- 数据库
- 分发器类型配置：支持企业微信和钉钉

#### 公众号配置

输入你订阅的公众号名称即可：

```python
WECHAT_LIST = [
    "老胡的储物柜",
]
```



#### 数据库配置

如果是本机开发，使用上述方法搭建的`MongoDB`，保持不变即可：

```python
# 数据库配置
MONGODB_CONFIG = {
    # "mongodb://0.0.0.0:27017"
    "username": os.getenv("CC_M_USER", ""),
    "password": os.getenv("CC_M_PASS", ""),
    "host": os.getenv("CC_M_HOST", "0.0.0.0"),
    "port": int(os.getenv("CC_M_PORT", "27017")),
    "db": os.getenv("CC_M_DB", "2c"),
}
```

#### 分发器配置

目前分发器支持类型如下：

- ding：钉钉
- wecom：企业微信

##### 钉钉

建立一个群聊用于接收文章消息，然后新增一个群机器人，如下图：

![GctXXh](https://images-1252557999.file.myqcloud.com/uPic/R318WKfH4QaCk5o.jpg)

然后配置机器人：

![7iWlhv](https://images-1252557999.file.myqcloud.com/uPic/L6Mp9Gh5dunYeUk.jpg)

最后记住机器人对应的`Token`，一般格式如下：`1dea61224e683d90c5d3694c89e30841681567747f41fb9722597d48655f4365`，那么此时分发器配置如下：

```python
# 分发配置，目标支持：ding[钉钉]、wecom[企业微信]、tg[Telegram] 等等
# 目前仅支持钉钉
SENDER_LIST = ["ding"]
# 申请钉钉TOKEN时候，关键字必须带有 [2c]
DD_TOKEN = os.getenv('CC_D_TOKEN', '1dea61224e683d90c5d3694c89e30841681567747f41fb9722597d48655f4365')
```

##### 企业微信

如果你热衷微信生态，`2C`同样对企业微信做了支持，请先随便用手机号注册一个[企业微信](https://work.weixin.qq.com/)。

首先创造应用：

![2Cfkbw](https://images-1252557999.file.myqcloud.com/uPic/9brGzsDd2oMQ5CK.png)

获取相关ID：

![zLGP5T](https://images-1252557999.file.myqcloud.com/uPic/36icNFMDLKeqhCa.png)

企业ID在`我的企业->企业信息->企业ID`。

为了方便可以在微信上接收消息，记得开启微信插件，进入下图所在位置，然扫码关注你的二维码即可：

![zlfcd9](https://images-1252557999.file.myqcloud.com/uPic/Ei65BKLWyR2aCku.png)

现在你获取了以下三个参数，请填写到对应配置：

```python
WECOM_ID = os.getenv("CC_WECOM_ID", "wwee29721ad4f6e1c9")
WECOM_AGENT_ID = os.getenv("CC_WECOM_AGENT_ID", "1000001")
WECOM_SECRET = os.getenv(
    "CC_WECOM_SECRET", "O4M9w38wuwAxCMr0O3lTqAgzLC7yxjsDGr6lgv12345"
)
```

关于配置，除了可以直接在代码中的配置文件中进行，更建议直接在`.env`中进行配置，具体说明请参考[环境变量](https://github.com/howie6879/2c/blob/main/docs/00.2C环境变量.md)文件。

### 运行2C

配置完成后，直接在终端运行即可：

```
pipenv run dev
```

不出意外，会得到以下输出：

```shell
[2c] pipenv run python src/run.py      
Loading .env environment variables…
[2021:04:11 22:08:50] INFO  Request <GET: https://wechat.privacyhide.com/VERSION?>
[2021:04:11 22:08:52] INFO  Ruia Spider started!
[2021:04:11 22:08:52] INFO  Ruia Worker started: 140195525068320
[2021:04:11 22:08:52] INFO  Ruia Worker started: 140195525068728
[2021:04:11 22:08:52] INFO  Request <GET: https://cdn.jsdelivr.net/gh/hellodword/wechat-feeds@4153bf9/details.json>
[2021:04:11 22:12:45] INFO  Ruia Stopping spider: Ruia
[2021:04:11 22:12:45] INFO  Ruia Total requests: 1
[2021:04:11 22:12:45] INFO  Ruia Time usage: 0:03:53.628657
[2021:04:11 22:12:45] INFO  Ruia Spider finished!
[2021:04:11 22:12:45] INFO  2c Schedule started successfully :)
[2021:04:11 22:12:45] INFO  2c Schedule time:
 07:10
 11:10
 16:10
 20:10
 23:10
```

这样就成功启动了，钉钉分发效果如下（微信分发效果在文首）：

![image-20210509160826972](https://images-1252557999.file.myqcloud.com/uPic/WrhHX9L6m5wFsdx.png)

## 说明

> 这里声明一点，看广告是对作者的支持，这样一定程度上可以促进作者更好地产出。但我看到喜欢的会直接**打赏支持**，所以**搭便车**的言论在我这里站不住脚，谢谢。

看到这里，说明这篇文章以及`2C`项目你是感兴趣的，非常期待你使用`2C`，让你有更好的阅读体验，如果能参与进来，一起收集样本，贡献项目，那更是欢迎至极。

<div align=center><img width="20%" src="https://images-1252557999.file.myqcloud.com/uPic/0aJMKn.jpg" /></div>
