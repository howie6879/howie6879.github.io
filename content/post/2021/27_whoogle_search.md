---
title: "基于Whoogle自建无广告、无追踪的搜索引擎"
date: 2021-12-01T20:35:47+08:00
lastmod: 2021-12-01T20:35:47+08:00
keywords: []
description: "基于Whoogle自建无广告、无追踪的搜索引擎"
tags: [Google, 效率]
categories:
  - Python
  - 工具
  - 教程
  - 云原生
author: "howie.hu"
toc: false
---

![whoogle-search](https://images-1252557999.file.myqcloud.com/uPic/wFf0Ci.png)

我在[周刊项目](https://github.com/howie6879/weekly/)[第003期 (08-30~09-03)](https://weekly.howie6879.cn/2021/08-30~09-03.%E6%88%91%E7%9A%84%E5%91%A8%E5%88%8A%EF%BC%88%E7%AC%AC003%E6%9C%9F%EF%BC%89.html?h=whoo#whoogle-search)中介绍了一个开源的元搜索引擎项目[whoogle-search](https://github.com/benbusby/whoogle-search)，这个项目有几个非常吸引我的特性：
- 没有广告以及赞助内容
- 不追踪个人IP
- Tor & HTTP/SOCKS 支持
- 设置 No JS&Cookie
- 易部署
- 更多特性去项目地址查看

到目前我差不多用了三个月，完全满足我日常使用需求，也很少用`Google`了，这次将`Whoogle`正式部署到了我的`k3s`集群，中间踩了不少坑，输出这篇文章以作记录。

## 部署

所谓元数据搜索引擎就是基于其他搜索引擎的基础上做一些基础功能，`whoogle-search`就是在`Google`搜索结果的基础上增加了上述功能，所以对于检索质量来说是和谷歌一致的。

如果你有自己的服务器（海外优先），我推荐你使用`Whoogle`作为自己的搜索引擎。如果没有服务器，也没关系，目前有很多免费资源供开发者日常使用，如：
- [heroku](https://www.heroku.com/about)
- [Repl.it](https://replit.com/)
- [Fly.io](https://fly.io/)

### 单机部署

单机部署的话非常简单，推荐直接使用`Docker`，一行命令搞定：

```shell
docker run -d -it -p 5000:5000 --restart=always --name whoogle-search benbusby/whoogle-search:latest
```

如果你有域名且希望上`https`，可以考虑结合`Caddy`使用`Letsencrypt`来实现。

### 集群部署

我个人的话在自己购买的几台服务器上有部署一套`k3s`集群（开源、极轻量的 Kubernetes 发行版），所以我准备直接在集群上部署`whoogle-search`并绑定域名上`https`，这件事花了我一整天的时间才做完，写这篇文章的目的也完全是为了记录我踩的坑，接下来直接放出可行的解决方案。

`k3s`集群相关依赖版本如下：
- [k3s](https://k3s.io/): v1.21.3+k3s1
- [traefik](https://doc.traefik.io/): v2.4.8
- [cert-manager](https://github.com/jetstack/cert-manager/): v1.6.1

`k3s`集群安装搭建这里先不提，后面等我玩熟了会出一篇文章聊聊，提一句，如果你不熟悉`k8s`但又感兴趣的话，可以看看我之前学习`k8s`过程中产出的开源笔记：
- [github](https://github.com/howie6879/k8s_note)
- [网页访问](https://www.howie6879.cn/k8s/)：k8s学习之路

先安装`cert-manager`：

```shell
kubectl create namespace cert-manager
kubectl apply --validate=false -f  https://github.com/jetstack/cert-manager/releases/download/v1.6.1/cert-manager.yaml

# 查看状态
kubectl get pods --namespace cert-manager

# 输出
NAME                                      READY   STATUS    RESTARTS   AGE
cert-manager-55658cdf68-4m5r5             1/1     Running   0          25h
cert-manager-cainjector-967788869-x5cpk   1/1     Running   0          25h
cert-manager-webhook-6668fbb57d-4cv2c     1/1     Running   0          25h
```

一旦运行成功，我们将使用`ClusterIssuer`资源类型来支持`Letsencrypt`为我们颁发证书，创建文件`letsencrypt.yaml`，输入内容：

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-issuer
spec:
  acme:
    email: <your>@<email>.<com> # input your email
    privateKeySecretRef:
      name: letsencrypt-issuer-account-key
    server: https://acme-v02.api.letsencrypt.org/directory
    solvers:
      - http01:
          ingress:
            ingressTemplate:
              metadata:
                annotations:
                  kubernetes.io/ingress.class: traefik
```

在终端执行：

```shell
kubectl apply -f letsencrypt.yaml

# 查看状态
kubectl get clusterissuers

# 输出
NAME                 READY   AGE
letsencrypt-issuer   True    114m

# 更详细的信息可查看
kubectl describe clusterissuers letsencrypt

# 输出
Status:
  Acme:
    Last Registered Email:  
    Uri:                    https://acme-v02.api.letsencrypt.org/acme/acct/
  Conditions:
    Last Transition Time:  2021-12-01T07:08:43Z
    Message:               The ACME account was registered with the ACME server
    Observed Generation:   1
    Reason:                ACMEAccountRegistered
    Status:                True
    Type:                  Ready
Events:                    <none>
```

接下来我们直接在`k3s`部署`whoogle`，创建文件`whoogle.yaml`，输入内容：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: whoogle
spec:
  ports:
  - name: whoogle-5000-tcp
    port: 5000
    protocol: TCP
    targetPort: 5000
  selector:
    app: whoogle
---
kind: Deployment
apiVersion: apps/v1
metadata:
  name: whoogle
  labels:
    app: whoogle
spec:
  replicas: 1
  selector:
    matchLabels:
      app: whoogle
  template:
    metadata:
      labels:
        app: whoogle
    spec:
      containers:
        - name: whoogle
          image: benbusby/whoogle-search:latest
          resources:
            limits:
              cpu: '1'
              memory: 512M
            requests:
              cpu: '1'
              memory: 512M
          ports:
              - containerPort: 5000
```

在终端执行：

```shell
kubectl apply -f ./whoogle.yaml

# 查看 pod 状态
kubectl get pods

# 输出
NAME                       READY   STATUS    RESTARTS   AGE
whoogle-7c95b49ddd-dqgs7   1/1     Running   0          25h

# 查看 svc 状态
kubectl get svc

# 输出
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
whoogle      ClusterIP   10.42.163.248   <none>        5000/TCP   25h
```

此时`whoogle`服务已经正常启动，接下来要做的就是利用`traefik`和`cert-manager`进行域名绑定和`https`，这个过程我踩了好几个坑，如果你也遇到了`Waiting for HTTP-01 challenge propagation: wrong status code '404', expected '200'`，可以参考我的配置示例，创建文件`whoogle-ingress-tls.yaml`，输入配置如下：

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: whoogle-ingress
  annotations:
    kubernetes.io/ingress.class: "traefik"
    cert-manager.io/cluster-issuer: "letsencrypt-issuer"
    traefik.ingress.kubernetes.io/frontend-entry-points: http, https
    traefik.ingress.kubernetes.io/redirect-entry-point: https
spec:
  tls:
    - secretName: s-demo-cn
      hosts:
        - s-demo-cn
  rules:
    - host: s-demo-cn
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: whoogle
                port:
                  number: 5000
```

等待片刻，在终端执行以下命令查看状态：

```shell
kubectl apply -f whoogle-ingress-tls.yaml

# 查看 ingress
kubectl get ingress

# 输出
NAME              CLASS    HOSTS             ADDRESS            PORTS     AGE
whoogle-ingress   <none>   s.demo.cn          1.2.3.4           80, 443   123m

# 查看证书
kubectl get cert

# 输出
NAME                  READY   SECRET                AGE
s-demo-cn             True    s-demo-cn            125m

# 查看密钥
kubectl describe secret s-demo-cn

# 输出
Name:         s-demo-cn
Namespace:    default
Labels:       <none>
Annotations:  cert-manager.io/alt-names: s.demo.cn
              cert-manager.io/certificate-name: s-demo-cn
              cert-manager.io/common-name: s.demo.cn
              cert-manager.io/ip-sans:
              cert-manager.io/issuer-group: cert-manager.io
              cert-manager.io/issuer-kind: ClusterIssuer
              cert-manager.io/issuer-name: letsencrypt-issuer
              cert-manager.io/uri-sans:
Type:  kubernetes.io/tls
Data
====
tls.crt:  5603 bytes
tls.key:  1675 bytes

# 如果有问题，可以查看详情进行排查
kubectl describe certificate s-demo-cn
kubectl describe certificaterequest s-demo-cn
kubectl describe challenges s-demo-cn
```

至此，问题解决，留此记录，本文主要提供了一种在`k3s`集群下利用`traefik&cert-manager`让服务`https`化的方法。

