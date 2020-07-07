# 访问限制


Python的Class机制采用双下划线开头的方式将属性隐藏起来（设置成私有的），但其实这仅仅只是一种变形操作，类中所有的双下划线开头的属性都会在类定义阶段，检测语法时自动变成`_类型__属性名`的形式：



* 这种隐藏只是一种语法上变形操作，并不会将属性真正隐藏起来
* 这种语法级别的变形，是在定义阶段发生的，并且只在定义阶段发生，在定义类之后的赋值操作，不会变形
* 在子类定义的`__x`不会覆盖在父类定义的`__x`，因为子类中变形成了：`_子类名__x`,而父类中变形成了：`_父类名__x`，即双下滑线开头的属性在继承给子类时，子类是无法覆盖的。



```
class A:
    def __func(self): # _A__func(self)
        print('from A.__func')

    def test(self): # self ---> b_obj
        # b_obj.__func()
        self.__func() # self._A__func()
        # 此处执行的b_obj.__func()其实是在执行b_obj._A_fun()


class B(A):

    def __func(self): # self._B__func(self)
        print('from B.__func')


b_obj = B()
b_obj.test()

> from A.__func
```




## 一、如何实现



```
class User:
    # __开头的属性
    __name = 'tank'  # __name变形为：_类名__name

    # __开头的方法
    def __run(self):
        print('tank is running....')


obj = User()
print(obj.__name)
# 报错，没有找到
NameError: name '__name' is not defined

print(obj._User__name)
# 没有报错
tank
```




## 二、什么是**访问限制**机制



凡是在内部定义的以__开头的属性和方法名，都会被限制，外部不能直接访问改属性



>
>   注意: 凡是在类内部定义__开头的属性或方法，都会变形为 _类名__属性/方法。(python特有的)
>



## 三、为什么要有**访问限制**



比如：将一些隐私的数据，隐藏起来，不让外部轻易获取



* 接口
  * 可以将一堆数据封装成一个接口，可以让用户去调用接口
  * 并且通过相应的逻辑，最后再将数据返回个用户



范例代码：



```
class User:
    __name = 'tank'
    __age = 17
    __sex = 'male'
    __ID = '234245353425345'
    __balance = '1132432'

    # 校验接口，获取用户信息
    def parse_user(self, username, password):
        if username == 'tank_jam' and password == '123':
            print(f'''
            通过验证，获取用户信息
            姓名：{self.__name}
            年龄：{self.__age}
            性别：{self.__sex}
            身份证ID：{self.__ID}
            用户资产：{self.__balance}
            ''')
        else:
            print('校验失败，无法查询用户信息！')


obj = User()
obj.parse_user('tank_jam', '123')
```

