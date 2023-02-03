---
title: sanic使用记录
date: 2017-02-28T16:33:44
categories:
  - Python
  - 教程
tags: [Sanic]
toc: true
---

在使用python异步的时候，我了解到了sanic这个据说是最快的web服务框架，其支持异步请求处理机制，这意味你可以使用python3.5的`async/await`来编写无阻塞的异步程序。
于是我利用业余时间使用[sanic](https://github.com/channelcat/sanic)编写了这个项目。

<!--more-->

## 前言
在使用过程中，我尽量将程序编写成异步的，首先进行安装：

``` python
python -m pip install sanic
```
sanic的[文档](http://sanic.readthedocs.io/en/latest/index.html)写得很详细，但是在使用过程中我还是有些问题。
下面记录的都是我在使用sanic过程中遇到的问题，后续有新问题会继续补充：

- 1.blueprint
- 2.html templates编写（引入jinja2）
- 3.session（引入sanic_session）
- 4.缓存（引入aiocache）
- 5.api接口验证（自定义一个装饰器）
- 6.恶意将其他域名绑定到你的网站独立ip
- 7.注意

## 问题记录


### blueprint

借用官方文档的例子，一个简单的sanic服务就搭好了：

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
上面的例子可以当做一个完整的小应用，关于blueprint的概念，可以这么理解，一个蓝图可以独立完成某一个任务，包括模板文件，静态文件，路由都是独立的，而一个应用可以通过注册许多蓝图来进行构建。
比如我现在编写的项目，我使用的是功能式架构，具体如下：

``` shell
├── server.py
├── static
│   └── novels
│       ├── css
│       │   ├── chapter.css
│       │   ├── content.css
│       │   ├── index.css
│       │   ├── main.css
│       │   └── result.css
│       ├── img
│       │   ├── bookMark.png
│       │   ├── favicon.ico
│       │   ├── read_bg.png
│       │   └── read_content.png
│       └── js
│           ├── list.js
│           └── main.js
├── template
│   └── novels
│       ├── chapter.html
│       ├── content.html
│       ├── donate.html
│       ├── feedback.html
│       ├── index.html
│       ├── main.html
│       └── result.html
└── views
    └── novels_blueprint.py
```
可以看到，总的templates以及静态文件还是放在一起，但是不同的blueprint则放在对应的文件夹中，还有一种分区式架构，则是将不同的templats以及static等文件夹全都放在不同的的blueprint中。
最后只要将每个单独的blueprint在主启动文件进行注册就好，比如我的这个项目：

``` python
# server.py
#!/usr/bin/env python
from sanic import Sanic
from novels_search.views.novels_blueprint import bp

app = Sanic(__name__)
app.blueprint(bp)

app.run(host="0.0.0.0", port=8000, debug=True)
```
具体的`novels_blueprint.py`请点击[这里](https://github.com/howie6879/novels-search/blob/master/novels_search/views/novels_blueprint.py)

### html templates编写
编写web服务，自然会涉及到html，sanic自带有html函数，但这并不能满足有些需求，故引入jinja2迫在眉睫，使用方法也很简单：

``` python
# novels_blueprint.py片段
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
这样，就实现了jinja2 的引入。

=========05-02===========
在官方文档发现了已经加入了如何引入jinja2，具体见：https://github.com/channelcat/sanic/blob/master/examples/jinja_example/jinja_example.py

### session
sanic对此有一个第三方插件[`sanic_session`](https://github.com/subyraman/sanic_session)，用法非常简单，见官方例子如下：

``` python
import asyncio_redis

from sanic import Sanic
from sanic.response import text
from sanic_session import RedisSessionInterface

app = Sanic()


# Token from https://github.com/subyraman/sanic_session

class Redis:
    """
    A simple wrapper class that allows you to share a connection
    pool across your application.
    """
    _pool = None

    async def get_redis_pool(self):
        if not self._pool:
            self._pool = await asyncio_redis.Pool.create(
                host='localhost', port=6379, poolsize=10
            )

        return self._pool


redis = Redis()

# pass the getter method for the connection pool into the session
session_interface = RedisSessionInterface(redis.get_redis_pool, expiry=604800)


@app.middleware('request')
async def add_session_to_request(request):
    # before each request initialize a session
    # using the client's request
    await session_interface.open(request)


@app.middleware('response')
async def save_session(request, response):
    # after each request save the session,
    # pass the response to set client cookies
    await session_interface.save(request, response)


@app.route("/")
async def test(request):
    # interact with the session like a normal dict
    if not request['session'].get('foo'):
        request['session']['foo'] = 0

    request['session']['foo'] += 1

    response = text(request['session']['foo'])

    return response


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8888, debug=True)
```
### 缓存
我在项目中主要使用redis作为缓存，使用[aiocache](https://github.com/argaen/aiocache)很方便就完成了我需要的功能，当然自己利用aioredis编写也不会复杂到哪里去。

``` python
@cached(ttl=1800, cache=RedisCache, key_from_attr='url', serializer=PickleSerializer(),
        endpoint=REDIS_DICT.get('REDIS_ENDPOINT', None), port=REDIS_DICT.get('REDIS_PORT', None), namespace="main")
async def cache_owllook_novels_chapter(url, netloc):
    async with aiohttp.ClientSession() as client:
        html = await target_fetch(client=client, url=url)
        if html:
            soup = BeautifulSoup(html, 'html5lib')
            selector = RULES[netloc].chapter_selector
            if selector.get('id', None):
                content = soup.find_all(id=selector['id'])
            elif selector.get('class', None):
                content = soup.find_all(class_=selector['class'])
            else:
                content = soup.find_all(selector.get('tag'))
            return str(content) if content else None
        return None

```
带上装饰器，什么都解决了。

### api接口验证

在编写接口的时候，有些接口可能需要在headers中加个验证头或者token，sanic官方貌似并没有提供，解决起来很简单，可以在request时候进行验证，代码如下：
``` python
# 比如想验证一个请求的 Owllook-Api-Key值是否跟自己约定的值一样，这里写个装饰器就好
from functools import wraps
from sanic.response import json
from novels_search.config import AUTH

def authenticator(key):
    """
    
    :param keys: 验证方式 Owllook-Api-Key : Maginc Key,  Authorization : Token
    :return: 返回值
    """

    def wrapper(func):
        @wraps(func)
        async def authenticate(request, *args, **kwargs):
            value = request.headers.get(key, None)
            if value and AUTH[key] == value:
                response = await func(request, *args, **kwargs)
                return response
            else:
                return json({'msg': 'not_authorized', 'status': 403})

        return authenticate

    return wrapper
```

至于使用
``` python
@api_bp.route("/bd_novels/<name>")
@authenticator('Owllook-Api-Key')
async def bd_novels(request, name):
    pass
```
具体见[例子](https://github.com/howie6879/novels-search/blob/master/novels_search/views/api_blueprint.py)

### 恶意将其他域名绑定到你的网站独立ip
前两天突然发现别人没经过我的同意就将自己的域名绑定到我的网站，不能忍，解决办法有不少，web服务器写配置解决，或者代码层面解决，我说下第二种。

首先，定义你允许访问的HOST，再获取访问时获取的host，比如：

``` python
# config.py
HOST = ['127.0.0.1:8001']

# sanic获取host
host = request.headers.get('host', None)
```
那么就可以在sanic中间件中定义：

```python
# 当host不满足条件就调到baidu
@app.middleware('request')
async def add_session_to_request(request):
    host = request.headers.get('host', None)
    if not host or host not in HOST:
        return redirect('http://www.baidu.com')
```

### 注意

sanic最好使用0.5.1+，因为在之前的版本被发现了一个安全漏洞，是关于之前引入static部分的，具体见https://github.com/channelcat/sanic/issues/633。
sanic更新换代还是很快的。

## 说明

遇到什么想到什么就继续更新，具体代码[地址](https://github.com/howie6879/novels-search)

