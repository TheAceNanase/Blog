# Django的缓存框架

动态网站的基本权衡是动态的。每次用户请求页面时，Web服务器都会进行各种计算-从数据库查询到模板呈现再到业务逻辑-创建站点访问者可以看到的页面。从处理开销的角度来看，这比标准的从文件中读取文件的服务器系统要贵得多。

对于大多数Web应用程序而言，此开销并不大。大多数Web应用程序不是washingtonpost.com或slashdot.org; 它们是流量中等的中小型网站。但是对于中到高流量的站点，必须尽可能减少开销。

那就是缓存的来源。

缓存某些内容是为了保存昂贵的计算结果，因此您下次不必执行计算。以下是一些伪代码，用于说明如何将其应用于动态生成的网页：

```
given a URL, try finding that page in the cache
if the page is in the cache:
    return the cached page
else:
    generate the page
    save the generated page in the cache (for next time)
    return the generated page
```

Django带有一个健壮的缓存系统，可让您保存动态页面，因此不必为每个请求都计算它们。为了方便起见，Django提供了不同级别的缓存粒度：您可以缓存特定视图的输出，可以仅缓存难以生成的片段，或者可以缓存整个站点。

Django还可以与“下游”缓存（例如Squid和基于浏览器的缓存）配合使用。这些是您不直接控制的缓存类型，但是您可以向它们提供提示（通过HTTP标头）有关站点的哪些部分以及应该如何缓存的提示。

也可以看看

该缓存框架的设计理念， 解释了一些框架的设计决策。

## 设置缓存

缓存系统需要少量设置。即，您必须告诉它缓存的数据应该存放在哪里–无论是在数据库中，在文件系统上还是直接在内存中。这是一个影响缓存性能的重要决定。是的，某些缓存类型比其他类型更快。

您的缓存首选项进入CACHES设置文件中的设置。以下是的所有可用值的说明 CACHES。

### Memcached

Memcached是Django原生支持的最快，最高效的缓存类型， 是一种完全基于内存的缓存服务器，最初是为处理LiveJournal.com上的高负载而开发的，随后由Danga Interactive开源。Facebook和Wikipedia等网站使用它来减少数据库访问并显着提高网站性能。

Memcached作为守护程序运行，并分配了指定数量的RAM。它所做的只是提供一个用于添加，检索和删除缓存中数据的快速接口。所有数据都直接存储在内存中，因此没有数据库或文件系统使用的开销。

本身安装Memcached后，您需要安装Memcached绑定。有几种可用的Python Memcached绑定。两种最常见的是python-memcached和pylibmc。

要将Memcached与Django结合使用，请执行以下操作：

- 设置BACKEND为 django.core.cache.backends.memcached.MemcachedCache或 django.core.cache.backends.memcached.PyLibMCCache（取决于您选择的内存缓存绑定）
- 设置LOCATION为ip:port值，其中ip是Memcached守护程序的IP地址，port是运行Memcached的端口，或者设置为unix:path值，其中 path是Memcached Unix套接字文件的路径。

在此示例中，Memcached使用python-memcached绑定在本地主机（127.0.0.1）端口11211上运行：

```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        'LOCATION': '127.0.0.1:11211',
    }
}
```

在此示例中，可以/tmp/memcached.sock使用python-memcached绑定通过本地Unix套接字文件使用Memcached ：

```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        'LOCATION': 'unix:/tmp/memcached.sock',
    }
}
```

使用pylibmc绑定时，请勿包括unix:/前缀：

```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.PyLibMCCache',
        'LOCATION': '/tmp/memcached.sock',
    }
}
```

Memcached的一项出色功能是能够在多个服务器上共享缓存。这意味着您可以在多台计算机上运行Memcached守护程序，并且该程序会将计算机组视为单个 缓存，而无需在每台计算机上重复缓存值。要利用此功能，请将所有服务器地址包含在中 LOCATION，以分号或逗号分隔的字符串或列表的形式。

在此示例中，缓存在IP地址为172.19.26.240和172.19.26.242且均在端口11211上运行的Memcached实例之间共享：

```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        'LOCATION': [
            '172.19.26.240:11211',
            '172.19.26.242:11211',
        ]
    }
}
```

在以下示例中，缓存在运行在IP地址172.19.26.240（端口11211），172.19.26.42（端口11212）和172.19.26.244（端口11213）上的Memcached实例上共享：

```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        'LOCATION': [
            '172.19.26.240:11211',
            '172.19.26.242:11212',
            '172.19.26.244:11213',
        ]
    }
}
```

关于Memcached的最后一点是基于内存的缓存有一个缺点：由于缓存的数据存储在内存中，因此如果服务器崩溃，数据将丢失。显然，内存不是用于永久性数据存储的，因此不要依赖基于内存的缓存作为唯一的数据存储。毫无疑问，任何 Django缓存后端都不应该用于永久存储-它们都旨在作为缓存而非存储的解决方案-但我们在此指出这一点是因为基于内存的缓存特别临时。

### 数据库高速缓存

