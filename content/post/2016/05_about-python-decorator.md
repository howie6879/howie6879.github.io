---
title: python之装饰器
date: 2016-07-31T20:24:53
categories:
  - Python
  - 教程
tags: []
toc: true
---

## 认识装饰器
在python中，对于一个函数，若想在其运行前后做点什么，那么装饰器是再好不过的选择，话不多说，上代码。
``` python
#!/usr/bin/env
# -*-coding:utf-8-*-
# script: 01.py
__author__ = 'howie'
from functools import wraps
def decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print("%s was called" % func.__name__)
        func(*args, **kwargs)
    return wrapper
@decorator
def hello(name="howie"):
    print("Hello %s!" % name)
hello()
```
```
outputs:
hello was called
Hello howie!
```
这段代码，初看之下，确实不是很理解，接下来一步一步分析，看看装饰器到底是怎么工作的。
<!-- more -->

## 装饰器原理

在python中，方法允许作为参数传递，想在某个函数执行前后加点料，也可以这样简单实现。
``` python
#!/usr/bin/env
# -*-coding:utf-8-*-
# script: 02-1.py
__author__ = 'howie'
def decorator(func):
    print("%s was called" % func.__name__)
    func()
def hello(name="howie"):
    print("Hello %s!" % name)
decorator(hello)
```
由此，上面代码也可以这样写：
``` python
#!/usr/bin/env
# -*-coding:utf-8-*-
# script: 02-2.py
__author__ = 'howie'
def decorator(func):
    print("%s was called" % func.__name__)
    func()
@decorator
def hello(name="howie"):
    print("Hello %s!" % name)
hello
```
两段代码执行后：
```
outputs:
hello was called
Hello howie!
```
表面上看来，`02-2.py`代码看起来也可以很好地执行啊，可请注意，在末尾处，`hello`只是函数名称，它并不能被调用，若执行`hello()`，就会报`TypeError: 'NoneType' object is not callable`对象不能调用错误，这是自然，在`decorator`中`func()`直接将传入的函数实例化了，有人会想，那如果这样改呢？
``` python
#!/usr/bin/env
# -*-coding:utf-8-*-
# script: 02-3.py
__author__ = 'howie'
def decorator(func):
    print("%s was called" % func.__name__)
    return func
@decorator
def hello(name="howie"):
    print("Hello %s!" % name)
hello()
```
确实，这样改是可以，可有没有想过，若想在函数执行结束后加点装饰呢？这样便行不通了，可能又有人会想，若这样改呢？
``` python
#!/usr/bin/env
# -*-coding:utf-8-*-
# script: 02-4.py
__author__ = 'howie'
def decorator(func):
    print("%s was called" % func.__name__)
    func()
    return bye
def bye():
    print("bye~")
@decorator
def hello(name="howie"):
    print("Hello %s!" % name)
hello()
```
这样写看起来，恩，怎么说呢，总有种没有意义的感觉，不如直接将在外部的函数放进`decorator`中，如下:
``` python
#!/usr/bin/env
# -*-coding:utf-8-*-
# script: 02-5.py
__author__ = 'howie'
def decorator(func):
    def wrapper():
      print("%s was called" % func.__name__)
      func()
      print("bye~")
    return wrapper
@decorator
def hello(name="howie"):
    print("Hello %s!" % name)
hello()
```
执行：
```
outputs:
hello was called
Hello howie!
bye~
```
怎么样，输出的结果是不是符合要求，其实简单来看的话，可以这样理解`hello()==decorator(hello)()==wrapper()`，最后其实就是执行`wrapper()`函数而已，事实就是如此的简单，不妨来验证一下：
``` python
#!/usr/bin/env
# -*-coding:utf-8-*-
# script: 02-6.py
__author__ = 'howie'
def decorator(func):
    def wrapper():
      print("%s was called" % func.__name__)
      func()
      print("bye~")
    return wrapper
@decorator
def hello(name="howie"):
    print("Hello %s!" % name)
hello()
print(hello.__name__)
```
```
outputs:
hello was called
Hello howie!
bye~
wrapper
```
果然就是执行了wrapper函数，解决问题的同时也会出现新的问题，那便是代码中本来定义的hello函数岂不是被wrapper函数覆盖了，又该如何解决这个问题呢？这时候`functions.wraps`就可以登场了，代码如下：
``` python
#!/usr/bin/env
# -*-coding:utf-8-*-
# script: 02-7.py
__author__ = 'howie'
from functools import wraps
def decorator(func):
    @wraps(func)
    def wrapper():
      print("%s was called" % func.__name__)
      func()
      print("bye~")
    return wrapper
@decorator
def hello(name="howie"):
    print("Hello %s!" % name)
hello()
print(hello.__name__)
```
执行代码：
```
outputs:
hello was called
Hello howie!
bye~
hello
```
`functions.wraps`作用是不是一目了然哈~到了这一步，再看01.py的代码，是不是代码结构清晰明了，只不过多了个参数~
``` python
#!/usr/bin/env
# -*-coding:utf-8-*-
# script: 01.py
__author__ = 'howie'
from functools import wraps
def decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print("%s was called" % func.__name__)
        func(*args, **kwargs)
    return wrapper
@decorator
def hello(name="howie"):
    print("Hello %s!" % name)
hello('world')
```
猜都猜得到执行后输出什么了。
## 结语
只要了解装饰器原理，不管是带参数的装饰器，还是装饰器类，都是小菜一碟。
若有错误，尽请指出。