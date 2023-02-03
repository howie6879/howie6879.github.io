---
categories:
  - 工具
  - Linux
date: 2019-04-15 16:16:58+08:00
image: /images/thumbs/h_47.jpg
tags: [效率]
title: "oh-my-zsh：让终端飞"
toc: true
---

上一次推文写了JupyterLab：程序员的笔记本神器，介绍的是如何在web端打造一个便捷的开发环境，发出后反响还不错

因此我决定再写几篇能提升程序员工作以及学习效率的文章，如果能形成一个系列那是最好~如果你有自己的效率工具以及方案，欢迎留言讨论

## 什么是oh-my-zsh

诸位大佬都知道，`Linux`下`shell`默认是`bash`，但还有一种`shell`，叫做`zsh`它比`bash`更加强大，功能也更加完善，`zsh`虽说功能强大，但是配置比较复杂导致流行度不是很高

但是好东西终究是好东西，开源界的大佬们是不会让明珠蒙尘，我等伸手党也是可以直接搭顺风车的，感谢`robbyrussell`大佬的开源项目 [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh) 吧，从此使用`zsh`也就几行命令的事情

`oh-my-zsh`项目目前有`80k+star`，贡献者超过`1300`，并且提供了200多个可选插件（rails，git，OSX，hub，capistrano，brew，ant，php，python等），以及超过140个主题供你选择，安装后你将享受以下特性：

- 首先兼容bash
- 自动cd：只需输入目录的名称即可
- 命令选项补齐，比如输入`git`，然后按`Tab`，即可显示出`git`都有哪些命令
- 目录一次性补全：比如输入`Doc/doc`按`Tab`键会自动变成`Documents/document/`
- 插件和主题支持（插件能进一步提升效率）

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/zsh_demo.gif)

## 安装oh-my-zsh

在安装oh-my-zsh之前，首先需要安装好`zsh`：

```shell
yum install -y zsh
```

切换shell为zsh：

```shell
chsh -s /bin/zsh
```

重启终端：

```shell
# 查看当前shell
echo $SHELL
```

输出`/bin/zsh`表示成功

oh-my-zsh的安装非常简单，参考官网，执行如下命令即可：

```shell
# curl
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

# wegt 
sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

输出如下表示成功：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190415090802.png)

## 配置oh-my-zsh

和`bash`不同，`zsh`的配置文件是` ~/.zshrc `，实际上`oh-my-zsh`的默认配置也够我们使用了，但是这样其真正的强大之处并不能得到很好的体现，因此我们可以继续看看对应的插件和主题功能

### 关于主题

`oh-my-zsh`的主题非常丰富，可以用如下命令查看已有主题：

```shell
ls .oh-my-zsh/themes
```

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/20190415092612.png)

个人比较喜欢简单的，因此用了`wedisagree`主题，进入`.zshrc`配置文件进行修改

```shell
vim ~/.zshrc
```

将第11行改为`ZSH_THEME="wedisagree"`，然后`:wq`保存退出，主题就自动生效

### 关于插件

`oh-my-zsh`的插件生态非常丰富，下面列出来的是我个人比较常用的插件，如果你有兴趣，可以取发掘能提高自身效率的插件~

**注意**：如果操作过程中出现`_arguments:448: _vim_files: function definition file not found`错误，请执行：`rm -f ~/.zcompdump`即可

#### incr

`incr`是一款自动提示插件，功能非常强大，官网演示demo，感受一下：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/incr.gif)

安装：

```shell
wget http://mimosa-pudica.net/src/incr-0.2.zsh
mkdir ~/.oh-my-zsh/plugins/incr
mv incr-0.2.zsh ~/.oh-my-zsh/plugins/incr
echo 'source ~/.oh-my-zsh/plugins/incr/incr*.zsh' >> ~/.zshrc
source ~/.zshrc
```

可以开心的敲命令行了~

#### autojump

这款插件基本上算是必备插件了，在终端操作里面比较常用的算是文件夹之间的切换，这款插件极大地简化了路径跳转的操作，在一键直达的功能下，自动补全也就一般般了哈

先安装：

```shell
yum install autojump-zsh
chmod 777 /usr/share/autojump/autojump.bash
echo "/usr/share/autojump/autojump.bash" >> ~/.zshrc
source ~/.zshrc
```

效果如下：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/autojump01.gif)

以前的`cd code`现在可以直接`j c`，路径越长，该插件效果就越明显

#### zsh-autosuggestions

这是一个命令自动补全插件，当你输入命令的几个字母，它会自动根据历史输入进行自动补全，然后按`→`，安装也很简单：

```shell
git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
vim ~/.zshrc
# 加入插件列表
plugins=(
  git
  zsh-autosuggestions
)
source ~/.zshrc
```

该插件已经在第一个动图有演示，这里不再重复

#### autoswitch_virtualenv

这个插件对于Python开发者来说可以算是神器了，在实际开发过程中，基本上一个Python项目就对应了一个新的虚拟环境，如果你使用`pipenv`，当你需要进入项目的虚拟环境时候，就需要执行`pipenv shell`命令，安装`autoswitch_virtualenv`后，该插件可以自动地完成这些事情：

```shell
git clone "https://github.com/MichaelAquilina/zsh-autoswitch-virtualenv.git" "$ZSH_CUSTOM/plugins/autoswitch_virtualenv"

vim ~/.zshrc
# 加入插件列表
plugins=(
  git
  zsh-autosuggestions
	autoswitch_virtualenv
)
source ~/.zshrc
```

该插件已经在第一个动图里面体现的很明显，这里不再重复演示

#### zsh-syntax-highlighting

这个插件的主要作用就是在提高颜值（高亮你的zsh可用命令），安装如下：

```shell
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
vim ~/.zshrc
# 加入插件列表
plugins=(
  git
  zsh-autosuggestions
  zsh-syntax-highlighting
)
source ~/.zshrc
```

效果如下图：

![](https://cdn.jsdelivr.net/gh/howie6879/oss/images/1547459A-3EFB-4366-B813-CFDF5BF8A0B0.png)

## 总结

程序员在开发过程中，效率快慢是个不可忽略的因素，提高效率，能一定程度上节省开发时间，从而形成一系列的正向反馈，何乐而不为？

![wechat_howie](https://cdn.jsdelivr.net/gh/howie6879/oss/uPic/qrcode_for_gh_3f02ace79dfb_258.jpg)