Django可以将其缓存的数据存储在您的数据库中。如果您拥有快速索引良好的数据库服务器，则此方法效果最佳。

要将数据库表用作缓存后端：

- 设置BACKEND于 django.core.cache.backends.db.DatabaseCache
- 设置LOCATION为tablename，数据库表的名称。该名称可以是您想要的任何名称，只要它是数据库中尚未使用的有效表名即可。

在此示例中，缓存表的名称为my_cache_table：

```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.db.DatabaseCache',
        'LOCATION': 'my_cache_table',
    }
}
```

#### 创建缓存表

在使用数据库缓存之前，必须使用以下命令创建缓存表：

```
python manage.py createcachetable
```

这会在您的数据库中创建一个表，该表的格式与Django的数据库缓存系统期望的格式相同。该表的名称取自 LOCATION。

如果使用多个数据库缓存，请createcachetable为每个缓存创建一个表。

如果您使用多个数据库，请createcachetable遵循allow_migrate()数据库路由器的 方法（请参见下文）。

像一样migrate，createcachetable不会触摸现有表格。它只会创建丢失的表。

要打印将要运行的SQL，而不是运行它，请使用 选项。createcachetable --dry-run

#### 多个数据库

如果将数据库缓存与多个数据库一起使用，则还需要为数据库缓存表设置路由说明。为了进行路由，数据库高速缓存表CacheEntry在名为的应用程序中显示为名为的模型 django_cache。该模型不会出现在模型缓存中，但是可以将模型详细信息用于路由目的。

例如，以下路由器会将所有缓存读取操作定向到cache_replica，并将所有写入操作定向到 cache_primary。缓存表将仅同步到 cache_primary：

```
class CacheRouter:
    """A router to control all database cache operations"""

    def db_for_read(self, model, **hints):
        "All cache read operations go to the replica"
        if model._meta.app_label == 'django_cache':
            return 'cache_replica'
        return None

    def db_for_write(self, model, **hints):
        "All cache write operations go to primary"
        if model._meta.app_label == 'django_cache':
            return 'cache_primary'
        return None

    def allow_migrate(self, db, app_label, model_name=None, **hints):
        "Only install the cache model on primary"
        if app_label == 'django_cache':
            return db == 'cache_primary'
        return None
```

如果您没有为数据库缓存模型指定路由方向，则缓存后端将使用default数据库。

当然，如果您不使用数据库缓存后端，则无需担心为数据库缓存模型提供路由说明。

### 文件系统缓存

基于文件的后端将每个缓存值序列化并存储为单独的文件。要将此后端设置BACKEND为"django.core.cache.backends.filebased.FileBasedCache"并 设置到 LOCATION合适的目录。例如，要在中存储缓存的数据/var/tmp/django_cache，请使用以下设置：

```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
        'LOCATION': '/var/tmp/django_cache',
    }
}
```

如果您使用的是Windows，请将驱动器号放在路径的开头，如下所示：

```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
        'LOCATION': 'c:/foo/bar',
    }
}
```

目录路径应该是绝对的-也就是说，它应该从文件系统的根目录开始。是否在设置的末尾加斜杠都没关系。

确保此设置指向的目录存在，并且Web服务器在其下运行的系统用户可以读写。继续上面的示例，如果您的服务器以用户身份运行apache，请确保该目录/var/tmp/django_cache存在并且可由用户读取和写入apache。

### 本地内存缓存

如果未在设置文件中指定其他缓存，则这是默认缓存。如果您想要内存中缓存的速度优势，但又不具备运行Memcached的功能，请考虑使用本地内存缓存后端。该缓存是按进程（请参阅下文）并且是线程安全的。要使用它，请设置BACKEND为"django.core.cache.backends.locmem.LocMemCache"。例如：

```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
        'LOCATION': 'unique-snowflake',
    }
}
```

高速缓存LOCATION用于标识各个内存存储。如果只有一个locmem缓存，则可以省略 LOCATION; 但是，如果您有多个本地内存缓存，则需要至少为其分配一个名称，以使它们分开。

缓存使用最近最少使用（LRU）淘汰策略。

请注意，每个进程都有其自己的专用缓存实例，这意味着不可能进行跨进程缓存。这显然也意味着本地内存缓存不是特别有效的内存，因此对于生产环境而言，它可能不是一个好选择。这对开发很好。

### 虚拟缓存（用于开发）

最后，Django附带了一个“虚拟”缓存，该缓存实际上并没有缓存-它只是实现了缓存接口而无所事事。

如果您的生产站点在各个地方都使用了重型缓存，但是在开发/测试环境中却不想缓存并且不想将代码更改为后者的特殊情况，这将非常有用。要激活虚拟缓存，设置BACKEND如下：

```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.dummy.DummyCache',
    }
}
```

### 使用自定义缓存后端

尽管Django开箱即用地支持许多缓存后端，但有时您可能希望使用自定义的缓存后端。要使用Django的外部缓存后端，使用Python导入路径作为 BACKEND该的CACHES设置，如下所示：

