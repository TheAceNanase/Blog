# Django错误报告

错误报告

在运行公共站点时，应始终关闭该 DEBUG设置。这将使您的服务器运行得更快，并且还可以防止恶意用户查看错误页面可能显示的应用程序详细信息。

但是，DEBUG将set 运行为False表示您将永远不会看到站点生成的错误-每个人都将看到您的公共错误页面。您需要跟踪部署站点中发生的错误，因此可以将Django配置为创建包含有关这些错误的详细信息的报告。

## 电子邮件报告

### 服务器错误

当DEBUGis时False，ADMINS只要您的代码引发未处理的异常并导致内部服务器错误（严格来说，对于HTTP状态代码为500或更高的任何响应），Django都会通过电子邮件发送设置中列出的用户 。这会给管理员立即通知任何错误。该ADMINS会得到错误的描述，一个完整的Python追踪，以及有关导致错误的HTTP请求的详细信息。

**注意:** 为了发送电子邮件，Django需要一些设置来告诉它如何连接到您的邮件服务器。至少，您需要指定，EMAIL_HOST并且可能 需要EMAIL_HOST_USER和EMAIL_HOST_PASSWORD，尽管根据您的邮件服务器的配置，可能还需要其他设置。有关与电子邮件相关的设置的完整列表，请参阅Django设置文档。

默认情况下，Django将通过root @ localhost发送电子邮件。但是，某些邮件提供商拒绝来自该地址的所有电子邮件。要使用其他发件人地址，请修改SERVER_EMAIL设置。

要激活此行为，请将收件人的电子邮件地址放入 ADMINS设置中。

也可以看看

服务器错误电子邮件是使用日志记录框架发送的，因此您可以通过自定义日志记录配置来自定义此行为。

### 404错误

Django也可以配置为通过电子邮件发送有关断开链接的错误（404“找不到页面”错误）。Django在以下情况下发送有关404错误的电子邮件：

- DEBUG是False;
- 您的MIDDLEWARE设置包括 django.middleware.common.BrokenLinkEmailsMiddleware。

如果满足这些条件，则MANAGERS只要您的代码引发404并且请求具有引荐来源，Django就会通过电子邮件将设置中列出的用户发送给 您。不用电子邮件发送没有推荐人的404，这些人通常是输入损坏的URL或损坏的Web bot的人。当引用者等于请求的URL时，它也会忽略404，因为此行为也来自损坏的Web机器人。

**注意:** BrokenLinkEmailsMiddleware必须出现在拦截404错误的其他中间件之前，例如 LocaleMiddleware或 FlatpageFallbackMiddleware。将其放在MIDDLEWARE设置的顶部。

您可以通过调整IGNORABLE_404_URLS设置来告诉Django停止报告特定的404 。它应该是已编译的正则表达式对象的列表。例如：

```
import re
IGNORABLE_404_URLS = [
    re.compile(r'\.(php|cgi)$'),
    re.compile(r'^/phpmyadmin/'),
]
```

在此示例中，任何以.php或结尾的URL的404 .cgi都不会被报告。以开头的网址也不会/phpmyadmin/。

以下示例显示了如何排除浏览器和搜寻器经常请求的一些常规URL：

```
import re
IGNORABLE_404_URLS = [
    re.compile(r'^/apple-touch-icon.*\.png$'),
    re.compile(r'^/favicon\.ico$'),
    re.compile(r'^/robots\.txt$'),
]
```

（请注意，这些是正则表达式，因此我们在句点前加了一个反斜杠以将其转义。）

如果您想django.middleware.common.BrokenLinkEmailsMiddleware进一步自定义行为 （例如忽略来自Web爬网程序的请求），则应将其子类化并覆盖其方法。

也可以看看

使用日志记录框架记录404错误。默认情况下，这些日志记录将被忽略，但是您可以通过编写处理程序并适当配置日志记录来将它们用于错误报告。

## 过滤错误报告

**警告：**筛选敏感数据是一个难题，几乎不可能保证敏感数据不会泄漏到错误报告中。因此，错误报告仅应提供给受信任的团队成员，并且您应避免通过Internet（例如通过电子邮件）传输未经加密的错误报告。

### 过滤敏感信息

错误报告对于调试错误确实很有帮助，因此通常记录尽可能多的有关这些错误的相关信息非常有用。例如，默认情况下，Django会记录引发的异常的完整追溯，每个追溯框架的局部变量以及 HttpRequest的属性。

但是，有时某些类型的信息可能过于敏感，因此可能不适用于跟踪例如用户的密码或信用卡号。因此，除了按照DEBUG文档中所述过滤掉似乎敏感的设置之外，Django还提供了一组函数装饰器，以帮助您控制应从生产环境中的错误报告中过滤掉哪些信息（即在何处 DEBUG设置为False）：sensitive_variables()和 sensitive_post_parameters()。

