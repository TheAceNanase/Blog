# Django安装

## Django 安装

 
在安装 Django 前，系统需要已经安装了Python的开发环境。


---


 ## Window 下安装 Django

 
如果你还未安装Python环境需要先下载Python安装包。


1、Python 下载地址：[https://www.python.org/downloads/](https://www.python.org/downloads/)


2、Django 下载地址：[https://www.djangoproject.com/download/](https://www.djangoproject.com/download/)


Python 安装(已安装的可跳过)
    



安装Python你只需要下载python-x.x.x.msi文件，然后一直点击"Next"按钮即可。



 ![](https://atts.w3cschool.cn/attachments/image/20160930/1475235365621422.png)


为了检查我们的python是否安装成功，可以在命令窗口中输入python进行查询，如显示下图一的信息则表示成功了。
    




![](https://atts.w3cschool.cn/attachments/image/20200611/1591868127468769.png)
    



安装完成后你需要设置Python环境变量。 右击计算机->属性->高级->环境变量->修改系统变量path，添加Python安装地址，本文实例使用的是C:\Python33，你需要根据你实际情况来安装。



![](https://atts.w3cschool.cn/attachments/image/20160930/1475235391119188.png)

 
##### 安装Django代码



   安装说明会有所不同，具体取决于您是安装特定于发行版的软件包，下载最新的官方发行版还是获取最新的开发版本。

 ### 使用安装正式版本pip


这是安装Django的推荐方法。


1. 安装[pip](https://pip.pypa.io/)。最简单的方法是使用[独立的pip安装程序](https://pip.pypa.io/en/latest/installing/#installing-with-get-pip-py)。如果您的发行版已经pip安装，则如果过时，则可能需要对其进行更新。如果过时，您将知道，因为安装将无法进行。
1. 看看[venv](https://docs.python.org/3/tutorial/venv.html)。该工具提供了隔离的Python环境，比在系统范围内安装软件包更实际。它还允许安装没有管理员权限的软件包。该[贡献教程](https://docs.djangoproject.com/en/3.0/intro/contributing/)走过了如何创建一个虚拟的环境。
1. 创建并激活虚拟环境后，根据不同的操作系统，输入以下命令：

```
Linux/MAC： python -m pip install Django
```

```
Windows: py -m pip 
```

### 安装特定于发行版的软件包


查看特定于发行版的说明，以查看您的平台/发行版是否提供了官方的Django软件包/安装程序。发行版提供的软件包通常将允许自动安装依赖项和受支持的升级路径；但是，这些软件包很少包含最新版本的Django。


### 安装开发版本

跟踪Django开发

如果您决定使用Django的最新开发版本，则需要密切注意[开发时间表](https://code.djangoproject.com/timeline)，并希望留意[即将发布](https://docs.djangoproject.com/en/3.0/releases/#development-release-notes)的[发行说明](https://docs.djangoproject.com/en/3.0/releases/#development-release-notes)。这将帮助您掌握可能要使用的所有新功能，以及在更新Django副本时需要对代码进行的任何更改。（对于稳定版本，任何必要的更改都记录在发行说明中。）

如果您希望能够偶尔通过最新的错误修复和改进来更新Django代码，请按照以下说明进行操作：

1. 确保已安装[Git](https://git-scm.com/)，并且可以从外壳运行其命令。（在shell提示符下输入以进行测试。）git help
1. 像这样检查Django的主要开发分支：


    
```
<p>Linux/MAC： $ git clone https://github.com/django/django.git
</p><p>Windows:... \&gt; git clone https://github.com/django/django.git</p>
```

这将`django`在当前目录中创建一个目录。

  3. 确保Python解释器可以加载Django的代码。最方便的方法是使用虚拟环境和pip。该 贡献教程走过了如何创建一个虚拟的环境。

  4. 设置并激活虚拟环境后，运行以下命令：

```
<p>Linux/MAC：$ python -m pip install -e django/
</p><p>Windows:   ... \&gt; py -m pip install -e django \</p>
```

这将使Django的代码可导入，并使 `django-admin`Utility命令可用。换句话说，您已经准备就绪！

当您想要更新Django源代码的副本时，请从目录中运行命令 。执行此操作时，Git将下载所有更改。`git pulldjango`

