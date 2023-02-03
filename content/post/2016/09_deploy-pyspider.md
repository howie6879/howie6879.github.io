---
title: CentOS7分布式部署pyspider
date: 2016-10-10T17:33:51
categories:
  - Python
tags: [CentOS,Spider]
toc: false
---

#### 搭建环境：

> **系统版本：**`Linux centos-linux.shared 3.10.0-123.el7.x86_64 #1 SMP Mon Jun 30 12:09:22 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux`
>
> **python版本**：`Python 3.5.1 `

##### 搭建python3环境：

本人在尝试过后选择集成环境**Anaconda**

<!-- more -->

###### 编译

```shell
# 下载依赖
yum install -y ncurses-devel openssl openssl-devel zlib-devel gcc make glibc-devel libffi-devel glibc-static glibc-utils sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-deve
# 下载python版本
wget https://www.python.org/ftp/python/3.5.1/Python-3.5.1.tgz
# 或者使用国内源
wget http://mirrors.sohu.com/python/3.5.1/Python-3.5.1.tgz
mv Python-3.5.1.tgz /usr/local/src;cd /usr/local/src
# 解压
tar -zxf Python-3.5.1.tgz;cd Python-3.5.1
# 编译安装
./configure --prefix=/usr/local/python3.5 --enable-shared
make && make install
# 建立软链接
ln -s /usr/local/python3.5/bin/python3 /usr/bin/python3
echo "/usr/local/python3.5/lib" > /etc/ld.so.conf.d/python3.5.conf
ldconfig
# 验证python3
python3
# Python 3.5.1 (default, Oct  9 2016, 11:44:24)
# [GCC 4.8.5 20150623 (Red Hat 4.8.5-4)] on linux
# Type "help", "copyright", "credits" or "license" for more information.
# >>>
# pip
/usr/local/python3.5/bin/pip3 install --upgrade pip
ln -s /usr/local/python3.5/bin/pip /usr/bin/pip
# 本人在安装时出现问题 将pip重装
wget https://bootstrap.pypa.io/get-pip.py --no-check-certificate
python get-pip.py
```

###### 集成环境anaconda

```shell
# 也可考虑集成环境anaconda(推荐)
wget https://repo.continuum.io/archive/Anaconda3-4.2.0-Linux-x86_64.sh
# 直接安装即可
./Anaconda3-4.2.0-Linux-x86_64.sh
# 若出错，可能是解压失败
yum install bzip2
```



##### 安装mariaDB

```shell
# 安装
yum -y install mariadb mariadb-server
# 启动
systemctl start mariadb
# 设置为开机启动
systemctl enable mariadb
# 配置密码 默认为空
mysql_secure_installation
# 登录
mysql -u root -p
# 创建一个用户 自己设定账户密码
CREATE USER 'user_name'@'localhost' IDENTIFIED BY 'user_pass';
GRANT ALL PRIVILEGES ON *.* TO 'user_name'@'localhost' WITH GRANT OPTION;
CREATE USER 'user_name'@'%' IDENTIFIED BY 'user_pass';
GRANT ALL PRIVILEGES ON *.* TO 'user_name'@'%' WITH GRANT OPTION;
```

##### 安装pyspider

本人使用`Anaconda`

```shell
# 搭建虚拟环境sbird python版本3.*
conda create -n sbird python=3*
# 进入环境
source activate sbird
# 安装pyspider
pip install pyspider
# 报错 
# it does not exist.  The exported locale is "en_US.UTF-8" but it is not supported
# 执行 可写入.bashrc
export LC_ALL=en_US.utf-8
export LANG=en_US.utf-8
#ImportError: pycurl: libcurl link-time version (7.29.0) is older than compile-time version (7.49.0)
conda install pycurl
# 退出
source deactivate sbird
# 若在虚拟机内 出现无法访问localhost:5000 可关闭防火墙
systemctl stop firewalld.service
#########直接运行源码==============
mkdir git;cd git
# 下载
git clone https://github.com/binux/pyspider.git
# 安装
/root/anaconda3/envs/sbird/bin/python  /root/git/pyspider/run.py
```

