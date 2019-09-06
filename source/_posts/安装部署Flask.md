---
title: CentOS7 安装部署Flask
date: 2019-09-6 20:44:48
subtitle: CentOS7 安装部署Flask
categories:
- 劈柴
tags:
- Python
- Flask
- CentOS
cover: http://prr6elh1d.sabkt.gdipper.com/static/images/IMG_3156.jpg
---
#### CentOS7 安装部署Flask

> 针对 Flask + vue-admin + mysql + redis 的部署文档

#### 安装Nginx

> ###### 1.更新yum 源
>
> ```shell
> sudo rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
> ```
>
> ###### 2.安装
>
> ```shell
> sudo yum install nginx
> ```
>
> ###### 3.配置nginx
>
> ```shell
> // 1.设置开机启动
> $ sudo systemctl enable nginx
> // 2.启动服务
> $ sudo systemctl start nginx
> // 3.停止服务
> $ sudo systemctl restart nginx
> // 4.重新加载，因为一般重新配置之后，不希望重启服务，这时可以使用重新加载。
> $ sudo systemctl reload nginx
> ```
>
> ###### 4.常见问题
>
> ```shell
> // 绑定其他端口
> // Nginx 默认绑定的端口是 http 协议的默认端口，端口号为：80，如果需要绑定其他端口，需要注意 SELinux 的配置
> 
> // 例如：绑定 8081 端口，但是会发现无法启动，一般的报错如下
> 
> // YYYY/MM/DD hh:mm:ss [emerg] 46123#0: bind() to 0.0.0.0:8081 failed (13: Permission denied)
> // 此时需要更改 SELinux 的设置。我们使用 SELinux 的管理工具 semanage 进行操作，比较方便。
> 
> // 安装 semanage 使用如下命令
> 
> sudo yum install policycoreutils-python
> // 然后查看是否有其他协议类型使用了此端口
> 
> sudo semanage port -l | grep 8081
> transproxy_port_t              tcp      8081
> // 返回了结果，表明已经被其他类型占用了，类型为 transproxy_port_t。
> 
> // 我们还要查看一下 Nginx 的在 SELinux 中的类型 http_port_t 绑定的端口
> 
> sudo semanage port -l | grep http_port_t
> http_port_t                    tcp      80, 81, 443, 488, 8008, 8009, 8443, 9000
> pegasus_http_port_t            tcp      5988
> // 第一行 http_port_t 中没有包含 8081 这个端口。因此需要修改 8081 端口到 http_port_t 类型中。
> 
> sudo semanage port -m -p tcp -t http_port_t 8081
> // 如果没有其他协议类型使用想要绑定的端口，如 8001，则我们只要新增到 SELinux 中即可。
> 
> sudo semanage port -l | grep 8001
> sudo semanage port -a -p tcp -t http_port_t 8001
> // 此时，重新启动 Nginx 即可。
> ```

#### 防火墙操作

> ###### 1. 查看已经开放的端口
>
> ```shell
> firewall-cmd --list-ports
> ```
>
> ###### 2.开启端口
>
> ```shell
> firewall-cmd --zone=public --add-port=80/tcp --permanent
> ```
>
> ###### 3.命令含义：
>
> > –zone #作用域
> >
> > –add-port=80/tcp #添加端口，格式为：端口/通讯协议
> >
> > –permanent #永久生效，没有此参数重启后失效
>
> ###### 4.重启防火墙
>
> > firewall-cmd --reload #重启firewall
> >
> > systemctl stop firewalld.service #停止firewall
> >
> > systemctl disable firewalld.service #禁止firewall开机启动
> >
> > firewall-cmd --state #查看默认防火墙状态（关闭后

#### CentOS 7 安装 Python3.7

