---
title: "基于Liuli追更&阅读小说"
date: 2022-02-13T20:35:47+08:00
lastmod: 2022-02-13T20:35:47+08:00
keywords: ["wechat", "rss", "liuli"]
description: "构建一个多源（公众号、RSS）、干净、个性化的阅读环境, 基于Liuli追更&阅读小说"
tags: [效率,Ruia,Liuli]
categories: [Python,工具,项目]
author: "howie.hu"
---

**Liuli**历史文章介绍：

- 起因: [打造一个干净且个性化的公众号阅读环境](https://www.howie6879.cn/post/2021/12_build_a_clean_env_for_reading/)
- 公众号应用场景：[基于Liuli构建纯净的RSS公众号信息流](https://www.howie6879.cn/post/2022/02_build_a_clean_wechat_rss_based_liuli/)

这次`Liuli`给大家带来了小说书籍阅读场景的订阅解决方案，搭建方式和之前[基于Liuli构建纯净的RSS公众号信息流](https://www.howie6879.cn/post/2022/02_build_a_clean_wechat_rss_based_liuli/)没什么区别。

最终效果如下图：

![owllook theme rss 客户端订阅](https://images-1252557999.file.myqcloud.com/uPic/841644501379_.pic_hd.png)

![heti theme 浏览器访问](https://images-1252557999.file.myqcloud.com/uPic/851644501500_.pic.png)

## 使用

`Liuli`的部署使用还是很方便的，推荐大家使用`Docker`进行部署，所以开始前大家手头的设备需要安装好`Docker`，如果没安装，点击这里进行[安装](https://get.daocloud.io/)即可。

当前`Liuli`的配置主要分两大块：

- 全局配置：就是全局环境变量，相关说明见[Liuli 环境变量](https://github.com/liuli-io/liuli/blob/main/docs/02.%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F.md)
- 任务配置：此配置针对用户需要解决的问题而形成，比如本文就会生成一个将书籍类网页统一采集、处理、输出成RSS的配置（诸位使用时候将我的配置复制过去即可使用）

这里就不再一步一步写怎么安装配置`Liuli`，因为**基于Liuli构建纯净的RSS公众号信息流**这篇文章已经讲得很详细了，所以使用前请大家一定要把这篇文章通读一遍。**切记！切记！切记！**

......

好了，读完了，现在你`Liuli`目录下应该有这几个目录文件:

```shell
(base) [liuli] tree -L 1        
├── docker-compose.yaml
├── liuli_config
├────default.json
├── mongodb_data
└── pro.env
```

然后对其中的`docker-compose.yaml`和`default.json`文件做一些修改：

- `docker-compose.yaml`请在github下载[最新](https://github.com/liuli-io/liuli/blob/main/docker-compose.yaml)的，如果嫌麻烦直接将`liuliio/schedule:v0.1.5`换成`liuliio/schedule:v0.1.6`即可
- `default.json`文件内容换成官方提供的[book.json](https://github.com/liuli-io/liuli/blob/main/liuli_config/book.json)即可，防止大家网络打不开，下面贴一下配置。

`default.json`文件内容如下：

```json
{
    "name": "book",
    "author": "liuli_team",
    "doc_source": "liuli_book",
    "collector": {
        "book_common": {
            "book_dict": {
                "诡秘之主": "https://www.yruan.com/article/38563.html"
            },
            "delta_time": 5
        }
    },
    "processor": {
        "before_collect": [],
        "after_collect": [{
            "func": "to_rss",
            "link_source": "github"

        }]
    },
    "sender": {
        "sender_list": ["wecom", "ding"],
        "query_days": 7,
        "delta_time": 3,
        "link_source": "github"
    },
    "backup": {
        "backup_list": ["github", "mongodb"],
        "query_days": 7,
        "delta_time": 3,
        "doc_html_dict": {
            "liuli_book": "book"
        },
        "init_config": {},
        "after_get_content": [{
            "func": "str_replace",
            "before_str": "本书首发",
            "after_str": ""
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

确认没问题后，执行：

```shell
docker-compose up -d
```

不出意外，会看到`Docker`启动了这三个容器：

![](https://images-1252557999.file.myqcloud.com/uPic/zH5y7i.png)

稍等片刻，你可以尝试访问一下采集器配置小说的`RSS`订阅地址`http://ip:8765/rss/liuli_book/小说名`，以我的为例，效果如下：

![](https://images-1252557999.file.myqcloud.com/uPic/KLydii.png)

注意红框部分，因为我使用的是`GitHub`备份器，所以地址显示的是`GitHub`地址，所有人都是可以直接访问的，比如点[这里](https://howie6879.github.io/liuli_backup//liuli_book/没钱上大学的我只能去屠龙了/%E7%AC%AC%E5%85%AB%E7%99%BE%E4%B8%89%E5%8D%81%E4%B8%80%E7%AB%A0%EF%BC%9A%E6%83%85%E7%A7%8D.html)(RSS订阅展示的内容就来自这个链接)：

![](https://images-1252557999.file.myqcloud.com/uPic/Xnip2022-02-10_22-18-35.jpg)

大家如果也想用这个，可以参考教程[备份器配置](https://github.com/liuli-io/liuli/blob/main/docs/04.%E5%A4%87%E4%BB%BD%E5%99%A8%E9%85%8D%E7%BD%AE.md)，我使用`GitHub`备份器效果如下如：

![](https://images-1252557999.file.myqcloud.com/uPic/vHyfkZ.png)

注意看，多了个`liuli_book`的目录出来了。

## 问答

> **问：我怎么添加书源？**

由于`Liuli`没有做任何小说数据采集，也没有对任何小说网站做适配（仅仅是做了个章节提取和核心内容识别这两个模块），所以是需要用户自己填写如下这种配置在`xxx.json`文件：

```shell
"book_dict": {
	"诡秘之主": "https://www.yruan.com/article/38563.html"
}
```

比如我在追这本`没钱上大学的我只能去屠龙了`，直接搜一下（这里用百度可能效果更好）:

![](https://images-1252557999.file.myqcloud.com/uPic/6RT0js.png)

随便选一个链接填到配置里面去，比如我选第二个，那么配置如下：

```shell
"book_dict": {
	"诡秘之主": "https://www.yruan.com/article/38563.html",
	"没钱上大学的我只能去屠龙了": "https://www.xbiquwx.la/90_90983/"
}
```

添加好书源后，直接重启调度器容器即可：

```shell
docker restart liuli_schedule
```

> **问：现在是演示小说订阅，我想和上次的微信订阅一起用怎么弄？**

很简单，两个配置([官方配置](https://github.com/liuli-io/liuli/tree/main/liuli_config))都放到文件夹下面即可，`Liuli`会自动识别的，如下：

```shell
(base) [liuli] tree -L 1        
├── docker-compose.yaml
├── liuli_config
├────wechat.json
├────book.json
├── mongodb_data
└── pro.env
```

**!!!注意：如果之前用过公众号的配置，请加上`"doc_source": "liuli_wechat"`配置才能兼容。**

> **还有其他问题怎么办？**

你可以在项目地址提[Issue](https://github.com/liuli-io/liuli/issues)，也可以直接在本文下面留言，还可以公众号右下角加我微信直接为你解答，更可以加下面的`Liuli交流群`：

![](https://images-1252557999.file.myqcloud.com/uPic/861644504399_.pic.jpg)

## 说明

`Liuli`还处在早期开发阶段，我个人希望**构建一个多源、干净、个性化的阅读环境**，所以现在初期主要做的是做不同阅读方向源的兼容，比如公众号类、博客类、小说类甚至漫画类，基于这些基础源，后续会重点给用户打造更精细的阅读环境，如现有的去广告、后续规划的智能标签、分类以及一套知识管理体系。

总之，前路漫漫，且做且珍惜，如果你有想法或者建议，欢迎参与，一起聊聊。最后，项目地址[liuli-io/liuli](https://github.com/liuli-io/liuli)在这里，给个Star鼓励下呗。