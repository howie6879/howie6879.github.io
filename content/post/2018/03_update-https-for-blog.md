---
categories:
  - 教程
  - Linux
comments: true
date: 2018-05-24T11:40:18+08:00
image: /images/thumbs/h_09.jpg
tags: [centos]
title: "博客升级HTTPS方案"
toc: false
---

很早就想将博客升级为HTTPS，在心中也确立了方案，步骤也很简单：

- 博文上传至Git
- Caddy结合Webhook自动更新，顺便上HTTPS

流程很简单，麻烦在于需要Caddy以及Webhook的设置，然后一台服务器也是必须的，这里本人推荐使用[搬瓦工的年付19.9$](https://bwh1.net/cart.php?a=confproduct&i=0)方案，购买之后，选择CentOS7，安装后进入shell：

``` shell
yum install git vim wget
# 创建用户
useradd howie
# 设定密码
passwd username
# 添加 sudo 权限
visudo
# 找到 
root  ALL=(ALL)   ALL 
# 添加 
howie  ALL=(ALL)   ALL
# 用刚创建的用户名登录
# 安装Caddy以及插件，这里我列出了我需要的插件 各位酌情选择
curl https://getcaddy.com | bash -s personal http.git,http.realip,http.ratelimit,http.ipfilter
```

首先是从git拉取代码，因为我们还是希望服务器可以自动从git远程拉取代码并且自动更新：

``` shell
mkdir git
cd git
git clone https://github.com/howie6879/howie6879.github.io.git
```

然后是编写`Caddyfile`，这是`Caddy`的配置文件，我将配置文件写在`/etc/caddy/Caddyfile`，贴上我的配置内容：

``` shell
cd ~
mkdir /etc/caddy/
vim /etc/caddy/Caddyfile
# 输入
www.howie6879.cn {
    root /home/howie/git/howie6879.github.io
    tls xiaozizayang@gmail.com
    timeouts none
    gzip
}
howie6879.cn {
    redir www.howie6879.cn
}

# 这里需要注意，请先将ip和博客域名解析好
```

Caddy官方也提供了实现脚本，见[这里](https://github.com/mholt/caddy/blob/master/dist/init/linux-systemd/caddy.service)，不过我就按照自己的习惯部署了，没有参考官方的

接下来就可以直接启动我们的博客服务了，Caddy会自动申请证书上HTTPS，非常方便，启动：

``` shell
caddy -conf /etc/caddy/Caddyfile
```

访问：https://www.howie6879.cn/ ，一切正常，至此，博客算迁移完成了，现在还有一个麻烦的地方在于，每次提交更新到Git仓库，总是需要手动下拉一次代码来更新网站博文，这样感觉比较麻烦

或许可以让Caddy每隔多长时间自动拉取最新的仓库内容，比如60S，这样配置：

``` shell
www.howie6879.cn {
    root /home/howie/git/howie6879.github.io
    tls xiaozizayang@gmail.com
    git {
        repo     https://github.com/howie6879/howie6879.github.io.git
        path     /var/www/howie6879.github.io
        interval 60
    }
    timeouts none
    gzip
}
howie6879.cn {
    redir www.howie6879.cn
}
```

但这样有点浪费资源，还是做成被动触发比较好，所以还是让我们的Webhook登场吧，我暂时有点懒，懒得动。。。

最后说下，利用`Supervisor`管理`Caddy`的事情，毕竟上面的启动方式还是不行的:

``` shell
sudo yum install python-setuptools
sudo easy_install supervisor

sudo mkdir /etc/supervisord.d/
sudo echo_supervisord_conf > /etc/supervisord.conf

sudo chmod -R 777 /etc/supervisord.d/
sudo chmod -R 777 /etc/caddy/Caddyfile
sudo chmod -R 777 /etc/supervisord.conf

vim /etc/supervisord.con

mkdir ~/supervisor/
# 加上这行
[include]
files = /etc/supervisord.d/*.ini
```

在`/etc/supervisord.d/`增加`caddy.ini`:

``` shell
[program:caddy]
command      = caddy -conf=“/etc/caddy/Caddyfile”
user         = howie
process_name = %(program_name)s
autostart    = true
autorestart  = true
startsecs    = 3
redirect_stderr         = true
stdout_logfile_maxbytes = 500MB
stdout_logfile_backups  = 10
stdout_logfile          = ~/supervisor/caddy.log
```

至此，大功告成