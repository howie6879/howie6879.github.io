---
categories:
  - Python
  - 教程
date: 2018-01-13T19:12:30+08:00
image: /images/thumbs/h_08.jpg
tags: [Sanic]
title: "Sanic 使用教程 - 6.常用的技巧"
toc: true
---

结合前面讲的配置、项目结构、页面渲染、数据库连接，构造一个优雅的Sanic应用对你来说估计没什么大问题了，但是在实际使用过程中，可能你会碰到各种各样的需求，与之对应，你也会遇到千奇百怪的问题，除了在官方pro提issue，你大部分问题都需要自己去面对，看官方的介绍大概就可以明白`Sanic`框架的重心不会放在诸如`session cache reload authorized`这些问题上

本文最新内容可到github上阅读 [6.常用的技巧](https://github.com/howie6879/Sanic-For-Pythoneer/blob/master/docs/part1/6.%E5%B8%B8%E7%94%A8%E7%9A%84%E6%8A%80%E5%B7%A7.md)

> Async Python 3.5+ web server that's written to go fast

此篇我将将我遇到的一些问题以及解决方案一一记录下来，估计会持续更新，因为问题是不断的哈哈，可能有些问题与前面讲的有些重复，你大可略过，我将其总结成一些小技巧，供大家参考，具体如下：

- api请求json参数以及api接口验证
- gRPC的异步调用方式
- Blueprint
- html&templates编写
- cache
- 热加载
- session

对于一些问题，我将编写一个小服务来演示这些技巧，具体见[demo06](https://github.com/howie6879/Sanic-For-Pythoneer/tree/master/examples/demo06)，依旧使用前面rss的那个例子，经过修改一番后的rss例子现在目录变成这样子了，里面加了我遇到的各种问题的解决方案，或许你只需要关注你想要了解的就好，如果你有其他的问题，欢迎issue提问，目录大概如下所示：

``` shell
src
├── config
│   ├── __init__.py
│   ├── config.py
│   ├── dev_config.py
│   └── pro_config.py
├── database
│   ├── __init__.py
│   └── redis_base
├── grpc_service
│   ├── __init__.py
│   ├── grpc_asyncio_client.py
│   ├── grpc_client.py
│   ├── grpc_server.py
│   ├── hello_grpc.py
│   ├── hello_pb2.py
│   ├── hello_pb2_grpc.py
│   └── proto
├── statics
│   ├── rss_html
│   │   ├── css
│   │   └── js
│   └── rss_json
│       ├── css
│       └── js
├── templates
│   ├── rss_html
│   └── rss_json
├── tools
│   ├── __init__.py
│   └── mid_decorator.py
├── views
│   ├── __init__.py
│   ├── rss_api.py
│   ├── rss_html.py
│   └── rss_json.py
└── run.py
```

和前面相比，可以看到目录里面增加了几个目录和文件，一个是关于数据库的`database`，以及关于gRPC的`grpc_service`，其实主要是为了演示而已，如果想了解，就可以看关于gRPC的部分，不喜欢就看其他的。

增加的文件是`rss_json.py`，假设这是个接口文件吧，将会根据你的请求参数返回一串json，具体还是建议直接去看代码吧，点这里[sample](https://github.com/howie6879/Sanic-For-Pythoneer/tree/master/examples/demo06/sample)。

## 验证问题

假设你正在编写一个api服务，比如根据传的blog名字返回其rss数据，比如`rss_json.py`：

``` python
#!/usr/bin/env python
from feedparser import parse
from sanic import Blueprint
from sanic.response import json

api_bp = Blueprint('rss_api', url_prefix='v1')


@api_bp.route("/get/rss/<param>")
async def get_rss_json(request, param):
    if param == 'howie6879':
        url = "http://blog.howie6879.cn/atom.xml"
        feed = parse(url)
        articles = feed['entries']
        data = []
        for article in articles:
            data.append({"title": article["title_detail"]["value"], "link": article["link"]})
        return json(data)
    else:
        return json({'info': '请访问 http://0.0.0.0:8000/v1/get/rss/howie6879'})
```

启动服务后，此时用`GET`方式访问`http://0.0.0.0:8000/v1/get/rss/howie6879`，就会返回一串你需要的json，这样使用，没什么问题，好，下面改成post请求，其中请求的参数如下：

``` json
{
    "name": "howie6879"
}
```

在`rss_json.py`中加入一个新的视图函数：

``` python
@api_bp.route("/post/rss/", methods=['POST'])
async def post_rss_json(request, **kwargs):
    post_data = json_loads(str(request.body, encoding='utf-8'))
    name = post_data.get('name')
    if name == 'howie6879':
        url = "http://blog.howie6879.cn/atom.xml"
        feed = parse(url)
        articles = feed['entries']
        data = []
        for article in articles:
            data.append({"title": article["title_detail"]["value"], "link": article["link"]})
        return json(data)
    else:
        return json({'info': '参数错误'})
```

发送post请求到`http://0.0.0.0:8000/v1/post/rss/`，依旧和上面的结果一样，代码里面有个问题，我们需要判断`name`参数是不是存在于post过来的data中，解决方案很简单，加一个判断就好了，可这个是最佳方案么？

显然并不是的，如果有十个参数呢？然后再增加十几个路由呢？每个路由里有十个参数需要判断，想象一下，这样实施下来，难道要在代码里面堆积满屏幕的判断语句么？这样实在是太可怕了，我们需要更好更通用的解决方案，是时候写个装饰器来验证参数了，打开`mid_decorator.py`文件，添加一个验证的装饰器函数：

``` python
def auth_params(*keys):
    """
    api请求参数验证
    :param keys: params
    :return:
    """

    def wrapper(func):
        @wraps(func)
        async def auth_param(request=None, rpc_data=None, *args, **kwargs):
            request_params, params = {}, []
            if isinstance(request, Request):
                # sanic request
                if request.method == 'POST':
                    try:
                        post_data = json_loads(str(request.body, encoding='utf-8'))
                    except Exception as e:
                        return response_handle(request, {'info': 'error'})
                    else:
                        request_params.update(post_data)
                        params = [key for key, value in post_data.items() if value]
                elif request.method == 'GET':
                    request_params.update(request.args)
                    params = [key for key, value in request.args.items() if value]
                else:
                    return response_handle(request, {'info': 'error'})
            else:
                pass

            if set(keys).issubset(set(params)):
                kwargs['request_params'] = request_params
                return await dec_func(func, request, *args, **kwargs)
            else:
                return response_handle(request, {'info': 'error'})

        return auth_param

    return wrapper


async def dec_func(func, request, *args, **kwargs):
    try:
        response = await func(request, *args, **kwargs)
        return response
    except Exception as e:
        return response_handle(request, {'info': 'error'})
```

注意，上面增加的路由函数改为这样：

``` python
@api_bp.route("/post/rss/", methods=['POST'])
@auth_params('name')
async def post_rss_json(request, **kwargs):
```

这样么一个请求进来，就会验证name是否存在，而在视图函数里面，就可以放心大胆地使用传进来的参数了，而且对于其他不同的参数验证，只要按照这个写法，直接增加验证参数就好，十分灵活方便。

对于请求验证的问题，解决方法也是类似，就是利用装饰器来实现，我自己也实现过，在上面的代码链接里面可以找到，不过现在的`Sanic`官方已经提供了`demo`，见[这里](https://github.com/channelcat/sanic/blob/master/examples/authorized_sanic.py)

## gRPC的异步调用方式

在编写微服务的时候，除了需要支持`http`请求外，一般还需要支持`gRPC`请求，我在使用`Sanic`编写微服务的时候，遇到关于异步请求`RPC`的需求，当时确实困扰了我，意外发现了这个库[grpclib](https://github.com/vmagamedov/grpclib)，进入`src/grpc_service`目录，里面就是解决方案，很简单，这里就不多说了，直接看代码就好。

## Blueprint

Blueprint前面的章节有仔细聊过，这里不多说，借用官方文档的例子，一个简单的sanic服务就搭好了：

``` python
# main.py
from sanic import Sanic
from sanic.response import json

app = Sanic()

@app.route("/")
async def test(request):
    return json({"hello": "world"})

#访问http://0.0.0.0:8000/即可
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000)
```
上面的例子可以当做一个完整的小应用，关于Blueprint的概念，可以这么理解，一个蓝图可以独立完成某一个任务，包括模板文件，静态文件，路由都是独立的，而一个应用可以通过注册许多蓝图来进行构建。
比如我现在编写的项目，我使用的是功能式架构，具体如下：

``` shell
├── server.py
├── static
│   └── novels
│       ├── css
│       │   └── result.css
│       ├── img
│       │   └── read_content.png
│       └── js
│           └── main.js
├── template
│   └── novels
│       └── index.html
└── views
    └── novels_blueprint.py
```
可以看到，总的templates以及静态文件还是放在一起，但是不同的blueprint则放在对应的文件夹中，还有一种分区式架构，则是将不同的templats以及static等文件夹全都放在不同的的blueprint中。
最后只要将每个单独的blueprint在主启动文件进行注册就好，上面的目录树是我以前的一个项目[owllook](https://github.com/howie6879/owllook)的，也是用`Sanic`编写的，有兴趣可以看看，不过现在结构改变挺大。

## html&templates编写

编写web服务，自然会涉及到html，sanic自带有html函数，但这并不能满足有些需求，故引入jinja2迫在眉睫，使用方法也很简单：

``` python
# 适用python3.5+
# 代码片段，明白意思就好
from sanic import Blueprint
from jinja2 import Environment, PackageLoader, select_autoescape

# 初始化blueprint并定义静态文件夹路径
bp = Blueprint('novels_blueprint')
bp.static('/static', './static/novels')

# jinjia2 config
env = Environment(
    loader=PackageLoader('views.novels_blueprint', '../templates/novels'),
    autoescape=select_autoescape(['html', 'xml', 'tpl']))

def template(tpl, **kwargs):
    template = env.get_template(tpl)
    return html(template.render(kwargs))
    
@bp.route("/")
async def index(request):
    return template('index.html', title='index')
```

如果是`python3.6`，可以试试下面的写法：

``` python
# 适用python3.5+
# 代码片段，明白意思就好
#!/usr/bin/env python
import sys

from feedparser import parse
from jinja2 import Environment, PackageLoader, select_autoescape
from sanic import Blueprint
from sanic.response import html

from src.config import CONFIG

# https://github.com/channelcat/sanic/blob/5bb640ca1706a42a012109dc3d811925d7453217/examples/jinja_example/jinja_example.py
# 开启异步特性  要求3.6+
enable_async = sys.version_info >= (3, 6)

html_bp = Blueprint('rss_html', url_prefix='html')
html_bp.static('/statics/rss_html', CONFIG.BASE_DIR + '/statics/rss_html')

# jinjia2 config
env = Environment(
    loader=PackageLoader('views.rss_html', '../templates/rss_html'),
    autoescape=select_autoescape(['html', 'xml', 'tpl']),
    enable_async=enable_async)


async def template(tpl, **kwargs):
    template = env.get_template(tpl)
    rendered_template = await template.render_async(**kwargs)
    return html(rendered_template)
```

## cache

我在项目中主要使用redis作为缓存，使用[aiocache](https://github.com/argaen/aiocache)很方便就完成了我需要的功能，当然自己利用aioredis编写也不会复杂到哪里去。

比如上面的例子，每次访问`http://0.0.0.0:8000/v1/get/rss/howie6879`，都要请求一次对应的rss资源，如果做个缓存那岂不是简单很多？

改写成这样：

``` python
@cached(ttl=1000, cache=RedisCache, key="rss", serializer=PickleSerializer(), port=6379, namespace="main")
async def get_rss():
    print("第一次请求休眠3秒...")
    await asyncio.sleep(3)
    url = "http://blog.howie6879.cn/atom.xml"
    feed = parse(url)
    articles = feed['entries']
    data = []
    for article in articles:
        data.append({"title": article["title_detail"]["value"], "link": article["link"]})
    return data


@api_bp.route("/get/rss/<name>")
async def get_rss_json(request, name):
    if name == 'howie6879':
        data = await get_rss()
        return json(data)
    else:
        return json({'info': '请访问 http://0.0.0.0:8000/v1/get/rss/howie6879'})
```

为了体现缓存的速度，首次请求休眠3秒，请求过后，redis中就会将此次json数据缓存进去了，下次去请求就会直接冲redis读取数据。

带上装饰器，什么都解决了。

## 热加载
我发现有个pr实现了这个需求，如果你有兴趣，可以看[这里](https://github.com/channelcat/sanic/pull/1047)

## session

sanic对此有一个第三方插件[`sanic_session`](https://github.com/subyraman/sanic_session)，用法非常简单，有兴趣可以看看
