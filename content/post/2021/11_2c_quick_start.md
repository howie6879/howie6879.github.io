---
title: "Liuli 使用教程"
date: 2021-04-11T21:35:47+08:00
lastmod: 2021-04-11T21:35:47+08:00
keywords: []
description: "构建一个多源（公众号、RSS）、干净、个性化的阅读环境"
tags: [效率,Ruia,Liuli]
categories: [Python,项目,工具,教程]
author: "howie.hu"
image: /images/thumbs/h_67.jpg
---


![uuNLVj](https://images-1252557999.file.myqcloud.com/uPic/uuNLVj.jpg)

[2C](https://github.com/howie6879/2c)的目的是为了**构建一个多源（公众号、RSS）、干净、个性化的阅读环境**，如果你在公众号阅读体验下深切感受到对于广告的无奈，那么这个项目就是你需要的，一起看看怎么安装部署[2C](https://github.com/howie6879/2c)吧。

## 开始

[2C](https://github.com/howie6879/2c)项目对于一些基础环境是有一点要求的，为了尽可能减少开发者部署使用的复杂度（特别是非Python开发者），因此我计划使用`Docker`进行调度运行，这样对用户的使用来说是比较方便的，请按照以下酌情执行`TODO`：

- 安装`Docker`
- [必读]环境变量
- 镜像安装运行：❤️ 推荐
  - 一键安装
  - 单独安装（已有DB）
- 代码安装`2C`：
  - 下载`2C`
  - 配置`2C`
  - 运行`2C`
- 分发器配置

推荐直接使用`Docker`进行安装，可使用[Docker 极速下载](https://get.daocloud.io/)进行安装。

## 环境变量

`2C`项目环境变量说明：

```shell
# ======================================系统环境配置======================================#
# 当前目录为模块
PYTHONPATH=${PYTHONPATH}:${PWD}

# =======================================数据库配置=======================================#
# MongoDB 用户名
CC_M_USER=""
# MongoDB 密码
CC_M_PASS=""
# MongoDB IP
# Docker Compose 形式启动的话，此行配置不变
CC_M_HOST="2c_mongodb"
# MongoDB 端口
CC_M_PORT="27017"
# MongoDB DB 最好不要变
CC_M_DB="2c"

# ======================================接口服务配置======================================#
# Flask 是否开启Flask的Debug模式
CC_FLASK_DEBUG=0
# Flask IP
CC_HOST="localhost"
# Flask 端口
CC_HTTP_PORT=8060
# Flask 服务启动的 worker 数量
CC_WORKERS=1

# =======================================采集器配置=======================================#
# 采集目标公众号，多个公众号请用;分割
CC_WECHAT_ACCOUNT="是不是很酷;老胡的储物柜"

# =======================================分发器配置=======================================#
# 2C 发送器中端配置，以;区分
# 目前支持：ding[钉钉] wecom[企业微信] 持续开发
CC_SENDER_NAME="ding;wecom"
# 分发终端为钉钉钉必须配置的Token
CC_D_TOKEN=""
# 分发终端为企业微信的配置
CC_WECOM_ID=""
CC_WECOM_AGENT_ID="-1"
CC_WECOM_SECRET=""
```

环境变量长期维护地址，见[环境配置](https://github.com/howie6879/2c/blob/main/docs/02.2C%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F.md)。


## 镜像安装

### 一键安装

```shell
mkdir 2c && cd 2c

# 数据库目录
mkdir mongodb_data

# 配置 pro.env 具体查看 doc/02.环境变量.md
vim pro.env
```

配置`docker-compose`，执行:`vim docker-compose.yaml`，输入:

```yaml
version: "3"
services:
  2c_schedule:
    image: howie6879/2c:schedule_v0.1.0
    restart: always
    container_name: 2c_schedule
    volumes:
      - ./pro.env:/data/code/pro.env
    links:
      - 2c_mongodb
    depends_on:
      - 2c_mongodb
    networks:
      - 2c-network
  2c_mongodb:
    image: mongo:3.6
    restart: always
    container_name: 2c_mongodb
    ports:
      - "27017:27017"
    volumes:
      - ./mongodb_data:/data/db
    networks:
      - 2c-network

networks:
  2c-network:
    driver: bridge
```

或者点击这里下载最新的[配置文件](https://raw.githubusercontent.com/howie6879/2c/main/docker-compose.yaml)。

然后在当前目录`2c_rooot_path`终端执行以下命令启动：

```shell
docker-compose up -d

# 输出
# Creating network "2c_2c-network" with driver "bridge"
# Creating 2c_mongodb ... done
# Creating 2c_schedule ... done
```

不出意外，可以看到下面容器成功启动：

![2c_docker-compose](https://images-1252557999.file.myqcloud.com/uPic/rRfTdg.png)

### 单独安装

[2C](https://github.com/howie6879/2c)项目的存储部署依赖`MongoDB`，如果你已经部署好了`MongoDB`，直接在**env配置文件**里面进行数据库配置即可。

如果你没有`MongoDB`，可以使用`Docker`一键执行：

```shell
# 数据库路径，开发者可自由设置
mkdir -p /data/db
docker run --name mongodb  --restart=always -p 27017:27017 -v /data/db:/data/db -d mongo:3.6
```

可在`Docker`查询是否成功启动：

```shell
docker ps -a

# 输出
# CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                      NAMES
# 758617a57827   mongo:3.6   "docker-entrypoint.s…"   3 seconds ago   Up 2 seconds   0.0.0.0:27017->27017/tcp   mongodb
```

接下来启动`2c`：

```shell
mkdir 2c

# 配置 pro.env 具体查看 doc/02.环境变量.md
vim pro.env

# 启动
docker run -d -it --restart=always -v $PWD/pro.env:/data/code/pro.env --name 2c_schedule howie6879/2c:schedule_v0.1.0
```
输出日志如下：

```shell
Loading .env environment variables...
[2021:12:23 23:08:35] INFO  2C Schedule started successfully :)
[2021:12:23 23:08:35] INFO  2C Schedule time: 00:00 06:00
[2021:12:23 23:09:36] INFO  2C playwright 匹配公众号 老胡的储物柜(howie_locker) 成功! 正在提取最新文章: 我的周刊(第018期)
[2021:12:23 23:09:39] INFO  2C 公众号文章持久化成功! 👉 老胡的储物柜
[2021:12:23 23:09:40] INFO  2C 🤗 微信公众号文章更新完毕(1/1)
```

## 代码安装

代码安装支持系统如下：
- Windows
- MacOS
- Ubuntu 18.04 & 20.04

其他系统请使用`Docker`进行安装。

### 下载2C

安装[2C](https://github.com/howie6879/2c)前，需要你的系统环境安装有[Python3.7+](https://www.python.org/)环境。如果确认准备好环境，请进入终端，做环境检查，如下命令：

```shell
[~] python --version                                                                 
Python 3.7.9 :: Anaconda, Inc.
[~] pip --version
pip 20.1 from /Users/howie/.local/share/virtualenvs/2c-pL4LHJaI/lib/python3.7/site-packages/pip (python 3.7)
```

本项目使用 [pipenv](https://pipenv.pypa.io/en/latest/) 进行项目管理，安装使用过程如下：

```shell
# 确保有Python3.7+环境
git clone https://github.com/howie6879/2c.git
cd 2c

# 创建基础环境
pipenv install --python={your_python3.7+_path}  --skip-lock --dev
```

搭建好基础环境后，就需要对项目进行配置，具体参考如下**2C配置**部分。

### 配置2C

[2C](https://github.com/howie6879/2c)项目的配置文件位于路径`src/config/config.py`下，使用者可以进行以下配置：

- 配置需要订阅的公众号
- 数据库
- 分发器类型配置

直接在`.env`文件中增加相关[环境配置]即可：

```.env
# 公众号配置, 输入你订阅的公众号名称即可
CC_WECHAT_ACCOUNT="是不是很酷;老胡的储物柜"

# 数据库配置
# 如果是本机开发，使用上述方法搭建的`MongoDB`，以下内容保持不变即可
CC_M_USER=""
CC_M_PASS=""
CC_M_HOST=""
CC_M_PORT="27017"
CC_M_DB="2c"

# 分发器配置
# 目前分发器支持类型如下：
#   - ding：钉钉
#   - wecom：企业微信
CC_SENDER_NAME="ding;wecom"
CC_D_TOKEN=""
CC_WECOM_ID=""
CC_WECOM_AGENT_ID="-1"
CC_WECOM_SECRET=""
```

对于分发器相关对象的密钥申请教程，详见本文下方的[2C分发器配置]。

### 运行2C

配置完成后，直接在终端运行即可：

```
# 配置.env 具体查看 doc/00.环境变量.md 启动
pipenv run dev_schedule
```

不出意外，会得到以下输出：

```shell
> pipenv run dev_schedule 

[2021:12:23 22:17:52] INFO  2C Schedule started successfully :)
[2021:12:23 22:17:53] INFO  2C Schedule time:
 00:00
 06:00
 09:00
 12:00
 15:00
 18:00
 21:00
[2021:12:23 22:17:56] INFO  2C playwright 匹配公众号 是不是很酷(isnt_it_cool) 成功! 正在提取最新文章: 软件工程师和算法竞赛
[2021:12:23 22:17:59] INFO  2C 公众号文章持久化成功! 👉 是不是很酷 
[2021:12:23 22:18:02] INFO  2C playwright 匹配公众号 老胡的储物柜(howie_locker) 成功! 正在提取最新文章: 我的周刊(第018期)
[2021:12:23 22:18:05] INFO  2C 公众号文章持久化成功! 👉 老胡的储物柜 
[2021:12:23 22:18:08] INFO  2C 🤗 微信公众号文章更新完毕(2/2)
```

这样就成功启动了，微信终端分发效果如下：

<div align=center><img width="40%" src="https://raw.githubusercontent.com/howie6879/oss/master/images/m3nJ61.png" /></div>

## 2C分发器配置

目前分发器支持类型如下：

- [推荐] wecom：企业微信
- ding：钉钉

### 钉钉

建立一个群聊用于接收文章消息，然后新增一个群机器人，如下图：

![GctXXh](https://images-1252557999.file.myqcloud.com/uPic/GctXXh.jpg)

然后配置机器人：

![7iWlhv](https://images-1252557999.file.myqcloud.com/uPic/7iWlhv.jpg)

最后记住机器人对应的`Token`，一般格式如下：`1dea61224e683d90c5d3694c89e30841681567747f41fb9722597d48655f4365`，那么此时分发器配置如下：

```python
# 分发配置，目标支持：ding[钉钉]、wecom[企业微信]、tg[Telegram] 等等
# 目前仅支持钉钉
SENDER_LIST = ["ding"]
# 申请钉钉TOKEN时候，关键字必须带有 [2c]
DD_TOKEN = os.getenv('CC_D_TOKEN', '1dea61224e683d90c5d3694c89e30841681567747f41fb9722597d48655f4365')
```

### 企业微信

如果你热衷微信生态，`2C`同样对企业微信做了支持，请先随便用手机号注册一个[企业微信](https://work.weixin.qq.com/)。

首先创造应用：

![2Cfkbw](https://images-1252557999.file.myqcloud.com/uPic/2Cfkbw.png)

获取相关ID：

![zLGP5T](https://raw.githubusercontent.com/howie6879/oss/master/images/zLGP5T.png)

企业ID在`我的企业->企业信息->企业ID`。

为了方便可以在微信上接收消息，记得开启微信插件，进入下图所在位置，然扫码关注你的二维码即可：

![zlfcd9](https://images-1252557999.file.myqcloud.com/uPic/zlfcd9.png)

现在你获取了以下三个参数，请到对应配置填写秘钥即可。

分发器文档长期维护地址，见[2C分发器配置](https://github.com/howie6879/2c/blob/main/docs/03.2C%E5%88%86%E5%8F%91%E5%99%A8%E9%85%8D%E7%BD%AE.md)。

## 说明

[2C](https://github.com/howie6879/2c)项目还处于快速迭代的开发状态，此文档随时会变动，切记。如果阅读体验不好，建议移步博客阅读[2C 使用教程](https://www.howie6879.cn/post/2021/11_2c_quick_start/)。