其他方法

```shell
# 搭建虚拟环境
pip install virtualenv
mkdir python;cd python
# 创建虚拟环境pyenv3
virtualenv -p /usr/bin/python3 pyenv3
# 进入虚拟环境 激活环境
cd pyenv3/
source ./bin/activate
pip install pyspider
# 若pycurl报错 
yum install libcurl-devel
# 继续
pip install pyspider
# 关闭
deactivate
```

本人推荐用anaconda方式安装

若pyspider运行过程中出现错误，参考`anaconda`安装部分，至此，访问`localhost:5000`可看到页面。

##### 安装Supervisor

```shell
# 安装
yum install supervisor -y
# 若无法检索 则添加阿里的epel源
vim /etc/yum.repos.d/epel.repo
# 添加以下内容
[epel]
name=Extra Packages for Enterprise Linux 7 - $basearch
baseurl=http://mirrors.aliyun.com/epel/7/$basearch
http://mirrors.aliyuncs.com/epel/7/$basearch
failovermethod=priority
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7

[epel-debuginfo]
name=Extra Packages for Enterprise Linux 7 - $basearch - Debug
baseurl=http://mirrors.aliyun.com/epel/7/$basearch/debug
http://mirrors.aliyuncs.com/epel/7/$basearch/debug
failovermethod=priority
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
gpgcheck=0

[epel-source]
name=Extra Packages for Enterprise Linux 7 - $basearch - Source
baseurl=http://mirrors.aliyun.com/epel/7/SRPMS
http://mirrors.aliyuncs.com/epel/7/SRPMS
failovermethod=priority
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
gpgcheck=0
# 安装
yum install supervisor -y
# 测试是否安装成功
echo_supervisord_conf
```

###### Supervisor用法

```shell
supervisord
supervisorctl
# 假设创建进程pyspider01
vim /etc/supervisord.d/pyspider01.ini
# 写入以下内容
[program:pyspider01]

command      = /root/anaconda3/envs/sbird/bin/python  /root/git/pyspider/run.py
directory    = /root/git/pyspider
user         = root
process_name = %(program_name)s
autostart    = true
autorestart  = true
startsecs    = 3

redirect_stderr         = true
stdout_logfile_maxbytes = 500MB
stdout_logfile_backups  = 10
stdout_logfile          = /pyspider/supervisor/pyspider01.log
# 重载
supervisorctl reload
# 启动
supervisorctl start pyspider01
# 也可这样启动
supervisord -c /etc/supervisord.conf
# 查看状态
supervisorctl status
# output 
pyspider01                       RUNNING   pid 4026, uptime 0:02:40
# 关闭
supervisorctl shutdown
```

##### 安装redis

```shell
# 消息队列采用redis
mkdir download;cd download
wget http://download.redis.io/releases/redis-3.2.4.tar.gz
tar xzf redis-3.2.4.tar.gz
cd redis-3.2.4
make
# 或者直接yum安装
yum -y install redis
# 启动
systemctl start redis.service
# 重启
systemctl restart redis.service
# 停止
systemctl stop redis.service
# 查看状态
systemctl status redis.service
# 更改文件/etc/redis.conf
vim /etc/redis.conf
# 更改内容
daemonize no 改为 daemonize yes
bind 127.0.0.1 改为 bind 10.211.55.22(当前服务器ip)
# 重启redis
systemctl restart redis.service
```

##### 

##### 关于自启动

```shell
# Supervisor添加到自启动服务
systemctl enable supervisord.service
# redis添加到自启动服务
systemctl enable redis.service
# 关闭防火墙自启动
systemctl disable firewalld.service
```



至此，pyspider单个服务器运行环境搭建且部署完毕，启动`localhost:5000`进入web界面。

也可编写脚本运行，在`/pyspider/supervisor/pyspider01.log`查看运行状态。

#### 分布式部署

