# 装饰器

**01. 装饰器语法糖**



如果你接触 Python 有一段时间了的话，想必你对 @ 符号一定不陌生了，没错 @ 符号就是装饰器的语法糖。

它放在一个函数开始定义的地方，它就像一顶帽子一样戴在这个函数的头上。和这个函数绑定在一起。在我们调用这个函数的时候，第一件事并不是执行这个函数，而是将这个函数做为参数传入它头顶上这顶帽子，这顶帽子我们称之为装饰函数 或 装饰器。



你要问我装饰器可以实现什么功能？我只能说你的脑洞有多大，装饰器就有多强大。



装饰器的使用方法很固定：


* 先定义一个装饰函数（帽子）（也可以用类、偏函数实现）

  * 再定义你的业务函数、或者类（人）

  * 最后把这顶帽子带在这个人头上


装饰器的简单的用法有很多，这里举两个常见的。


* 日志打印器
* 时间计时器


**02. 入门用法：日志打印器**



首先是日志打印器。



它要实现的功能是


* 在函数执行前，先打印一行日志告知一下主人，我要执行函数了。

  * 在函数执行完，也不能拍拍屁股就走人了，咱可是有礼貌的代码，再打印一行日志告知下主人，我执行完啦。





 
# 这是装饰函数

```
def logger(func):

    def wrapper(*args, **kw):

        print('我准备开始计算：{} 函数了:'.format(func.__name__))

        # 真正执行的是这行。

        func(*args, **kw)

        print('啊哈，我计算完啦。给自己加个鸡腿！！')

    return wrapper

```
 

假如，我的业务函数是，计算两个数之和。写好后，直接给它带上帽子。


```

@logger

def add(x, y):

    print('{} + {} = {}'.format(x, y, x+y))

```
 

然后我们来计算一下。


```
    add(200, 50)
```

快来看看输出了什么，神奇不？

> 我准备开始计算：add 函数了:
> 200 + 50 = 250
> 啊哈，我计算完啦。给自己加个鸡腿！


**03. 入门用法：时间计时器**



再来看看 时间计时器


实现功能：顾名思义，就是计算一个函数的执行时长。


  # 这是装饰函数
```
def timer(func):

    def wrapper(*args, **kw):

        t1=time.time()

        # 这是函数真正执行的地方

        func(*args, **kw)

        t2=time.time()

    

        # 计算下时长

        cost_time = t2-t1 

        print("花费时间：{}秒".format(cost_time))

    return wrapper
```


假如，我们的函数是要睡眠10秒。这样也能更好的看出这个计算时长到底靠不靠谱。

```

import time
@timer
def want_sleep(sleep_time):

    time.sleep(sleep_time)

want_sleep(10)

```
 

来看看，输出。真的是10秒。


> 花费时间：10.0073800086975098秒



**04. 进阶用法：带参数的函数装饰器**



通过上面简单的入门，你大概已经感受到了装饰的神奇魅力了。



不过，装饰器的用法远不止如此。我们今天就要把这个知识点学透。



上面的例子，装饰器是不能接收参数的。其用法，只能适用于一些简单的场景。不传参的装饰器，只能对被装饰函数，执行固定逻辑。



如果你有经验，你一定经常在项目中，看到有的装饰器是带有参数的。



装饰器本身是一个函数，既然做为一个函数都不能携带函数，那这个函数的功能就很受限。只能执行固定的逻辑。这无疑是非常不合理的。而如果我们要用到两个内容大体一致，只是某些地方不同的逻辑。不传参的话，我们就要写两个装饰器。小明觉得这不能忍。



那么装饰器如何实现传参呢，会比较复杂，需要两层嵌套。



同样，我们也来举个例子。



我们要在这两个函数的执行的时候，分别根据其国籍，来说出一段打招呼的话。

```

def american():

    print("I am from America.")

def chinese():

    print("我来自中国。")

```

在给他们俩戴上装饰器的时候，就要跟装饰器说，这个人是哪国人，然后装饰器就会做出判断，打出对应的招呼。

戴上帽子后，是这样的。


```
@say_hello("china")

def chinese():

    print("我来自中国。")

@say_hello("america")

def american():

    print("I am from America.")
```



万事俱备，只差帽子了。来定义一下，这里需要两层嵌套。

```
def say_hello(contry):
    def wrapper(func):
        def deco(*args, **kwargs):
            if contry == "china":
                print("你好!")
            elif contry == "america":
                print('hello.')
            else:
                return
        
            # 真正执行函数的地方
            func(*args, **kwargs)
        return deco
    return wrapper
```


执行一下

```
american()
print("------------")
chinese()
```



看看输出结果。


> 你好!
> 我来自中国。
> ------------
> hello.
> I am from America


emmmm，这很NB。。。



**05. 高阶用法：不带参数的类装饰器**



以上都是基于函数实现的装饰器，在阅读别人代码时，还可以时常发现还有基于类实现的装饰器。



