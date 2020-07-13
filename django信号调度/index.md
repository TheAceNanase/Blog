# Django信号调度

## Django信号调度
Django中提供了"信号调度",用于在框架执行操作时解耦.

一些动作发生的时候,系统会根据信号定义的函数执行相应的操作	

django自带一套信号机制来帮助我们在框架的不同位置之间传递信息。也就是说，当某一事件发生时，信号系统可以允许一个或多个发送者（senders）将通知或信号（signals）发送给一组接受者（receivers）。

### 信号系统包含以下三要素：

- 发送者－信号的发出方
- 信号－信号本身
- 接收者－信号的接受者

### Django内置了一整套信号，下面是一些比较常用的：

- django.db.models.signals.pre_save & django.db.models.signals.post_save

在ORM模型的save()方法调用之前或之后发送信号

- django.db.models.signals.pre_delete & django.db.models.signals.post_delete

在ORM模型或查询集的delete()方法调用之前或之后发送信号。

- django.db.models.signals.m2m_changed

当多对多字段被修改时发送信号。

- django.core.signals.request_started & django.core.signals.request_finished

当接收和关闭HTTP请求时发送信号。

### 监听信号

要接收信号，请使用Signal.connect()方法注册一个接收器。当信号发送后，会调用这个接收器。

方法原型：

Signal.connect(receiver, sender=None, weak=True, dispatch_uid=None)[source]

参数：

receiver ：当前信号连接的回调函数，也就是处理信号的函数。  sender ：指定从哪个发送方接收信号。  weak ： 是否弱引用 dispatch_uid ：信号接收器的唯一标识符，以防信号多次发送。

下面以如何接收每次HTTP请求结束后发送的信号为例，连接到Django内置的现成的request_finished信号。

1. 编写接收器

接收器其实就是一个Python函数或者方法：

def my_callback(sender, **kwargs):    print("Request finished!")

请注意，所有的接收器都必须接收一个sender参数和一个**kwargs通配符参数。

2. 连接接收器

有两种方法可以连接接收器，一种是下面的手动方式：

from django.core.signals import request_finished request_finished.connect(my_callback)

另一种是使用receiver()装饰器：

from django.core.signals import request_finished from django.dispatch import receiver @receiver(request_finished) def my_callback(sender, **kwargs):    print("Request finished!")

3. 接收特定发送者的信号

一个信号接收器，通常不需要接收所有的信号，只需要接收特定发送者发来的信号，所以需要在sender参数中，指定发送方。下面的例子，只接收MyModel模型的实例保存前的信号。

from django.db.models.signals import pre_save from django.dispatch import receiver from myapp.models import MyModel @receiver(pre_save, sender=MyModel) def my_handler(sender, **kwargs):    ...

my_handler函数只在MyModel实例保存时被调用。

4. 防止重复信号

为了防止重复信号，可以设置dispatch_uid参数来标识你的接收器，标识符通常是一个字符串，如下所示：

from django.core.signals import request_finished request_finished.connect(my_callback, dispatch_uid="my_unique_identifier")

最后的结果是，对于每个唯一的dispatch_uid值，你的接收器都只绑定到信号一次。

### 自定义信号

除了Django为我们提供的内置信号（比如前面列举的那些），很多时候，我们需要自己定义信号。

类原型：class Signal(providing_args=list)[source]

所有的信号都是django.dispatch.Signal的实例。providing_args参数是一个列表，由信号将提供给监听者的参数的名称组成。可以在任何时候修改providing_args参数列表。

下面定义了一个新信号：

import django.dispatch pizza_done = django.dispatch.Signal(providing_args=["toppings", "size"])

上面的例子定义了pizza_done信号，它向接受者提供size和toppings 参数。

### 发送信号

Django中有两种方法用于发送信号。

Signal.send(sender, **kwargs)[source]  Signal.send_robust（sender， **kwargs）[source] 

必须提供sender参数（大部分情况下是一个类名），并且可以提供任意数量的其他关键字参数。

例如，这样来发送前面的pizza_done信号：

class PizzaStore(object):    ...     def send_pizza(self, toppings, size):        pizza_done.send(sender=self.__class__, toppings=toppings, size=size)        ...

send()和send_robust()返回一个元组对的列表[（receiver, response）， ... ]，表示接收器和响应值二元元组的列表。

### 断开信号

方法：

Signal.disconnect(receiver=None, sender=None, dispatch_uid=None)[source]

Signal.disconnect()用来断开信号的接收器。和Signal.connect()中的参数相同。如果接收器成功断开，返回True，否则返回False。
