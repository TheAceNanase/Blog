# 递归函数

# 什么是递归？

    在数学与见算计科学中，是指在函数的定义中使用函数自身的方法。
    递归算法就是一种直接或者间接的调用自身函数或者方法的算法
    递归算法的实质是把问题分解规模小的同类问题的子问题
    然后调用自身方法来解

# 递归的基本原理

    每一级的函数调用都有自己的变量
    每一次函数调用都会有一次返回
    递归函数中，位于递归调用前的语句和各级被调用函数具有相同的执行顺序
    递归函数中，位于递归调用后的语句的执行顺序和各个被调用函数的顺序相反
    虽然每一级递归都有自己的变量，但是函数的代码不会得到复制

# 递归的优缺点

* 优点

    实现简单
    可读性好

* 缺点

    递归调用，占用空间大
    递归太深，容易发生栈溢出
    可能存在重复计算
    最大递归深度为 998，下文会解决最大递归深度

* 递归的三大要素

    明确函数要做什么
    寻找递归结束条件
    找出函数的等价关系式



* 解决最大递归深度

import sys
```
sys.setrecursionlimit(3000)
```

* 高斯求和
```
def count_number(n):
    if n <= 0:
        return 0
    return n + count_number(n-1)
  
def count_Number2(n):
  sum = 0
  for i in n:
    sum += i
  return sum
```

* 斐波那契
有关递归应用的应用有很多，例如注明的斐波拉契数列就可以通过递归来实现:
```
def fib(x):
    if x < 2:
        return 0 if x == 0 else 1
    # 当x > 2时，开始递归调用fib()函数:
    return fib(x - 1) + fib(x - 2)

print(fib(6))  # 打印结果为:8
```
这里需要对i<2时的特殊情况做出判断，当x==0时，直接返回0，当x==1时，直接返回1

* 青蛙跳台阶
一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。
```
fib = lambda n: n if n < 2 else 2 * fib(n - 1)
```

*  遍历文件
首先我们需要了解遍历的算法:定义的file_display函数以某个目录(/home/pushy)作为遍历的起点.遇到一个子目录时，就先接着遍历子目录(递归调用函数)。遇到一个文件时，就直接对改文件进行操作(这里只打印出文件的文件名):
```
import os

def file_display(filepath):
	for each in os.listdir(filepath):
    	# 得到文件的绝对路径:
		absolute_path = os.path.join(filepath， each)
        # 得到是否为文件还是目录的布尔值:
		is_file = os.path.isfile(absolute_path)
		if is_file:
        	# 当前的绝对路径为文件:
			print(each)
		else:
        	# 当前的绝对路径为目录:
			file_display(absolute_path)

file_display('C:\Users\nanase\Desktop')

```
这样我们就可以遍历到C:\Users\nanase\Desktop路径下的所有文件.

递归就是不断的调用自己示例：

```python
import sys
# print(sys.getrecursionlimit())  # 默认最大递归限制：1000

# sys.setrecursionlimit(999999999)  # 不管数值多大，最多到20963册

def recursion(n):

    print(n)

    recursion(n + 1)

recursion(1)递归与栈的关系

```

 <br />在计算机中，函数调用时通过栈(stack)这种数据结构实现的，每当进入一个函数调用，栈就会加一层栈帧，每当函数返回，栈就会减一层栈帧。 

 由于栈的大小不是无限的，所以，递归调用的次数过多，就会导致栈溢出。<br /><br /> 

递归的特点：

<ol>


- 必须有一个明确的结束条件，要不就会变成死循环了，最终撑爆系统

- 每次进入更深一层递归时，问题规模相比上次递归都应有所减少

- 递归执行效率不高，递归层次过多会导致栈溢出

</ol>

```python
def zero(n):

    n = n // 2

    print(n)

    if n == 0:

        return 'Done'

    zero(n)

    print(n)  # 1 2 5





'''

第一层会n=5，第二层会n=2，第三层会n=1，第四层n=0,符合条件程序停止。停止之后，程序的控制权会回到第三层调用第四层的位置，也就是zero(n)，然后print出1，然后回到第二层print出2,最后回到第一层print出5。

整个程序是先一层层进去，然后在一层层出来。

'''

zero(10)  # 5 2 1 0 1 2 5

```



阶乘<br />n! = n * (n-1)

```python
def factorial(num):

    if num == 1:

        return 1

    return num * factorial(num - 1)

print(factorial(10))

```


尾递归优化

执行完一层调用下一层的时候，把这一层的数据给销毁掉,并且下一层和这一层没有关系，叫尾递归。<br />阶乘不是尾递归  return num * factorial(num - 1) 因为num还在等着factorial(num - 1)的结果。

```python
def cal(n):

    print(n)

    return cal(n + 1)  # 虽然这用的是尾递归优化，但是python不支持尾递归优化，C语言和JS支持。

cal(1)

```




