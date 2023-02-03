---
title: "梯度下降推导"
date: 2021-01-14T22:35:47+08:00
lastmod: 2021-01-14T22:35:47+08:00
draft: false
keywords: []
toc: true
description: "梯度下降数学推导"
tags: [神经网络与深度学习,nndl-book]
categories: [ML&DL]
author: "howie.hu"
markup: mmark
image: /images/thumbs/h_55.png
---

以感知器为例，可以梯度下降来学习合适的权重和偏置：

![shadow-感知器图示](https://cdn.jsdelivr.net/gh/howie6879/oss/uPic/image-20210114211230457.png)

假设有`n`个样本，第`i`次的实际输出为`y`，对于样本的预测输出可以表示为：

$$
\bar{y}^i = w_1x_1^i+w_2x_2^i+...+w_nx_n^i+b
$$

任意一个样本的实际输出和预测输出单个样本的误差，可以使用`MES`表示：

$$
e^i=\frac{1}{2}(y^i-\bar{y}^i)^{2}
$$

那么所有误差的和可以表示为：

$$
\begin{aligned}
E &= e^1+e^2+...+e^n
\\ &= \sum_{i=1}^ne^i
\\ &= \frac{1}{2}\sum_{i=1}^n(y^i-w^Tx^i)^2
\end{aligned}
$$

想象一下，当你从山顶往下走，只要你沿着最陡峭的位置往下走，那么终将走到最底部（也可能是局部最低）：

![img](https://cdn.jsdelivr.net/gh/howie6879/oss/uPic/1042406-20161017221342935-1872962415.png)

我们学习的目的就是在$E$尽量最小，然后得到此时的$w$和$b$，前面说的**最陡峭的位置**该怎么定义呢？我们可以引入**梯度**，这是一个向量，指的是函数值上升最快的方向，那么**最陡峭的位置**就可以用在最陡峭的方向迈出一步（步长，学习速率），用数学公式表示为：

$$
\mathbf{x}_{n e w}=\mathbf{x}_{\text {old }}-\eta \nabla f(x)
$$

其中：

- $\nabla$表示梯度算子
- $\nabla f(x)$表示函数的梯度
- $\eta$表示梯度、学习速率，可以理解为找准下山的方向后要迈多大步子

现在有了目标函数，也知道怎么找到让目标函数值最小的办法，对于参数$w$：

$$
E_{(w)} = \frac{1}{2}\sum_{i=1}^n(y^i-w^Tx^i)^2
$$

那么$W$值的更新公式为：

$$
w_{n e w}=w_{\text {old }}-\eta \nabla E_{(w)}
$$

关键步骤来了，来看看$E_{(w)}$的推导吧：

$$
\begin{aligned}
\nabla E(\mathrm{w}) &=\frac{\partial}{\partial \mathrm{w}} E(\mathrm{w}) \\
&=\frac{\partial}{\partial \mathrm{w}} \frac{1}{2} \sum_{i=1}^{n}\left(y^{(i)}-\bar{y}^{(i)}\right)^{2}
\\&=\frac{1}{2}\frac{\partial}{\partial \mathrm{w}} \sum_{i=1}^{n}
\left(y^{(i)2}-2y^{(i)}\bar{y}^{(i)}+\bar{y}^{(i)2}\right)
\end{aligned}
$$

再引入链式求导法则：

$$
\begin{aligned}
\nabla E(\mathrm{w}) &=\frac{\partial}{\partial \mathrm{w}} E(\mathrm{w}) \\
&=\frac{1}{2} \sum_{i=1}^{n} \frac{\partial}{\partial \mathrm{w}}\left(y^{(i) 2}-2 y^{(i)} \bar{y}^{(i)}+\bar{y}^{(i) 2}\right) \\
&=\frac{1}{2} \sum_{i=1}^{n}\left(\frac{\partial}{\partial \bar{y}^{(i)}}\left(y^{(i) 2}-2 y^{(i)} \bar{y}^{(i)}+\bar{y}^{(i) 2}\right) \frac{\partial y_{(i)}}{\partial \mathrm{w}}\right) \\
&=\frac{1}{2} \sum_{i=1}^{n}\left(\left(-2 y^{(i)}+2 \bar{y}^{(i)}\right) \mathbf{x}^{(i)}\right) \\
&=-\sum_{i=1}^{n}\left(y^{(i)}-\bar{y}^{(i)}\right) \mathrm{x}^{(i)}
\end{aligned}
$$

前面提到$W$值的更新公式为：

$$
w_{n e w}=w_{\text {old }}-\eta \nabla E_{(w)}
$$

将上面计算结果带入：

$$
w_{n e w}=w_{\text {old }}+\eta \sum_{i=1}^{n}\left(y^{(i)}-\bar{y}^{(i)}\right) \mathrm{x}^{(i)}
$$

参数的更新方式就这样计算出来了，其实所谓的学习，就是确定一个目标函数用一定的计算方法让其算出最优的参数。

<div align=center><img width="20%" src="https://cdn.jsdelivr.net/gh/howie6879/oss/uPic/qrcode_for_gh_3f02ace79dfb_258.jpg" /></div>