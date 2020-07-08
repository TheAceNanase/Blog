# Django使用基于类的视图进行表单处理

# 形成基于类的意见处理

表单处理通常具有3条路径：

- 初始GET（空白或预填充形式）
- POST包含无效数据（通常重新显示带有错误的表单）
- 使用有效数据进行POST（处理数据并通常重定向）

自己实现这一点通常会导致很多重复的样板代码（请参阅[在视图中使用表单](https://docs.djangoproject.com/en/3.0/topics/forms/#using-a-form-in-a-view)）。为了避免这种情况，Django提供了一组通用的基于类的视图以进行表单处理。

## 基本形式

给出联系表：

的forms.py 

```
from django import forms

class ContactForm(forms.Form):
    name = forms.CharField()
    message = forms.CharField(widget=forms.Textarea)

    def send_email(self):
        # send email using the self.cleaned_data dictionary
        pass
```

可以使用构造视图FormView：

的views.py 

```
from myapp.forms import ContactForm
from django.views.generic.edit import FormView

class ContactView(FormView):
    template_name = 'contact.html'
    form_class = ContactForm
    success_url = '/thanks/'

    def form_valid(self, form):
        # This method is called when valid form data has been POSTed.
        # It should return an HttpResponse.
        form.send_email()
        return super().form_valid(form)
```

笔记：

- FormView是继承的， TemplateResponseMixin因此 template_name 可以在这里使用。
- 的默认实现 form_valid()只是将重定向到success_url。

## 模型的形式

使用模型时，通用视图确实很出色。这些通用视图将自动创建一个ModelForm，只要它们能够确定要使用的模型类：

- 如果model给定了属性，则将使用该模型类。
- 如果get_object() 返回一个对象，则将使用该对象的类。
- 如果queryset给出a，将使用该查询集的模型。

模型表单视图提供了一种 form_valid()自动保存模型的实现。如果有特殊要求，可以覆盖此设置。请参阅下面的示例。

您甚至不需要提供success_urlfor CreateView或 UpdateView- get_absolute_url()如果可用，它们将 在模型对象上使用。

如果要使用自定义ModelForm（例如添加额外的验证），请form_class在视图上进行设置 。

**注意：**指定自定义表单类时，即使form_class可能是，您仍必须指定模型ModelForm。

首先，我们需要添加get_absolute_url()到 Author类中：

models.py中

```
from django.db import models
from django.urls import reverse

class Author(models.Model):
    name = models.CharField(max_length=200)

    def get_absolute_url(self):
        return reverse('author-detail', kwargs={'pk': self.pk})
```

然后我们可以CreateView和朋友一起做实际的工作。注意这里我们是如何配置通用的基于类的视图的。我们不必自己编写任何逻辑：

的views.py 

```
from django.urls import reverse_lazy
from django.views.generic.edit import CreateView, DeleteView, UpdateView
from myapp.models import Author

class AuthorCreate(CreateView):
    model = Author
    fields = ['name']

class AuthorUpdate(UpdateView):
    model = Author
    fields = ['name']

class AuthorDelete(DeleteView):
    model = Author
    success_url = reverse_lazy('author-list')
```

**注意：**我们必须使用reverse_lazy()而不是 reverse()，因为导入文件时不会加载url。

该fields属性的工作方式与fields上内部Meta类的属性相同ModelForm。除非您以其他方式定义表单类，否则该属性是必需的，否则视图将引发ImproperlyConfigured异常。

如果同时指定fields 和form_class属性， ImproperlyConfigured则会引发异常。

最后，我们将这些新视图连接到URLconf中：

的urls.py 

```
from django.urls import path
from myapp.views import AuthorCreate, AuthorDelete, AuthorUpdate

urlpatterns = [
    # ...
    path('author/add/', AuthorCreate.as_view(), name='author-add'),
    path('author/<int:pk>/', AuthorUpdate.as_view(), name='author-update'),
    path('author/<int:pk>/delete/', AuthorDelete.as_view(), name='author-delete'),
]
```

**注意：**些观点继承 SingleObjectTemplateResponseMixin 它使用 template_name_suffix 了构建 template_name 基于模型。

在此示例中：

- CreateView和UpdateView使用myapp/author_form.html
- DeleteView 用途 myapp/author_confirm_delete.html

如果希望为CreateView和 提供单独的模板UpdateView，则可以 在视图类上设置 template_name或 template_name_suffix。

## 模型和request.user

要跟踪使用创建对象的用户，CreateView可以使用自定义方法ModelForm来执行此操作。首先，将外键关系添加到模型：

models.py中

```
from django.contrib.auth.models import User
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=200)
    created_by = models.ForeignKey(User, on_delete=models.CASCADE)

    # ...
```

在视图中，确保您不包括created_by要编辑的字段列表，并覆盖 form_valid()以添加用户：

的views.py 

```
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic.edit import CreateView
from myapp.models import Author

class AuthorCreate(LoginRequiredMixin, CreateView):
    model = Author
    fields = ['name']

    def form_valid(self, form):
        form.instance.created_by = self.request.user
        return super().form_valid(form)
```

LoginRequiredMixin防止未登录的用户访问表单。如果您忽略了这一点，则需要处理中的未授权用户form_valid()。

## AJAX示例

这是一个示例，显示了如何实现适用于AJAX请求以及“普通”表单POST的表单：

```
from django.http import JsonResponse
from django.views.generic.edit import CreateView
from myapp.models import Author

class AjaxableResponseMixin:
    """
    Mixin to add AJAX support to a form.
    Must be used with an object-based FormView (e.g. CreateView)
    """
    def form_invalid(self, form):
        response = super().form_invalid(form)
        if self.request.is_ajax():
            return JsonResponse(form.errors, status=400)
        else:
            return response

    def form_valid(self, form):
        # We make sure to call the parent's form_valid() method because
        # it might do some processing (in the case of CreateView, it will
        # call form.save() for example).
        response = super().form_valid(form)
        if self.request.is_ajax():
            data = {
                'pk': self.object.pk,
            }
            return JsonResponse(data)
        else:
            return response

class AuthorCreate(AjaxableResponseMixin, CreateView):
    model = Author
    fields = ['name']
```