基于类装饰器的实现，必须实现 __call__ 和 __init__ 两个内置函数。


* __init__ ：接收被装饰函数

* __call__ ：实现装饰逻辑。




```
class logger(object):
    def __init__(self, func):
        self.func = func
    
    def __call__(self, *args, **kwargs):
        print("[INFO]: the function {func}() is running..."\
        .format(func=self.func.__name__))
        return self.func(*args, **kwargs)
 
@logger
def say(something):
    print("say {}!".format(something))
 
say("hello")

```
 


执行一下，看看输出


> [INFO]: the function say() is running...
> say hello!



**06. 高阶用法：带参数的类装饰器**



上面不带参数的例子，你发现没有，只能打印INFO级别的日志，正常情况下，我们还需要打印DEBUG WARNING等级别的日志。 这就需要给类装饰器传入参数，给这个函数指定级别了。



带参数和不带参数的类装饰器有很大的不同。


* __init__ ：不再接收被装饰函数，而是接收传入参数。

* __call__ ：接收被装饰函数，实现装饰逻辑。



```
class logger(object):
    def __init__(self, level='INFO'):
        self.level = level
    
    def __call__(self, func): # 接受函数
        def wrapper(*args, **kwargs):
        print("[{level}]: the function {func}() is running..."\
            .format(level=self.level, func=func.__name__))
        func(*args, **kwargs)
        return wrapper #返回函数
    
@logger(level='WARNING')
def say(something):
    print("say {}!".format(something))
    
say("hello")
```


我们指定WARNING级别，运行一下，来看看输出。

> [WARNING]: the function say() is running...
> say hello!


**07. 使用偏函数与类实现装饰器**



绝大多数装饰器都是基于函数和闭包实现的，但这并非制造装饰器的唯一方式。



事实上，Python 对某个对象是否能通过装饰器（ @decorator）形式使用只有一个要求：decorator 必须是一个“可被调用（callable）的对象。



对于这个 callable 对象，我们最熟悉的就是函数了。



除函数之外，类也可以是 callable 对象，只要实现了__call__ 函数（上面几个盒子已经接触过了），还有比较少人使用的偏函数也是 callable 对象。



接下来就来说说，如何使用 类和偏函数结合实现一个与众不同的装饰器。



如下所示，DelayFunc 是一个实现了 __call__ 的类，delay 返回一个偏函数，在这里 delay 就可以做为一个装饰器。（以下代码摘自 Python工匠：使用装饰器的小技巧）

```
import time
import functools
 
class DelayFunc:
    def __init__(self, duration, func):
        self.duration = duration
        self.func = func
    
    def __call__(self, *args, **kwargs):
        print(f'Wait for {self.duration} seconds...')
        time.sleep(self.duration)
        return self.func(*args, **kwargs)
    
    def eager_call(self, *args, **kwargs):
        print('Call without delay')
        return self.func(*args, **kwargs)
 
def delay(duration):
    """
    装饰器：推迟某个函数的执行。
    同时提供 .eager_call 方法立即执行
    """
    # 此处为了避免定义额外函数，
    # 直接使用 functools.partial 帮助构造 DelayFunc 实例
    return functools.partial(DelayFunc, duration)
    # 此处为了避免定义额外函数，
    # 直接使用 functools.partial 帮助构造 DelayFunc 实例

    return functools.partial(DelayFunc, duration)

```


我们的业务函数很简单，就是相加
```
@delay(duration=2)
def add(a, b):
    return a+b
```



来看一下执行过程


> add  # 可见 add 变成了 Delay 的实例
<__main__.DelayFunc object at 0x107bd0be0>
> 
> add(3,5) # 直接调用实例，进入 __call__
Wait for 2 seconds...
8
> 
> add.func # 实现实例方法
<function add at 0x107bef1e0>

 

**08. 如何写能装饰类的装饰器？**

用 Python 写单例模式的时候，常用的有三种写法。其中一种，是用装饰器来实现的。

以下便是我自己写的装饰器版的单例写法。

```
instances = {}
 
def singleton(cls):
    def get_instance(*args, **kw):
        cls_name = cls.__name__
        print('===== 1 ====')
        if not cls_name in instances:
        print('===== 2 ====')
        instance = cls(*args, **kw)
        instances[cls_name] = instance
        return instances[cls_name]
    return get_instance
 
@singleton
class User:
    _instance = None
    
    def __init__(self, name):
        print('===== 3 ====')
        self.name = name
```




可以看到我们用singleton 这个装饰函数来装饰 User 这个类。装饰器用在类上，并不是很常见，但只要熟悉装饰器的实现过程，就不难以实现对类的装饰。在上面这个例子中，装饰器就只是实现对类实例的生成的控制而已。




**09. wraps 装饰器有啥用？**



在 functools 标准库中有提供一个 wraps 装饰器，你应该也经常见过，那他有啥用呢？