- `sensitive_variables`（**变量*）

    如果代码中的函数（视图或任何常规回调）使用易于包含敏感信息的局部变量，则可以使用`sensitive_variables`装饰器防止这些变量的值包含在错误报告中：`from django.views.decorators.debug import sensitive_variables @sensitive_variables('user', 'pw', 'cc') def process_info(user):    pw = user.pass_word    cc = user.credit_card_number    name = user.name    ... `在上述例子中，对于该值`user`，`pw`和`cc` 变量将被隐藏，并用星（替换`**********`）错误的报告，而值`name`变量将被公开。要从错误日志中系统地隐藏函数的所有局部变量，请不要向`sensitive_variables`装饰器提供任何参数：`@sensitive_variables() def my_function():    ... `使用多个装饰器时如果要隐藏的变量也是函数参数（例如`user`，在下面的示例中为“ ”），并且如果修饰的函数具有多个修饰符，则请确保将其放置`@sensitive_variables` 在修饰符链的顶部。这样，当它通过其他装饰器传递时，它还将隐藏函数参数：`@sensitive_variables('user', 'pw', 'cc') @some_decorator @another_decorator def process_info(user):    ... `

- `sensitive_post_parameters`（** parameters*）

    如果您的一个视图接收到一个易受敏感信息影响的[`HttpRequest`](https://docs.djangoproject.com/en/3.0/ref/request-response/#django.http.HttpRequest)对象，则可以使用装饰器阻止这些参数的值包含在错误报告中 ：[`POST parameters`](https://docs.djangoproject.com/en/3.0/ref/request-response/#django.http.HttpRequest.POST)`sensitive_post_parameters``from django.views.decorators.debug import sensitive_post_parameters @sensitive_post_parameters('pass_word', 'credit_card_number') def record_user_profile(request):    UserProfile.create(        user=request.user,        password=request.POST['pass_word'],        credit_card=request.POST['credit_card_number'],        name=request.POST['name'],    )    ... `在上面的示例中，错误报告内的请求表示中，`pass_word`和 `credit_card_number`参数的值将被隐藏并用星号（`**********`）替换，而该`name`参数的值将被公开。要在错误报告中系统地隐藏请求的所有POST参数，请不要向`sensitive_post_parameters`装饰器提供任何参数：`@sensitive_post_parameters() def my_view(request):    ... `所有POST参数被系统过滤的某些错误报告出来[`django.contrib.auth.views`](https://docs.djangoproject.com/en/3.0/topics/auth/default/#module-django.contrib.auth.views)次（`login`， `password_reset_confirm`，`password_change`，和`add_view`和 `user_change_password`在`auth`管理员），以防止敏感信息泄漏的诸如用户密码。

### 自定义错误报告

All sensitive_variables()和sensitive_post_parameters()do分别用敏感变量的名称注释修饰的函数，并HttpRequest用敏感的POST参数的名称注释对象，以便以后在发生错误时可以从报告中过滤掉此敏感信息。实际的过滤是由Django的默认错误报告过滤器完成的 django.views.debug.SafeExceptionReporterFilter。**********生成错误报告时，此过滤器使用修饰符的注释将相应的值替换为star（）。如果您希望为整个网站覆盖或自定义此默认行为，则需要定义自己的过滤器类，并告诉Django通过以下DEFAULT_EXCEPTION_REPORTER_FILTER设置使用它 ：

```
DEFAULT_EXCEPTION_REPORTER_FILTER = 'path.to.your.CustomExceptionReporterFilter'
```

您还可以通过设置HttpRequest的exception_reporter_filter 属性，以更精细的方式控制要在任何给定视图中使用的过滤器：

```
def my_view(request):
    if request.user.is_authenticated:
        request.exception_reporter_filter = CustomExceptionReporterFilter()
    ...
```

您的自定义过滤器类需要继承 django.views.debug.SafeExceptionReporterFilter并可以覆盖以下方法：

- *类*`SafeExceptionReporterFilter`

    

- `SafeExceptionReporterFilter.``is_active`（*请求*）

    返回`True`以激活其他方法中操作的过滤。默认情况下，如果[`DEBUG`](https://docs.djangoproject.com/en/3.0/ref/settings/#std:setting-DEBUG)为，则过滤器处于活动状态`False`。

- `SafeExceptionReporterFilter.``get_post_parameters`（*请求*）

    返回过滤后的POST参数字典。默认情况下，它将敏感参数的值替换为星号（`**********`）。

- `SafeExceptionReporterFilter.``get_traceback_frame_variables`（*request*，*tb_frame*）

    返回给定回溯帧的局部变量的过滤字典。默认情况下，它将敏感变量的值替换为star（`**********`）。

也可以看看

您还可以通过编写自定义的异常中间件来设置自定义错误报告 。如果您确实编写了自定义错误处理，则最好模拟Django的内置错误处理，如果[DEBUG](https://docs.djangoproject.com/en/3.0/ref/settings/#std:setting-DEBUG)是，则仅报告/记录错误False。
