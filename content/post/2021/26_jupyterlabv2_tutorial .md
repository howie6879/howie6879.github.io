---
title: "JupyterLab使用教程：程序员的笔记本神器v2.0"
date: 2021-11-15T20:35:47+08:00
lastmod: 2021-11-15T20:35:47+08:00
keywords: []
description: "JupyterLab使用教程，构造属于你的跨平台Web在线交互式代码实验室"
tags: [jupyterlab]
categories:
  - Python
  - 工具
  - 教程
author: "howie.hu"
image: /images/thumbs/h_11.jpg
toc: true
---

之前写过一份`JupyterLab`的使用教程，但是随着个人使用时间的增加和更多优秀插件的出现，断断续续我基于`JupyterLab:v3.0+`衍生出了自己的最佳实践，本篇文章将从以下方面更加全面地介绍`JupyterLab`：
- 搭建安装
- 基本功能
- 插件推荐
- 容器化最佳实践

最终成品如下图所示，有兴趣的话一起来使用看看吧✨
![JupyterLab-demo](https://images-1252557999.file.myqcloud.com/uPic/RK8fvL.png)

## 介绍

了解`JupyterLab`之前先说下什么是`Jupyter Notebook`，简单来说它是一个交互式的开发环境，其提供了在`Web`上编写运行代码的能力，还可以同时使用`md`语法编写文档。

而`JupyterLab`是`Jupyter Notebook`的下一代产品，如今已经发展到第三个大版本了，其主要优势有：
- 更现代化的界面设计和更灵活的架构
- 提供终端交互和文件浏览器
- 结合插件可以快速打造成Web上的IDE笔记本

通过使用`JupyterLab`，我们能够以灵活，集成和可扩展的方式处理代码与文档，对于以下人群非常有帮助：

- 随时随地希望试验一些代码片段，如Python、Julia、R、Go、Scala等
- 大数据开发&机器学习进行数据分析、处理、建模等
- 作为笔记本，可以将代码的输入输出解释直接导出成笔记文档，结合插件还可以绘制流程图等

## 安装

接下来，我将以Python为默认语言来搭建`JupyterLab`，这里我为了获取一个干净的环境直接使用`Docker`进行构建：

```shell
docker run -it --name jupyter_test -p 8888:8888 python:3.7-slim /bin/bash
```

用`Docker`目的是方便出错后删除重来，这里仅做演示，大家直接在本机操作即可，只需要确认你安装好了`Python`基本环境就行：

```shell
# Input
python

# Output
Python 3.6.15 (default, Oct 13 2021, 09:43:57)
[GCC 10.2.1 20210110] on linux
Type "help", "copyright", "credits" or "license" for more information.
```

有了`Python`环境就可以直接安装`JupyterLab`：

```shell
# 先换源
pip config set global.index-url https://pypi.douban.com/simple/
# 一行命令搞定
pip install jupyterlab
```

这一行命令我们就已经安装好了`JupyterLab`，启动起来试试看吧：

```shell
jupyter lab --allow-root --no-browser
```

直接访问：`http://127.0.0.1:8888/lab?token={TOKEN}`就能看到界面了！怎么样，方便吧。但是要想更加优雅地使用`JupyterLab`，还是得进行一些配置，让我们继续吧。

## 基本功能

### 设置密码

每次启动`JupyterLab`默认是通过`Token`的形式进行访问的，但是`Token`每次都不一样，而且还会过期，所以我们还是设置成密码的形式进行访问吧：

```shell
# 进入python交互环境
python
```

生成密码：

```python
from notebook.auth import passwd
passwd()
# 输入你自己设置登录JupyterLab界面的密码 然后会有一串输出，记得复制下来，等会配置需要使用
```

修改`JupyterLab` 配置文件：

```shell
# Input
jupyter lab --generate-config

# Output
Writing default config to: /root/.jupyter/jupyter_lab_config.py
```

编辑这个文件`vim /root/.jupyter/jupyter_lab_config.py`，设置以下参数：

```shell
c.ServerApp.allow_root = True
c.ExtensionApp.open_browser = False
c.ServerApp.password = 'argon2:$argon2id$v=19$m=10240,t=10,p=8$C2MHPsnHYOGQBXF30d7eag$bdeIwZFwzt7HOYkTECRPVA'
```

```shell
jupyter lab
```

此时再次进入`http://127.0.0.1:8888/`就需要输入密码了，为了方便后续一些功能的演示，我们顺便建立一个交互式脚本吧，操作流程如下图：

![](https://images-1252557999.file.myqcloud.com/uPic/9RBjIW.png)

### 代码交互

> 代码提示

进入文件后，我们先使用`!pip install numpy`安装一个第三方库用于测试。在代码关键字后按`Tab`即可看到代码提示：

![](https://images-1252557999.file.myqcloud.com/uPic/DG5jdh.png)

> 文档注释&查看

按`Shift + Tab`即可看到文档注释提示：

![](https://images-1252557999.file.myqcloud.com/uPic/vldGm0.png)

注：如果代码提示功能有问题，设置下`jedi`版本，`pip install jedi==0.17.2`。

对于数据科学方向的程序员来说，一些常用库的文档查看特别方便，`Jupyterlab`已经将常用文档内置，还提供了关键函数搜索功能，再也不用打开官方文档查找了：

![](https://images-1252557999.file.myqcloud.com/uPic/20190320123935.png)

> 代码调试

`JupyterLab`默认支持代码调试，使用也很简单，点击右上角的`Bug`图标开启即可：

![](https://images-1252557999.file.myqcloud.com/uPic/Hr2l3X.png)

> 文档多窗口

一般开发者的屏幕都是比较大的，这个功能特别适合左边窗口实验代码，右边看教程文档的情景：

![](https://images-1252557999.file.myqcloud.com/uPic/20190320123455.png)

> `cell`可以拖拽且输出可以新窗口显示

![](https://images-1252557999.file.myqcloud.com/uPic/20190320124146.png)

> 随时启动新的终端交互

![](https://images-1252557999.file.myqcloud.com/uPic/20190320124245.png)

### TOC目录

`JupyterLab`脚本是可以编写`MD`文本的，为了方便代码的可读性，侧边栏提供了目录展示功能，程序员可以更加方便地对自己代码进行管理：

![](https://images-1252557999.file.myqcloud.com/uPic/NRnOEU.png)

### 风格设置

> 按键风格

支持`vim emacs`等按键风格：

![](https://images-1252557999.file.myqcloud.com/uPic/20190320123958.png)

> 界面&代码主题

`JupyterLab`默认提供了黑暗&明亮两种主题，开发者可以自由使用，如果对于主题有更多要求，接下来在插件部分我会介绍我使用的插件主题：

![](https://images-1252557999.file.myqcloud.com/uPic/Ouehgc.png)

编辑器界面主题设置：

![](https://images-1252557999.file.myqcloud.com/uPic/20190320123535.png)

## 插件

`JupyterLab`本身的功能了解上面那么多就已经够日常基本使用了，但是社区的开发者还是贡献了很多有意思的插件。接下来我会介绍一些比较基础但又很实用的插件，让大家的效率进一步提升。

### [jupyterlab_code_formatter](https://github.com/ryantam626/jupyterlab_code_formatter)

代码格式写插件，我使用的是`Python`，用这个插件可以设置保存的时候自动用`black和isort`进行代码格式化，安装：

```shell
# 安装插件
pip install jupyterlab_code_formatter
# 安装格式化工具
pip install black isort
```

安装完成后，脚本界面右上角就会有一个格式化按钮，开发者也可以设置保存就触发格式化：

![](https://images-1252557999.file.myqcloud.com/uPic/PQjvBA.png)

### [ipydrawio](https://ipydrawio.readthedocs.io/en/stable/)

开发者除了日常开发，还有文档编写工作，涉及到文档编写，基本上都会涉及到流程图绘制需求，`ipydrawio`则可以让你方便地在`JupyterLab`绘制流程图，安装：

```shell
pip install ipydrawio[all]
```

使用起来也非常方便，还可以分享出去，支持绘制的类型如下：

![](https://images-1252557999.file.myqcloud.com/uPic/mlWD97.jpg)

安装成功后和之前新建脚本的方式一样绘制流程图：

![](https://images-1252557999.file.myqcloud.com/uPic/VBsYTe.png)

### [jupyterlab-unfold](https://github.com/jupyterlab-contrib/jupyterlab-unfold)

这个插件让`JupyterLab`的文件浏览具有了和IDE一样的功能，原本`JupyterLab`在目录间跳转是一级一级进出，有了这个插件可以按照目录树的形式进行操作，极大地提升了打开不同目录下脚本的效率。

```shell
pip install jupyterlab-unfold
```

使用效果截图：

![](https://images-1252557999.file.myqcloud.com/uPic/BcaByz.jpg)

### Theme

`JupyterLab`本身仅提供了简单的黑白两套主题，但是我对代码主题还是挺有追求的，所以也在社区找了下看是否有比较好的主题实现，万幸我个人常用的主题都有其他人开源了出来，大家有兴趣了可以试试：

- [theme-darcula](https://github.com/telamonian/theme-darcula)：非常漂亮的`Darcula`主题，目前在`JupyterLab`主题里面热度最高
- [jupyterlab-theme-solarized-dark](https://github.com/AllanChain/jupyterlab-theme-solarized-dark)：`Solarized` 黑暗主题，效果可以看第一部分的效果展示图

安装：

```shell
pip install theme-darcula
pip install jupyterlab-theme-solarized-dark
```

`Darcula`主题效果图如下：

![](https://images-1252557999.file.myqcloud.com/uPic/xrqZtr.png)

### 汉化

官方针对中文用户提供了汉化包，还是挺良心的：

```shell
pip install jupyterlab-language-pack-zh-CN
```
效果如下：

![](https://images-1252557999.file.myqcloud.com/uPic/CzKfj2.png)

## 容器化最佳实践

为了方便在不同系统环境下能够快速使用`JupyterLab`，于是我选用`Docker`构建镜像，具体配置见[pylab-jupyterlab-docker](https://github.com/howie6879/pylab/blob/master/docker/jupyterlab/Dockerfile)这样可以直接将配置和插件全部统一为镜像，这样有以下好处：

- 跨平台，其他平台只要安装`Docker`就可以一键使用`JupyterLab`
- 插件配置：
  - 保存则自动代码格式化
  - 主题支持：`Solarized`和`Darcula`
  - 中文汉化支持，默认还是英文
  - 代码变量跳转支持
  - 流程图、目录树等
- 终端支持`oh my zsh`

所以上面介绍了这么多，你只需要看一遍下来熟悉下相关概念即可，倒是不用从头都搭一遍，如果你要使用，运行下面这行命令即可：

```shell
docker run --name jupyter_pylab -it -d --restart=always -p 0.0.0.0:8765:8888 -e SHELL="/bin/zsh" -v "`pwd`:/project-dir" howie6879/jupyter-lab-for-python37:v3.1.4 --allow-root --no-browser --port=8888
```

享受`JupyterLab`吧，如有错漏，请留言交流，谢谢。

