---
date: 2019-03-20T13:37:56+08:00
image: /images/thumbs/h_46.png
tags: [jupyterlab]
categories:
  - Python
  - 工具
  - 教程
title: "JupyterLab使用教程：程序员的笔记本神器v1.0"
toc: false
---

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190320100202.png)

> JupyterLab对于Jupyter Notebook有着完全的支持  

`JupyterLab`是一个交互式的开发环境，是`jupyter notebook`的下一代产品，集成了更多的功能，等其正式版发布，相信那时就是`jupyter notebook`被取代的时候

通过使用`JupyterLab`，能够以灵活，集成和可扩展的方式处理文档和活动：

- 可以开启终端，用于交互式运行代码，完全支持丰富的输出
- 支持`Markdown，Python，R，LaTeX`等任何文本文件
- 增强notebook功能
- 更多插件支持

如果你在日常生活中，有以下需求，我觉得你可以安装一个`JupyterLab`

- 随时随地希望试验一些代码片段
- 多语言、多文档支持
- 有记笔记需求（文本+代码）

> 安装  

接下来，我将以Python为默认语言来搭建`JupyterLab`，首先确认你安装好了`Python`基本环境：

```shell
# 一行命令搞定
pip install jupyterlab
# 安装ipython
pip install ipython
```

如果在服务器使用的话，个人建议还是设置一下密码，配置过程如下：

```shell
# 进入ipython交互环境
ipython
```

生成密码：

```python
from notebook.auth import passwd
passwd()
# 输入你自己设置登录JupyterLab界面的密码 然后会有一串输出，记得复制下来，等会配置需要使用
```

修改`JupyterLab` 配置文件：

```shell
jupyter lab --generate-config
```

修改以下配置：

```txt
c.NotebookApp.allow_root = True
c.NotebookApp.open_browser = False
c.NotebookApp.password = '刚才复制的一串数字粘贴到这里'
```

为了后续能够方便地安装插件，请先安装好`node`环境，假设你安装好，接下来演示一下怎么安装插件：

```shell
# 以安装一个生成目录的插件为例
jupyter labextension install @jupyterlab/toc
# 查看安装的插件
jupyter labextension list
```

安装完毕后，打开`JupyterLab`：

```shell
jupyter-lab --ip=0.0.0.0 
```

点击`Settings->Advanced Settings Editor`，将`false`改成`true`，如下图：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190320113152.png)

此时在界面左侧应该可以看到一个插件管理的图标，点击就可以看到刚才安装的插件

通过这个插件查询功能，你可以很方便的安装插件，安装完成后可以直接热更新，看一下我的`JupyterLab`首页：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190320114243.png)

画流程图、写代码、写文档，各种文档渲染、多语言支持，怎么样，有兴趣你也可以搭建一个~

> 插件  

`JupyterLab`目前的插件也算丰富，我目前使用的插件如下：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190320123219.png)

> 功能  

代码提示：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190320124326.png)

使用文档提示：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190320124408.png)

支持`vim emacs`等按键风格：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190320123958.png)

文档查看特别方便：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190320123935.png)

代码以及界面主题设置：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190320123535.png)

文档多窗口：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190320123455.png)

`cell`可以拖拽且输出可以新窗口显示：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190320124146.png)

随时启动新的终端交互：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190320124245.png)

这里只是捡了一些常见的功能说说，更多功能可以查看官方文档来发现，如果你有更好的使用技巧，欢迎交流~

> 更多  

- 官方地址：https://github.com/jupyterlab
- 文档：https://jupyterlab.readthedocs.io/en/stable/
- 插件：https://github.com/topics/jupyterlab-extension

![wechat_howie](https://cdn.jsdelivr.net/gh/howie6879/oss/uPic/qrcode_for_gh_3f02ace79dfb_258.jpg)