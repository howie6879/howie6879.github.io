---
title: cx_Freeze打包py文件
date: 2016-04-17T20:30:57
categories:
  - Python
  - 工具
tags: []
---

最近需要将python代码打包成exe，打包过程中出现了一些问题，特此记录，也顺便记录下cx_Freeze使用方法，留待日后查看。

<!-- more -->

首先进行[下载](https://sourceforge.net/projects/cx-freeze/files/)，需要注意对应的版本号，比如本人使用python3.4，64位，故下载[cx_Freeze-4.3.3.win-amd64-py3.4.msi](https://sourceforge.net/projects/cx-freeze/files/4.3.3/cx_Freeze-4.3.3.win-amd64-py3.4.msi/download)这个版本，下载后在`python`安装目录下就可以看到cxfreeze,然后配置好环境变量就可以使用了,如图：
![tJkBP6](https://images-1252557999.file.myqcloud.com/uPic/tJkBP6.jpg)

![v0TaQN](https://images-1252557999.file.myqcloud.com/uPic/v0TaQN.jpg)

`cxfreeze`有两种打包方式，一是`cxfreeze script`,这种方式很简单，只要打开`cmd`，`cd`到`python`文件所在目录，比如文件名为`hello.py`,执行：
```
cxfreeze hello.py --target-dir dist
```
如果要生成可安装包文件，就要用到这种打包方式，名为`distutils setup script`，这种方式必须创建一个`setup.py`文件，可以使用官方提供的：
``` python
import sysfrom cx_Freeze import setup, Executable
# Dependencies are automatically detected, but it might need fine tuning.
build_exe_options = {"packages": ["os"], "excludes": ["tkinter"]}
# GUI applications require a different base on Windows (the default is for a console application).
base = None
if sys.platform == "win32": 
    base = "Win32GUI"
setup( name = "guifoo", 
       version = "0.1", 
       description = "My GUI application!", 
       options = {"build_exe": build_exe_options}, 
       executables = [Executable("guifoo.py", base=base)]
)
```
或者在`cmd `窗口输入`cxfreeze-quickstart`也可以自动生成`setup.py`，如下：

![88QkOI](https://images-1252557999.file.myqcloud.com/uPic/88QkOI.jpg)

接下来只要到python文件的目录，运行
```
python setup.py bdist_msi
```
如果想更加详细了解操作方法，可以查看官方文档
>官方操作文档：[查看](http://cx-freeze.readthedocs.org/en/latest/overview.html)

# *问题*
可是我在使用```
cxfreeze hello.py --target-dir dist```后，发现生成的exe文件无法运行，总是一闪而过，心好累，一番谷歌，找到如下解决办法：
1、去这个网站下载cx_Freeze（注意32/64位）
[cx_Freeze‑4.3.4‑cp34‑none‑win64.whl](http://www.lfd.uci.edu/~gohlke/pythonlibs/)
2、把扩展名whl，改为zip进行解压
4、然后进入`C:\Python34\Lib\site-packages`，请参考各自python安装路径，删除cx_Freeze相关的包，我这里有两个，全部都删除掉
5、然后将`cx_Freeze‑4.3.4‑cp34‑none‑win64`目录下的文件夹全部复制到`C:\Python34\Lib\site-packages`，如图：


![9TiL5v](https://images-1252557999.file.myqcloud.com/uPic/9TiL5v.jpg)

最后进行打包，```cxfreeze hello.py --target-dir dist```,终于可以运行了，如果想打包成一个exe文件的话，可以将dist文件夹下面的文件全部创建自解压文件，不会的看[这里](http://jingyan.baidu.com/article/597035523d9ec28fc00740a7.html)。

这样就解决了，希望能有帮助。