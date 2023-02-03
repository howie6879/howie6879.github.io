---
title: Sanic 使用教程 - 2.配置
date: 2017-10-06T08:04:29
image: /images/thumbs/h_04.jpg
categories:
  - Python
  - 教程
tags: [Sanic]
toc: false
---
对于一个项目来说，配置是一个很严肃的问题，比如说：在开发环境和生产环境中，配置是不同的，那么一个项目该如何自由地在不同的配置环境中进行切换呢，思考下，然后带着答案或者疑问往下阅读。

新建文件夹 `demo2` ，内部建立这样的文件结构：

```shell
demo02
├── config
│   ├── __init__.py
│   └── config.py
└── run.py
```

<!-- more -->

其中 `run.py` 内容如下：

```python
#!/usr/bin/env python
from sanic import Sanic
from sanic.response import text

app = Sanic()


@app.route("/")
async def test(request):
    return text('Hello World!')


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000, debug=True)
```

代码示例中开启了 `debug` 模式，假设我们需要通过 `config.py` 配置文件来实现控制服务的 `debug` 模式开启与否，那该怎么实现呢。

在 `config.py` 中添加一行：`DEBUG=True` ，然后 `run.py` 内容改为：

```python
#!/usr/bin/env python
from sanic import Sanic
from sanic.response import text
from config import DEBUG

app = Sanic()


@app.route("/")
async def test(request):
    return text('Hello World!')


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000, debug=DEBUG)
```

表面上看，功能确实实现了，但这实际上却不是很好的做法，若部署在生产环境中，难道还要特地再将 `debug` 改为 `False` 么，这显然很浪费时间，如果需要改变的参数有很多，那就很难维护了。

那么，正确的做法应该是怎么样的呢？

首先在文件夹 `demo2` ，内部建立这样的文件结构：

```shell
demo02
├── config
│   ├── __init__.py
│   ├── config.py
│   ├── dev_config.py
│   └── pro_config.py
└── run.py

```

然后使用类继承的方式使这三个配置文件联系起来，比如在 `config.py` 中就只放公有配置，如：

```python
#!/usr/bin/env python
import os


class Config():
    """
    Basic config for demo02
    """
    # Application config
    TIMEZONE = 'Asia/Shanghai'
    BASE_DIR = os.path.dirname(os.path.dirname(__file__))
```

而在 `pro_config.py或dev_config.py` 中就可以自由地编写不同的配置了：

```python
# dev_config
#!/usr/bin/env python
from .config import Config


class DevConfig(Config):
    """
    Dev config for demo02
    """

    # Application config
    DEBUG = True

# pro_config
#!/usr/bin/env python
from .config import Config


class ProConfig(Config):
    """
    Pro config for demo02
    """

    # Application config
    DEBUG = False
```

配置文件还需要根据系统环境变量的设置进行不同配置环境的切换，比如设置 `MODE` 系统环境变量，然后可以根据其不同的值切换到不同的配置文件，因此在 `__init__.py` 中需要这么写：

```python
#!/usr/bin/env python
import os


def load_config():
    """
    Load a config class
    """

    mode = os.environ.get('MODE', 'DEV')
    try:
        if mode == 'PRO':
            from .pro_config import ProConfig
            return ProConfig
        elif mode == 'DEV':
            from .dev_config import DevConfig
            return DevConfig
        else:
            from .dev_config import DevConfig
            return DevConfig
    except ImportError:
        from .config import Config
        return Config


CONFIG = load_config()
```

默认 `MODE` 设置为 `DEV`，在 `run.py` 文件中就可以这么调用：

```python
#!/usr/bin/env python
from sanic import Sanic
from sanic.response import text
from config import CONFIG

app = Sanic()
app.config.from_object(CONFIG)

@app.route("/")
async def test(request):
    return text('Hello World!')


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000, debug=app.config['DEBUG'])
```

而在生产环境的服务器上，直接通过：

```shell
# 通过设置MODE的值进行配置文件的选择
export MODE=PRO 
```

若是利用 `supervisor` 来启动服务，可通过添加`environment = MODE="PRO"` 来设置环境变量，是不是很方便呢。

