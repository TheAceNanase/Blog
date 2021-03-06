# Django部署清单

# 部署清单

互联网是一个敌对的环境。在部署Django项目之前，您应该花一些时间在考虑安全性，性能和操作的情况下检查设置。

Django包含许多安全功能。有些是内置的，并且始终启用。其他选项是可选的，因为它们并不总是合适的，或者因为它们不方便开发。例如，强制HTTPS可能不适用于所有网站，并且对于本地开发而言是不切实际的。

性能优化是另一种权衡取舍的方法。例如，缓存在生产中很有用，而对于本地开发则没有用。错误报告的需求也大不相同。

以下清单包括以下设置：

- 必须正确设置Django以提供预期的安全级别；
- 预期在每种环境中都会有所不同；
- 启用可选的安全功能；
- 实现性能优化；
- 提供错误报告。

其中许多设置都是敏感的，应视为机密。如果要发布项目的源代码，通常的做法是发布合适的设置进行开发，并使用私有设置模块进行生产。

## 运行manage.py check --deploy

使用该选件可以自动执行以下所述的某些检查。确保按照选件文档中的说明针对生产设置文件运行它。

```
check --deploy
```

## 关键设置

### SECRET_KEY

密钥必须是一个较大的随机值，并且必须保密。

确保生产中使用的密钥不在其他任何地方使用，并避免将其提交给源代码管理。这减少了攻击者可以从中获取密钥的向量的数量。

可以考虑从环境变量加载密钥，而不是在设置模块中对密钥进行硬编码：

```
import os
SECRET_KEY = os.environ['SECRET_KEY']
```

或来自文件：

```
with open('/etc/secret_key.txt') as f:
    SECRET_KEY = f.read().strip()
```

### DEBUG

您绝不能在生产中启用调试。

当然，您正在使用开发您的项目，因为这将启用方便的功能，例如浏览器中的完整追溯。DEBUG = True

但是，对于生产环境而言，这是一个非常糟糕的主意，因为它会泄露有关项目的大量信息：源代码的摘录，局部变量，设置，使用的库等。

## 特定于环境的设置

### ALLOWED_HOSTS

那时，如果没有合适的值，Django根本无法工作。DEBUG = FalseALLOWED_HOSTS

需要此设置来保护您的站点免受某些CSRF攻击。如果使用通配符，则必须对HostHTTP标头执行自己的验证，否则，请确保您不容易受到此类攻击。

您还应该配置位于Django前面的Web服务器以验证主机。它应该以静态错误页面响应或忽略对不正确主机的请求，而不是将请求转发给Django。这样，您将避免在Django日志（或电子邮件（如果您以这种方式配置了错误报告）中）出现虚假错误。例如，在nginx上，您可以设置默认服务器以在无法识别的主机上返回“ 444 No Response”：

```
server {
    listen 80 default_server;
    return 444;
}
```

### CACHES

如果您使用缓存，则连接参数在开发和生产中可能会有所不同。Django默认对每个进程进行本地内存缓存，这可能是不希望的。

缓存服务器通常具有弱认证。确保它们仅接受来自您的应用程序服务器的连接。

### DATABASES

数据库连接参数在开发和生产中可能有所不同。

数据库密码非常敏感。您应该像保护他们一样 SECRET_KEY。

为了获得最大的安全性，请确保数据库服务器仅接受来自应用程序服务器的连接。

如果您尚未为数据库设置备份，请立即执行！

### EMAIL_BACKEND和相关设置

如果您的站点发送电子邮件，则需要正确设置这些值。

默认情况下，Django从webmaster @ localhost和root @ localhost发送电子邮件。但是，某些邮件提供商拒绝来自这些地址的电子邮件。要使用其他发件人地址，请修改DEFAULT_FROM_EMAIL和 SERVER_EMAIL设置。

### STATIC_ROOT和STATIC_URL

静态文件由开发服务器自动提供。在生产中，必须定义一个STATIC_ROOT目录，collectstatic将它们复制到该目录中 。

有关更多信息，请参见管理静态文件（例如，图像，JavaScript，CSS）。

### MEDIA_ROOT和MEDIA_URL

媒体文件由您的用户上传。他们是不信任的！确保您的Web服务器从不尝试解释它们。例如，如果用户上传 .php文件，则Web服务器不应执行该文件。

现在是检查这些文件的备份策略的好时机。

## HTTPS 

任何允许用户登录的网站都应实施站点范围的HTTPS，以避免明文传输访问令牌。在Django中，访问令牌包括登录名/密码，会话cookie和密码重置令牌。（如果要通过电子邮件发送密码重置令牌，则无法做很多事情来保护它们。）

保护敏感区域（例如用户帐户或管理员）是不够的，因为HTTP和HTTPS使用相同的会话cookie。您的网络服务器必须将所有HTTP通信重定向到HTTPS，并且只能将HTTPS请求传输到Django。

设置HTTPS后，请启用以下设置。

### CSRF_COOKIE_SECURE

进行设置True以避免在HTTP上意外传输CSRF cookie。

### SESSION_COOKIE_SECURE

进行设置True以避免在HTTP上意外传输会话cookie。

## 性能优化

设置将禁用仅在开发中有用的几个功能。此外，您可以调整以下设置。DEBUG = False

### 会话

考虑使用缓存的会话来提高性能。

如果使用数据库支持的会话，请定期清除旧会话，以避免存储不必要的数据。

### CONN_MAX_AGE

如果在请求处理时间的很大一部分时间内连接到数据库帐户，则启用持久性数据库连接可以大大提高速度。

这对网络性能有限的虚拟主机有很大帮助。

### TEMPLATES

启用缓存的模板加载器通常会大大提高性能，因为它避免了每次需要渲染每个模板时都对它们进行编译。有关更多信息，请参见 [模板加载器文档](https://docs.djangoproject.com/en/3.0/ref/templates/api/#template-loaders)。

## 错误报告

当您将代码推向生产时，它有望变得健壮，但不能排除意外错误。值得庆幸的是，Django可以捕获错误并相应地通知您。

### LOGGING

在将网站投入生产之前，请查看日志记录配置，并在收到一些流量后立即检查它是否按预期工作。

有关日志记录的详细信息，请参见日志记录。

### ADMINS和MANAGERS

ADMINS 将通过电子邮件通知500个错误。

MANAGERS将会收到404错误的通知。 IGNORABLE_404_URLS可以帮助过滤掉虚假报告。

有关通过电子邮件报告错误的详细信息，请参见错误报告。

通过电子邮件报告的错误无法很好地扩展

在您的收件箱被报告淹没之前，请考虑使用诸如Sentry之类的错误监视系统。哨兵也可以汇总日志。

### 自定义默认错误视图

Django包含一些HTTP错误代码的默认视图和模板。您可能希望通过在根模板目录中创建以下模板来覆盖缺省模板：404.html，500.html，403.html，和 400.html。使用这些模板的默认错误视图足以满足99％的Web应用程序的要求，但是您也可以对其进行 自定义。
