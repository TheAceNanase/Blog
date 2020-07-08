# Django+Nginx+uWSGI生产环境部署

![img](https://img2020.cnblogs.com/blog/1394324/202005/1394324-20200519152007439-2012338036.png)

 

#### WSGI是什么？

1. WSGI是一种规范，它定义了使用python编写的web app(django)与web server（uWSGI）之间接口格式，实现web app与web server间的解耦。

1. WSGI 没有官方的实现, 因为WSGI更像一个协议. 只要遵照这些协议,WSGI应用(Application)都可以在任何服务器(Server)上运行

1. WSGI实质：WSGI是一种描述web服务器（如nginx，uWSGI等服务器）如何与web应用程序（如用Django、Flask框架写的程序）通信的规范、协议。

#### 为什么需要web协议：

　1）不同的框架有不同的开发方式，但是无论如何，开发出的应用程序都要和服务器程序配合，才能为用户提供服务。

　2） 这样，服务器程序就需要为不同的框架提供不同的支持,只有支持它的服务器才能被开发出的应用使用，显然这是不可行的。

　3）web协议本质：就是定义了Web服务器和Web应用程序或框架之间的一种简单而通用的接口规范。

#### Web协议介绍

　Web协议出现顺序： CGI -> FCGI -> WSGI -> uwsgi

　1. CGI： 最早的协议

　2. FCGI： 比CGI快

1. WSGI： Python专用的协议
2. uwsgi： 比FCGI和WSGI都快，是uWSGI项目自有的协议，主要特征是采用二进制来存储数据， 议都是使用字符串，所以在存储空间和解析速度上，都优于字符串型协议.

 

#### uwsgi是什么？

1. 它是一个二进制协议，可以携带任何类型的数据。
2. 一个uwsgi分组的头4个字节描述了这个分组包含的数据类型。
3. uwsgi是一种线路协议而不是通信协议，在此常用于在uWSGI服务器与其他网络服务器的数据通信；

 

#### uWSGI是什么？（web服务器 和nginx类似）

1. 什么是uWSGI： uWSGI是一个全功能的HTTP服务器，实现了WSGI协议、uwsgi协议、http协议等。

1. uWSGI\作用：它要做的就是把HTTP协议转化成语言支持的网络协议，比如把HTTP协议转化成WSGI协议，让Python可以直接使用。

1. uWSGI特点：轻量级，易部署，性能比nginx差很多

　注：

　　如果架构是Nginx+uWSGI+APP，uWSGI是一个中间件 　　如果架构是uWSGI+APP，uWSGI是一个服务器

 

#### WSGI / uwsgi / uWSGI 这三个概念的区分。

- WSGI是一种通信协议。通常用户django框架和uWSGI服务器之间通信。（如果说的更细致的话就是用来和Python WSGI model）
- uwsgi是一种线路协议而不是通信协议，在此常用于在uWSGI服务器与其他网络服务器的数据通信。
- 而uWSGI是实现了uwsgi和WSGI等协议的Web服务器

 

#### nginx是什么？

1. Nginx是一个Web服务器,其中的HTTP服务器功能和uWSGI功能很类似
2. 但是Nginx还可以用作更多用途，比如最常用的反向代理、负载均衡、拦截攻击等，而且性能极高

 

#### django是什么？

　1. Django是一个Web框架，框架的作用在于处理request和 reponse，其他的不是框架所关心的内容。

　2. 所以如何部署Django不是Django所需要关心的。

 

#### 请求处理整体流程　　　　　

- nginx接收到浏览器发送过来的http请求，将包进行解析，分析url
- 静态文件请求：就直接访问用户给nginx配置的静态文件目录，直接返回用户请求的静态文件
- 动态接口请求：那么nginx就将请求转发给uWSGI，最后到达django处理

#### 各模块作用

　1. nginx：是对外的服务器，外部浏览器通过url访问nginx，nginx主要处理静态请求

　2. uWSGI：是对内的服务器，主要用来处理动态请求

　3. uwsgi：是一种web协议，接收到请求之后将包进行处理，处理成wsgi可以接受的格式，并发给wsgi

　4. WSGI：是python专用的web协议，根据请求调用应用程序（django）的某个文件，某个文件的某个函数

　5. django：是真正干活的，查询数据等资源，把处理的结果再次返回给WSGI， WSGI 将返回值进行打包，打包成uwsgi能够接收的格式

　6. uwsgi接收wsgi发送的请求，并转发给nginx,nginx最终将返回值返回给浏览器

 

#### Django + uWSGI方案

　1. 没有nginx而只有uwsgi的服务器，则是Internet请求直接由uwsgi处理，并反馈到web项目中。

　2. nginx可以实现安全过滤，防DDOS等保护安全的操作，并且如果配置了多台服务器，nginx可以保证服务器的负载相对均衡。

　3. 而uwWSGI则是一个web服务器，实现了WSGI协议(Web Server Gateway Interface)，http协议等，它可以接收和处理请求，发出响应等。所以只用uwsgi也是可以的。

 

#### nginx和uWSGI特点

##### 　1.nginx的作用

　　1.反向代理，可以拦截一些web攻击，保护后端的web服务器

　　2.负载均衡，根据轮询算法，分配请求到多节点web服务器

　　3.缓存静态资源，加快访问速度，释放web服务器的内存占用，专项专用

##### 　2.uWSGI的适用

　　1.单节点服务器的简易部署

　　2.轻量级，好部署

 

### Django + uWSGI+ Nginx 的生产环境部署

1. 在centos 7中安装python3环境
```
# 1、yum更新yum源
yum update
# 2、安装Python 3.7所需的依赖否则安装后没有pip3包
yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel libffi-devel gcc make
# 3、在官网下载所需版本，这里用的是3.7.0版本
wget https://www.python.org/ftp/3.7.0/Python-3.7.0.tgz
　　2、安装Python

# 1、解压
tar -xvf Python-3.7.0.tgz

#2、配置编译
cd Python-3.7.0
./configure --prefix=/usr/local/python3  # 配置编译的的路径（这里--prefix是指定编译安装的文件夹）
./configure --enable-optimizations  # 执行该代码后，会编译安装到 /usr/local/bin/ 下，且不用添加软连接或环境变量
make && make install
ln -s /usr/local/python3/bin/python3 /usr/bin/python3  # 添加软连接
ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3

#3、将/usr/local/python3/bin加入PATH
[root@linux-node1 testProj]# vim /etc/profile
#然后在文件末尾添加
export PATH=$PATH:/usr/local/python3/bin

[root@linux-node1 testProj]# source /etc/profile # 修改完后，还需要让这个环境变量在配置信息中生效，执行命令
```
1. 初始化一个django项目
```
[root@linux-node1 /]# pip3 install django==2.0.4
[root@linux-node1 /]# mkdir /code/
[root@linux-node1 /]# cd /code/
[root@linux-node1 testProj]# django-admin startproject mmcsite
[root@linux-node1 testProj]# cd /code/mmcsite
[root@linux-node1 testProj]# python3 manage.py runserver 0.0.0.0:8000
# 页面中访问：http://192.168.56.11:8000/
```
1. 安装uWSGI 并使用uWSGI启动这个服务
```
'''1. 安装uwsgi'''
[root@linux-node1 /]# pip3 install uwsgi
[root@linux-node1 /]# ln -s /usr/local/python3/bin/uwsgi /usr/bin/uwsgi

'''2. 配置uwsgi.ini启动文件'''
[root@linux-node1 /]# vim uwsgi.ini
[uwsgi]
socket = 0.0.0.0:3031
chdir = /code/mmcsite
wsgi-file = /code/mmcsite/wsgi.py
processes = 5
threads = 30
master = true
daemonize = /code/mmcsite/uwsgi.log
module=mmcsite.wsgi
pidfile = /code/mmcsite/uwsgi.pid
chmod-socket=666
enable-threads = true

'''3. 使用uwsgi启动django：一定要在这个项目目录中'''
[root@linux-node1 /]# uwsgi --http 192.168.56.11:80 --file mmcsite/wsgi.py --static-map=/static=static
访问项目：http://192.168.56.11
```
4. 配置uWSGI文件
```
[root@linux-node2 demo2]# vim /code/mmcsite/uwsgi.ini  # uwsgi.ini文件
```
```
[uwsgi]
socket = 0.0.0.0:3031                  # 指定socket监听的地址和端口
chdir = /code/mmcsite                  # 项目路径 
wsgi-file = /code/mmcsite/wsgi.py      # django的wsgi文件路径
processes = 5                          # 启动五个进程
threads = 30                           # 每个进程启动30个线程
master = true
daemonize = /code/mmcsite/uwsgi.log    # 日志存放路径
module=mmcsite.wsgi                    # 使用mmcsite.wsgi模块
pidfile = /code/mmcsite/uwsgi.pid      # uwsgi启动进程id存放路径
chmod-socket=666                       # socket权限
enable-threads = true                  # 允许用内嵌的语言启动线程，这将允许你在app程序中产生一个子线程
```

5. 安装nginx
```
'''1. 配置nginx YUM源'''
[root@linux-node1 /] vim /etc/yum.repos.d/nginx.repo
```
[nginx]
name=nginx repo
# 下面这行centos根据你自己的操作系统修改比如：OS/rehel
# 6是你Linux系统的版本，可以通过URL查看路径是否正确
baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=0
enabled=1
```
'''2. 安装nginx'''
[root@linux-node1 /] yum -y install nginx
```

6. 配置nginx

```
[root@linux-node1 /]# vim /etc/nginx/conf.d/django.conf 
```
```
server {
    listen       8888;
    server_name  192.168.56.11;
    client_max_body_size 5M;
    gzip on;
    gzip_buffers 32 4K;#压缩在内存中缓冲32块 每块4K
    gzip_comp_level 6 ;#压缩级别 推荐6
    gzip_min_length 4000;#开始压缩的最小长度4bit
        gzip_types text/plain application/json application/javascript application/x-javascript application/css application/xml application/xml+rss text/javascript application/x-httpd-php image/jpeg image/gif image/png image/x-ms-bmp;
        location / {
              include uwsgi_params;
              uwsgi_pass 127.0.0.1:3031;
              uwsgi_ignore_client_abort on;
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }

}

```

1. 启动项目
```
[root@linux-node1 demo2]# systemctl restart nginx   # 开启nginx
[root@linux-node1 demo2]# uwsgi --ini uwsgi.ini     # 启动uwsgi的django项目
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　 # http://192.168.56.11:8888/ 访问项目
[root@linux-node1 demo2]# uwsgi --stop uwsgi.pid    # 关闭uwsgi
```
