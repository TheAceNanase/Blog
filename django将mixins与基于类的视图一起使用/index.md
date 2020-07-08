# Django将mixins与基于类的视图一起使用

# 在基于类的视图中使用mixins

**警告：** 这是一个高级主题。在探索这些技术之前，建议您对[Django基于类的视图](https://www.w3cschool.cn/django/django-lq1g37h4.html)有一定的了解。

Django的内置基于类的视图提供了许多功能，但您可能需要单独使用其中的一些功能。例如，您可能想编写一个视图，该视图呈现一个模板以进行HTTP响应，但是您不能使用 TemplateView;  也许您只需要在上渲染模板POST，然后GET完全执行其他操作即可。虽然您可以TemplateResponse直接使用 ，但这可能会导致代码重复。

因此，Django还提供了许多混合器，这些混合器提供了更多离散功能。例如，模板呈现封装在中 TemplateResponseMixin。Django参考文档包含所有mixins的完整文档。

## 上下文和模板答复

提供了两个中央mixin，它们有助于在使用基于类的视图中的模板时提供一致的界面。

- TemplateResponseMixin每个返回a的内置视图都 TemplateResponse将调用提供的 render_to_response()  方法TemplateResponseMixin。在大多数情况下，都会为您调用此get()方法（例如，由TemplateView和  实现的方法都调用它DetailView）；同样，不太可能需要覆盖它，尽管如果您希望响应返回的内容不是通过Django模板呈现的，那么您就需要这样做。有关此示例，请参见JSONResponseMixin示例。render_to_response()本身调用 get_template_names()，默认情况下会  template_name在基于类的视图上查询；其他两个mixin（SingleObjectTemplateResponseMixin 和  MultipleObjectTemplateResponseMixin）覆盖此属性，以便在处理实际对象时提供更灵活的默认值。
- ContextMixin每个需要上下文数据（例如用于渲染模板（包括TemplateResponseMixin上述内容））的内置视图都应调用 get_context_data()传递它们想要确保位于其中的任何数据作为关键字参数。  get_context_data()返回字典；在ContextMixin其中返回其关键字参数，但是通常会覆盖此参数以将更多成员添加到字典中。您也可以使用该 extra_context属性。

## 建立Django基于类的通用视图

让我们看看Django的两个基于类的通用视图是如何通过提供离散功能的mixin构建的。我们将考虑 DetailView，它呈现一个对象的“详细”视图，而  ListView，它将呈现一个对象列表（通常来自查询集），并可选地对它们进行分页。这将向我们介绍四个mixin，它们在使用单个Django对象或多个对象时提供有用的功能。

也有参与通用编辑观点混入（FormView和特定型号的意见CreateView， UpdateView和 DeleteView），并在基于日期的通用视图。这些已在mixin参考文档中介绍。

### DetailView：使用单个Django对象

要显示对象的详细信息，我们基本上需要做两件事：我们需要查找对象，然后需要TemplateResponse使用合适的模板制作一个 ，并将该对象作为上下文。

要获得该对象，需要DetailView 依赖SingleObjectMixin，该get_object() 方法提供了一种方法，该  方法可以根据请求的URL找出对象（它会根据URLConf中的声明查找pk和slug关键字参数，然后从model视图的属性中查找该对象 ，或提供的 queryset 属性）。SingleObjectMixin也会覆盖  get_context_data()，它在所有Django的所有基于类的内置视图中使用，以为模板渲染提供上下文数据。

然后TemplateResponse，要 DetailView使用SingleObjectTemplateResponseMixin，则使用 ，  如上所讨论的，它扩展了TemplateResponseMixin，覆盖  get_template_names()。实际上，它提供了一组相当复杂的选项，但是大多数人要使用的主要选项是  /_detail.html。该_detail部分可以通过设置来改变 template_name_suffix  的一个子类别的东西。（例如，通用编辑观点使用_form的创建和更新的意见，并 _confirm_delete进行删除的意见。）

### ListView：使用许多Django对象

对象列表遵循大致相同的模式：我们需要一个（可能是分页的）对象列表，通常为 QuerySet，然后我们需要TemplateResponse使用该对象列表使用合适的模板制作一个 。

要获取对象，请ListView使用 MultipleObjectMixin，同时提供 get_queryset() 和  paginate_queryset()。与with不同SingleObjectMixin，不需要关闭URL的一部分来确定要使用的查询集，因此默认值使用 view类上的querysetor model属性。覆盖get_queryset() 此处的常见原因  是动态地改变对象，例如取决于当前用户或排除博客的将来帖子。

MultipleObjectMixin还重写 get_context_data()以包括用于分页的适当上下文变量（如果禁用了分页，则提供虚拟变量）。它依赖object_list于作为关键字参数进行传递的关键字参数ListView。

做一个TemplateResponse， ListView然后使用 MultipleObjectTemplateResponseMixin;  与SingleObjectTemplateResponseMixin  上面的方法一样，此方法将覆盖，get_template_names()以提供，最常用的是 ，而该部分再次从  属性中获取。（基于日期的通用视图使用诸如的后缀， 以此类推，以便为各种专门的基于日期的列表视图使用不同的模板。）a range of  options/_list.html``_listtemplate_name_suffix_archive``_archive_year

## 使用Django的基于类的视图混入

现在，我们已经了解了Django的基于类的通用视图如何使用提供的mixins，让我们看一下将它们组合在一起的其他方法。当然，我们仍将它们与内置的基于类的视图或其他通用的基于类的视图结合起来，但是您可以解决许多比Django开箱即用的罕见问题。

**警告：**并非所有的mixin都可以一起使用，也不是所有基于通用类的视图都可以与所有其他mixin一起使用。在这里，我们提供了一些可行的示例。如果要合并其他功能，则必须考虑使用的不同类之间重叠的属性和方法之间的交互，以及方法解析顺序将如何影响以何种顺序调用哪些版本的方法。

Django的基于类的视图和基于类的视图mixin的参考文档将帮助您了解哪些属性和方法可能会导致不同的类和mixin之间发生冲突。

如有疑问，通常最好还是放弃并以 View或为基础TemplateView，也许使用 SingleObjectMixin和  MultipleObjectMixin。尽管您可能最终会写出更多的代码，但是以后其他人可能更容易理解它，而更少的交互性让您担心，可以节省一些时间。（当然，您总是可以深入了解Django基于类的通用视图的实现，以获取有关如何解决问题的灵感。）

### SingleObjectMixin与View一起使用

如果我们要编写一个仅对做出响应的基于类的视图，则将创建子POST类View并post()在该子类中编写一个方法。但是，如果我们希望我们的处理能够处理从URL识别的特定对象，我们将需要提供的功能 SingleObjectMixin。

我们将通过在基于通用类的视图简介中Author使用的模型来 演示这一点。

的views.py 

```
from django.http import HttpResponseForbidden, HttpResponseRedirect
from django.urls import reverse
from django.views import View
from django.views.generic.detail import SingleObjectMixin
from books.models import Author

class RecordInterest(SingleObjectMixin, View):
    """Records the current user's interest in an author."""
    model = Author

    def post(self, request, *args, **kwargs):
        if not request.user.is_authenticated:
            return HttpResponseForbidden()

        # Look up the author we're interested in.
        self.object = self.get_object()
        # Actually record interest somehow here!

        return HttpResponseRedirect(reverse('author-detail', kwargs={'pk': self.object.pk}))
```

实际上，您可能希望将兴趣记录在键值存储而不是关系数据库中，因此我们省略了这一点。唯一需要担心使用的视图 SingleObjectMixin就是我们要查找感兴趣的作者的地方，它通过调用来完成  self.get_object()。mixin会为我们处理其他所有事务。

我们可以很容易地将其挂接到我们的URL中：

的urls.py 

```
from django.urls import path
from books.views import RecordInterest

urlpatterns = [
    #...
    path('author/<int:pk>/interest/', RecordInterest.as_view(), name='author-interest'),
]
```

请注意pk命名组，该组 get_object()用于查找Author实例。您还可以使用slug或的任何其他功能 SingleObjectMixin。

### SingleObjectMixin与 使用ListView

ListView提供内置的分页功能，但是您可能希望对所有通过外键链接到另一个对象的对象列表进行分页。在我们的出版示例中，您可能希望对特定出版商的所有书籍进行分页。

一种实现方法是与结合ListView使用 SingleObjectMixin，以便分页图书清单的查询集可以脱离作为单个对象找到的出版商。为此，我们需要有两个不同的查询集：

- Book 由...使用的查询集 ListView由于我们可以访问Publisher要列出的书，因此我们可以覆盖get_queryset()和使用Publisher的反向外键管理器。
- Publisher queryset用于  get_object()我们将依靠的默认实现get_object()来获取正确的Publisher对象。但是，我们需要显式传递一个queryset参数，因为否则默认的的实现get_object()将调用 get_queryset()我们已重写以返回Book对象而不是对象的对象Publisher。

**注意：**我们必须仔细考虑get_context_data()。由于SingleObjectMixin和 ListView都会将事物放置在上下文数据中（context_object_name如果已设置）的值之下，因此我们将明确确保事物在  Publisher上下文数据中。ListView 将增加在合适的page_obj和paginator我们提供我们记得打电话super()。

现在我们可以写一个新的PublisherDetail：

```
from django.views.generic import ListView
from django.views.generic.detail import SingleObjectMixin
from books.models import Publisher

class PublisherDetail(SingleObjectMixin, ListView):
    paginate_by = 2
    template_name = "books/publisher_detail.html"

    def get(self, request, *args, **kwargs):
        self.object = self.get_object(queryset=Publisher.objects.all())
        return super().get(request, *args, **kwargs)

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['publisher'] = self.object
        return context

    def get_queryset(self):
        return self.object.book_set.all()
```

请注意我们是如何设置的self.object，get()以便稍后在get_context_data()和中再次使用它get_queryset()。如果您未设置template_name，则模板将默认为正常 ListView选择，在这种情况下，这是 "books/book_list.html"因为它是一本书籍清单；  ListView一无所知SingleObjectMixin，因此毫无 头绪Publisher。

## 避免任何更复杂

一般来说，你可以使用 TemplateResponseMixin和  SingleObjectMixin当你需要它们的功能。如上所示，稍加注意，您甚至可以SingleObjectMixin与结合使用  ListView。但是，随着您尝试这样做，事情变得越来越复杂，一个好的经验法则是：

**暗示：**您的每个视图都应仅使用mixins或一组基于类的通用视图中的视图：详细信息，列表，编辑和日期。例如，将TemplateView（内置于视图中）与 MultipleObjectMixin（通用列表）结合在一起是很好的选择  ，但是将SingleObjectMixin（通用细节）与MultipleObjectMixin（通用列表）结合起来可能会遇到问题。

为了显示当您尝试变得更复杂时会发生什么，我们展示了一个示例，该示例在存在更简单的解决方案时会牺牲可读性和可维护性。首先，让我们看一下与结合DetailView使用的天真尝试 ， FormMixin以使我们能够 POST将Django Form带到与我们使用来显示对象相同的URL DetailView。

### FormMixin与  使用DetailView

回想一下我们先前使用View和 SingleObjectMixin在一起的示例。我们正在记录用户对特定作者的兴趣；现在说，我们要让他们留下信息，说明他们为什么喜欢他们。再次，让我们假设我们不会将其存储在关系数据库中，而是存储在我们不再担心的更深奥的东西中。

此时，很自然地可以Form将封装从用户浏览器发送到Django的信息。还要说我们在REST上投入了巨资，因此我们想使用与显示来自用户的消息相同的URL来显示作者。让我们重写我们的代码AuthorDetailView。

尽管必须将a添加到上下文数据中，以便将其呈现在模板中，但我们将保留GET来自的处理。我们还希望从中引入表单处理，并编写一些代码，以便在表单上正确调用。DetailViewFormFormMixinPOST

**注意：**我们使用FormMixin并实现 post()自己，而不是尝试DetailView与之 混合FormView（这post()已经提供了合适的方法），因为这两个视图都实现get()，并且事情会变得更加混乱。

我们的新AuthorDetail外观如下所示：

```
# CAUTION: you almost certainly do not want to do this.
# It is provided as part of a discussion of problems you can
# run into when combining different generic class-based view
# functionality that is not designed to be used together.

from django import forms
from django.http import HttpResponseForbidden
from django.urls import reverse
from django.views.generic import DetailView
from django.views.generic.edit import FormMixin
from books.models import Author

class AuthorInterestForm(forms.Form):
    message = forms.CharField()

class AuthorDetail(FormMixin, DetailView):
    model = Author
    form_class = AuthorInterestForm

    def get_success_url(self):
        return reverse('author-detail', kwargs={'pk': self.object.pk})

    def post(self, request, *args, **kwargs):
        if not request.user.is_authenticated:
            return HttpResponseForbidden()
        self.object = self.get_object()
        form = self.get_form()
        if form.is_valid():
            return self.form_valid(form)
        else:
            return self.form_invalid(form)

    def form_valid(self, form):
        # Here, we would record the user's interest using the message
        # passed in form.cleaned_data['message']
        return super().form_valid(form)
```

get_success_url()提供重定向到的位置，该位置可用于的默认实现form_valid()。post()如前所述，我们必须提供我们自己的。

### 更好的解决方案

之间微妙的相互作用的数量 FormMixin和DetailView已测试我们的管理事物的能力。您不太可能想自己编写此类。

在这种情况下，尽管编写处理代码涉及很多重复，但是您可以post()自己编写方法，并保持 DetailView唯一的通用功能 Form。

可替代地，它仍然会比上面的方法工作少到具有用于处理的形式，其可以使用一个单独的视图 FormView从不同 DetailView无顾虑。

### 另一种更好的解决方案

我们实际上在这里试图做的是使用来自同一URL的两个不同的基于类的视图。那么为什么不这样做呢？我们在这里有一个非常清楚的划分：GET请求应获取 DetailView（Form添加到上下文数据中），POST请求应获取FormView。让我们先设置这些视图。

该AuthorDisplay视图与我们首次引入AuthorDetail时几乎相同；我们在写我们自己get_context_data()，使 AuthorInterestForm可用的模板。get_object()为了清楚起见，我们将跳过之前的 替代：

```
from django import forms
from django.views.generic import DetailView
from books.models import Author

class AuthorInterestForm(forms.Form):
    message = forms.CharField()

class AuthorDisplay(DetailView):
    model = Author

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['form'] = AuthorInterestForm()
        return context
```

然后AuthorInterest是a FormView，但是我们必须带进来，  SingleObjectMixin以便我们可以找到我们正在谈论的作者，并且我们必须记住进行设置template_name以确保表单错误将呈现与AuthorDisplayon 相同的模板GET：

```
from django.http import HttpResponseForbidden
from django.urls import reverse
from django.views.generic import FormView
from django.views.generic.detail import SingleObjectMixin

class AuthorInterest(SingleObjectMixin, FormView):
    template_name = 'books/author_detail.html'
    form_class = AuthorInterestForm
    model = Author

    def post(self, request, *args, **kwargs):
        if not request.user.is_authenticated:
            return HttpResponseForbidden()
        self.object = self.get_object()
        return super().post(request, *args, **kwargs)

    def get_success_url(self):
        return reverse('author-detail', kwargs={'pk': self.object.pk})
```

最后，我们以一种新的AuthorDetail观点将它们组合在一起。我们已经知道，调用as_view()基于类的视图会使我们的行为与基于函数的视图完全相同，因此我们可以在两个子视图之间进行选择。

当然，您可以通过传递关键字参数as_view()的方式与在URLconf中传递 方式相同，例如，如果您希望该AuthorInterest行为也出现在另一个URL上，但使用不同的模板：

```
from django.views import View

class AuthorDetail(View):

    def get(self, request, *args, **kwargs):
        view = AuthorDisplay.as_view()
        return view(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        view = AuthorInterest.as_view()
        return view(request, *args, **kwargs)
```

此方法还可以与任何其他直接从View或继承的常规基于类的视图或您自己的基于类的视图一起使用 TemplateView，因为它可以使不同的视图尽可能地分开。

当您想多次执行相同的操作时，基于类的视图就会大放异彩。假设您正在编写一个API，并且每个视图都应返回JSON而不是呈现的HTML。

我们可以创建一个mixin类以在所有视图中使用，一次处理到JSON的转换。

例如，JSON混合可能看起来像这样：

```
from django.http import JsonResponse

class JSONResponseMixin:
    """
    A mixin that can be used to render a JSON response.
    """
    def render_to_json_response(self, context, **response_kwargs):
        """
        Returns a JSON response, transforming 'context' to make the payload.
        """
        return JsonResponse(
            self.get_data(context),
            **response_kwargs
        )

    def get_data(self, context):
        """
        Returns an object that will be serialized as JSON by json.dumps().
        """
        # Note: This is *EXTREMELY* naive; in reality, you'll need
        # to do much more complex handling to ensure that arbitrary
        # objects -- such as Django model instances or querysets
        # -- can be serialized as JSON.
        return context
```

**注意：**请查看序列化Django对象文档，以获取有关如何正确将Django模型和查询集正确转换为JSON的更多信息。

这个mixin提供了render_to_json_response()一种与签名相同的方法render_to_response()。要使用它，我们需要将其混入一个TemplateView例如，并重写 render_to_response()以render_to_json_response()代替调用：

```
from django.views.generic import TemplateView

class JSONView(JSONResponseMixin, TemplateView):
    def render_to_response(self, context, **response_kwargs):
        return self.render_to_json_response(context, **response_kwargs)
```

同样，我们可以将我们的mixin与通用视图之一一起使用。我们可以DetailView通过JSONResponseMixin与django.views.generic.detail.BaseDetailView– 混合来制作自己的版本（混合 了 DetailView模板渲染之前的行为）：

```
from django.views.generic.detail import BaseDetailView

class JSONDetailView(JSONResponseMixin, BaseDetailView):
    def render_to_response(self, context, **response_kwargs):
        return self.render_to_json_response(context, **response_kwargs)
```

然后可以以与其他任何视图相同的方式部署此视图 DetailView，并且行为完全相同-除了响应的格式。

如果你想成为真正的冒险，你甚至可以混合使用一个 DetailView子类，它能够返回两个 HTML和JSON的内容，根据不同的HTTP请求，的某些属性，如查询参数或HTTP标头。混合使用  JSONResponseMixin和和  SingleObjectTemplateResponseMixin，并render_to_response()  根据用户请求的响应类型覆盖的实现， 以采用适当的呈现方法：

```
from django.views.generic.detail import SingleObjectTemplateResponseMixin

class HybridDetailView(JSONResponseMixin, SingleObjectTemplateResponseMixin, BaseDetailView):
    def render_to_response(self, context):
        # Look for a 'format=json' GET argument
        if self.request.GET.get('format') == 'json':
            return self.render_to_json_response(context)
        else:
            return super().render_to_response(context)
```

由于Python解决方法重载的方式，对的调用 super().render_to_response(context)最终调用的 render_to_response() 实现TemplateResponseMixin。
