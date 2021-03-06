# 协程

### 协程，又称微线程，纤程。英文名Coroutine。一句话说明什么是线程：协程是一种用户态的轻量级线程。


* 协程拥有自己的寄存器上下文和栈。协程调度切换时，将寄存器上下文和栈保存到其他地方，在切回来的时候，恢复先前保存的寄存器上下文和栈。因此：
* 协程能保留上一次调用时的状态（即所有局部状态的一个特定组合），每次过程重入时，就相当于进入上一次调用的状态，换种说法：进入上一次离开时所处逻辑流的位置。
* 协程最主要的作用是在单线程的条件下实现并发的效果，但实际上还是串行的（像yield一样）


### 协程的好处：

1. 无需线程上下文切换的开销
1. 无需原子操作锁定及同步的开销
1. 方便切换控制流，简化编程模型

高并发+高扩展性+低成本：一个CPU支持上万的协程都不是问题。所以很适合用于高并发处理。

### 缺点：

1. 无法利用多核资源：协程的本质是个单线程,它不能同时将 单个CPU 的多个核用上,协程需要和进程配合才能运行在多CPU上.当然我们日常所编写的绝大部分应用都没有这个必要，除非是cpu密集型应用。
1. 进行阻塞（Blocking）操作（如IO时）会阻塞掉整个程序

### 协程为何能处理大并发1：Greenlet遇到I/O手动切换
* 协程之所以快是因为遇到I/O操作就切换（最后只有CPU运算）
* greenlet实现手动的对各个协程之间切换
* 其实Gevent模块仅仅是对greenlet的再封装，将I/O间的手动切换变成自动切换

### 协程为何能处理大并发2：Gevent遇到I/O自动切换
* Gevent 是一个第三方库，可以轻松通过gevent实现并发同步或异步编程
* 在gevent中用到的主要模式是Greenlet, 它是以C扩展模块形式接入Python的轻量级协程
* Greenlet全部运行在主程序操作系统进程的内部，但它们被协作式地调度。
* Gevent原理是只要遇到I/O操作就会自动切换到下一个协程

### 使用协程处理并发
注：Gevent只用起一个线程，当请求发出去后gevent就不管,永远就只有一个线程工作，谁先回来先处理

