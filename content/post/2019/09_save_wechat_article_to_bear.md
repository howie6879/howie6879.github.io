---
categories:
  - Python
  - 项目
  - 工具
comments: true
date: 2019-09-16T08:37:56+08:00
image: /images/thumbs/h_07.jpg
tags: [效率]
title: "利用微信同步文章到Bear"
toc: true
---

> 如果图片失效：见[【教程&工具】微信同步文章到Bear](https://mp.weixin.qq.com/s/qRQO9xMvGTQL-ysolXJAxQ)

在我日常工作中，我会将各种互联网以及生活中产出的信息汇总到Bear，再通过Bear的云同步使我各个终端的信息保持一致。

以前在使用有道云笔记的时候，有个功能我很喜欢，就是当看到一篇想收藏的文章的话，就可以直接右上角发送到有道云笔记，如下图：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190916120642.png)

> 顺便一提：熊掌记是一款优雅、灵活的写作笔记应用。  
> 回到正题，我现在面临的需求是能不能在看到喜欢的文章的时候，也通过类似于右上角`分享一下`就可以直接将文章同步到我各个终端上的`Bear`，最终成果如下：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/w2b_demo.gif)

## 解决方案

要实现上述的需求，我大概思考了如下的解决方案：

1. 准备一个微信号（这里直接称作小号）专门接收待收藏到Bear的文章
2. 编写一个服务监控小号的消息，比如收到推文类型的消息就进行内容提取
3. 监控服务将提取后的内容发送到Bear（这里要求服务运行在Mac OS上）

所以在继续之前，你需要有以下条件：

* 基本的Python基础知识（写小脚本Python真的很方便）
* 一台装有Bear的Mac OS

## 方案调研

上面的解决方案看起来还是挺好实现，第一步不用多说，这年头谁没个小号，第二点的话，我印象中Python是有个第三方库可以直接监听微信对应账号的消息。

因为这些第三方库都是基于`Web`版的微信，所以在使用之前我想验证下此方案是否可行，刚准备登录网页版微信，就直接提示：

```html
<error><ret>1203</ret><message>To protect your account, logging in to WeChat via the web has been suspended. Use WeChat for Windows or WeChat for Mac to log in on a computer. Download WeChat for Windows or Mac at http://wechat.com.</message></error>
```

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/ArhyCYUg1d925qz.png)

果不其然现在微信准备加强`Web`版本的限制了，心里凉凉的，第二步还没开始就已经被宣判死刑。

只能换个思路了，怎么办。其实这一步走不通我还是能接受的，因为我一直觉得依赖`Web`版总有一天会挂掉，毕竟多了个依赖总是会增加复杂度。

能不能依靠客户端？

我们知道，微信数据是有同步功能的，开发过客户端的都知道，这就意味着微信的数据必然保存一份在客户端本地系统上。

所以对于第二点的解决思路就转换成了如何获取微信保存在客户端本地的数据，找到某个软件的数据文件夹自然是很简单的事情，比如微信客户端的数据就存放在：

```shell
# howie6879是我的用户名，请自行替换
/Users/howie6879/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9
```

具体有如下目录：

```shell
├── 988eebd1078a0d794bff2b6f5c8d5176
├── Avatar
├── CGI
├── CrashReport
├── KeyValue
├── MMResourceMgr
├── checkVersionFile
├── d41d8cd93400b204e9800998ecf8427e
├── f965739b566114f907dc394322e1e826
├── topinfo.data
├── upgradeHistoryFile
├── whatsNewVersionFile
└── wx.dat

8 directories, 5 files
```

不知道上面那三个32位的字符串大家看起来熟悉不熟悉：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/bqu2sUkc6VwxLIR.png)

一想到`32`，就是`md5`加密，我第一反应就是对于每个登录账号的id加密值，我们先不管，直接进去看更深一层的文件夹：

```shell
├── Account
├── Avatar
├── Contact
├── Favorites
├── FileStateSync
├── FunctionMsg
├── Group
├── Message
├── RevokeMsg
├── Session
├── Stickers
├── Sync
├── complexSearch
├── mmexpt
└── newabtest

15 directories, 0 files
```

`Message`出来了，这是不是我们想要的呢？再往下看里面的目录：

```shell
├── MessageTemp
├── fts
├── msg_0.db
├── msg_0.db-backup
├── msg_0.db-shm
├── msg_0.db-wal
├── msg_1.db
```

如果你登录过该台电脑并同步过信息，那么不出意外会有挺多`*.db`后缀的文件。大胆地猜测一下，这是不是我们想要的聊天数据存放路径呢？

不要管太多，先看看总不会错，一般本地存储的数据库，咱们程序员第一反应应该就是`SQLite`，要不要试试？

```shell
sqlite3 Message/msg_0.db
sqlite> .schema
Error: file is not a database
sqlite>
```

？？提示不是数据库，此时陷入了瓶颈，怎么就不是数据库了呢。反思一下，是不是打开的姿势不对。

