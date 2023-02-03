---
title: vscode搭建haskell环境
date: 2017-02-11T14:58:20
categories:
  - 工具
tags: [vscode]
toc: true
---

![haskell](https://cdn.jsdelivr.net/gh/howie6879/oss/images/dynx43.jpg)

## 前言
> An advanced, purely functional programming language

为什么学习这门语言，现在我也讲不清楚，但如官网所说：这个是一门高级的纯函数式编程语言，本人也是刚刚接触，在此记录下使用vscode搭建环境的过程。

<!-- more-->

## 安装
具体安装的话看[这里](https://www.haskell.org/downloads)就好，本人使用mac，经过后面的折腾，我建议直接安装`stack`，这个一些特性可以看下面：
> Installing GHC automatically, in an isolated location.
> Installing packages needed for your project.
> Building your project.
> Testing your project.
> Benchmarking your project.

安装的话很简单：`brew install haskell-stack`，虽然花的时间略长，但是好用又方便啊，如果不想使用`satck`，那么可以直接安装：

``` shell
brew install ghc
brew install cabal-install
```
个人推荐使用`stack`，具体安装以及介绍可以看[这里](https://docs.haskellstack.org/en/stable/README/)。
毕竟源在国外，所以我们首先必须要进行换源，幸好[清华大学开源网站镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/stackage/)有提供，更具体一点可以看[Stackage 镜像使用说明](https://zhuanlan.zhihu.com/p/25005809?refer=marisa)，这里记录下：

``` shell
vim ~/.stack/config.yaml
# add
package-indices:
- name: Tsinghua
  download-prefix: https://mirrors.tuna.tsinghua.edu.cn/hackage/package/
  http: https://mirrors.tuna.tsinghua.edu.cn/hackage/00-index.tar.gz
setup-info: "http://mirrors.tuna.tsinghua.edu.cn/stackage/stack-setup.yaml"
urls:
  latest-snapshot: http://mirrors.tuna.tsinghua.edu.cn/stackage/snapshots.json
  lts-build-plans: http://mirrors.tuna.tsinghua.edu.cn/stackage/lts-haskell/
  nightly-build-plans: http://mirrors.tuna.tsinghua.edu.cn/stackage/stackage-nightly/
# 开始使用stack，这个命令需要稍稍等待
stack setup
# 安装完成之后
stack ghci
# 会出现以下输出
Configuring GHCi with the following packages:
GHCi, version 8.0.1: http://www.haskell.org/ghc/  :? for help
Loaded GHCi configuration from /private/var/folders/0s/j3c0tlx10z9_x9wzhl14xmgh0000gn/T/ghci11066/ghci-script
Prelude>
```
至此，已经安装完毕。
## 搭建
打开`vscode`，下载extension，这里我推荐这四个插件：`Haskell Syntax Highlighting、Haskell ghc-mod 、haskell-linter、Haskelly `，其中第四个插件离不开`stack`。
要想使用以上插件，必须安装以下几个包：

``` shell
# for Haskell ghc-mod 
stack install ghc-mod
# for haskell-linter
stack install hlint
# for Haskelly
stack install intero
stack install QuickCheck
stack install stack-run
```
然后打开`vscode`的配置文件，加上`ghc-mod和hlint`的路径，如下：

``` json
"haskell.ghcMod.executablePath": "/Users/howie/.cabal/bin/ghc-mod",
"haskell.hlint.executablePath": "/Users/howie/.cabal/bin/hlint"
```
![zamUqE](https://cdn.jsdelivr.net/gh/howie6879/oss/images/zamUqE.jpg)

