# Django创建第一个项目


## Django 创建第一个项目

 本章我们将介绍如何使用 Django 来创建项目。

使用 django-admin.py 来创建名为***的项目：

 
```  django-admin startproject xxx```

 创建完成后我们可以查看下项目的目录结构：

 
```
[root@solar ~]# cd HelloWorld/
[root@solar HelloWorld]# tree
. manage.py 管理器
|--*** 
|   |-- __init__.py 包
|   |-- settings.py  设置文件
|   |-- urls.py   路由
|   `-- wsgi.py   部署

```

 目录说明：

* **HelloWorld:** 项目的容器。
* ** manage.py:** 一个实用的命令行工具，可让你以各种方式与该 Django 项目进行交互。
* ** HelloWorld/__init__.py:** 一个空文件，告诉 Python 该目录是一个 Python 包。
* ** HelloWorld/settings.py:** 该 Django 项目的设置/配置。
* ** HelloWorld/urls.py:** 该 Django 项目的 URL 声明; 一份由 Django 驱动的网站"目录"。
* ** HelloWorld/wsgi.py:** 一个 WSGI 兼容的 Web 服务器的入口，以便运行你的项目。

创建一个app模块会自动生成app文件夹，该文件夹包括几个文件：

```
python manage.py start app
```

 各个目录的说明：

* init.py 包
* admin.py 管理后台
* apps.py
* migrations
* init.py 迁移
* model.py 模型
* test.py 测试
* view.py 视图

在目录中找到***包里面的setting.py，在NSTALLED_APPS当中注册APP模块：

```
<p>NSTALLED_APPS = [</p><p>    &#39;django.contrib.admin&#39;,</p><p>    &#39;django.contrib.auth&#39;,</p><p>    &#39;django.contrib.contenttypes&#39;,</p><p>    &#39;django.contrib.sessions&#39;,</p><p>    &#39;django.contrib.messages&#39;,</p><p>    &#39;django.contrib.staticfiles&#39;,</p><p>    &#39;app&#39;,</p>
```

在包下输入命令，启动项目：

```
python manage.py runserver
```

在浏览器输入你服务器的ip及端口号，如果正常启动，会得到如下界面，则表示项目创建完成：

![Django](https://atts.w3cschool.cn/attachments/image/20170706/1499339400726027.jpg)