> ######  1. 我们先看看现有的 python2在哪里
>
> ```shell
> [root@lidan /]# whereis python
> python: /usr/bin/python /usr/bin/python2.7 /usr/bin/python.bak /usr/lib/python2.7 /usr/lib64/python2.7 /etc/python /usr/include/python2.7 /usr/share/man/man1/python.1.gz
> [root@lidan bin]# ll python*
> lrwxrwxrwx. 1 root root    9 5月  27 2016 python2 -> python2.7
> -rwxr-xr-x. 1 root root 7136 11月 20 2015 python2.7
> lrwxrwxrwx. 1 root root    7 5月  27 2016 python.bak -> python2
> ```
>
> ######  2. 接下来我们要安装编译 Python3的相关包
>
> ```shell
> yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make libffi-devel
> ```
>
> 这里面有一个包很关键`libffi-devel`，因为只有3.7才会用到这个包，如果不安装这个包的话，在 make 阶段会出现如下的报错：
>
> ```shell
> ModuleNotFoundError: No module named '_ctypes'
> ```
>
> ######  3. 安装pip，因为 CentOs 是没有 pip 的。
>
> ```shell
> #运行这个命令添加epel扩展源 
> yum -y install epel-release 
> #安装pip 
> yum install python-pip
> ```
>
> ######  4. 可以用yum 安装 安装一下 wget
>
> ```shell
> yum -y install wget
> ```
>
> ######  5. 我们可以下载 python3.7的源码包了
>
> ```shell
> wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz
> #解压缩
> tar -zxvf Python-3.7.0.tgz
> 
> #进入解压后的目录，依次执行下面命令进行手动编译
> ./configure prefix=/usr/local/python3 
> make && make install
> ```
>
> 如果最后没提示出错，就代表正确安装了，在/usr/local/目录下就会有python3目录
>
> ######  6. 添加软链接
>
> ```shell
> #添加python3的软链接 
> ln -s /usr/local/python3/bin/python3.7 /usr/bin/python3.7 
> #添加 pip3 的软链接 
> ln -s /usr/local/python3/bin/pip3.7 /usr/bin/pip3.7
> #测试是否安装成功了 
> python -V
> ```
>
> ######  7. 更改yum配置，因为其要用到python2才能执行，否则会导致yum不能正常使用（不管安装 python3的那个版本，都必须要做的）
>
> ```shell
> vi /usr/bin/yum 
> 把 #! /usr/bin/python 修改为 #! /usr/bin/python2 
> vi /usr/libexec/urlgrabber-ext-down 
> 把 #! /usr/bin/python 修改为 #! /usr/bin/python2
> ```
>
> ######  8.安装pipenv
>
> ```shell
> pip install pipenv
> ```
>
> ######  9.安装git
>
> ```shell
> yum install git
> ```

#### 安装mysql8.0

