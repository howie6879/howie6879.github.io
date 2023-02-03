---
categories:
  - Python
  - 项目
  - 工具
comments: true
date: 2019-10-21T08:37:56+08:00
image: /images/thumbs/h_50.jpg
tags: [效率]
title: "不论微信钉钉，我写了个通用消息监控处理机器人"
toc: true
---

上一篇推文中提到，我希望通过监控微信对应的聊天记录，来实现一个消息自动处理的机器人，上篇文章实现的就是自动保存感兴趣的文章到**Bear**。

虽说那篇文章比较实用，也有很多朋友表示喜欢，但还有不少缺陷：
- 对技术薄弱的朋友复现困难，项目很多配置需要手动生成，前期校验工作很多
- 二次开发比较困难，不能直接作为第三方包使用
- 项目兼容性不强，目前只支持Mac并且只支持微信这个APP，其他如钉钉就没辙
- 项目稳定性不强，微信更改机制可能又需要重头再来

对，问题很多，但在勉强能用的情况下我并不是很有动力进行新方案的开发。那么是什么原因促使我更新方案了呢~

> 背景：在公司和数据打交道居多，运营那块每天会将一些表格数据发到钉钉群里面，然后我组内成员需要手动下载并找到对应的处理脚本进行处理  

然后我问了下两周需要耗费多长时间在这件事情上，答曰**1**小时，这确实不能忍了！

于是我花了**10**小时做了这个处理方案！顺便解决上面说的四个问题，赚了。。。

## 调研
开始之前，自然是先下决心将一切历史问题解决掉：
- 跨系统！跨软件！
- 程序能直接作为第三方包使用，并且傻瓜式！

好，**flag**已立，开始吧，核心技术点就是如何监控所有平台软件的消息。

上一篇推文的方案只能支持微信，所以我目前的背景需求里面的监控对象是钉钉，难道我又要按照以前的方法去搞db文件么？

总结来说，我监控的是消应用息，并且不只是微信、还需要钉钉、QQ等其他任意软件的消息，而且我也不能一个个地去破解（就算这次解密成功，如果下次需求是其他通信软件呢？），那该怎么办呢？

所以这次处理问题的角度需要站在上帝视角，说来简单，做起来也简单，哈哈。

话说我灵光一闪，既然目标对象是所有应用的消息，那我我为何不将视角从应用层面移到系统层面呢？

我以我的Mac为例，这不有现成的通知中心么？

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20191021143142.png)

可谓是柳暗花明又一村，老胡我这招隔山打牛，姿势还可以吧？

现在问题很明确了：
- 我管你微信、钉钉、还是QQ，我只看系统的通知中心
- 我管你怎么加密、怎么限制，你总不会限制系统吧，我还是只看系统的通知中心

我背靠**操作系统**老大，现在还害怕你几个小应用，一起上，我哭算我输。

在操作系统的领域内，这个方案就是武侠世界的九阳神功，还配上了屠龙刀，可谓：**十步杀一人，千里不留行。**

调研结束。

## 实践
解决方案确立了，接下来无非就是验证加实现，这里还是以`Mac`为例。

来来来，看看苹果的系统中心好不好攻占，首先，咋们来确立系统中心数据的存储方式，进入终端：

```shell
cd `getconf DARWIN_USER_DIR`/com.apple.notificationcenter/db2
ls
db     db-shm db-wal
```

可以看到，有三个文件，哎，好熟悉，`SQLite`呗。

打开：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20191021143214.png)

分别有以下几个表：

- app
- categories
- dbinfo
- delivered
- displayed
- record
- requests
- snoozed

可能有点多，但我们只需要关注两个表就行：

- app：应用id
- record：监控的应用消息

建表语句分别如下：

```mysql
create table app
(
    app_id     INTEGER
        primary key,
    identifier VARCHAR,
    badge      INTEGER
);

create table record
(
    rec_id            INTEGER
        primary key,
    app_id            INTEGER,
    uuid              BLOB,
    data              BLOB,
    request_date      REAL,
    request_last_date REAL,
    delivered_date    REAL,
    presented         Bool,
    style             INTEGER,
    snooze_fire_date  REAL
);
```

在app表的这行，可以看到微信应用的`id`是`35`。

```txt
app_id	identifier
35			com.tencent.xinwechat	
```

知道应用`id`后就可以直接在`record`表里面直接找到通知消息：

```mysql
SELECT app_id,data, presented, delivered_date FROM record WHERE app_id IN (35)  ORDER BY delivered_date DESC;
```

得到结果如下：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20191021143236.png)

一切顺利，但定睛一看，中间那一大串是什么？不要慌，里面`bplist`，给了很大的提示。

在Python的世界里，没有什么问题是引入一个第三方包解决不了的，如果有，那么引入两个第三方包就可以了。

```shell
pip install biplist
```

利用`biplist`会将那一串加载成我们人类可以看懂的语言。

好像没有一点阻碍，那就可以直接编码实现了。

## 编码
算了，不多说，多了我也说不出来，直接开源吧，见 [https://github.com/howie6879/examiner](https://github.com/howie6879/examiner) ，不要吝啬你的**Star**（可点击阅读原文）。

```shell
git clone https://github.com/howie6879/examiner
cd examiner
# 推荐使用pipenv 你也可以使用自己中意的环境构建方式
pipenv install --python=/Users/howie6879/anaconda3/envs/python36/bin/python3.6  --skip-lock
```

接下来只需要在根目录构建自己的监控脚本就行，比如监控微信，监理文件命名为 *`wechat_app.py`*:

```python
from examiner.notification import notification_factory

def get_data(app_names: list):
    os_notification = notification_factory(app_names)
    info_list = os_notification.get_target_notification()
    for each in info_list:
        # 自行实现监控逻辑以及处理方案
        print(each)


if __name__ == "__main__":
    app_names = ["WeChat"]
    get_data(app_names)
```

和上一篇比起来，这代码，不需要配置、不需要提前准备什么，只需要在列表里面填上你想监控的目标应用即可（我顺便支持了多应用），你可以同时监控钉钉和微信等等

自己可以慢慢玩，一般会这样输出：

```shell
{'title': '老胡的储物柜', 'subtitle': '', 'body': '测试消息监控，任何应用都行', 'delivered_date': datetime.datetime(2019, 10, 20, 21, 40, 26, 428654), 'presented': 1, 'app_identifier': 'com.tencent.xinwechat', 'app_name': 'WeChat', 'md5': '75e24e2ccc502f01c101fcbd3637950b'}
```

搞定收工，有兴趣欢迎关注我的公众号：

<div align=center><img width="20%" src="https://cdn.jsdelivr.net/gh/howie6879/oss/uPic/qrcode_for_gh_3f02ace79dfb_258.jpg" /></div>