```
CACHES = {
    'default': {
        'BACKEND': 'path.to.backend',
    }
}
```

如果要构建自己的后端，则可以将标准缓存后端用作参考实现。您将django/core/cache/backends/在Django源代码的目录中找到代码 。

注意：如果没有真正令人信服的理由，例如不支持它们的主机，则应坚持使用Django随附的缓存后端。他们已经过充分的测试并且有据可查。

### 缓存参数

可以为每个缓存后端提供其他参数来控制缓存行为。这些参数作为设置中的其他键提供 CACHES。有效参数如下：

- TIMEOUT：用于缓存的默认超时（以秒为单位）。此参数默认为300秒（5分钟）。您可以设置TIMEOUT为None默认情况下，缓存键永不过期。值的值0使键立即过期（有效地是“不缓存”）。
- OPTIONS：应传递到缓存后端的所有选项。有效选项的列表将随每个后端而有所不同，并且由第三方库支持的缓存后端会将其选项直接传递给基础缓存库。实现自己的扑杀战略（即缓存后端locmem，filesystem以及database后端）将履行下列选项：MAX_ENTRIES：删除旧值之前，缓存中允许的最大条目数。此参数默认为300。CULL_FREQUENCY：MAX_ENTRIES到达时被剔除的条目分数。实际比例为 ，因此设置为在达到时剔除一半条目。此参数应为整数，默认为。1 / CULL_FREQUENCYCULL_FREQUENCY2MAX_ENTRIES3值0for CULL_FREQUENCY表示MAX_ENTRIES到达时将转储整个缓存。在一些后端（database尤其是）这使得扑杀多 以更高速缓存未命中的代价更快。Memcached后端将OPTIONS as关键字参数的内容传递给客户端构造函数，从而可以更高级地控制客户端行为。有关用法示例，请参见下文。
- KEY_PREFIX：一个字符串，它将自动包含在Django服务器使用的所有缓存键中（默认为前缀）。有关更多信息，请参见缓存文档。
- VERSION：Django服务器生成的缓存键的默认版本号。有关更多信息，请参见缓存文档。
- KEY_FUNCTION 一个字符串，其中包含指向函数的虚线路径，该函数定义了如何将前缀，版本和键组成最终的缓存键。有关 更多信息，请参见[缓存文档](https://docs.djangoproject.com/en/3.0/topics/cache/#cache-key-transformation)。

在此示例中，文件系统后端的超时时间为60秒，最大容量为1000：

```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
        'LOCATION': '/var/tmp/django_cache',
        'TIMEOUT': 60,
        'OPTIONS': {
            'MAX_ENTRIES': 1000
        }
    }
}
```

这python-memcached是对象大小限制为2MB 的基于后端的示例配置：

```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        'LOCATION': '127.0.0.1:11211',
        'OPTIONS': {
            'server_max_value_length': 1024 * 1024 * 2,
        }
    }
}
```

这是pylibmc基于基础的后端的示例配置，该配置启用了二进制协议，SASL身份验证和ketama行为模式：

```
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.PyLibMCCache',
        'LOCATION': '127.0.0.1:11211',
        'OPTIONS': {
            'binary': True,
            'username': 'user',
            'password': 'pass',
            'behaviors': {
                'ketama': True,
            }
        }
    }
}
```

## 每个站点的缓存

一旦设置了缓存，使用缓存的最简单方法就是缓存整个站点。您需要将'django.middleware.cache.UpdateCacheMiddleware'和 添加 'django.middleware.cache.FetchFromCacheMiddleware'到 MIDDLEWARE设置中，如以下示例所示：

```
MIDDLEWARE = [
    'django.middleware.cache.UpdateCacheMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.cache.FetchFromCacheMiddleware',
]
```

注意

不，这不是输入错误：“更新”中间件必须位于列表的第一位，“获取”中间件必须位于最后。细节有些晦涩，但是如果您想了解完整的故事，请参阅下面的MIDDLEWARE顺序。

然后，将以下必需设置添加到Django设置文件中：

- CACHE_MIDDLEWARE_ALIAS –用于存储的缓存别名。
- CACHE_MIDDLEWARE_SECONDS –每个页面应缓存的秒数。
- CACHE_MIDDLEWARE_KEY_PREFIX–如果使用同一Django安装在多个站点之间共享缓存，请将其设置为站点名称或该Django实例唯一的其他字符串，以防止按键冲突。如果您不在乎，请使用空字符串。

FetchFromCacheMiddleware缓存状态为200的GET和HEAD响应，其中请求和响应标头允许。对具有不同查询参数的相同URL请求的响应被视为唯一页面，并分别进行缓存。该中间件期望用与相应的GET请求相同的响应头来应答HEAD请求。在这种情况下，它可以为HEAD请求返回缓存的GET响应。

此外，会UpdateCacheMiddleware自动在每个标题中设置一些标题 HttpResponse：

- 将Expires标题设置为当前日期/时间加上定义的 CACHE_MIDDLEWARE_SECONDS。
- Cache-Control再次设置，将页眉设置为页面的最长使用期限CACHE_MIDDLEWARE_SECONDS。

有关中间件的更多信息，请参见中间件。

如果视图设置了自己的缓存到期时间（即max-age，其Cache-Control标题中有一个部分），则页面将一直缓存到到期时间，而不是CACHE_MIDDLEWARE_SECONDS。使用装饰器 django.views.decorators.cache可以轻松设置视图的过期时间（使用cache_control()装饰器）或禁用视图的缓存（使用 never_cache()装饰器）。有关这些装饰器的更多信息，请参见“ 使用其他标头”部分。

如果USE_I18N设置为，True则生成的缓存键将包括活动语言的名称-另请参见 Django如何发现语言首选项。这使您可以轻松地缓存多语言站点，而不必自己创建缓存密钥。

缓存键还包括积极的语言时， USE_L10N设置为True与当前时区时USE_TZ被设置为True。

## 每视图缓存

- `django.views.decorators.cache.``cache_page`（）

    

使用缓存框架的更精细的方法是缓存单个视图的输出。django.views.decorators.cache定义一个cache_page 装饰器，该装饰器将自动为您缓存视图的响应：

```
from django.views.decorators.cache import cache_page

@cache_page(60 * 15)
def my_view(request):
    ...
```

cache_page有一个参数：缓存超时（以秒为单位）。在上面的示例中，my_view()视图结果将被缓存15分钟。（请注意，我们出于可读性的目的编写了该代码。它将被评估为– 15分钟乘以每分钟60秒。）60 * 1560 * 15900

与每个站点的缓存一样，每个视图的缓存也是从URL键入的。如果多个URL指向同一视图，则每个URL将被分别缓存。继续该my_view示例，如果您的URLconf如下所示：

```
urlpatterns = [
    path('foo/<int:code>/', my_view),
]
```

然后，您所期望的/foo/1/和的请求/foo/23/将分别进行缓存。但是，一旦/foo/23/请求了特定的URL（例如），则对该URL的后续请求将使用缓存。

cache_page也可以采用可选的关键字参数，cache该参数指示装饰器CACHES在缓存视图结果时使用特定的缓存（来自您的 设置）。默认情况下， default将使用缓存，但是您可以指定所需的任何缓存：

```
@cache_page(60 * 15, cache="special_cache")
def my_view(request):
    ...
```

您还可以基于每个视图覆盖缓存前缀。cache_page 带有一个可选的关键字参数，key_prefix其工作方式CACHE_MIDDLEWARE_KEY_PREFIX 与中间件的设置相同。可以这样使用：

```
@cache_page(60 * 15, key_prefix="site1")
def my_view(request):
    ...
```

的key_prefix和cache参数可以被同时指定。该 key_prefix参数和KEY_PREFIX 规定下，CACHES将串联。

### 在URLconf中指定每个视图的缓存

上一节中的示例已经硬编码了缓存视图的事实，因为该视图cache_page改变了my_view功能。这种方法将您的视图耦合到高速缓存系统，由于多种原因，这种方法并不理想。例如，您可能想在另一个无缓存的站点上重用视图功能，或者可能希望将视图分发给可能希望在不被缓存的情况下使用它们的人。这些问题的解决方案是在URLconf中指定每个视图的缓存，而不是在视图函数本身旁边。

您可以通过cache_page在URLconf中引用视图函数时将其包装在一起来实现。这是之前的旧版URLconf：

```
urlpatterns = [
    path('foo/<int:code>/', my_view),
]
```

这是同一件事，my_view包裹在其中cache_page：

```
from django.views.decorators.cache import cache_page

urlpatterns = [
    path('foo/<int:code>/', cache_page(60 * 15)(my_view)),
]
```

## 模板片段缓存

如果您希望获得更多控制权，则还可以使用cachetemplate标签缓存模板片段。要让您的模板访问此标签，请放在 模板顶部附近。

该模板标签缓存块中的内容给定的时间量。它至少需要两个参数：高速缓存超时（以秒为单位）和提供高速缓存片段的名称。如果超时为，则片段将永远被缓存。名称将按原样使用，请勿使用变量。



有时，您可能希望根据片段内显示的一些动态数据来缓存片段的多个副本。例如，您可能想要为站点中的每个用户提供上一个示例中使用的侧边栏的单独的缓存副本。为此，请将一个或多个其他参数（可以是带或不带过滤器的变量）传递给模板标记，以唯一地标识缓存片段


如果USE_I18N将设置为True每个站点，则中间件缓存将 尊重活动语言。对于cache模板标记，您可以使用模板中可用的特定于 翻译的变量之一来获得相同的结果：


缓存超时可以是模板变量，只要模板变量解析为整数值即可。



此功能有助于避免模板中的重复。您可以在一个位置的变量中设置超时，然后重用该值。

默认情况下，缓存标签将尝试使用名为“ template_fragments”的缓存。如果不存在这样的缓存，它将退回到使用默认缓存。您可以选择与using关键字参数一起使用的备用缓存后端，该参数必须是标记的最后一个参数。


指定未配置的缓存名称被视为错误。

- `django.core.cache.utils.``make_template_fragment_key`（*fragment_name*，*vary_on =无*）

    

如果要获取用于缓存片段的缓存密钥，可以使用 make_template_fragment_key。fragment_name与cachetemplate标签的第二个参数相同；vary_on是传递给标记的所有其他参数的列表。该功能对于使缓存项无效或覆盖很有用
## 低级缓存API

有时，缓存整个渲染的页面并不会带来太多好处，实际上，这会带来不便。

例如，也许您的站点包含一个视图，该视图的结果取决于几个昂贵的查询，这些查询的结果以不同的间隔更改。在这种情况下，使用每个站点或每个视图缓存策略提供的全页缓存并不理想，因为您不想缓存整个结果（因为某些数据经常更改），但您仍想缓存很少更改的结果。

对于此类情况，Django会公开一个低级缓存API。您可以使用此API以所需的任意粒度级别将对象存储在缓存中。您可以缓存可以安全腌制的任何Python对象：字符串，字典，模型对象列表等。（可以对大多数常见的Python对象进行腌制；有关腌制的更多信息，请参考Python文档。）

### 访问缓存

- `django.core.cache.``caches`

    您可以[`CACHES`](https://docs.djangoproject.com/en/3.0/ref/settings/#std:setting-CACHES)通过类似dict的对象访问在设置中配置的缓存：`django.core.cache.caches`。在同一线程中重复请求相同的别名将返回相同的对象。`>>> from django.core.cache import caches >>> cache1 = caches['myalias'] >>> cache2 = caches['myalias'] >>> cache1 is cache2 True `如果指定的键不存在，`InvalidCacheBackendError`将引发。为了提供线程安全，将为每个线程返回不同的缓存后端实例。

- `django.core.cache.``cache`

    作为一种快捷方式，默认高速缓存可用于 `django.core.cache.cache`：`>>> from django.core.cache import cache `此对象等效于`caches['default']`。

### 基本用法

基本界面是：

- `cache.``set`（*key*，*value*，*timeout = DEFAULT_TIMEOUT*，*version = None*）

    `>>> cache.set('my_key', 'hello, world!', 30) `

- `cache.``get`（*key*，*default = None*，*version = None*）

    `>>> cache.get('my_key') 'hello, world!' `

key应该是str，并且value可以是任何可挑选的Python对象。

该timeout参数是可选的，并且默认为设置中timeout相应后端的参数CACHES（如上所述）。这是该值应存储在缓存中的秒数。传递 Nonefor timeout将永远缓存该值。一个timeout的0 将不缓存值。

如果对象在缓存中不存在，则cache.get()返回None：

```
>>> # Wait 30 seconds for 'my_key' to expire...
>>> cache.get('my_key')
None
```

我们建议不要将文字值存储None在缓存中，因为您将无法区分存储的None值和返回值为的缓存未命中None。

cache.get()可以default争论。这指定了如果对象在缓存中不存在则返回哪个值：

```
>>> cache.get('my_key', 'has expired')
'has expired'
```

- `cache.``add`（*key*，*value*，*timeout = DEFAULT_TIMEOUT*，*version = None*）

    

若要仅在密钥尚不存在时添加密钥，请使用add()方法。它使用与相同的参数set()，但是如果指定的键已经存在，它将不会尝试更新缓存：

```
>>> cache.set('add_key', 'Initial value')
>>> cache.add('add_key', 'New value')
>>> cache.get('add_key')
'Initial value'
```

如果您需要知道是否add()在缓存中存储了值，则可以检查返回值。True如果存储了值，它将返回， False否则返回。

- `cache.``get_or_set`（*key*，*default*，*timeout = DEFAULT_TIMEOUT*，*version = None*）

    

如果要获取键的值，或者如果键不在缓存中则要设置值，则可以使用该get_or_set()方法。它使用与相同的参数，get() 但默认设置为该键的新缓存值，而不是返回：

```
>>> cache.get('my_new_key')  # returns None
>>> cache.get_or_set('my_new_key', 'my new value', 100)
'my new value'
```

您还可以将任何callable作为默认值传递：

```
>>> import datetime
>>> cache.get_or_set('some-timestamp-key', datetime.datetime.now)
datetime.datetime(2014, 12, 11, 0, 15, 49, 457920)
```

- `cache.``get_many`（*keys*，*version = None*）

    

还有一个get_many()接口只命中一次缓存。 get_many()返回一个字典，其中包含您要求的所有键，这些键实际上已存在于缓存中（并且尚未过期）：

```
>>> cache.set('a', 1)
>>> cache.set('b', 2)
>>> cache.set('c', 3)
>>> cache.get_many(['a', 'b', 'c'])
{'a': 1, 'b': 2, 'c': 3}
```

- `cache.``set_many`（*dict*，*超时*）

    

要更有效地设置多个值，请使用set_many()传递键值对字典：

```
>>> cache.set_many({'a': 1, 'b': 2, 'c': 3})
>>> cache.get_many(['a', 'b', 'c'])
{'a': 1, 'b': 2, 'c': 3}
```

像一样cache.set()，set_many()带有一个可选timeout参数。

在支持的后端（memcached）上，set_many()返回未能插入的密钥列表。

- `cache.``delete`（*key*，*version = None*）

    

您可以显式删除键，delete()以清除特定对象的缓存：

```
>>> cache.delete('a')
```

- `cache.``delete_many`（*keys*，*version = None*）

    

如果要一次清除一堆键，delete_many()可以列出要清除的键列表：

```
>>> cache.delete_many(['a', 'b', 'c'])
```

- `cache.``clear`（）

    

最后，如果要删除缓存中的所有键，请使用 cache.clear()。注意这一点。clear()将从 缓存中删除所有内容，而不仅仅是您的应用程序设置的键。

```
>>> cache.clear()
```

- `cache.``touch`（*key*，*timeout = DEFAULT_TIMEOUT*，*version = None*）

    

cache.touch()为密钥设置新的到期时间。例如，要将密钥更新为从现在起10秒钟过期：

```
>>> cache.touch('a', 10)
True
```

与其他方法一样，该timeout参数是可选的，并且默认为设置中TIMEOUT相应后端的 选项[CACHES](https://docs.djangoproject.com/en/3.0/ref/settings/#std:setting-CACHES)。

touch()True如果按键被成功触摸，False 则返回；否则返回。

- `cache.``incr`（*key*，*delta = 1*，*version = None*）

    

- `cache.``decr`（*key*，*delta = 1*，*version = None*）

    

您也可以分别使用incr()或decr()方法递增或递减已存在的键 。默认情况下，现有的高速缓存值将递增或递减1。可以通过为递增/递减调用提供参数来指定其他递增/递减值。如果您尝试增加或减少不存在的缓存键，将引发ValueError：

```
>>> cache.set('num', 1)
>>> cache.incr('num')
2
>>> cache.incr('num', 10)
12
>>> cache.decr('num')
11
>>> cache.decr('num', 5)
6
```

**注意：**incr()/ decr()方法不保证是原子的。在那些支持原子增量/减量的后端（最值得注意的是，内存缓存的后端）上，增量和减量操作将是原子的。但是，如果后端本身不提供增量/减量操作，则将使用两步检索/更新来实现。

- `cache.``close`（）

    

close()如果由缓存后端实现，则可以关闭与缓存的连接。

```
>>> cache.close()
```

**注意：**对于不实现close方法的缓存，它是无操作的。

### 缓存键前缀

如果要在服务器之间或生产环境与开发环境之间共享缓存实例，则一台服务器缓存的数据可能会被另一台服务器使用。如果服务器之间的缓存数据格式不同，则可能导致某些很难诊断的问题。

为防止这种情况，Django提供了为服务器使用的所有缓存键添加前缀的功能。保存或检索特定的缓存键后，Django将自动为缓存键添加KEY_PREFIX缓存设置的值 。

通过确保每个Django实例都有一个不同的 KEY_PREFIX，您可以确保缓存值不会发生冲突。

### 缓存版本

更改使用缓存值的运行代码时，可能需要清除所有现有的缓存值。最简单的方法是刷新整个缓存，但这会导致仍然有效且有用的缓存值丢失。

Django提供了一种更好的方法来定位各个缓存值。Django的缓存框架具有系统范围的版本标识符，该标识符使用VERSION缓存设置指定。此设置的值会自动与缓存前缀和用户提供的缓存键组合在一起，以获取最终的缓存键。

默认情况下，任何密钥请求都将自动包含站点默认的缓存密钥版本。但是，原始缓存功能都包含一个version参数，因此您可以指定要设置或获取的特定缓存键版本。例如：

```
>>> # Set version 2 of a cache key
>>> cache.set('my_key', 'hello world!', version=2)
>>> # Get the default version (assuming version=1)
>>> cache.get('my_key')
None
>>> # Get version 2 of the same key
>>> cache.get('my_key', version=2)
'hello world!'
```

可以使用incr_version()和decr_version()方法对特定键的版本进行递增和递减。这样可以使特定的键更改为新版本，而其他键不受影响。继续前面的示例：

```
>>> # Increment the version of 'my_key'
>>> cache.incr_version('my_key')
>>> # The default version still isn't available
>>> cache.get('my_key')
None
# Version 2 isn't available, either
>>> cache.get('my_key', version=2)
None
>>> # But version 3 *is* available
>>> cache.get('my_key', version=3)
'hello world!'
```

### 缓存键转换

如前两节所述，用户不能完全原样使用用户提供的缓存密钥，而是将其与缓存前缀和密钥版本结合使用以提供最终的缓存密钥。默认情况下，这三个部分使用冒号连接起来以生成最终字符串：

```
def make_key(key, key_prefix, version):
    return '%s:%s:%s' % (key_prefix, version, key)
```

如果要以不同的方式组合各部分，或对最终键进行其他处理（例如，获取键部分的哈希摘要），则可以提供自定义键功能。

的KEY_FUNCTION高速缓存设置指定匹配的原型的功能的虚线路径 make_key()的上方。如果提供，将使用此自定义按键功能代替默认的按键组合功能。

### 缓存键警告

Memcached是最常用的生产缓存后端，它不允许长度超过250个字符或包含空格或控制字符的缓存键，并且使用此类键会导致异常。为了鼓励缓存可移植的代码并最大程度地减少令人不快的意外，django.core.cache.backends.base.CacheKeyWarning如果使用的键会导致memcached错误，则其他内置的缓存后端会发出警告（）。

如果您使用的生产后端可以接受更广泛的键范围（自定义后端或非内存缓存的内置后端之一），并且希望在没有警告的情况下使用更大范围的键，则可以CacheKeyWarning在此代码中保持静音management您之一的模块 INSTALLED_APPS：

```
import warnings

from django.core.cache import CacheKeyWarning

warnings.simplefilter("ignore", CacheKeyWarning)
```

如果您想为内置后端之一提供自定义密钥验证逻辑，则可以对其进行子类化，仅覆盖validate_key 方法，并按照说明使用自定义缓存后端。例如，要为locmem后端执行此操作，请将以下代码放在模块中：

```
from django.core.cache.backends.locmem import LocMemCache

class CustomLocMemCache(LocMemCache):
    def validate_key(self, key):
        """Custom validation, raising exceptions or warnings as needed."""
        ...
```

…并在设置的BACKEND一部分中使用指向该类的虚线Python路径 CACHES。

## 下游缓存

到目前为止，本文档的重点是缓存您自己的数据。但是另一种类型的缓存也与Web开发有关：由“下游”缓存执行的缓存。这些系统甚至可以在请求到达您的网站之前为用户缓存页面。

以下是下游缓存的一些示例：

- 您的ISP可能会缓存某些页面，因此，如果您从https://example.com/请求一个页面 ，您的ISP将向您发送该页面，而无需直接访问example.com。example.com的维护者不了解这种缓存。ISP位于example.com和您的Web浏览器之间，透明地处理所有缓存。
- 您的Django网站可能位于代理缓存（例如Squid Web代理缓存（http://www.squid-cache.org/））之后，该缓存可以缓存页面以提高性能。在这种情况下，每个请求首先将由代理处理，并且仅在需要时才将其传递给您的应用程序。
- 您的Web浏览器也缓存页面。如果网页发出适当的标题，您的浏览器将使用本地缓存的副本来对该页面的后续请求，甚至无需再次联系该网页以查看其是否已更改。

下游缓存可以极大地提高效率，但是却存在危险：许多网页的内容基于身份验证和许多其他变量而有所不同，并且仅基于URL盲目保存页面的缓存系统可能会将不正确或敏感的数据暴露给后续这些页面的访问者。

例如，如果您使用Web电子邮件系统，则“收件箱”页面的内容取决于登录的用户。如果ISP盲目缓存了您的站点，则第一个通过该ISP登录的用户将拥有其用户。特定的收件箱页面已缓存，以供该站点的后续访问者使用。那不酷。

幸运的是，HTTP提供了解决此问题的方法。存在许多HTTP标头，以指示下游缓存根据指定的变量来区分其缓存内容，并告知缓存机制不要缓存特定页面。我们将在以下各节中介绍其中的一些标题。

## 使用Vary标题

的Vary报头定义哪些请求头的高速缓存机制建立其缓存键时应该考虑到。例如，如果网页的内容取决于用户的语言偏好，则称该页面“随语言而异”。

默认情况下，Django的缓存系统使用请求的标准URL（例如，）创建其缓存密钥 "https://www.example.com/stories/2005/?order_by=author"。这意味着对该URL的每个请求都将使用相同的缓存版本，而不管用户代理差异（例如Cookie或语言偏好设置）如何。但是，如果此页面根据请求标头（例如Cookie，语言或用户代理）的不同而产生不同的内容，则需要使用Vary 标头来告诉缓存机制页面输出取决于那些的东西。

要在Django中执行此操作，请使用方便的 django.views.decorators.vary.vary_on_headers()视图装饰器，如下所示：

```
from django.views.decorators.vary import vary_on_headers

@vary_on_headers('User-Agent')
def my_view(request):
    ...
```

在这种情况下，缓存机制（例如Django自己的缓存中间件）将为每个唯一的用户代理缓存页面的单独版本。

使用vary_on_headers装饰器而不是手动设置Vary标头（使用类似的东西 ）的好处是，装饰器将添加到 标头（可能已经存在），而不是从头开始设置它并可能覆盖其中的任何内容。response['Vary'] = 'user-agent'Vary

您可以将多个标头传递给vary_on_headers()：

```
@vary_on_headers('User-Agent', 'Cookie')
def my_view(request):
    ...
```

这告诉下游缓存在这两者上有所不同，这意味着用户代理和cookie的每种组合都将获得自己的缓存值。例如，具有用户代理Mozilla和cookie值foo=bar的请求将被认为不同于具有用户代理Mozilla和cookie值 的请求foo=ham。

因为在cookie上进行更改非常普遍，所以有一个 django.views.decorators.vary.vary_on_cookie()装饰器。这两个视图是等效的：

```
@vary_on_cookie
def my_view(request):
    ...

@vary_on_headers('Cookie')
def my_view(request):
    ...
```

您传递给的标头vary_on_headers不区分大小写； "User-Agent"和一样"user-agent"。

您也可以django.utils.cache.patch_vary_headers()直接使用辅助功能。此函数设置或添加到。例如：Vary header

```
from django.shortcuts import render
from django.utils.cache import patch_vary_headers

def my_view(request):
    ...
    response = render(request, 'template_name', context)
    patch_vary_headers(response, ['Cookie'])
    return response
```

patch_vary_headers将HttpResponse实例作为第一个参数，并将不区分大小写的标头名称的列表/元组作为第二个参数。

有关Vary标头的更多信息，请参见 [官方Vary规格](https://tools.ietf.org/html/rfc7231.html#section-7.1.4)。

## 控制缓存：使用其他头文件

缓存的其他问题是数据的私密性以及应在级联缓存中存储数据的位置的问题。

用户通常面临两种缓存：他们自己的浏览器缓存（私有缓存）和提供者的缓存（公共缓存）。公共缓存由多个用户使用，并由其他人控制。这就给敏感数据带来了麻烦，例如，您不希望将银行帐号存储在公共缓存中。因此，Web应用程序需要一种方法来告诉缓存哪些数据是私有数据，哪些是公共数据。

解决方案是指示页面的缓存应为“专用”。要在Django中执行此操作，请使用cache_control()视图装饰器。例：

```
from django.views.decorators.cache import cache_control

@cache_control(private=True)
def my_view(request):
    ...
```

该装饰器负责在后台发送适当的HTTP标头。

请注意，缓存控制设置“专用”和“公用”是互斥的。装饰器确保如果应设置为“ private”，则删除“  public”指令（反之亦然）。这两个伪指令的示例用法是提供私有条目和公共条目的博客网站。公共条目可以缓存在任何共享缓存中。以下代码使用 patch_cache_control()手动方式修改缓存控制标头（由cache_control()装饰器内部调用 ）：

```
from django.views.decorators.cache import patch_cache_control
from django.views.decorators.vary import vary_on_cookie

@vary_on_cookie
def list_blog_entries_view(request):
    if request.user.is_anonymous:
        response = render_only_public_entries()
        patch_cache_control(response, public=True)
    else:
        response = render_private_and_public_entries(request.user)
        patch_cache_control(response, private=True)

    return response
```

您还可以通过其他方式控制下游缓存（请参见 RFC 7234，了解有关HTTP缓存的详细信息）。例如，即使您不使用Django的服务器端缓存框架，您仍然可以使用以下命令告诉客户端将视图缓存一定的时间：最大年龄 指令：

```
from django.views.decorators.cache import cache_control

@cache_control(max_age=3600)
def my_view(request):
    ...
```

（如果你做使用缓存中间件，它已经设置max-age与值CACHE_MIDDLEWARE_SECONDS的设置。在这种情况下，自定义max_age从 cache_control()装饰将优先考虑，并且头部值将被正确地合并。）

任何有效的Cache-Control响应指令在中均有效cache_control()。这里还有更多示例：

- no_transform=True
- must_revalidate=True
- stale_while_revalidate=num_seconds

可以在IANA注册中心中找到已知指令的完整列表 （请注意，并非所有指令都适用于响应）。

如果要使用标头来完全禁用缓存，请 never_cache()使用视图装饰器来添加标头，以确保浏览器或其他缓存不会缓存响应。例：

```
from django.views.decorators.cache import never_cache

@never_cache
def myview(request):
    ...
```

## MIDDLEWARE

如果您使用缓存中间件，则将每一半放在MIDDLEWARE设置中的正确位置上很重要。这是因为缓存中间件需要知道通过哪些头来更改缓存存储。中间件总是尽可能在Vary响应头中添加一些内容。

UpdateCacheMiddleware在响应阶段运行，中间件以相反的顺序运行，因此列表顶部的项目在响应阶段最后运行。因此，您需要确保它UpdateCacheMiddleware 出现在任何其他可能向Vary 标头添加内容的中间件之前。以下中间件模块可以这样做：

- SessionMiddleware 添加 Cookie
- GZipMiddleware 添加 Accept-Encoding
- LocaleMiddleware 添加 Accept-Language

FetchFromCacheMiddleware另一方面，在请求阶段运行，其中中间件首先应用，因此列表顶部的项目在请求阶段首先运行。该FetchFromCacheMiddleware还需要经过其他中间件更新运行Vary头，所以 FetchFromCacheMiddleware必须在后的是这样做的任何项目。
