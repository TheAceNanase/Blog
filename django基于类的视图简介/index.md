# Django基于类的视图简介

# 基于类的视图

视图是可调用的，它接受请求并返回响应。这不仅可以是一个函数，而且Django提供了一些可用作视图的类的示例。这些使您可以利用继承和混合来构造视图并重用代码。对于任务，还有一些通用的视图，我们将在以后进行介绍，但是您可能想要设计自己的可重用视图结构，以适合您的用例。有关完整的详细信息，请参见[基于类的视图参考文档](https://docs.djangoproject.com/en/3.0/ref/class-based-views/)。



## 基本的例子

Django提供了适合各种应用程序的基本视图类。所有视图都从[View](https://docs.djangoproject.com/en/3.0/ref/class-based-views/base/#django.views.generic.base.View)该类继承，该类负责将视图链接到URL，HTTP方法分派和其他常见功能。RedirectView提供HTTP重定向，并TemplateView扩展基类以使其也呈现模板。

## URLconf中的用法

使用通用视图的最直接方法是直接在URLconf中创建它们。如果仅在基于类的视图上更改一些属性，则可以将它们传递给as_view()方法调用本身：

```
from django.urls import path
from django.views.generic import TemplateView

urlpatterns = [
    path('about/', TemplateView.as_view(template_name="about.html")),
]
```

传递给任何参数as_view()将覆盖在类上设置的属性。在此示例中，我们将设置template_name 为TemplateView。可以对上的url属性使用类似的覆盖模式 RedirectView。

## 子类化通用视图

使用通用视图的第二种更强大的方法是从现有视图继承并覆盖子类中的属性（例如template_name）或方法（例如get_context_data）以提供新的值或方法。例如，考虑一个仅显示一个模板的视图 about.html。Django有一个通用视图可以执行此操作-- TemplateView因此我们可以将其子类化，并覆盖模板名称：

```
# some_app/views.py
from django.views.generic import TemplateView

class AboutView(TemplateView):
    template_name = "about.html"
```

然后，我们需要将此新视图添加到我们的URLconf中。 TemplateView是一个类，而不是一个函数，因此我们将URL指向as_view()类方法，该方法为基于类的视图提供类似函数的条目：

```
# urls.py
from django.urls import path
from some_app.views import AboutView

urlpatterns = [
    path('about/', AboutView.as_view()),
]
```

有关如何使用内置通用视图的更多信息，请参考下一个基于通用类的视图的主题。

### 支持其他的HTTP方法

假设有人想使用视图作为API通过HTTP访问我们的图书库。API客户端会时不时地进行连接，并下载自上次访问以来发布的图书的图书数据。但是，如果从那以后没有新书出现，那么从数据库中获取书本，呈现完整的响应并将其发送给客户端将浪费CPU时间和带宽。最好向API询问最新书籍的发布时间。

我们将URL映射到URLconf中的书列表视图：

```
from django.urls import path
from books.views import BookListView

urlpatterns = [
    path('books/', BookListView.as_view()),
]
```

和视图：

```
from django.http import HttpResponse
from django.views.generic import ListView
from books.models import Book

class BookListView(ListView):
    model = Book

    def head(self, *args, **kwargs):
        last_book = self.get_queryset().latest('publication_date')
        response = HttpResponse()
        # RFC 1123 date format
        response['Last-Modified'] = last_book.publication_date.strftime('%a, %d %b %Y %H:%M:%S GMT')
        return response
```

如果从GET请求访问视图，则在响应中返回对象列表（使用book_list.html模板）。但是，如果客户发出HEAD请求，则响应的主体为空，Last-Modified 标题会指示最新书籍的发布时间。根据此信息，客户端可以下载也可以不下载完整的对象列表。
