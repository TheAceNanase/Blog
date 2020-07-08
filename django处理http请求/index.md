# Django处理HTTP请求

##### URL调度 

干净，优雅的URL方案是高质量Web应用程序中的重要细节。Django允许您根据需要设计URL，而无框架限制。

万维网创建者蒂姆·伯纳斯-李（Tim Berners-Lee）的文章“ Cool URIs not not change”中有关为什么URL应该干净和可用的出色论据，请参见。

## 概述

要设计应用程序的URL，您可以创建一个非正式地称为URLconf（URL配置）的Python模块 。该模块是纯Python代码，并且是URL路径表达式到Python函数（您的视图）之间的映射。

该映射可以根据需要短或长。它可以引用其他映射。而且，由于它是纯Python代码，因此可以动态构建。

Django还提供了一种根据活动语言翻译URL的方法。有关更多信息，请参见[国际化文档](https://docs.djangoproject.com/en/3.0/topics/i18n/translation/#url-internationalization)。

## Django是如何处理一个请求

当用户从您的Django支持的网站请求页面时，系统将使用以下算法来确定要执行的Python代码：

1. Django确定要使用的根URLconf模块。通常，这是ROOT_URLCONF设置的值，但是如果传入 HttpRequest对象具有urlconf 属性（由中间件设置），则将使用其值代替 ROOT_URLCONF设置。
2. Django加载该Python模块并查找变量 urlpatterns。这应该是一个序列的 django.urls.path()和/或django.urls.re_path()实例。
3. Django按顺序遍历每个URL模式，并在第一个与请求的URL匹配的URL处停止，与匹配 path_info。
4. 一旦其中一个URL模式匹配，Django就会导入并调用给定的视图，该视图是Python函数（或基于类的视图）。该视图将传递以下参数：的实例HttpRequest。如果匹配的URL模式不包含命名组，则来自正则表达式的匹配项将作为位置参数提供。关键字参数由提供的路径表达式匹配的任何命名部分组成，这些名称部分由或 的可选kwargs参数中指定的任何参数覆盖。django.urls.path()django.urls.re_path()在Django  3.0中进行了更改：在旧版本中，带有None值的关键字参数也由未提供的命名部分组成。
5. 如果没有URL模式匹配，或者在此过程中的任何时候引发异常，Django都会调用一个适当的错误处理视图。请参阅下面的错误处理。

## 例子

这是一个示例URLconf：

```
from django.urls import path

from . import views

urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    path('articles/<int:year>/', views.year_archive),
    path('articles/<int:year>/<int:month>/', views.month_archive),
    path('articles/<int:year>/<int:month>/<slug:slug>/', views.article_detail),
]
```

笔记：

- 要从URL捕获值，请使用尖括号。
- 捕获的值可以选择包括转换器类型。例如，用于 `捕获整数参数。如果不包括转换器/`，则匹配除字符以外的任何字符串。
- 无需添加斜杠，因为每个URL都有该斜杠。例如articles，不是/articles。

请求示例：

- 请求/articles/2005/03/匹配列表中的第三个条目。Django将调用该函数 。views.month_archive(request, year=2005, month=3)
- /articles/2003/会匹配列表中的第一个模式，而不是第二个，因为这些模式是按顺序测试的，而第一个是第一个通过的测试。随意利用命令来插入类似这样的特殊情况。在这里，Django将调用该函数 views.special_case_2003(request)
- /articles/2003 不会与任何这些模式匹配，因为每种模式都要求URL以斜杠结尾。
- /articles/2003/03/building-a-django-site/将匹配最终模式。Django将调用该函数 。views.article_detail(request, year=2003, month=3,  slug="building-a-django-site")

## 路径转换器

默认情况下，以下路径转换器可用：

- str-匹配任何非空字符串，但路径分隔符除外'/'。如果表达式中不包含转换器，则为默认设置。
- int-匹配零或任何正整数。返回int。
- slug-匹配由ASCII字母或数字以及连字符和下划线字符组成的任何条形字符串。例如， building-your-1st-django-site。
- uuid-匹配格式化的UUID。为防止多个URL映射到同一页面，必须包含破折号，并且字母必须小写。例如，075194d3-6885-417e-a8a8-6c931e272f00。返回一个 UUID实例。
- path-匹配任何非空字符串，包括路径分隔符 '/'。这样，您就可以匹配完整的URL路径，而不是像一样匹配URL路径的一部分str。

## 注册自定义路径转换器

对于更复杂的匹配要求，您可以定义自己的路径转换器。

转换器是包含以下内容的类：

- 一regex类属性，作为一个字符串。
- 一种方法，用于将匹配的字符串转换为应传递给视图函数的类型。如果无法转换给定值，则应加注。A 被解释为不匹配，结果404响应会发送给用户，除非另一个URL模式匹配。to_python(self, value)``ValueError``ValueError
- 一种方法，用于将Python类型转换为要在URL中使用的字符串。to_url(self, value)

例如：

```
class FourDigitYearConverter:
    regex = '[0-9]{4}'

    def to_python(self, value):
        return int(value)

    def to_url(self, value):
        return '%04d' % value
```

使用[register_converter()](https://docs.djangoproject.com/en/3.0/ref/urls/#django.urls.register_converter)以下命令在URLconf中注册自定义转换器类 ：

```
from django.urls import path, register_converter

from . import converters, views

register_converter(converters.FourDigitYearConverter, 'yyyy')

urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    path('articles/<yyyy:year>/', views.year_archive),
    ...
]
```

## 使用正则表达式

如果路径和转换器语法不足以定义URL模式，则还可以使用正则表达式。为此，请使用 **re_path()**代替**path()**。

在Python正则表达式中，命名正则表达式组的语法为(?Ppattern)，其中name是组的名称，并且 pattern是匹配的某种模式。

这是前面的示例URLconf，使用正则表达式重写：

```
from django.urls import path, re_path

from . import views

urlpatterns = [
    path('articles/2003/', views.special_case_2003),
    re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/$', views.month_archive),
    re_path(r'^articles/(?P<year>[0-9]{4})/(?P<month>[0-9]{2})/(?P<slug>[\w-]+)/$', views.article_detail),
]
```

这可以完成与上一个示例大致相同的操作，除了：

- 将要匹配的确切URL受到更多限制。例如，年份10000将不再匹配，因为年份整数被限制为正好是四位数长。
- 无论正则表达式进行哪种匹配，每个捕获的参数都将作为字符串发送到视图。

当从使用切换为使用path()， re_path()反之亦然时，特别重要的是要注意视图参数的类型可能会更改，因此您可能需要调整视图。

### 使用未命名的正则表达式组

除了命名组语法（例如）之外(?P[0-9]{4})，您还可以使用较短的未命名组（例如）([0-9]{4})。

不建议特别使用此用法，因为这样可以更轻松地在匹配的预期含义和视图的参数之间意外引入错误。

无论哪种情况，建议在给定的正则表达式中仅使用一种样式。当两种样式混合使用时，任何未命名的组都会被忽略，只有命名的组才会传递给视图函数。

### 嵌套参数

正则表达式允许嵌套参数，而Django会解析它们并将其传递给视图。反转时，Django将尝试填写所有外部捕获的参数，而忽略任何嵌套的捕获参数。考虑以下URL模式，这些URL模式可以选择采用page参数：

```
from django.urls import re_path

urlpatterns = [
    re_path(r'^blog/(page-(\d+)/)?$', blog_articles),                  # bad
    re_path(r'^comments/(?:page-(?P<page_number>\d+)/)?$', comments),  # good
]
```

两种模式都使用嵌套参数，并将解析：例如， blog/page-2/将导致与匹配blog_articles两个位置参数：page-2/和2。的第二个模式  comments将comments/page-2/与关键字参数 page_number设置为2  匹配。在这种情况下，外部参数是一个非捕获参数(?:...)。

该blog_articles视图需要最外层捕获的参数被反转，page-2/或者在这种情况下不需要参数，而视图 comments可以不带参数也没有值而被反转page_number。

嵌套的捕获参数在视图参数和URL之间建立了牢固的耦合，如下所示blog_articles：视图接收部分URL（page-2/），而不是仅接收视图感兴趣的值。这种反转在反转时更为明显，因为反转视图，我们需要传递该URL而不是页码。

根据经验，当正则表达式需要参数但视图将其忽略时，仅捕获视图需要使用的值，并使用非捕获参数。

## URLconf搜索的内容

URLconf按照正常的Python字符串搜索请求的URL。这不包括GET或POST参数或域名。

例如，在对的请求中https://www.example.com/myapp/，URLconf将寻找myapp/。

在请求中https://www.example.com/myapp/?page=3，URLconf将寻找myapp/。

URLconf不会查看请求方法。换句话说，所有的请求方法- ，，POST 等-将被路由到相同的URL相同的功能。GET``HEAD

## 为视图参数指定默认值

一个方便的技巧是为视图的参数指定默认参数。这是一个示例URLconf和视图：

```
# URLconf
from django.urls import path

from . import views

urlpatterns = [
    path('blog/', views.page),
    path('blog/page<int:num>/', views.page),
]

# View (in blog/views.py)
def page(request, num=1):
    # Output the appropriate page of blog entries, according to num.
    ...
```

在上面的示例中，两个URL模式都指向同一视图– views.page–但是第一个模式未从URL中捕获任何内容。如果第一个模式匹配，该page()函数将使用它的默认参数num，1。如果第二个模式匹配， page()将使用num捕获的任何值。

## 性能

中的每个正则表达式urlpatterns都是在首次访问时进行编译。这使系统运行起来非常快。

## 在语法urlpatterns变量

urlpatterns应该是一个序列的path() 和/或re_path()实例。

## 错误处理

当Django无法找到所请求URL的匹配项或引发异常时，Django会调用错误处理视图。

这些情况下使用的视图由四个变量指定。它们的默认值足以满足大多数项目的需要，但可以通过覆盖其默认值来进行进一步的自定义。

有关完整的详细信息，请参见有关自定义错误视图的文档。

可以在您的根URLconf中设置这些值。在任何其他URLconf中设置这些变量将无效。

值必须是可调用的，或者是表示视图的完整Python导入路径的字符串，应该调用该视图来处理当前的错误情况。

变量是：

- handler400–请参阅[django.conf.urls.handler400](https://docs.djangoproject.com/en/3.0/ref/urls/#django.conf.urls.handler400)。
- handler403–请参阅[django.conf.urls.handler403](https://docs.djangoproject.com/en/3.0/ref/urls/#django.conf.urls.handler403)。
- handler404–请参阅[django.conf.urls.handler404](https://docs.djangoproject.com/en/3.0/ref/urls/#django.conf.urls.handler404)。
- handler500–请参阅[django.conf.urls.handler500](https://docs.djangoproject.com/en/3.0/ref/urls/#django.conf.urls.handler500)。

## 包括其他的URLconf 

在任何时候，您urlpatterns都可以“包括”其他URLconf模块。实质上，这会将“ URL”“植根”在其他URL之下。

例如，这是Django网站 本身的URLconf的摘录。它包括许多其他URLconf：

```
from django.urls import include, path

urlpatterns = [
    # ... snip ...
    path('community/', include('aggregator.urls')),
    path('contact/', include('contact.urls')),
    # ... snip ...
]
```

每当Django遇到时include()，它都会截断直到该时间点匹配的URL的任何部分，并将剩余的字符串发送到包含的URLconf中以进行进一步处理。

另一种可能性是通过使用path()实例列表包括其他URL模式 。例如，考虑以下URLconf：

```
from django.urls import include, path

from apps.main import views as main_views
from credit import views as credit_views

extra_patterns = [
    path('reports/', credit_views.report),
    path('reports/<int:id>/', credit_views.report),
    path('charge/', credit_views.charge),
]

urlpatterns = [
    path('', main_views.homepage),
    path('help/', include('apps.help.urls')),
    path('credit/', include(extra_patterns)),
]
```

在此示例中，/credit/reports/URL将由credit_views.report()Django视图处理 。

这可用于从URLconf中删除重复使用单个模式前缀的冗余。例如，考虑以下URLconf：

```
from django.urls import path
from . import views

urlpatterns = [
    path('<page_slug>-<page_id>/history/', views.history),
    path('<page_slug>-<page_id>/edit/', views.edit),
    path('<page_slug>-<page_id>/discuss/', views.discuss),
    path('<page_slug>-<page_id>/permissions/', views.permissions),
]
```

我们可以通过只声明一次公共路径前缀并对不同的后缀进行分组来改善这一点：

```
from django.urls import include, path
from . import views

urlpatterns = [
    path('<page_slug>-<page_id>/', include([
        path('history/', views.history),
        path('edit/', views.edit),
        path('discuss/', views.discuss),
        path('permissions/', views.permissions),
    ])),
]
```

### 捕捉的参数

包含的URLconf从父URLconfs接收任何捕获的参数，因此以下示例有效：

```
# In settings/urls/main.py
from django.urls import include, path

urlpatterns = [
    path('<username>/blog/', include('foo.urls.blog')),
]

# In foo/urls/blog.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.blog.index),
    path('archive/', views.blog.archive),
]
```

在上面的示例中，捕获的"username"变量按预期传递给包含的URLconf。

## 传递额外的选项来查看函数

URLconfs有一个钩子，可让您将额外的参数作为Python字典传递给视图函数。

该[path()](https://docs.djangoproject.com/en/3.0/ref/urls/#django.urls.path)函数可以使用可选的第三个参数，该参数应该是传递给view函数的额外关键字参数的字典。

例如：

```
from django.urls import path
from . import views

urlpatterns = [
    path('blog/<int:year>/', views.year_archive, {'foo': 'bar'}),
]
```

在此示例中，对于的请求/blog/2005/，Django将调用 。views.year_archive(request, year=2005, foo='bar')

该技术在 联合框架中用于将元数据和选项传递给视图。

处理冲突

URL模式可能会捕获命名的关键字参数，并在其额外参数字典中传递具有相同名称的参数。发生这种情况时，将使用字典中的参数代替URL中捕获的参数。

### 将额外的选项传递给include()

同样，您可以将额外选项传递给include()，所包含的URLconf中的每一行都将传递额外选项。

例如，这两个URLconf集在功能上是相同的：

设置一：

```
# main.py
from django.urls import include, path

urlpatterns = [
    path('blog/', include('inner'), {'blog_id': 3}),
]

# inner.py
from django.urls import path
from mysite import views

urlpatterns = [
    path('archive/', views.archive),
    path('about/', views.about),
]
```

设置二：

```
# main.py
from django.urls import include, path
from mysite import views

urlpatterns = [
    path('blog/', include('inner')),
]

# inner.py
from django.urls import path

urlpatterns = [
    path('archive/', views.archive, {'blog_id': 3}),
    path('about/', views.about, {'blog_id': 3}),
]
```

请注意，无论行的视图是否实际接受这些选项，额外的选项将始终传递到所包含的URLconf中的每一行。因此，仅当您确定所包含的URLconf中的每个视图都接受要传递的额外选项时，此技术才有用。

## URL的反向解析

在Django项目上进行工作时，通常需要获取最终形式的URL，以嵌入生成的内容（视图和资产URL，向用户显示的URL等）或在服务器上处理导航流程侧面（重定向等）

强烈希望避免对这些URL进行硬编码（一种费力，不可扩展且易于出错的策略）。同样危险的是，设计临时机制来生成与URLconf描述的设计平行的URL，这可能导致URL的生成随着时间的推移而变得陈旧。

换句话说，需要一种DRY机制。除其他优点外，它还允许URL设计的发展，而不必遍历所有项目源代码来搜索和替换过时的URL。

我们可以获得URL的主要信息是负责处理它的视图的标识（例如名称）。视图参数的类型（位置，关键字）和值还必须包含在正确的URL查找中的其他信息。

Django提供了一个解决方案，使得URL映射器是URL设计的唯一存储库。您将其与URLconf一起提供，然后可以在两个方向上使用它：

- 从用户/浏览器请求的URL开始，它将调用正确的Django视图，以提供可能需要的任何参数以及从URL中提取的值。
- 从标识相应的Django视图以及将传递给该视图的参数值开始，获取关联的URL。

第一个是我们在上一节中讨论的用法。第二种是所谓的URL反向解析，反向URL匹配，反向URL查找或简称URL反向。

Django提供了执行URL反转的工具，这些工具与需要URL的不同层相匹配：

- 在模板中：使用url模板标记。
- 在Python代码中：使用reverse()函数。
- 与Django模型实例的URL处理相关的高级代码：get_absolute_url()方法。

### 例子

再次考虑以下URLconf条目：

```
from django.urls import path

from . import views

urlpatterns = [
    #...
    path('articles/<int:year>/', views.year_archive, name='news-year-archive'),
    #...
]
```

根据这种设计，对应于年度归档文件的URL NNNN 是/articles//。

可以使用以下模板代码获取它们：


或在Python代码中：

```
from django.http import HttpResponseRedirect
from django.urls import reverse

def redirect_to_year(request):
    # ...
    year = 2006
    # ...
    return HttpResponseRedirect(reverse('news-year-archive', args=(year,)))
```

如果出于某种原因决定更改发布年度文章存档内容的URL，则只需要更改URLconf中的条目即可。

在视图具有一般性质的某些情况下，URL和视图之间可能存在多对一关系。对于这些情况，在反向URL时，视图名称并不是一个足够好的标识符。阅读下一节以了解Django为此提供的解决方案。

## 命名URL模式

为了执行URL反向，您需要 像上面的示例一样使用命名的URL模式。URL名称使用的字符串可以包含您喜欢的任何字符。您不限于有效的Python名称。

在命名URL模式时，请选择不太可能与其他应用程序的名称冲突的名称。如果调用URL模式，comment 而另一个应用程序执行相同的操作，则reverse()找到的URL 取决于项目urlpatterns列表中最后一个模式。

在您的URL名称上添加前缀（可能源自应用程序名称（例如myapp-comment而不是comment）），可以减少发生冲突的机会。

如果要覆盖视图，可以故意选择与另一个应用程序相同的URL名称。例如，一个常见的用例是覆盖 LoginView。Django和大多数第三方应用程序的某些部分假定此视图具有名称为的URL模式  login。如果你有一个自定义登录查看，并给它的URL的名字login， reverse()会发现自定义视图，只要它在  urlpatterns以后django.contrib.auth.urls包括（如果这是包含在所有）。

如果多个URL模式的参数不同，也可以使用相同的名称。除URL名称外，还要reverse() 匹配参数数量和关键字参数的名称。

## URL命名空间

### 简介

URL名称空间允许您唯一地反向命名URL模式，即使不同的应用程序使用相同的URL名称。对于第三方应用程序，始终使用命名空间的URL是一个好习惯（就像我们在本教程中所做的那样）。同样，如果部署了一个应用程序的多个实例，它还允许您反向URL。换句话说，由于单个应用程序的多个实例将共享命名URL，因此名称空间提供了一种区分这些命名URL的方法。

对于特定站点，可以多次使用正确使用URL名称空间的Django应用程序。例如，django.contrib.admin 有一AdminSite类允许您  部署多个admin实例。在下一个示例中，我们将讨论从教程在两个不同位置部署民意调查应用程序的想法，以便我们可以为两个不同的受众（作者和发布者）提供相同的功能。

URL名称空间分为两部分，都是字符串：

- 应用程序名称空间这描述了正在部署的应用程序的名称。单个应用程序的每个实例将具有相同的应用程序名称空间。例如，Django的admin应用程序具有可预测的应用程序名称空间'admin'。
- 实例名称空间这标识了应用程序的特定实例。实例名称空间在整个项目中应该是唯一的。但是，实例名称空间可以与应用程序名称空间相同。这用于指定应用程序的默认实例。例如，默认Django管理实例的实例名称空间为'admin'。

使用':'操作符指定以名称分隔的URL 。例如，使用引用管理应用程序的主索引页面'admin:index'。这表示的命名空间'admin'，以及的命名URL 'index'。

命名空间也可以嵌套。命名的URL 'sports:polls:index'将寻找'index'在命名空间中命名的模式，该模式'polls'本身是在顶级命名空间中定义的'sports'。

### 反向命名空间的URL

给定'polls:index'要解析的命名空间URL（例如）后，Django会将完全限定的名称拆分为多个部分，然后尝试以下查找：

1. 首先，Django寻找匹配的应用程序名称空间（在本示例中为'polls'）。这将产生该应用程序实例的列表。
2. 如果定义了当前应用程序，则Django查找并返回该实例的URL解析器。可以使用 函数的current_app参数指定当前应用程序reverse()。该url模板标签使用当前解决视图在当前应用程序的命名空间  RequestContext。您可以通过在request.current_app属性上设置当前应用程序来覆盖此默认设置。
3. 如果没有当前应用程序，则Django将查找默认应用程序实例。默认的应用程序实例是具有该实例实例命名空间匹配应用的命名空间（在此示例中，的一个实例polls称为'polls'）。
4. 如果没有默认应用程序实例，则Django将选择该应用程序的最后部署实例，无论其实例名称是什么。
5. 如果在步骤1中提供的名称空间与应用程序名称空间不匹配，Django将尝试直接查找该名称空间作为 实例名称空间。

如果存在嵌套的名称空间，则对名称空间的每个部分重复这些步骤，直到仅解析视图名称为止。然后，将视图名称解析为找到的名称空间中的URL。

#### 例子

为了展示该解决方案的实际作用，请考虑polls本教程中应用程序的两个实例的示例：一个称为'author-polls' ，一个称为'publisher-polls'。假设我们已经增强了该应用程序，以便在创建和显示民意测验时考虑实例名称空间。

的urls.py 

```
from django.urls import include, path

urlpatterns = [
    path('author-polls/', include('polls.urls', namespace='author-polls')),
    path('publisher-polls/', include('polls.urls', namespace='publisher-polls')),
]
```

民调/的urls.py 

```
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail'),
    ...
]
```

使用此设置，可以进行以下查找：



### URL命名空间和包含的URLconf 

可以通过两种方式指定包含的URLconf的应用程序名称空间。

首先，您可以app_name在包含的URLconf模块中设置与该urlpatterns属性相同级别的属性。您必须将实际模块或对该模块的字符串引用传递给include()，而不是其urlpatterns自身的列表。

民调/的urls.py 

```
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail'),
    ...
]
```

的urls.py 

```
from django.urls import include, path

urlpatterns = [
    
    path('polls/', include('polls.urls')),
]
```

中定义的URL polls.urls将具有一个应用程序名称空间polls。

其次，您可以包括一个包含嵌入式名称空间数据的对象。如果您include()列出[path()](https://docs.djangoproject.com/en/3.0/ref/urls/#django.urls.path)或 [re_path()](https://docs.djangoproject.com/en/3.0/ref/urls/#django.urls.re_path)实例，则该对象中包含的URL将被添加到全局名称空间中。
例如：

```
from django.urls import include, path

from . import views

polls_patterns = ([
    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail'),
], 'polls')

urlpatterns = [
    path('polls/', include(polls_patterns)),
]
```

这会将提名的URL模式包括到给定的应用程序名称空间中。

可以使用的namespace参数 指定实例名称空间include()。如果未指定实例名称空间，它将默认为包含的URLconf的应用程序名称空间。这意味着它将也是该名称空间的默认实例。
