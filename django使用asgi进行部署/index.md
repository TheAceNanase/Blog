# Django使用ASGI进行部署



## 使用ASGI进行部署

除了WSGI，Django还支持在ASGI上进行部署，ASGI是用于异步Web服务器和应用程序的新兴Python标准。

Django的startproject管理命令为您设置了一个默认的ASGI配置，您可以根据项目的需要对其进行调整，并指导任何符合ASGI的应用服务器使用。

Django包括以下ASGI服务器的入门文档：

- 如何在Daphne中使用Django
- 如何在Uvicorn中使用Django

## 该application对象

与WSGI一样，ASGI也为您提供了一个application可调用对象，应用程序服务器使用该可调用对象与您的代码进行通信。通常application以在服务器可访问的Python模块中命名的对象的形式提供。

该startproject命令将创建一个/asgi.py包含此类application可调用文件的文件 。

开发服务器（runserver）不会使用它，但是任何ASGI服务器都可以在开发或生产中使用它。

ASGI服务器通常采用可调用的应用程序路径作为字符串。对于大多数Django项目，它看起来像myproject.asgi:application。

**警告：**尽管Django的默认ASGI处理程序将在同步线程中运行所有代码，但如果您选择运行自己的异步处理程序，则必须注意异步安全性。

不要在任何异步代码中调用阻塞同步函数或库。Django禁止您使用非异步安全的Django部分执行此操作，但第三方应用程序或Python库可能并非如此。

## 配置设置模块

当ASGI服务器加载您的应用程序时，Django需要导入设置模块-定义整个应用程序的位置。

Django使用 DJANGO_SETTINGS_MODULE环境变量以找到适当的设置模块。它必须包含指向设置模块的虚线路径。您可以将不同的值用于开发和生产。这完全取决于您如何组织设置。

如果未设置此变量，则默认asgi.py将其设置为 mysite.settings，其中mysite是项目的名称。

## 应用ASGI中间件

要应用ASGI中间件，或将Django嵌入另一个ASGI应用程序中，可以将Django的application对象包装在asgi.py文件中。例如：

```
from some_asgi_library import AmazingMiddleware
application = AmazingMiddleware(application)
```

# 在Daphne中使用Django

Daphne是用于UNIX的纯Python ASGI服务器，由Django项目的成员维护。它充当ASGI的参考服务器。

## 安装Daphne

您可以使用以下命令安装Daphne pip：

```
python -m pip install daphne
```

## 在Daphne中运行Django

安装Daphne后，将daphne提供一个命令来启动Daphne服务器进程。最简单的说，需要使用包含ASGI应用程序对象的模块的位置来调用Daphne，然后是调用该应用程序的位置（用冒号分隔）。

对于典型的Django项目，调用Daphne如下所示：

```
daphne myproject.asgi:application
```

这将开始监听一个进程127.0.0.1:8000。它要求您的项目位于Python路径上；确保从与manage.py文件相同的目录中运行此命令。

# Django与Uvicorn 的使用

Uvicorn是基于uvloop和的ASGI服务器httptools，着重于速度。

## 安装Uvicorn 

您可以使用以下命令安装Uvicorn pip：

```
python -m pip install uvicorn
```

## 在Uvicorn运行Django 

安装Uvicorn后，将uvicorn提供运行ASGI应用程序的命令。必须使用包含ASGI应用程序对象的模块的位置来调用Uvicorn，然后再调用该应用程序（由冒号分隔）。

对于一个典型的Django项目，调用Uvicorn如下所示：

```
uvicorn myproject.asgi:application
```
