# 全文检索

# Haystack

## 1.什么是Haystack

　　Haystack是django的开源全文搜索框架(全文检索不同于特定字段的模糊查询，使用全文检索的效率更高 )，该框架支持**Solr**,**Elasticsearch**,**Whoosh**,**Xapian 搜索引擎它是一个可插拔的后端（很像Django的数据库层），所以几乎你所有写的代码都可以在不同搜索引擎之间便捷切换



全文检索不同于特定字段的模糊查询，使用全文检索的效率更高，并且能够对于中文进行分词处理


* haystack：django的一个包，可以方便地对model里面的内容进行索引、搜索，设计为支持whoosh,solr,Xapian,Elasticsearc四种全文检索引擎后端，属于一种全文检索的框架


* whoosh：纯Python编写的全文搜索引擎，虽然性能比不上sphinx、xapian、Elasticsearc等，但是无二进制包，程序不会莫名其妙的崩溃，对于小型的站点，whoosh已经足够使用



* jieba：一款免费的中文分词包，如果觉得不好用可以使用一些收费产品



* 搜索引擎就好比一个数据库，搜索引擎将mgsql中的数据复制一份到搜索引擎。查询的时候直接通过搜索引擎来快速查询数据的结果。



## 2.安装

```md-fences md-end-block ty-contain-cm modeLoaded">pip install django-haystack<br />pip install whoosh<br />pip install jieba
## 3.配置

1 添加Haystack到`INSTALLED_APPS`

　　跟大多数Django的应用一样，你应该在你的设置文件(通常是`settings.py`)添加Haystack到`INSTALLED_APPS`. 示例： 

​```python

INSTALLED_APPS = [

    'django.contrib.admin',

    'django.contrib.auth',

    'django.contrib.contenttypes',

    'django.contrib.sessions',

    'django.contrib.sites',

​

    # 添加

    'haystack',

​

    # 你的app

    'blog',

]

```

2 修改`settings.py`

　　在你的`settings.py`中，你需要添加一个设置来指示站点配置文件正在使用的后端，以及其它的后端设置。 `HAYSTACK&mdash;&mdash;CONNECTIONS`是必需的设置，并且应该至少是以下的一种： 

#### Solr示例

```python
HAYSTACK_CONNECTIONS = {

    'default': {

        'ENGINE': 'haystack.backends.solr_backend.SolrEngine',

        'URL': 'http://127.0.0.1:8983/solr'

        # ...or for multicore...

        # 'URL': 'http://127.0.0.1:8983/solr/mysite',

    },

}

```

#### Elasticsearch示例

```python
HAYSTACK_CONNECTIONS = {

    'default': {

        'ENGINE': 'haystack.backends.elasticsearch_backend.ElasticsearchSearchEngine',

        'URL': 'http://127.0.0.1:9200/',

        'INDEX_NAME': 'haystack',

    },

}

```

#### Whoosh示例

```md-fences md-end-block ty-contain-cm modeLoaded">#需要设置PATH到你的Whoosh索引的文件系统位置<br />
​```python

import os

HAYSTACK_CONNECTIONS = {

    'default': {

        'ENGINE': 'haystack.backends.whoosh_backend.WhooshEngine',

        'PATH': os.path.join(os.path.dirname(__file__), 'whoosh_index'),

    },

}

​<br />

​```md-fences md-end-block ty-contain-cm modeLoaded"># 自动更新索引<br />HAYSTACK_SIGNAL_PROCESSOR = 'haystack.signals.RealtimeSignalProcessor'

```

#### Xapian示例

```md-fences md-end-block ty-contain-cm modeLoaded">#首先安装Xapian后端（http://github.com/notanumber/xapian-haystack/tree/master）<br />#需要设置PATH到你的Xapian索引的文件系统位置。<br />
​```python

import os

HAYSTACK_CONNECTIONS = {

    'default': {

        'ENGINE': 'xapian_backend.XapianEngine',

        'PATH': os.path.join(os.path.dirname(__file__), 'xapian_index'),

    },

}

```

## 4.处理数据

### 创建索引 

如果你想针对某个app例如blog做全文检索，则必须在blog的目录下面建立`search_indexes.py`文件，文件名不能修改 

```python
from haystack import indexes

from app01.models import Article

​

class ArticleIndex(indexes.SearchIndex, indexes.Indexable):

    #类名必须为需要检索的Model_name+Index，这里需要检索Article，所以创建ArticleIndex

    text = indexes.CharField(document=True, use_template=True)#创建一个text字段 

    #其它字段

    desc = indexes.CharField(model_attr='desc')

    content = indexes.CharField(model_attr='content')

​

    def get_model(self):#重载get_model方法，必须要有！

        return Article

​

    def index_queryset(self, using=None):

        return self.get_model().objects.all()

```

&nbsp;

