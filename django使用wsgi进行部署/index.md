# Django使用WSGI进行部署

## 简介

Django的主要部署平台是[WSGI](https://wsgi.readthedocs.io/en/latest/)，这是Web服务器和应用程序的Python标准。

Django的startproject管理命令为您设置了一个最小的默认WSGI配置，您可以根据项目的需要对其进行调整，并指导任何符合WSGI的应用服务器使用。

Django包括以下WSGI服务器的入门文档：

- [如何在Gunicorn中使用Django](https://docs.djangoproject.com/en/3.0/howto/deployment/wsgi/gunicorn/)
- [如何在uWSGI中使用Django](https://docs.djangoproject.com/en/3.0/howto/deployment/wsgi/uwsgi/)
- [如何将Django与Apache和 mod_wsgi](https://docs.djangoproject.com/en/3.0/howto/deployment/wsgi/modwsgi/)
- [从Apache对Django用户数据库进行身份验证](https://docs.djangoproject.com/en/3.0/howto/deployment/wsgi/apache-auth/)

## 该application对象

使用WSGI进行部署的关键概念是application应用服务器用来与您的代码进行通信的Callable。通常application以在服务器可访问的Python模块中命名的对象的形式提供。

该startproject命令将创建一个/wsgi.py包含此类application可调用文件的文件 。

Django的开发服务器和生产WSGI部署都使用了它。

WSGI服务器application从其配置中获取可调用对象的路径。Django的内置服务器（即runserver 命令）从WSGI_APPLICATION设置中读取它。默认情况下，它设置为.wsgi.application，它指向中的application 可调用对象/wsgi.py。

## 配置设置模块

当WSGI服务器加载您的应用程序时，Django需要导入settings模块-定义整个应用程序的地方。

Django使用 DJANGO_SETTINGS_MODULE环境变量以找到适当的设置模块。它必须包含指向设置模块的虚线路径。您可以将不同的值用于开发和生产。这完全取决于您如何组织设置。

如果未设置此变量，则默认wsgi.py将其设置为 mysite.settings，其中mysite是项目的名称。这就是 runserver默认情况下发现默认设置文件的方式。

**注意：**由于环境变量是进程范围的，因此当您在同一进程中运行多个Django站点时，这将无效。这与mod_wsgi一起发生。

为避免此问题，请对每个站点在自己的守护进程中使用mod_wsgi的守护程序模式，或通过在中强制执行来覆盖环境中的值。os.environ["DJANGO_SETTINGS_MODULE"] = "mysite.settings"``wsgi.py

## 应用WSGI中间件

要应用WSGI中间件，您可以包装应用程序对象。例如，您可以在以下位置添加这些行wsgi.py：

```
from helloworld.wsgi import HelloWorldApplication
application = HelloWorldApplication(application)
```

如果您想将Django应用程序与另一个框架的WSGI应用程序结合使用，也可以用自定义WSGI应用程序替换Django WSGI应用程序，该应用程序以后将其委托给Django WSGI应用程序。

# Django与Gunicorn 的使用

Gunicorn（“绿色独角兽”）是用于UNIX的纯Python WSGI服务器。它没有依赖性，可以使用安装pip。

## 安装Gunicorn 

通过运行安装gunicorn 。有关更多详细信息，请参见[gunicorn文档](https://docs.gunicorn.org/en/latest/install.html)。

```
python -m pip install gunicorn
```

在Gunicorn中将Django作为通用WSGI应用程序运行

安装Gunicorn后，将gunicorn提供一个命令来启动Gunicorn服务器进程。gunicorn的最简单调用是传递包含名为的WSGI应用程序对象的模块的位置application，对于典型的Django项目，该对象应 类似于：

```
gunicorn myproject.wsgi
```

这将启动一个进程，该进程运行一个正在侦听的线程127.0.0.1:8000。它要求您的项目位于Python路径上；确保该命令最简单的方法是在与manage.py文件相同的目录中运行此命令。

有关其他提示，请参见Gunicorn的[部署文档](https://docs.gunicorn.org/en/latest/deploy.html)。

# 如何使用Django与uWSGI 

uWSGI是使用纯C编码的快速，自修复且对开发人员/ sysadmin友好的应用程序容器服务器。

也可以看看

uWSGI文档提供了一个涵盖Django，nginx和uWSGI（许多可能的部署设置）的[教程](https://uwsgi.readthedocs.io/en/latest/tutorials/Django_and_nginx.html)。以下文档重点介绍如何将Django与uWSGI集成。

## 先决条件：uWSGI 

uWSGI Wiki描述了几种安装过程。使用Python软件包管理器pip，您可以通过单个命令安装任何uWSGI版本。例如：

```
# Install current stable version.
$ python -m pip install uwsgi

# Or install LTS (long term support).
$ python -m pip install https://projects.unbit.it/downloads/uwsgi-lts.tar.gz
```

### uWSGI模型

uWSGI在客户端-服务器模型上运行。您的Web服务器（例如nginx，Apache）与django-uwsgi“工作者”进程进行通信以提供动态内容。

### 为Django配置和启动uWSGI服务器

uWSGI支持多种配置过程的方式。

这是启动uWSGI服务器的示例命令：

```
uwsgi --chdir=/path/to/your/project \
    --module=mysite.wsgi:application \
    --env DJANGO_SETTINGS_MODULE=mysite.settings \
    --master --pidfile=/tmp/project-master.pid \
    --socket=127.0.0.1:49152 \      # can also be a file
    --processes=5 \                 # number of worker processes
    --uid=1000 --gid=2000 \         # if root, uwsgi can drop privileges
    --harakiri=20 \                 # respawn processes taking more than 20 seconds
    --max-requests=5000 \           # respawn processes after serving 5000 requests
    --vacuum \                      # clear environment on exit
    --home=/path/to/virtual/env \   # optional path to a virtual environment
    --daemonize=/var/log/uwsgi/yourproject.log      # background the process
```

假设您有一个名为的顶级项目包mysite，并且mysite/wsgi.py其中包含一个包含WSGI application  对象的模块。如果您使用Django的最新版本（使用您自己的项目名称代替）运行，这将是您的布局。如果该文件不存在，则需要创建它。请参阅“ [如何使用WSGI进行部署”](https://docs.djangoproject.com/en/3.0/howto/deployment/wsgi/)文档，以获取应放在此文件中的默认内容以及可以添加的其他内容。django-admin startproject mysite``mysite

Django特定的选项如下：

- chdir：需要在Python的导入路径上的目录路径-即包含mysite软件包的目录。
- module：要使用的WSGI模块-可能mysite.wsgi是startproject创建的模块。
- env：应该至少包含DJANGO_SETTINGS_MODULE。
- home：项目虚拟环境的可选路径。

示例ini配置文件：

```
[uwsgi]
chdir=/path/to/your/project
module=mysite.wsgi:application
master=True
pidfile=/tmp/project-master.pid
vacuum=True
max-requests=5000
daemonize=/var/log/uwsgi/yourproject.log
```

ini配置文件用法示例：

```
uwsgi --ini uwsgi.ini
```

修复UnicodeEncodeError文件上传

如果UnicodeEncodeError在上传文件名包含非ASCII字符的文件时看到，请确保将uWSGI配置为接受非ASCII文件名，方法是将其添加到您的uwsgi.ini：

```
env = LANG=en_US.UTF-8
```

有关详细信息，请参见Unicode参考指南的“ 文件”部分。

请参阅有关管理uWSGI进程的uWSGI文档，以获取有关启动，停止和重新加载uWSGI工作程序的信息。

# 在Apache和Django中使用Django和mod_wsgi

使用Apache和mod_wsgi部署Django 是使Django投入生产的一种久经考验的方法。

mod_wsgi是一个Apache模块，可以承载任何Python WSGI应用程序，包括Django。Django可以与任何支持mod_wsgi的Apache版本一起使用。

在官方的mod_wsgi文档是你的所有关于如何使用mod_wsgi的详细信息来源。您可能需要从安装和配置文档开始。

## 基本配置

安装并激活mod_wsgi后，请编辑Apache服务器的 httpd.conf文件并添加以下内容。

```
WSGIScriptAlias / /path/to/mysite.com/mysite/wsgi.py
WSGIPythonHome /path/to/venv
WSGIPythonPath /path/to/mysite.com

<Directory /path/to/mysite.com/mysite>
<Files wsgi.py>
Require all granted
</Files>
</Directory>
```

该WSGIScriptAlias行的第一位是您要在其上为应用程序提供服务的基本URL路径（/表示根URL），第二位是“  WSGI文件”的位置–参见下文–在系统上，通常在项目内部包（mysite在此示例中）。这告诉Apache使用该文件中定义的WSGI应用程序为给定URL下的任何请求提供服务。

如果您将项目的Python依赖项安装在中，请使用添加路径。有关更多详细信息，请参见[mod_wsgi虚拟环境指南](https://modwsgi.readthedocs.io/en/develop/user-guides/virtual-environments.html)。virtual environmentWSGIPythonHome

该WSGIPythonPath行确保您的项目包可用于在Python路径上导入；换句话说，那行得通。import mysite

该`部分确保Apache可以访问您的wsgi.py` 文件。

接下来，我们需要确保wsgi.pyWSGI应用程序对象存在。从Django 1.4版开始，startproject将为您创建一个。否则，您将需要创建它。请参阅[WSGI概述文档](https://docs.djangoproject.com/en/3.0/howto/deployment/wsgi/)，以获取应放在此文件中的默认内容以及可以添加的其他内容。

**警告：**如果在单个mod_wsgi进程中运行多个Django站点，则所有站点都将使用碰巧先运行的站点的设置。可以通过以下方法解决：

```
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "{{ project_name }}.settings")
```

在中wsgi.py，至：

```
os.environ["DJANGO_SETTINGS_MODULE"] = "{{ project_name }}.settings"
```

或通过使用mod_wsgi守护程序模式并确保每个站点都在其自己的守护进程中运行。

修复UnicodeEncodeError文件上传

如果UnicodeEncodeError在上传文件名包含非ASCII字符的文件时看到，请确保Apache配置为接受非ASCII文件名：

```
export LANG='en_US.UTF-8'
export LC_ALL='en_US.UTF-8'
```

放置此配置的常见位置是/etc/apache2/envvars。

有关详细信息，请参见Unicode参考指南的“ [文件”](https://docs.djangoproject.com/en/3.0/ref/unicode/#unicode-files)部分。

## 使用mod_wsgi守护程序模式

建议使用“守护程序模式”运行mod_wsgi（在非Windows平台上）。要创建所需的守护进程组并委派Django实例在其中运行，您将需要添加适当的  WSGIDaemonProcess和WSGIProcessGroup指令。如果您使用守护程序模式，则对上述配置的进一步更改是您不能使用WSGIPythonPath；相反，您应该使用的python-path选项 WSGIDaemonProcess，例如：

```
WSGIDaemonProcess example.com python-home=/path/to/venv python-path=/path/to/mysite.com
WSGIProcessGroup example.com
```

如果要在子目录中服务项目（https://example.com/mysite在此示例中），则可以添加WSGIScriptAlias 到上面的配置中：

```
WSGIScriptAlias /mysite /path/to/mysite.com/mysite/wsgi.py process-group=example.com
```

有关[设置守护程序模式的详细信息，](https://modwsgi.readthedocs.io/en/develop/user-guides/quick-configuration-guide.html#delegation-to-daemon-process)请参见mod_wsgi官方文档。

## 服务文件

Django本身不提供文件；它将工作交给您选择的任何Web服务器。

我们建议使用单独的Web服务器（即未同时运行Django的服务器）来提供媒体服务。这里有一些不错的选择：

- Nginx的
- 精简版的Apache

但是，如果您别无选择，只能在与VirtualHostDjango 相同的Apache上提供媒体文件，则 可以设置Apache以将某些URL用作静态媒体，而将另一些URL使用Django的mod_wsgi接口提供。

这个例子设置的Django在站点根目录，但发球robots.txt， favicon.ico和在什么/static/和/media/URL空间作为静态文件。所有其他URL将使用mod_wsgi提供：

```
Alias /robots.txt /path/to/mysite.com/static/robots.txt
Alias /favicon.ico /path/to/mysite.com/static/favicon.ico

Alias /media/ /path/to/mysite.com/media/
Alias /static/ /path/to/mysite.com/static/

<Directory /path/to/mysite.com/static>
Require all granted
</Directory>

<Directory /path/to/mysite.com/media>
Require all granted
</Directory>

WSGIScriptAlias / /path/to/mysite.com/mysite/wsgi.py

<Directory /path/to/mysite.com/mysite>
<Files wsgi.py>
Require all granted
</Files>
</Directory>
```

## 提供管理文件

在django.contrib.staticfiles中时INSTALLED_APPS，Django开发服务器会自动提供admin应用程序（以及所有其他已安装的应用程序）的静态文件。但是，当您使用任何其他服务器布置时，情况并非如此。您负责设置Apache或您使用的任何Web服务器来提供管理文件。

管理文件位于django/contrib/admin/static/adminDjango发行版的（）中。

我们强烈建议您使用django.contrib.staticfiles处理管理文件（如上一节中列出Web服务器一起，使用这种手段collectstatic的管理命令收集的静态文件STATIC_ROOT，然后配置你的Web服务器服务STATIC_ROOT的STATIC_URL），但这里有三个其他方法：

1. 从文档根目录创建指向管理静态文件的符号链接（这可能+FollowSymLinks在您的Apache配置中需要）。
2. Alias如上所示，使用指令将适当的URL（可能为STATIC_URL+ admin/）别名为管理文件的实际位置。
3. 复制管理静态文件，以使它们位于Apache文档根目录中。

## 从Apache对Django用户数据库进行身份验证

Django提供了一个处理程序，允许Apache直接针对Django的身份验证后端对用户进行身份验证。

由于与多个Apache处理身份验证数据库时保持同步是一个常见问题，因此可以将Apache配置为直接针对Django的身份验证系统进行身份 验证。这需要Apache版本> = 2.2和mod_wsgi> = 2.0。例如：

- 仅将静态/媒体文件直接从Apache提供给经过身份验证的用户。
- 拥有一定的权限，针对Django用户验证对Subversion存储库的访问。
- 允许某些用户连接到使用mod_dav创建的WebDAV共享。

**注意：**如果您已经安装了自定义用户模型并希望使用此默认身份验证处理程序，则它必须支持一个is_active  属性。如果要使用基于组的授权，则自定义用户必须具有一个名为“组”的关系，该关系引用具有“名称”字段的相关对象。如果您的自定义不符合这些要求，则还可以指定自己的自定义mod_wsgi身份验证处理程序。

## 使用进行身份验证mod_wsgi

**注意：**在以下配置中使用时，假设您的Apache实例仅运行一个Django应用程序。如果您正在运行多个Django应用程序，请参阅mod_wsgi文档的“ 定义应用程序组”部分以获取有关此设置的更多信息。WSGIApplicationGroup %{GLOBAL}

确保已安装并激活了mod_wsgi，并且已按照步骤使用mod_wsgi设置Apache。

接下来，编辑您的Apache配置，以添加仅希望经过身份验证的用户才能查看的位置：

```
WSGIScriptAlias / /path/to/mysite.com/mysite/wsgi.py
WSGIPythonPath /path/to/mysite.com

WSGIProcessGroup %{GLOBAL}
WSGIApplicationGroup %{GLOBAL}

<Location "/secret">
    AuthType Basic
    AuthName "Top Secret"
    Require valid-user
    AuthBasicProvider wsgi
    WSGIAuthUserScript /path/to/mysite.com/mysite/wsgi.py
</Location>
```

该WSGIAuthUserScript指令告诉mod_wsgi check_password在指定的wsgi脚本中执行该 功能，并传递从提示符处收到的用户名和密码。在此示例中，与  定义由django-admin startproject创建的应用程序WSGIAuthUserScript的相同。WSGIScriptAlias

结合使用Apache 2.2和身份验证

确保mod_auth_basic和mod_authz_user已加载。

这些可能会静态地编译到Apache中，或者您可能需要使用LoadModule在您的中动态加载它们httpd.conf：

```
LoadModule auth_basic_module modules/mod_auth_basic.so
LoadModule authz_user_module modules/mod_authz_user.so
```

最后，mysite.wsgi通过导入以下check_password 功能来编辑WSGI脚本，以将Apache的身份验证与站点的身份验证机制联系起来：

```
import os

os.environ['DJANGO_SETTINGS_MODULE'] = 'mysite.settings'

from django.contrib.auth.handlers.modwsgi import check_password

from django.core.handlers.wsgi import WSGIHandler
application = WSGIHandler()
```

/secret/现在开始的请求将要求用户进行身份验证。

mod_wsgi 访问控制机制文档提供了其他详细信息和有关替代身份验证方法的信息。

### mod_wsgi和Django组的授权

mod_wsgi还提供了将特定位置限制为组成员的功能。

在这种情况下，Apache配置应如下所示：

```
WSGIScriptAlias / /path/to/mysite.com/mysite/wsgi.py

WSGIProcessGroup %{GLOBAL}
WSGIApplicationGroup %{GLOBAL}

<Location "/secret">
    AuthType Basic
    AuthName "Top Secret"
    AuthBasicProvider wsgi
    WSGIAuthUserScript /path/to/mysite.com/mysite/wsgi.py
    WSGIAuthGroupScript /path/to/mysite.com/mysite/wsgi.py
    Require group secret-agents
    Require valid-user
</Location>
```

为了支持该WSGIAuthGroupScript指令，相同的WSGI脚本 mysite.wsgi还必须导入该groups_for_user函数，该函数返回给定用户所属的列表组。

```
from django.contrib.auth.handlers.modwsgi import check_password, groups_for_user
```