>###### 1.下载mysql yum包
>
>官方下载地址:https://dev.mysql.com/downloads/repo/yum/ 或者直接使用wget下载
>
>```shell
>wget https://repo.mysql.com//mysql80-community-release-el7-1.noarch.rpm
>```
>
>###### 2.安装软件源
>
>```shell
>rpm -Uvh https://repo.mysql.com//mysql80-community-release-el7-1.noarch.rpm
>```
>
>###### 3.安装mysql
>
>```shell
>yum install mysql-community-server
># 安装过程中如果出现y/n的话就一直y就行了，如果嫌麻烦可以用这个命令来安装
>yum install -y mysql-community-server
>```
>
>###### 4.启动&配置
>
>```shell
>#启动
>service mysqld start
>#查看运行状态
>service mysqld status
># 看到绿色的running代表已经启动成功，然后mysql在5.6之后的版本都会默认生成一个默认密码，是root用户的。通过如下命令查看密码
>grep 'temporary password' /var/log/mysqld.log
>```
>
>###### 5.进入mysql
>
>执行完如下命令之后输入默认密码
>
>```shell
>mysql -u root -p
>```
>
>修改root密码
>
>```mysql
>alter user 'root'@'localhost' identified by '密码';
>```
>
>刷新
>
>```mysql
>flush privileges;
>```
>
>###### 6.创建用户和分配权限
>
>由于mysql8新提供了一种密码加密方式caching-sha2-password，且为默认，目前很多客户端都不支持，所以我们在创建新角色用户的时候可以指定其为mysql_native_password，原来的root账户等不去做任何改变(`无形之中增加了安全性有木有`)
>
>```mysql
>#创建一个test用户(一般情况下root用户只在本地环境下使用),密码是29dIg;2^，数据库的密码最好设置得连自己都记不住。
>CREATE USER 'test'@'%' IDENTIFIED WITH mysql_native_password BY '29dIg.2^';
>#授权数据库给用户，并设置所有ip都可以远程连接
>#如果只授予用户某些数据库的话就把*.*改成 数据库名称.*  意思就是 数据库.表
>#如果只授予用户某些权限的话，比如只能读不能写，就把GRANT ALL改成GRANT SELECT
>GRANT ALL ON *.* TO 'test'@'%';
>#当然，你也可以撤销授权,用法和授权几乎都是一样的。
>REVOKE ALL ON *.* from 'test'@'%'; 
>```
>
>###### 7.总结
>
>官方表示 MySQL 8 要比 MySQL 5.7 快 `2` 倍，还带来了大量的改进和更快的性能！所以我也是第一时间把[我的网站](https://www.plaza4me.com/)的mysql数据库从5.7升级到了8.0版本。 有些大神已经总结了8.0的诸多好处，我就不多赘述了。

#### 安装redis

> ###### 1.安装
>
> ```shell
> yum -y install redis
> ```
>
> ###### 2.启动redis
>
> ```shell
> service redis start
> ```
>
> ###### 3.停止redis
>
> ```shell
> service redis stop
> ```
>
> ###### 4.查看redis运行状态
>
> ```shell
> service redis status
> ```
>
> ###### 5.查看redis进程
>
> ```shell
> ps -ef | grep redis
> ```
>
> ###### 6.设置redis为开机自动启动
>
> ```shell
> chkconfig redis on
> ```
>
> ###### 7.进入redis服务
>
> ```shell
> # 进入本机redis
> redis-cli
> 
> # 列出所有key
> keys *
> ```
>
> ###### 8.防火墙开放相应端口
>
> ```shell
> # 开启6379 
> /sbin/iptables -I INPUT -p tcp --dport 6379 -j ACCEPT
> # 开启6380 
> /sbin/iptables -I INPUT -p tcp --dport 6380 -j ACCEPT
> # 保存 
> /etc/rc.d/init.d/iptables save
> # centos 7下执行 
> service iptables save
> ```
>
> ###### 9.修改redis默认端口和密码
>
> ```shell
> # 打开配置文件
> vi /etc/redis.conf
> 
> # 修改默认端口，查找 port 6379 修改为相应端口即可
> # 修改默认密码，查找 requirepass foobared 将 foobared 修改为你的密码
> # 使用配置文件启动 redis
> redis-server /etc/redis.conf &
> 
> # 使用端口登录
> redis-cli -h 127.0.0.1 -p 6179
> 
> # 此时再输入命令则会报错
> # 输入刚才输入的密码
> auth 111
> 
> # 停止redis
> redis-cli -h 127.0.0.1 -p 6179 shutdown
> 
> # 使用进程ID杀死
> ps -ef | grep redis kill -9 XXX
> 
> # 使用redis desktop manager远程连接redis
> ```

#### uwsgi安装配置

> ###### 1.直接使用pip 安装uwsgi
>
> 安装完成后配置uwsgi.ini文件
>
> ```ini
> [uwsgi]
> # 暴露给nginx代理的端口及ip
> socket=0.0.0.0:9090
> # 启动主进程，来管理其他进程，其它的uwsgi进程都是这个master进程的子进程，如果kill这个master进程，相当于重启所有的uwsgi进程。
> master=true
> # 在app加载前切换到当前目录， 指定运行目录
> chdir=/home/app/
> # 启动uwsgi的用户名和用户组
> uid=root
> gid=root
> #指定虚拟环境路径
> home=/root/.local/share/virtualenvs/app-4uwEKS64
> # 指定加载wsgi文件 app入口
> wsgi-file=app.py
> # uWSGI加载的模块中哪个变量将被调用
> callable=app
> # 设置工作进程的数量
> processes=1
> # 设置每个工作进程的线程数
> threads=4
> # 序列化接受的内容，如果可能的话
> thunder-lock=true
> # 设置缓冲
> post-buffering=4096
> # 缓冲区
> # buffer-size = 32768
> #允许用内嵌的语言启动线程。这将允许你在app程序中产生一个子线程
> enable-threads = true
> #设置在平滑的重启（直到接收到的请求处理完才重启）一个工作子进程中，等待这个工作>结束的最长秒数。这个配置会使在平滑地重启工作子进程中，如果工作进程结束时间超过了
> #8秒就会被强行结束（忽略之前已经接收到的请求而直接结束）
> reload-mercy = 8
> #为每个工作进程设置请求数的上限。当一个工作进程处理的请求数达到这个值，那么该工>作进程就会被回收重用（重启）。你可以使用这个选项来默默地对抗内存泄漏
> max-requests = 5000
> #通过使用POSIX/UNIX的setrlimit()函数来限制每个uWSGI进程的虚拟内存使用数。这个配>置会限制uWSGI的进程占用虚拟内存不超过256M。如果虚拟内存已经达到256M，并继续申请>虚拟内存则会使程序报内存错误，本次的http请求将返回500错误。
> limit-as = 256
> #一个请求花费的时间超过了这个harakiri超时时间，那么这个请求都会被丢弃，并且当前>处理这个请求的工作进程会被回收再利用（即重启）
> harakiri = 60
> # sock文件权限
> chmod-socket=666
> # log日志权限
> logfile-chmod=644
> # uwsgi的进程名称前缀
> procname-prefix-spaced=mysite
> 
> # py文件修改，自动加载
> # py-autoreload=1
> 
> # 监听端口，测试时候使用
> # http=0.0.0.0:8080
> 
> # 退出uwsgi是否清理中间文件，包含pid、sock和status文件
> vacuum=true
> 
> # socket文件，配置nginx时候使用
> socket=%(chdir)/uwsgi.sock
> 
> # status文件，可以查看uwsgi的运行状态
> stats=%(chdir)/uwsgi.status
> 
> # pid文件，通过该文件可以控制uwsgi的重启和停止
> pidfile=%(chdir)/uwsgi.pid
> 
> 
> # 日志文件，通过该文件查看uwsgi的日志
> daemonize=%(chdir)/uwsgi.log
> ```
>
> ###### 2.启动
>
> ```shell
> uwsgi --ini uwsgi.ini
> ```
>
> ###### 3.停止
>
> ```shell
> uwsgi --stop uwsgi.pid
> ```
>
> ###### 4.重启
>
> ```shell
> uwsgi --reload uwsgi.pid
> ```