先来看一个例子

```
def wrapper(func):
    def inner_function():
        pass
    return inner_function
 
@wrapper
def wrapped():
     pass
 
print(wrapped.__name__)
#inner_function
```


为什么会这样子？不是应该返回 func 吗？



这也不难理解，因为上边执行func 和下边 decorator(func)  是等价的，所以上面 func.__name__ 是等价于下面decorator(func).__name__ 的，那当然名字是 inner_function

```
def wrapper(func):
    def inner_function():
        pass
    return inner_function
 
def wrapped():
     pass
 
print(wrapper(wrapped).__name__)
#inner_function
```
  




那如何避免这种情况的产生？方法是使用 functools .wraps 装饰器，它的作用就是将 被修饰的函数(wrapped) 的一些属性值赋值给 修饰器函数(wrapper) ，最终让属性的显示更符合我们的直觉。


```
from functools import wraps
 
def wrapper(func):
    @wraps(func)
    def inner_function():
        pass
    return inner_function
    
@wrapper
def wrapped():
    pass
 
print(wrapped.__name__)
# wrapped
```








准确点说，wraps 其实是一个偏函数对象（partial），源码如下

```

def wraps(wrapped,

    assigned = WRAPPER_ASSIGNMENTS,

    updated = WRAPPER_UPDATES):

return partial(update_wrapper, wrapp=wrapped,assign=assigned, updat=updated)


```






可以看到wraps其实就是调用了一个函数update_wrapper，知道原理后，我们改写上面的代码，在不使用 wraps的情况下，也可以让 wrapped.__name__ 打印出 wrapped，代码如下：


```
from functools import update_wrapper
 
WRAPPER_ASSIGNMENTS = ('__module__', '__name__', '__qualname__', '__doc__',
            '__annotations__')
 
def wrapper(func):
    def inner_function():
        pass
    
    update_wrapper(inner_function, func, assigned=WRAPPER_ASSIGNMENTS)
    return inner_function
 
@wrapper
def wrapped():
    pass
 
print(wrapped.__name__)
```



**10. 内置装饰器：property**



以上，我们介绍的都是自定义的装饰器。



其实Python语言本身也有一些装饰器。比如property这个内建装饰器，我们再熟悉不过了。



它通常存在于类中，可以将一个函数定义成一个属性，属性的值就是该函数return的内容。



通常我们给实例绑定属性是这样的

```
class Student(object):
    def __init__(self, name, age=None):
        self.name = name
        self.age = age
    
# 实例化
XiaoMing = Student("小明")
 
# 添加属性
XiaoMing.age=25
 
# 查询属性
XiaoMing.age
 
# 删除属性
del XiaoMing.age
```


但是稍有经验的开发人员，一下就可以看出，这样直接把属性暴露出去，虽然写起来很简单，但是并不能对属性的值做合法性限制。为了实现这个功能，我们可以这样写。


```
class Student(object):
    def __init__(self, name):
        self.name = name
        self.name = None
    
    def set_age(self, age):
        if not isinstance(age, int):
        raise ValueError('输入不合法：年龄必须为数值!')
        if not 0 < age < 100:
        raise ValueError('输入不合法：年龄范围必须0-100')
        self._age=age
    
    def get_age(self):
        return self._age
    
    def del_age(self):
        self._age = None
    
 
XiaoMing = Student("小明")
 
# 添加属性
XiaoMing.set_age(25)
 
# 查询属性
XiaoMing.get_age()
 
# 删除属性
XiaoMing.del_age()
```





上面的代码设计虽然可以变量的定义，但是可以发现不管是获取还是赋值（通过函数）都和我们平时见到的不一样。

按照我们思维习惯应该是这样的。

```
# 赋值
XiaoMing.age = 25
# 获取
XiaoMing.age
```



那么这样的方式我们如何实现呢。

```
class Student(object):
    def __init__(self, name):
        self.name = name
        self.name = None
    
    @property
    def age(self):
        return self._age
    
    @age.setter
    def age(self, value):
        if not isinstance(value, int):
        raise ValueError('输入不合法：年龄必须为数值!')
        if not 0 < value < 100:
        raise ValueError('输入不合法：年龄范围必须0-100')
        self._age=value
    
    @age.deleter
    def age(self):
        del self._age
 
XiaoMing = Student("小明")
 
# 设置属性
XiaoMing.age = 25
 
# 查询属性
XiaoMing.age
 
# 删除属性
del XiaoMing.age
```





用@property装饰过的函数，会将一个函数定义成一个属性，属性的值就是该函数return的内容。同时，会将这个函数变成另外一个装饰器。就像后面我们使用的@age.setter和@age.deleter。



@age.setter 使得我们可以使用XiaoMing.age = 25这样的方式直接赋值。



@age.deleter 使得我们可以使用del XiaoMing.age这样的方式来删除属性。



property 的底层实现机制是「描述符」
