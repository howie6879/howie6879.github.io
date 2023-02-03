---
categories:
  - Python
  - 教程
date: 2018-01-12T10:50:30+08:00
image: /images/thumbs/h_07.jpg
tags: [Sanic]
title: "Sanic 使用教程 - 5.数据库使用"
toc: true
---

介绍中说的很明白，`Sanic` 是一个可以使用 `async/await` 语法编写项目的**异步**非阻塞框架，既然是异步框架，那么在使用过程中用到的第三方包也最好是异步的，比如http请求，最好就使用`aihttp`而非`requests`，对于数据库的连接，也是同样如此，下面我将用代码的形式来说明下如何在Sanic中连接数据库。

## 操作Mysql
对于mysql数据库的异步操作，我只在一些脚本中用过，用的是[aiomysql](https://github.com/aio-libs/aiomysql)，其中官方文档中讲得很清楚，也支持结合`sqlalchemy`编写`ORM`，然后aiomysql提供了自己编写的异步引擎。

``` python
from aiomysql.sa import create_engine
# 这个才是关键
```

下面我编写一个具体的例子来用异步语句操作下数据库，首先建立如下目录：

``` shel
aio_mysql
├── demo.py
├── model.py
└── requirements.txt
```

建立表：

``` mysql
create database test_mysql;

CREATE TABLE user
(
  id        INT AUTO_INCREMENT
    PRIMARY KEY,
  user_name VARCHAR(16) NOT NULL,
  pwd       VARCHAR(32) NOT NULL,
  real_name VARCHAR(6)  NOT NULL
);
```

一切准备就绪，下面编写代码：

``` python
# script: model.py
import sqlalchemy as sa

metadata = sa.MetaData()

user = sa.Table(
    'user',
    metadata,
    sa.Column('id', sa.Integer, autoincrement=True, primary_key=True),
    sa.Column('user_name', sa.String(16), nullable=False),
    sa.Column('pwd', sa.String(32), nullable=False),
    sa.Column('real_name', sa.String(6), nullable=False),
)

# script: demo.py
import asyncio

from aiomysql.sa import create_engine

from model import user,metadata


async def go(loop):
    """
    aiomysql项目地址：https://github.com/aio-libs/aiomysql
    :param loop:
    :return:
    """
    engine = await create_engine(user='root', db='test_mysql',
                                 host='127.0.0.1', password='123456', loop=loop)
    async with engine.acquire() as conn:
        await conn.execute(user.insert().values(user_name='user_name01', pwd='123456', real_name='real_name01'))
        await conn.execute('commit')

        async for row in conn.execute(user.select()):
            print(row.user_name, row.pwd)

    engine.close()
    await engine.wait_closed()


loop = asyncio.get_event_loop()
loop.run_until_complete(go(loop))

```
运行 python demo.py，会看到如下输出：

``` shell
user_name01 123456
```

很简单吧，具体示例见[aio_mysql](https://github.com/howie6879/Sanic-For-Pythoneer/tree/master/examples/demo05/aio_mysql)

## 操作MongoDB
我业余写的一个项目，基本用的就是`MongoDB`来储存数据，对于异步操作`MongoDB`，目前Python主要用的是[motor](https://motor.readthedocs.io/en/stable/index.html)，使用起来依旧很简单，但是结合具体功能，就有不同的的需求，最后就会形成各种各样的连接方案，这里我主要分享下自己是如何使用的，目录如下所示：

``` shell
aio_mongo
├── demo.py
└── requirements.txt
```

`MongoDB`是一个基于分布式文件存储的数据库，它介于关系数据库和非关系数据库之间，所以它使用起来也是比较灵活的，打开`demo.py`：

``` python
#!/usr/bin/env python
import os

from functools import wraps

from motor.motor_asyncio import AsyncIOMotorClient

MONGODB = dict(
    MONGO_HOST=os.getenv('MONGO_HOST', ""),
    MONGO_PORT=os.getenv('MONGO_PORT', 27017),
    MONGO_USERNAME=os.getenv('MONGO_USERNAME', ""),
    MONGO_PASSWORD=os.getenv('MONGO_PASSWORD', ""),
    DATABASE='test_mongodb',
)

class MotorBaseOld:
    """
    默认实现了一个db只创建一次，缺点是更换集合麻烦
    """
    _db = None
    MONGODB = MONGODB

    def client(self, db):
        # motor
        self.motor_uri = 'mongodb://{account}{host}:{port}/{database}'.format(
            account='{username}:{password}@'.format(
                username=self.MONGODB['MONGO_USERNAME'],
                password=self.MONGODB['MONGO_PASSWORD']) if self.MONGODB['MONGO_USERNAME'] else '',
            host=self.MONGODB['MONGO_HOST'] if self.MONGODB['MONGO_HOST'] else 'localhost',
            port=self.MONGODB['MONGO_PORT'] if self.MONGODB['MONGO_PORT'] else 27017,
            database=db)
        return AsyncIOMotorClient(self.motor_uri)

    @property
    def db(self):
        if self._db is None:
            self._db = self.client(self.MONGODB['DATABASE'])[self.MONGODB['DATABASE']]

        return self._db
```
我最开始，使用的是这样种方式来连接`MongoDB`，上面代码保证了集合中的db被_db维护，保证只会创建一次，如果你项目中不会随意更改集合的话，也没什么大问题，如果不是，我推荐使用下面这样的连接方式，可以自由地更换集合与db：

``` python

def singleton(cls):
    """
    用装饰器实现的实例 不明白装饰器可见附录 装饰器：https://github.com/howie6879/Sanic-For-Pythoneer/blob/master/docs/part2/%E9%99%84%E5%BD%95%EF%BC%9A%E5%85%B3%E4%BA%8E%E8%A3%85%E9%A5%B0%E5%99%A8.md
    :param cls: cls
    :return: instance
    """
    _instances = {}

    @wraps(cls)
    def instance(*args, **kw):
        if cls not in _instances:
            _instances[cls] = cls(*args, **kw)
        return _instances[cls]

    return instance

@singleton
class MotorBase:
    """
    更改mongodb连接方式 单例模式下支持多库操作
    About motor's doc: https://github.com/mongodb/motor
    """
    _db = {}
    _collection = {}
    MONGODB = MONGODB

    def __init__(self):
        self.motor_uri = ''

    def client(self, db):
        # motor
        self.motor_uri = 'mongodb://{account}{host}:{port}/{database}'.format(
            account='{username}:{password}@'.format(
                username=self.MONGODB['MONGO_USERNAME'],
                password=self.MONGODB['MONGO_PASSWORD']) if self.MONGODB['MONGO_USERNAME'] else '',
            host=self.MONGODB['MONGO_HOST'] if self.MONGODB['MONGO_HOST'] else 'localhost',
            port=self.MONGODB['MONGO_PORT'] if self.MONGODB['MONGO_PORT'] else 27017,
            database=db)
        return AsyncIOMotorClient(self.motor_uri)

    def get_db(self, db=MONGODB['DATABASE']):
        """
        获取一个db实例
        :param db: database name
        :return: the motor db instance
        """
        if db not in self._db:
            self._db[db] = self.client(db)[db]

        return self._db[db]

    def get_collection(self, db_name, collection):
        """
        获取一个集合实例
        :param db_name: database name
        :param collection: collection name
        :return: the motor collection instance
        """
        collection_key = db_name + collection
        if collection_key not in self._collection:
            self._collection[collection_key] = self.get_db(db_name)[collection]

        return self._collection[collection_key]

```

为了避免重复创建MotorBase实例，可以实现一个单例模式来保证资源的有效利用，具体代码以及运行demo见[aio_mongo](https://github.com/howie6879/Sanic-For-Pythoneer/tree/master/examples/demo05/aio_mongo)

## 操作Redis

对于Redis的异步操作，我选用的是`asyncio_redis`，你大可不必非要使用这个，或许其他的库实现地更好，我只是用这个举个例子，建立如下目录：

``` shell 
aio_redis
├── demo.py
└── requirements.txt
```

建立一个redis连接池：

``` python
#!/usr/bin/env python
import os
import asyncio_redis

REDIS_DICT = dict(
    IS_CACHE=True,
    REDIS_ENDPOINT=os.getenv('REDIS_ENDPOINT', "localhost"),
    REDIS_PORT=os.getenv('REDIS_PORT', 6379),
    REDIS_PASSWORD=os.getenv('REDIS_PASSWORD', None),
    DB=0,
    POOLSIZE=10,
)


class RedisSession:
    """
    建立redis连接池
    """
    _pool = None

    async def get_redis_pool(self):
        if not self._pool:
            self._pool = await asyncio_redis.Pool.create(
                host=str(REDIS_DICT.get('REDIS_ENDPOINT', "localhost")), port=int(REDIS_DICT.get('REDIS_PORT', 6379)),
                poolsize=int(REDIS_DICT.get('POOLSIZE', 10)), password=REDIS_DICT.get('REDIS_PASSWORD', None),
                db=REDIS_DICT.get('DB', None)
            )

        return self._pool

```

具体见[aio_redis](https://github.com/howie6879/Sanic-For-Pythoneer/blob/master/examples/demo05/aio_redis/demo.py)，使用起来很简单，不做多叙述。

## 说明

如果你使用其它类型的数据库，其实使用方式也是类似。
本章代码地址，见[demo05](https://github.com/howie6879/Sanic-For-Pythoneer/tree/master/examples/demo05)