刚才配置的服务器，将其命名为`centos01`，按照这样的配置，再分别部署两台`centos02、centos03`。

如下：

|  服务器名称   |      ip      |                    说明                    |
| :------: | :----------: | :--------------------------------------: |
| centos01 | 10.211.55.22 |         redis,mariaDB, scheduler         |
| centos02 | 10.211.55.23 | fetcher, processor, result_worker,phantomjs |
| centos03 | 10.211.55.24 | fetcher, processor,,result_worker,webui  |

##### centos01

进入服务器`centos01`，经过第一步，基本环境已经搭好，首先编辑配置文件`/pyspider/config.json`

```json
{
  "taskdb": "mysql+taskdb://user_name:user_pass@10.211.55.22:3306/taskdb",
  "projectdb": "mysql+projectdb://user_name:user_pass@10.211.55.22:3306/projectdb",
  "resultdb": "mysql+resultdb://user_name:user_pass@10.211.55.22:3306/resultdb",
  "message_queue": "redis://10.211.55.22:6379/db",
  "logging-config": "/pyspider/logging.conf",
  "phantomjs-proxy":"10.211.55.23:25555",
  "webui": {
    "username": "",
    "password": "",
    "need-auth": false,
  "host":"10.211.55.24",
  "port":"5000",
  "scheduler-rpc":"http:// 10.211.55.22:5002",
  "fetcher-rpc":"http://10.211.55.23:5001"
  },
  "fetcher": {
    "xmlrpc":true,
    "xmlrpc-host": "0.0.0.0",
    "xmlrpc-port": "5001"
  },
  "scheduler": {
    "xmlrpc":true,
    "xmlrpc-host": "0.0.0.0",
    "xmlrpc-port": "5002"
  }
}

```

尝试运行下：

```shell
/root/anaconda3/envs/sbird/bin/python /root/git/pyspider/run.py -c /pyspider/config.json scheduler
# 报错
ImportError: No module named 'mysql'
# 下载 mysql-connector-python
cd ~/git/
git clone https://github.com/mysql/mysql-connector-python.git
# 安装
source activate sbird
cd mysql-connector-python
python setup.py install
# 安装redis
pip install redis
source deactivate
# 运行
/root/anaconda3/envs/sbird/bin/python /root/git/pyspider/run.py -c /pyspider/config.json scheduler
# 输出 ok
[I 161010 15:57:25 scheduler:644] scheduler starting...
[I 161010 15:57:25 scheduler:779] scheduler.xmlrpc listening on 0.0.0.0:5002
[I 161010 15:57:25 scheduler:583] in 5m: new:0,success:0,retry:0,failed:0
```

运行成功后，可直接更改`/etc/supervisord.d/pyspider01.ini`如下：

```vim
[program:pyspider01]

command      = /root/anaconda3/envs/sbird/bin/python /root/git/pyspider/run.py -c /pyspider/config.json scheduler
directory    = /root/git/pyspider
user         = root
process_name = %(program_name)s
autostart    = true
autorestart  = true
startsecs    = 3

redirect_stderr         = true
stdout_logfile_maxbytes = 500MB
stdout_logfile_backups  = 10
stdout_logfile          = /pyspider/supervisor/pyspider01.log
```

```shell
# 重载
supervisorctl reload
# 查看状态
supervisorctl status
```

`centos01`部署完毕。

##### centos02

在`centos02`中，需要运行`result_worker、processor、phantomjs、fetcher`

分别建立文件：

`/etc/supervisord.d/result_worker.ini`

```ini
[program:result_worker]

command      = /root/anaconda3/envs/sbird/bin/python /root/git/pyspider/run.py -c /pyspider/config.json result_worker
directory    = /root/git/pyspider
user         = root
process_name = %(program_name)s
autostart    = true
autorestart  = true
startsecs    = 3

redirect_stderr         = true
stdout_logfile_maxbytes = 500MB
stdout_logfile_backups  = 10
stdout_logfile          = /pyspider/supervisor/result_worker.log
```