　　为什么要创建索引？索引就像是一本书的目录，可以为读者提供更快速的导航与查找。在这里也是同样的道理，当数据量非常大的时候，若要从这些数据里找出所有的满足搜索条件的几乎是不太可能的，将会给服务器带来极大的负担。所以我们需要为指定的数据添加一个索引（目录），在这里是为Note创建一个索引，索引的实现细节是我们不需要关心的，至于为它的哪些字段创建索引，怎么指定 ,下面开始讲解每个索引里面必须有且只能有一个字段为 document=True，这代表haystack 和搜索引擎将使用此字段的内容作为索引进行检索(primary field)。其他的字段只是附属的属性，方便调用，并不作为检索数据

```md-fences md-end-block ty-contain-cm modeLoaded">注意：如果使用一个字段设置了document=True，则一般约定此字段名为text，这是在ArticleIndex类里面一贯的命名，以防止后台混乱，当然名字你也可以随便改，不过不建议改。另外，我们在`text`字段上提供了`use_template=True`。这允许我们使用一个数据模板（而不是容易出错的级联）来构建文档搜索引擎索引。你应该在模板目录下建立新的模板`search/indexes/blog/article_text.txt`，并将下面内容放在里面。 

```md-fences mock-cm md-end-block">#在目录&ldquo;templates/search/indexes/应用名称/&rdquo;下创建&ldquo;模型类名称_text.txt&rdquo;文件
​```python

{{ object.title }}

{{ object.desc }}

{{ object.content }}

```

&nbsp;

这个数据模板的作用是对`Note.title`,&nbsp;`Note.user.get_full_name`,`Note.body`这三个字段建立索引，当检索的时候会对这三个字段做全文检索匹配 

## 5.设置视图

### 添加`SearchView`到你的`URLconf`

在你的`URLconf`中添加下面一行：

```python
前后端不分离url<br />url(r'^search/', include('haystack.urls')),<br /><br />前后端分离url<br />path('search/', MySearchView(), name='haystack_search'),

```

这会拉取Haystack的默认URLconf，它由单独指向`SearchView`实例的URLconf组成。你可以通过传递几个关键参数或者完全重新它来改变这个类的行为。

&nbsp;

前后端分离后台重写create_response方法，

```python
from haystack.views import SearchView



class MySearchView(SearchView):



    def create_response(self):

        context = super().get_context()

        keyword = self.request.GET.get('q', None)  # 关键字为q

        if not keyword:

            return JsonResponse({'message': '没有相关信息'})

        else:

            data_list = [{'id': i.object.id, 'name': i.object.name,

                          'model_img': 'http://api.modelbox.cn:8000/media/' + str(i.object.model_img),

                          'download_num': i.object.download_num, 'collect_num': i.object.download_num} for i in

                         context['page'].object_list]

         return JsonResponse(data_list, safe=False, json_dumps_params={'ensure_ascii': False})

```

### 搜索模板

你的搜索模板(默认在`search/search.html`)将可能非常简单。下面的足够让你的搜索运行(你的`template/block`应该会不同)

```python
<!DOCTYPE html>

<html>

<head>

    <title></title>

    <style>

        span.highlighted {

            color: red;

        }

    </style>

</head>

<body>

{% load highlight %}

{% if query %}

    <h3>搜索结果如下：</h3>

    {% for result in page.object_list %}

{#        <a href="/{{ result.object.id }}/">{{ result.object.title }}</a><br/>#}

        <a href="/{{ result.object.id }}/">{%   highlight result.object.title with query max_length 2%}</a><br/>

        <p>{{ result.object.content|safe }}</p>

        <p>{% highlight result.content with query %}</p>

    {% empty %}

        <p>啥也没找到</p>

    {% endfor %}



    {% if page.has_previous or page.has_next %}

        <div>

            {% if page.has_previous %}

                <a href="?q={{ query }}&amp;amp;page={{ page.previous_page_number }}">{% endif %}&amp;laquo; 上一页

            {% if page.has_previous %}</a>{% endif %}

            |

            {% if page.has_next %}<a href="?q={{ query }}&amp;amp;page={{ page.next_page_number }}">{% endif %}下一页 &amp;raquo;

            {% if page.has_next %}</a>{% endif %}

        </div>

    {% endif %}

{% endif %}

</body>

</html>

```

需要注意的是`page.object_list`实际上是`SearchResult`对象的列表。这些对象返回索引的所有数据。它们可以通过`{{result.object}}`来访问。所以`{{ result.object.title}}`实际使用的是数据库中Article对象来访问`title`字段的。 

### 重建索引

现在你已经配置好了所有的事情，是时候把数据库中的数据放入索引了。Haystack附带的一个命令行管理工具使它变得很容易。

简单的运行python `./manage.py rebuild_index`。你会得到有多少模型进行了处理并放进索引的统计。