**会不会是加密了**？依照这个思路，我了解到有一款基于`SQLite`的扩展数据库[SQLCipher]([https://github.com/sqlcipher/sqlcipher](https://github.com/sqlcipher/sqlcipher)，`SQLCipher`是一个在`SQLite`基础之上进行扩展的开源数据库，它主要是在`SQLite`的基础之上增加了数据加密功能。

实践证明，我猜想的是对的，接下来主要做的怎么打开`Message/msg_0.db`这个文件并成功读取里面的数据。

最后我参考到一份有意思的问答，我就是参考这个[问答](https://www.v2ex.com/t/466053#reply15)对数据库进行解密，这里我复述一下：

* 打开微信，但是先不登录
* 打开终端，输入`lldb -p $(pgrep WeChat)`
* 会看到进入了`lldb`，然后输入`br set -n sqlite3_key`，按回车
* 在`lldb`中，输入`c`，按回车
* 打开微信并扫码登录
* 然后回到`lldb`中，输入`memory read --size 1 --format x --count 32 $rsi`

此时就会得到以下类似的输出：

```shell
0x600003888340: 0xd1 0x05 0x29 0x04 0x75 0xc5 0x45 0x05
0x600003888348: 0x92 0x26 0xa1 0x65 0x95 0xe5 0x15 0x3f
0x600003888350: 0xf3 0xc7 0x43 0x85 0x05 0x35 0x45 0x3d
0x600003888358: 0x84 0xc8 0x64 0xe5 0x35 0x65 0x45 0xe2
```

去掉冒号前面的那一串，后面是四行八列的数据，再去除掉`0x`、`空格`、`\n`等，就会得到一串64位的字符串，举个例子：

```shell
df012f587cc546000025a56599e81530f9cc49800329423d8ec460e1386549e2
```

这就是我们进入数据库的钥匙，接下来，请安装sqlcipher的相关软件，如：

```shell
brew install sqlcipher
brew cask install db-browser-for-sqlite
```

让我们用`db-browser-for-sqlite`打开`db`后缀的文件看看有什么不一样吧：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/27CUG48QzEuKWop.png)

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/XE7lHKqdbvuM4g8.png)

点击`OK`，成功打开！

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/4JD5TBSgn3Aptyb.png)

随便进入一个表：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/q6iGsJNZYC5WBpg.png)

很显然，我们成功获取了本地的聊天记录，总算将第二步流程打通了，如今我们可以监控发送收藏文章的微信账户的聊天记录，只要收到此账号发来的推文消息，此时监控服务可以立马反应过来并解析发送到Bear。

有个小问题，怎么知道发推文的账号在哪个库哪个表呢？可以这样看，在电脑上登录发推文的账号，打开文件`userinfo.data`：

```shell
cd /Users/howie6879/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/988eebd1023a0d794bff2b6f5c8d5176/Account
cat userinfo.data
```

大概输出如下：

```textile
":BHPpx��127417592694754732��wxid_epXXXXXXXfj12�    Howie6879�老胡的储物柜�
```

这里很明显我的`wxid`就是：`wxid_epXXXXXXXfj12`，那么对应需要监控的表名就是：`Chat_md5(wxid_epXXXXXXXfj12)`，形式如同这样

```shell
Chat_f965739xxxx114fxxxxc394322exxxx
```

随后实现在库里面找到对应的表即可，我本机发现对应账户的表存在于库 `msg_5.db`中。

接下来要做的事情就很简单了，就是将提取后的内容发送到Bear，这里可以利用[X callback url Scheme documentation](https://bear.app/faq/X-callback-url%20Scheme%20documentation/)，比如你在终端输入：

```shell
open 'bear://x-callback-url/create?title=Test%20Bear&text=Hello%20Bear'
```

立马就可以看到Bear自动建立了一篇笔记

## 编码实现

终于到了编码阶段，好心酸：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/UDTA6xBphuvoFbs.png)

第一步，拿到必须要的常量：

* S_ACCOUNT_ID：微信发送账户ID，可以在`Account/userinfo.data`下查看
* R_ACCOUNT_ID：微信接收账户ID，同上
* RAW_KEY：解密Key，就是上面介绍的64位字符串
* DB_PATH_TEM：定义的是消息DB路径，比如："/Users/howie6879/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/{0}/Message/"

定义这四个常量，接下来的事情就一帆风顺了哈，我将项目开源在`Github`，地址见[w2b](https://github.com/howie6879/w2b)，接下来我直接说说怎么用：

```shell
git clone https://github.com/howie6879/w2b
cd w2b
# 推荐使用pipenv 你也可以使用自己中意的环境构建方式
pipenv install --python=/Users/howie6879/anaconda3/envs/python36/bin/python3.6  --skip-lock
# 运行前需要填好配置文件
pipenv run python w2b/run.py
```

随后，会有日志输出：

```shell
[w2b] pipenv run python w2b/run.py                                                                                                
Loading .env environment variables…
[2019:09:13 09:16:35] INFO  w2b  目标表 Chat_f965739b676114fxxxxc394322e1e826 存在于库 msg_5.db
```

好，代码跑起来后，接下来电脑上登录你的小号（也就是接收微信文章的微信号），然后在手机上登录发送文章的微信号，最终成功就和文章一开始的动图一样了~

搞定收工，有兴趣欢迎关注我的公众号：

<div align=center><img width="20%" src="https://cdn.jsdelivr.net/gh/howie6879/oss/uPic/qrcode_for_gh_3f02ace79dfb_258.jpg" /></div>
