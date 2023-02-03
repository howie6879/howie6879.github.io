---
title: gRPC使用初试
date: 2017-08-03T20:57:28
categories:
  - Python
  - 教程
tags: [rpc]
toc: true
---

## 1.前言

[gRPC](https://grpc.io/)是一个开源的高性能并且能在任何环境中运行的RPC框架，其采用` protocol buffer`:

` protocol buffer`是一个用于结构化数据序列化的一个灵活的、有效率的自动化机制，类似于XML(但比其更简单、小巧且简单)，对于某个服务需要定义的数据结构，可以使用`protocol buffer`(`proto3`)来进行定义，即根据`protocol buffer language`来定义你的`protocol buffer data`

最后再利用`protocol buffer`编译定义的 `.proto` 文件生成客户端和服务端代码，本文将举一个小例子进行介绍，且默认站在python的角度来编写代码

<!-- more -->

## ２.实践

假设有这样一个需求，将客户端传来的某个字符串转化为小写，那么，首先从定义数据结构开始，暂时先新建如下结构的项目：

``` shell
demo
└── proto
    └── lower.proto
```
接下来将编写`proto`文件，本例使用`proto3`语法进行编写，`protobuf`定义的语法请看[这里](https://developers.google.com/protocol-buffers/docs/proto3)

`lower.proto`文件内容如下：

``` proto
syntax = "proto3";
package lower;

service Lower {
    rpc Lower (LowerRequest) returns (LowerResult) {
    }
}

message LowerRequest {
    string message = 1;
}

message LowerResult {
    string lower_message = 1;
}
```
定义一个服务`Lower`，其接受一个字符串消息并返回小写的字符串消息，编写完毕，下面进行编译，创建文件`proto_compile.py`，并安装相关的包：

``` shell
pip install grpcio
pip install grpcio-tools
```

``` python
# script proto_compile.py
from grpc_tools import protoc

# 或者在项目根目录运行：
# python -m grpc_tools.protoc -I. --python_out=../ --grpc_python_out=../ ./cal.proto
protoc.main(
    (
        '',
        '-Iproto',
        '--python_out=.',
        '--grpc_python_out=.',
        'proto/lower.proto',
    )
)

```
运行后，项目结构如下：

``` shell
demo
├── lower_pb2_grpc.py     # 编译生成的文件
├── lower_pb2.py          # 编译生成的文件
├── proto
│   └── lower.proto
└── proto_compile.py

```
最后编写客户端以及服务端脚本`lower_client.py`、`lower_server.py`，内容分别如下：

``` python
# lower_server.py

import time
import grpc
from concurrent import futures
import lower_pb2, lower_pb2_grpc

_TIME_WAIT = 10


class Lower(lower_pb2_grpc.LowerServicer):
    def Lower(self, request, context):
        return lower_pb2.LowerResult(lower_message=request.message.lower())


def serve():
    server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))
    lower_pb2_grpc.add_LowerServicer_to_server(Lower(), server)
    server.add_insecure_port('[::]:50052')
    server.start()
    try:
        while True:
            time.sleep(_TIME_WAIT)
    except KeyboardInterrupt:
        server.stop(0)


if __name__ == '__main__':
    serve()

# lower_client.py

#!/usr/bin/env python
# -*- coding: utf-8 -*-
import grpc

import lower_pb2, lower_pb2_grpc


def run():
    channel = grpc.insecure_channel("localhost:50052")
    stub = lower_pb2_grpc.LowerStub(channel)
    response = stub.Lower(lower_pb2.LowerRequest(message="HELLO WORLD!"))
    print("Message result received:  %s" % response.lower_message)


if __name__ == '__main__':
    run()

```

此时文件目录结构如下：

``` shell
demo
├── lower_client.py
├── lower_pb2_grpc.py
├── lower_pb2.py
├── lower_server.py
├── proto
│   └── lower.proto
├── proto_compile.py
```
先运行`lower_server.py`，再运行`lower_client.py`，就会看到终端有结果输出：

``` shell
Message result received:  hello world!
```
可以看到，返回小写字符，`demo`演示完毕

## 3.最后

以下是我了解`gRPC`过程中收集的一些资料：

- 官方中文文档：http://doc.oschina.net/grpc?t=58008

- proto中文翻译： http://colobu.com/2017/03/16/Protobuf3-language-guide/

- 参考实例：http://blog.csdn.net/lavorange/article/details/74504837