## 6.使用jieba分词

```md-fences mock-cm md-end-block">#建立ChineseAnalyzer.py文件
#保存在haystack的安装文件夹下，路径如&ldquo;D:\python3\Lib\site-packages\haystack\backends&rdquo;

​```python

import jieba

from whoosh.analysis import Tokenizer, Token



class ChineseTokenizer(Tokenizer):

    def __call__(self, value, positions=False, chars=False,

                 keeporiginal=False, removestops=True,

                 start_pos=0, start_char=0, mode='', **kwargs):

        t = Token(positions, chars, removestops=removestops, mode=mode,

                  **kwargs)

        seglist = jieba.cut(value, cut_all=True)

        for w in seglist:

            t.original = t.text = w

            t.boost = 1.0

            if positions:

                t.pos = start_pos + value.find(w)

            if chars:

                t.startchar = start_char + value.find(w)

                t.endchar = start_char + value.find(w) + len(w)

            yield t



def ChineseAnalyzer():

    return ChineseTokenizer()

```

```md-fences mock-cm md-end-block">#复制whoosh_backend.py文件，改名为whoosh_cn_backend.py
#注意：复制出来的文件名，末尾会有一个空格，记得要删除这个空格

from .ChineseAnalyzer import ChineseAnalyzer 

查找

analyzer=StemmingAnalyzer()

改为

analyzer=ChineseAnalyzer()



## 7.在模版中创建搜索栏

​```python

<form method='get' action="/search/" target="_blank">

    <input type="text" name="q">

    <input type="submit" value="查询">

</form>

```

&nbsp;

## 8.其它配置

### 增加更多变量

```python
from haystack.views import SearchView  

from .models import *  

      

class MySeachView(SearchView):  

     def extra_context(self):       #重载extra_context来添加额外的context内容  

         context = super(MySeachView,self).extra_context()  

         side_list = Topic.objects.filter(kind='major').order_by('add_date')[:8]  

         context['side_list'] = side_list  

         return context  

      

#路由修改

url(r'^search/', search_views.MySeachView(), name='haystack_search'),  

```

&nbsp;

### 高亮显示

```python
{% highlight result.summary with query %}  

# 这里可以限制最终{{ result.summary }}被高亮处理后的长度  

{% highlight result.summary with query max_length 40 %}  



#html中

    <style>

        span.highlighted {

            color: red;

        }

    </style>

```

&nbsp;

&nbsp;

**Elasticsearch** 

&nbsp;

**简介：**

Elasticsearch 是一个分布式可扩展的实时搜索和分析引擎,一个建立在全文搜索引擎 Apache Lucene(TM) 基础上的搜索引擎.当然 Elasticsearch 并不仅仅是 Lucene 那么简单，它不仅包括了全文搜索功能，还可以进行以下工作:



- 分布式实时文件存储，并将每一个字段都编入索引，使其可以被搜索。

- 可实现亿级数据实时查询

- 实时分析的分布式搜索引擎。

- 可以扩展到上百台服务器，处理PB级别的结构化或非结构化数据。



&nbsp;

**安装：**

<a href="https://www.elastic.co/cn/downloads/elasticsearch" target="_blank">&nbsp;下载地址</a>

注意：Elasticsearch是用Java开发的，最新版本的Elasticsearch需要安装jdk1.8以上的环境

安装包下载完，解压，进入到bin目录，启动 elasticsearch.bat 即可

&nbsp;

## python操作ElasticSearch

&nbsp;

```python
from elasticsearch import Elasticsearch



obj = Elasticsearch()

# 创建索引（Index）

result = obj.indices.create(index='user', body={"userid":'1','username':'lqz'},ignore=400)

# print(result)

# 删除索引

# result = obj.indices.delete(index='user', ignore=[400, 404])

# 插入数据

# data = {'userid': '1', 'username': 'lqz','password':'123'}

# result = obj.create(index='news', doc_type='politics', id=1, body=data)

# print(result)

# 更新数据

'''

不用doc包裹会报错

ActionRequestValidationException[Validation Failed: 1: script or doc is missing

'''

# data ={'doc':{'userid': '1', 'username': 'lqz','password':'123ee','test':'test'}}

# result = obj.update(index='news', doc_type='politics', body=data, id=1)

# print(result)





# 删除数据

# result = obj.delete(index='news', doc_type='politics', id=1)



# 查询

# 查找所有文档

query = {'query': {'match_all': {}}}

#  查找名字叫做jack的所有文档

# query = {'query': {'term': {'username': 'lqz'}}}



# 查找年龄大于11的所有文档

# query = {'query': {'range': {'age': {'gt': 11}}}}



allDoc = obj.search(index='news', doc_type='politics', body=query)

print(allDoc['hits']['hits'][0]['_source'])

```


