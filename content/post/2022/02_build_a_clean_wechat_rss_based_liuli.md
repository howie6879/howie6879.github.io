---
title: "基于Liuli构建纯净的RSS公众号信息流"
date: 2022-01-26T20:35:47+08:00
lastmod: 2022-01-26T20:35:47+08:00
keywords: ["wechat", "rss", "liuli"]
description: "构建一个多源（公众号、RSS）、干净、个性化的阅读环境, 基于Liuli构建纯净的RSS公众号信息流"
tags: [效率,Ruia,Liuli]
categories: [Python,工具,项目]
author: "howie.hu"
image: /images/thumbs/h_68.jpg
---

首先介绍下，`Liuli`是什么？这是我最近开发的一个开源项目，主要目的是为了让有阅读习惯的朋友**快速构建一个多源、干净、个性化的阅读环境**。

**为什么叫`Liuli`？**

`Liuli`原来命名为`2C`，后面交流群的朋友提供了**琉璃**这个名字，取自梅尧臣《缑山子晋祠 会善寺》中的**琉璃开净界，薜荔启禅关**，其寓意也很符合项目的立意：

> 为用户构建一方阅读净土如东方琉璃净世界

距离上次发布已经过去了一个月（我实在是太拖了，反省），今天很高兴地和大家宣布`Liuli`又有一大波更新了！🥳具体见[v0.2.0任务看板](https://github.com/liuli-io/liuli/projects/1)。

接下来我将根据`Liuli`最新版本`V0.1.5`来给大家介绍下如何**基于Liuli构建纯净的RSS公众号信息流**，最终订阅效果展示如下：

![liuli_demo](https://images-1252557999.file.myqcloud.com/uPic/liuli_demo.jpg)

## 开始

首先从需求谈起，目前我有三点诉求：
- 聚合目前订阅的公众号，通过RSS输出然后分别订阅
- 对订阅的文章做广告识别
- 对文章做一个快速备份

以上诉求正好就是本篇文章的主题**构建纯净的RSS公众号信息流**，具体实现这里不详谈，有兴趣可以在交流群里面交流，本篇文章只说怎么用。

如果你阅读过我之前写的那篇[打造一个干净且个性化的公众号阅读环境](https://www.howie6879.cn/post/2021/12_build_a_clean_env_for_reading/)，那么你对采集器、处理器、分发器等概念可能有些了解，但这里为了阅读的连贯性，所以再次介绍下，首先看架构图：

![liuli_process](https://images-1252557999.file.myqcloud.com/uPic/XGnVFm.jpg)

简单解释一下：

- **采集器**：监控各自关注的公众号或者博客等自定义阅读源，以统一标准格式流入`Liuli`作为输入源；
- **处理器**：对目标内容进行自定义处理，如基于历史广告数据，利用机器学习实现一个广告分类器自动打标签，或者引入钩子函数在相关节点执行等；
- **分发器**：依靠接口层进行数据请求&响应，为使用者提供个性化配置，然后根据配置自动进行分发，将干净的文章流向微信、钉钉、TG甚至自建网站；
- **备份器**：将处理后的文章进行备份，如持久化到数据库或者GitHub等。

其实不了解流程也没关系，知道怎么用就行了，接下来请详细跟着教程一步一步来，最好有台电脑跟着操作。

## 使用

好，正戏开始。`Liuli`的部署使用还是很方便的，推荐大家使用`Docker`进行部署，所以开始前大家手头的设备需要安装好`Docker`，如果没安装，点击这里进行[安装](https://get.daocloud.io/)即可。

### 配置

当前`Liuli`的配置主要分两大块：
- 全局配置：就是全局环境变量，相关说明见[Liuli 环境变量](https://github.com/liuli-io/liuli/blob/main/docs/02.%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F.md)
- 任务配置：此配置针对用户需要解决的问题而形成，比如本文就会生成一个将公众号采集、处理、输出成RSS的配置（诸位使用时候将我的配置复制过去即可使用）

#### 全局配置

首先说说**全局配置**，其实默认提供的配置也能让大家跑起来，但是如果你需要将文章分发到微信或者钉钉等，那就需要填写相关配置，好了，让我们开始吧，请打开终端或者用你熟悉的方式，建立一些文件夹或者文件。

```shell
mkdir liuli
cd liuli
# 存放调度任务配置，默认命名为default.json
mkdir liuli_config
# 数据库
mkdir mongodb_data
# 下拉 docker-compose 配置
# 如果网络不好请手动填写，内容见附录
wget https://raw.githubusercontent.com/howie6879/liuli/main/docker-compose.yaml
# 配置 pro.env 具体查看全局配置处的Liuli 环境变量
vim pro.env
```

对于`pro.env`，想了解详情的话建议查看[全局配置](https://github.com/liuli-io/liuli/blob/main/docs/02.%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F.md)处的`Liuli`环境变量，当然你不想看也没关系，跟着此教程填即可，首先请将以下配置复制到`pro.env`:

```env
PYTHONPATH=${PYTHONPATH}:${PWD}
LL_M_USER="liuli"
LL_M_PASS="liuli"
LL_M_HOST="liuli_mongodb"
LL_M_PORT="27017"
LL_M_DB="admin"
LL_M_OP_DB="liuli"
LL_FLASK_DEBUG=0
LL_HOST="0.0.0.0"
LL_HTTP_PORT=8765
LL_WORKERS=1
# 上面这么多配置不用改，下面的才需要各自配置
# 请填写你的实际IP
LL_DOMAIN="http://{real_ip}:8765"
# 请填写微信分发配置
LL_WECOM_ID=""
LL_WECOM_AGENT_ID="-1"
LL_WECOM_SECRET=""
```

假设你和我一样使用微信作为分发终端，只需要通过以下步骤获取以下参数即可：
- LL_WECOM_ID
- LL_WECOM_AGENT_ID
- LL_WECOM_SECRET

获取流程如下，请先随便用手机号注册一个[企业微信](https://work.weixin.qq.com/)。

首先创造应用：

![](https://images-1252557999.file.myqcloud.com/uPic/lswMwG.jpg)

获取相关ID：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/uPic/2F5E8J.jpg)

企业ID在`我的企业->企业信息->企业ID`。

为了方便可以在微信上接收消息，记得开启微信插件，进入下图所在位置，扫码关注你的二维码即可：

![](https://images-1252557999.file.myqcloud.com/uPic/c3yaOb.jpg)

现在你获取了以下三个参数，请到对应配置填写秘钥即可。

#### 任务配置

任务配置主要是让使用者可以更加个性化地使用`Liuli`，从而满足用户各种需求，当前`Liuli`还只能支持公众号采集、过滤、分发、备份操作，也就是本文的核心目的，大家将以下内容复制到`liuli_config/default.json`即可：

```json
{
    "name": "default",
    "author": "liuli_team",
    "collector": {
        "wechat_sougou": {
            "wechat_list": [
                "老胡的储物柜"
            ],
            "delta_time": 5,
            "spider_type": "playwright"
        }
    },
    "processor": {
        "before_collect": [],
        "after_collect": [{
            "func": "ad_marker",
            "cos_value": 0.6
        }, {
            "func": "to_rss",
            "link_source": "github"
        }]
    },
    "sender": {
        "sender_list": ["wecom"],
        "query_days": 7,
        "delta_time": 3
    },
    "backup": {
        "backup_list": ["mongodb"],
        "query_days": 7,
        "delta_time": 3,
        "init_config": {},
        "after_get_content": [{
            "func": "str_replace",
            "before_str": "data-src=\"",
            "after_str": "src=\"https://images.weserv.nl/?url="
        }]
    },
    "schedule": {
        "period_list": [
            "00:10",
            "12:10",
            "21:10"
        ]
    }
}
```

注意上面的`wechat_list`字段，你只需要将自己想订阅的公众号输入进去即可，后面这块会做界面进行配置，先将就着用用吧。

### 启动

感谢你能看到这里，现在距离成功就只有一行命令了，请先检查`liuli`目录下文件树是否是下面这个样子：

```shell
(base) [liuli] tree -L 1        
├── docker-compose.yaml
├── liuli_config
├────default.json
├── mongodb_data
└── pro.env
```

确认没问题后，执行：

```shell
docker-compose up -d
```

不出意外，会看到`Docker`启动了这三个容器：

![](https://images-1252557999.file.myqcloud.com/uPic/GQgPSU.png)

查看`liuli_schedule`，会有日志如下：

输出日志如下：

```shell
Loading .env environment variables...
[2022:01:26 23:09:24] INFO  Liuli Schedule(v0.1.5) started successfully :)
[2022:01:26 23:09:24] INFO  Liuli Schedule time:
 00:10
 12:10
 21:10
[2022:01:26 23:09:36] INFO  Liuli playwright 匹配公众号 老胡的储物柜(howie_locker) 成功! 正在提取最新文章: 我的周刊(第023期)
[2022:01:26 23:09:39] INFO  Liuli 公众号文章持久化成功! 👉 老胡的储物柜
[2022:01:26 23:09:40] INFO  Liuli 🤗 微信公众号文章更新完毕(1/1)
...
[2022:01:26 23:09:45] INFO  Liuli 备份器执行完毕!
```

执行完毕后，你可以进入MongoDB数据库，会出现如下`collection`:

- liuli_articles: 获取的文章元信息
- liuli_backup: 文章全部备份
- liuli_rss: 生成的RSS
- liuli_send_list: 分发状态
- liuli_backup_list: 备份状态

假设你公众号源有`老胡的储物柜`，那么启动完毕，你可以访问`老胡的储物柜`的`RSS`订阅地址`http://ip:8765/rss/liuli_wechat/老胡的储物柜/`，效果如下：

![](https://images-1252557999.file.myqcloud.com/uPic/o15Urk.png)

注意红框部分，因为我使用的是`GitHub`备份器，所以地址显示的是`GitHub`地址，大家如果也想用这个，可以参考教程[备份器配置](https://github.com/liuli-io/liuli/blob/main/docs/04.%E5%A4%87%E4%BB%BD%E5%99%A8%E9%85%8D%E7%BD%AE.md)，我使用`GitHub`备份器效果如下如：

![](https://images-1252557999.file.myqcloud.com/uPic/Rgaclg.png)
每日更新的文章都会被`Liuli`自动同步到这个项目，如果大家都用`Liuli`的`GitHub`备份器，一起将备份结果结合起来的话，那将会是一股非常庞大的力量，可以期待下。

## 展示

`Liuli`启动成功后，对于使用者来说，主要感知在分发订阅这一层。

微信分发终端效果图：

![](https://images-1252557999.file.myqcloud.com/uPic/4RIQB0.jpg)

订阅效果如下图：

![](https://images-1252557999.file.myqcloud.com/uPic/liuli_demo-20220520220053008.jpg)

## 说明

本项目还属于非常稚嫩的早期阶段，如果大家觉得有用的话，希望快点用起来，趁早提点意见，让`Liuli`成长得更加迅速。

如果觉得这个项目不错，麻烦在`GitHub`给`Liuli`一个Star，项目地址点这里👉[liuli-io/liuli](https://github.com/liuli-io/liuli)。

如果你在搭建&使用过程中有任何问题或者意见，你可以直接在`GitHub`提`Issue`或者直接进群我们详聊（如果过期，公众号里面有我微信，拉你进群）：

![](https://images-1252557999.file.myqcloud.com/uPic/kGIEDt.png)

## 附录

`docker-compose.yaml`配置如下：

```json
version: "3"
services:
  liuli_api:
    image: liuliio/api:v0.1.1
    restart: always
    container_name: liuli_api
    ports:
      - "8765:8765"
    volumes:
      - ./pro.env:/data/code/pro.env
    links:
      - liuli_mongodb
    depends_on:
      - liuli_mongodb
    networks:
      - liuli-network
  liuli_schedule:
    image: liuliio/schedule:v0.1.5
    restart: always
    container_name: liuli_schedule
    volumes:
      - ./pro.env:/data/code/pro.env
      - ./liuli_config:/data/code/liuli_config
    links:
      - liuli_mongodb
    depends_on:
      - liuli_mongodb
    networks:
      - liuli-network
  liuli_mongodb:
    image: mongo:3.6
    restart: always
    container_name: liuli_mongodb
    environment:
      - MONGO_INITDB_ROOT_USERNAME=liuli
      - MONGO_INITDB_ROOT_PASSWORD=liuli
    ports:
      - "27027:27017"
    volumes:
      - ./mongodb_data:/data/db
    command: mongod
    networks:
      - liuli-network

networks:
  liuli-network:
    driver: bridge
```
