---
title: Sanic 使用教程 - 4.展示一个页面
date: 2018-01-05T09:20:14
image: /images/thumbs/h_01.jpg
categories:
  - Python
  - 教程
tags: [Sanic]
toc: true
---

前面一节介绍[项目结构](https://github.com/howie6879/Sanic-For-Pythoneer/blob/master/docs/part1/3.%E9%A1%B9%E7%9B%AE%E7%BB%93%E6%9E%84.md)的时候，很粗略地讲了下如何将rss的文章内容在网页上进行展示。

相信你应该已经了解清楚，`sanic`是怎么接收请求并返回被请求的资源的，简单来说概括如下：

- 接收请求
- 找到对应的路由并执行路由对应的视图函数
- [Jinja2](http://jinja.pocoo.org/docs/2.10/)模板渲染返回视图

<!--- more --->

## 路由和视图函数

在此我假设你理解 `python` 中的装饰器，如果你并不清楚，可以看我另写的关于[装饰器](http://blog.howie6879.cn/2016/09/28/04/)的介绍，回归正题，还记得第一节中的代码实例么？

``` python
#!/usr/bin/env python
from sanic import Sanic
from sanic.response import text

app = Sanic()

# 此处将路由 / 与视图函数 test 关联起来
@app.route("/")
async def test(request):
    return text('Hello World!')


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```

在前言介绍里，出现这几个名词 **路由 视图函数 视图** ,在上面那段代码中，`test` 就是视图函数。

这是一段执行逻辑，比如客户端请求 `0.0.0.0:8000/` 此时返回的内容就是由`test` 这个视图函数提供的。

在我看来，视图函数就是一个纽带，它起到承上启下的作用，那么，到底是怎样的承上启下呢？让我们结合代码([sanic0.1.2源码](https://github.com/howie6879/Sanic-For-Pythoner/blob/master/docs/part2/1.Sanic%E6%BA%90%E7%A0%81%E9%98%85%E8%AF%BB-%E5%9F%BA%E4%BA%8E0.1.2.md))来分析下：

``` python

@app.route("/")
async def test(request):
    return text('Hello World!')

```

这个路由装饰器的作用很简单，就是将 `/` 这个 `uri` 与视图函数`test`关联起来，或许你可以将路由想象成一个 `dict`，当客户端若请求 `0.0.0.0:8000/`，路由就会`get` `/` 对应的视图函数`test`，然后执行。

其实真实情况和我们想象的差不多，请看 `sanic.py` 中的第三十行:

``` python

# Decorator
def route(self, uri, methods=None):

    def response(handler):
        # 路由类的add 方法将视图函数handler 与uri 关联起来
        # 然后整个路由列表会新增一个 namedtuple 如下：
        # Route(handler=handler, methods=methods_dict, pattern=pattern, parameters=parameters)
        self.router.add(uri=uri, methods=methods, handler=handler)
        return handler

    return response

```

此时，路由就和 `uri` 对应的视图函数关联起来了，这就是承上，路由和视图函数就是这样对应的关系。
103行有个`handle_request`函数：

``` python

async def handle_request(self, request, response_callback):
    """
    Takes a request from the HTTP Server and returns a response object to be sent back
    The HTTP Server only expects a response object, so exception handling must be done here
    :param request: HTTP Request object
    :param response_callback: Response function to be called with the response as the only argument
    :return: Nothing
    """

```

当服务器监听到某个请求的时候，`handle_request`可以通过参数中的`request.url` 来找到视图函数并且执行，随即生成视图返回，这便是所谓的启下。

其实浏览器显示的内容就是我们所谓的视图，视图是视图函数生成的，其中Jinja2起到模板渲染的作用。

## 蓝图

到这里，你一定已经很明白sanic框架是怎么处理一个请求并将视图返回给客户端，然后也掌握了如何编写自己定义的模板(html)以及样式(css)，通过前面一节[项目结构](https://github.com/howie6879/Sanic-For-Pythoneer/blob/master/docs/part1/3.%E9%A1%B9%E7%9B%AE%E7%BB%93%E6%9E%84.md)的介绍，我们可以总结出如下经验：

- 对于css、js等静态文件，常规操作是将其放在自己建立的statics下面
- 对于html模板，常规操作是将其放在自己建立的templates下面
- 视图函数(即服务的逻辑实现)放在自己建立的views下面

是时候考虑以下这种情况了，你需要编写一个比较复杂的http服务，你需要定义几百个路由，在编写过程中你会发现有许多不顺心的地方：

- 各种不同类型的路由互相交杂在一起，命名困难，管理困难
- 不同页面的css文件同样堆积在一起，html也是如此
- ...

实在是令人烦恼，可能你想要一个模块化的编写方式，一个文件编写后台，url统一是`/admin/***`，一个文件编写发帖，`/post/***` 等等，各个文件下面url自动会带上自定义的前缀，不用考虑命名问题，不用重复写url前缀，多么美好

**Blueprint**，就是sanic为你提供的解决方案，依然是上节rss的例子，让我们利用Blueprint来简单实现一下我们的需求，比如我们要构建的网站分为两个部分：

- `/json/index` 返回json格式的数据
- `/html/index` 返回html视图

我们在上节代码的基础上添加Blueprint，先看看定好的项目结构：

``` shell

.
├── __init__.py
├── config
│   ├── __init__.py
│   ├── config.py
│   ├── dev_config.py
│   └── pro_config.py
├── run.py
├── statics
│   ├── rss_html # rss_html蓝图的 css js 文件存放目录
│   │   ├── css
│   │   │   └── main.css
│   │   └── js
│   └── rss_json # rss_json蓝图的 css js 文件存放目录
├── templates
│   ├── rss_html # rss_html蓝图的 html 文件存放目录
│   │   ├── index.html
│   │   └── rss.html
│   └── rss_json # rss_json蓝图的 html 文件存放目录
│       └── index.html
└── views
    ├── __init__.py
    ├── rss_html.py # rss_html 蓝图
    └── rss_json.py # rss_json 蓝图

```

蓝图的目的就是让我们构建的项目灵活，可扩展，容易阅读，方便管理，一个个小的蓝图就构建了一个大型的项目，run.py里面可以随意组合注册蓝图，可抽插式的构建我们的服务，[sample01](https://github.com/howie6879/Sanic-For-Pythoneer/tree/master/examples/demo04/sample01)里是本次示例的代码，建议先读一遍，十分简单，主要就是讲路由根据你自己定义的特性分割成一个个蓝图，比如这里的`rss_html`和`rss_json`:

``` python

#!/usr/bin/env python
# 部分代码
# rss_html.py
import sys

from sanic import Blueprint
from sanic.response import html

from src.config import CONFIG


html_bp = Blueprint('rss_html', url_prefix='html')
html_bp.static('/statics/rss_html', CONFIG.BASE_DIR + '/statics/rss_html')

@html_bp.route("/")
async def index(request):
    return await template('index.html')

#!/usr/bin/env python
# 部分代码
# rss_json.py
import sys

from sanic import Blueprint
from sanic.response import html

from src.config import CONFIG


json_bp = Blueprint('rss_json', url_prefix='json')
json_bp.static('/statics/rss_json', CONFIG.BASE_DIR + '/statics/rss_json')

@json_bp.route("/")
async def index(request):
    return await template('index.html')

```

不知你是否感受到这样编写服务的好处，让我们列举下：

- 每个蓝图都有其自己定义的前缀，如 /html/ /json/
- 不同蓝图下面route可相同
- html以及css等 根据蓝图名称目录引用
- 模块化
- ...

不多说，看运行效果：

``` shell

cd /Sanic-For-Pythoneer/examples/demo04/sample01/src
python run.py

```

现在，请访问：

- http://0.0.0.0:8000/html/
- http://0.0.0.0:8000/json/
- http://0.0.0.0:8000/html/index
- http://0.0.0.0:8000/json/index

感觉这个设计方便了全世界^_^

## 说明

代码地址：[demo04](https://github.com/howie6879/Sanic-For-Pythoneer/tree/master/examples/demo04/)