`/etc/supervisord.d/processor.ini`

```ini
[program:processor]

command      = /root/anaconda3/envs/sbird/bin/python /root/git/pyspider/run.py -c /pyspider/config.json processor
directory    = /root/git/pyspider
user         = root
process_name = %(program_name)s
autostart    = true
autorestart  = true
startsecs    = 3

redirect_stderr         = true
stdout_logfile_maxbytes = 500MB
stdout_logfile_backups  = 10
stdout_logfile          = /pyspider/supervisor/processor.log
```



`/etc/supervisord.d/phantomjs.ini`

```ini
[program:phantomjs]

command      = /pyspider/phantomjs --config=/pyspider/pjsconfig.json /pyspider/phantomjs_fetcher.js 25555
directory    = /root/git/pyspider
user         = root
process_name = %(program_name)s
autostart    = true
autorestart  = true
startsecs    = 3

redirect_stderr         = true
stdout_logfile_maxbytes = 500MB
stdout_logfile_backups  = 10
stdout_logfile          = /pyspider/supervisor/phantomjs.log
```



`/etc/supervisord.d/fetcher.ini`

```ini
[program:fetcher]

command      = /root/anaconda3/envs/sbird/bin/python /root/git/pyspider/run.py -c /pyspider/config.json fetcher
directory    = /root/git/pyspider
user         = root
process_name = %(program_name)s
autostart    = true
autorestart  = true
startsecs    = 3

redirect_stderr         = true
stdout_logfile_maxbytes = 500MB
stdout_logfile_backups  = 10
stdout_logfile          = /pyspider/supervisor/fetcher.log
```



在`pyspider`目录中建立pjsconfig.json

```json
{
  /*--ignore-ssl-errors=true */
  "ignoreSslErrors": true,
  
  /*--ssl-protocol=true */
  "sslprotocol": "any",
  
  /* Same as: --output-encoding=utf8 */
  "outputEncoding": "utf8",
  
  /* persistent Cookies. */
  /*cookiesfile="e:/phontjscookies.txt",*/
  cookiesfile="pyspider/phontjscookies.txt",
  
  /* load image */
  autoLoadImages = false
}

```

下载phantomjs至`/pyspider/`文件夹，将`git/pyspider/pyspider/fetcher/phantomjs_fetcher.js`复制到`phantomjs_fetcher.js`

```shell
# 重载
supervisorctl reload
# 查看状态
supervisorctl status
# output
fetcher                          RUNNING   pid 3446, uptime 0:00:07
phantomjs                        RUNNING   pid 3448, uptime 0:00:07
processor                        RUNNING   pid 3447, uptime 0:00:07
result_worker                    RUNNING   pid 3445, uptime 0:00:07
```

`centos02`部署完毕。

##### centos03

部署这三个进程`fetcher, processor, result_worker`和`centos02` 一样，本服务器主要是在前面的基础上加上`webui`

建立文件：

`/etc/supervisord.d/webui.ini`

```ini
[program:webui]

command      = /root/anaconda3/envs/sbird/bin/python /root/git/pyspider/run.py -c /pyspider/config.json webui
directory    = /root/git/pyspider
user         = root
process_name = %(program_name)s
autostart    = true
autorestart  = true
startsecs    = 3

redirect_stderr         = true
stdout_logfile_maxbytes = 500MB
stdout_logfile_backups  = 10
stdout_logfile          = /pyspider/supervisor/webui.log
```



```shell
# 重载
supervisorctl reload
# 查看状态
supervisorctl status
# output
fetcher                          RUNNING   pid 2724, uptime 0:00:07
processor                        RUNNING   pid 2725, uptime 0:00:07
result_worker                    RUNNING   pid 2723, uptime 0:00:07
webui                            RUNNING   pid 2726, uptime 0:00:07
```

#### 总结

访问 http://10.211.55.25:5000 即可，尽情爬取吧。

![EQIEle](https://images-1252557999.file.myqcloud.com/uPic/EQIEle.jpg)