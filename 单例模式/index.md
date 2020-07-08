# 单例模式



## 意图Intent
Singleton Pattern：Ensure a class has only one instance, and provide a global point of access to it. 单例模式：确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例。

****

## 适用场景
* 系统只需要一个实例对象，如系统要求提供一个唯一的序列号生成器或资源管理器，或者需要考虑资源消耗太大而只允许创建一个对象。
* 客户调用类的单个实例只允许使用一个公共访问点，除了该公共访问点，不能通过其他途径访问该实例。

****

## Show me the code

单例模式的实现有多种方式，而且根据是否线程安全又可以定出多种组合，先来看一种方式，它在 『Java 与模式』中被称为饿汉式。

```
public class Singleton {
    // 在自己内部定义自己一个实例
    // 注意这是 private 只供内部调用
    private static final Singleton instance = new Singleton();
    // 如上面所述，将构造函数设置为私有
    private Singleton() {
    }
    // 静态工厂方法，提供了一个供外部访问得到对象的静态方法
    public static Singleton getInstance() {
        return instance;
    }
}

```
在上面的代码中，我们将Singleton的实例被声明成 static 和 final 变量了，在第一次加载类到内存中时就会初始化，所以创建实例本身是线程安全的。但是这种写法有个缺点是它不是一种懒加载模式（lazy initialization），单例会在加载类后一开始就被初始化，即使客户端没有调用 getInstance()方法。
****
下面这种方式被称为懒汉式：

```
public class Singleton {
    private static Singleton instance = null;
    // 设置为私有的构造函数
    private Singleton() {
    }
    // 静态工厂方法
    public static synchronized Singleton getInstance() {
        // 这个方法比上面有所改进
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```
****
在上面的懒汉式中我们虽然使用了懒加载方式，而且加上了synchronized关键字来保证线程安全，但是它会导致代码效率低下，因为synchronized在任何时候只能有一个线程调用 getInstance() 方法。所有就又有了一种相对高效的双重检验锁模式：
```
public class Singleton {
    //volatile 关键字:被volatile修饰的变量的值,不会被本地线程缓存,所有对该变量的读写都是直接操作内存,从而确保多个线程能正确处理该变量
    private volatile static Singleton instance = null;
    // 设置为私有的构造函数
    private Singleton() {
    }
public static Singleton getInstance() {
    if (instance == null) {                         //Single Checked
        synchronized (Singleton.class) {
            if (instance == null) {                 //Double Checked
                instance = new Singleton();
            }
        }
    }
    return instance ;
}
}
```

双重检查加锁机制在进入getInstance()方法之后，先检查实例是否存在，如果不存在才进入下面的同步块，这是第一重检查。进入同步块过后，再次检查实例是否存在，如果不存在，就在同步的情况下创建一个实例，这是第二重检查。我们在声明Singleton实例时添加了Singleton关键字，被volatile修饰的变量的值，将不会被本地线程缓存，所有对该变量的读写都是直接操作共享内存,从而确保多个线程能正确的处理该变量。
****
除了上面的懒汉式、饿汉式外还有一种静态内部类 static nested class的单例模式，这种方法也是《Effective Java》上所推荐的。
```
public class Singleton {  
    private static class SingletonHolder {  
        private static final Singleton INSTANCE = new Singleton();  
    }  
    private Singleton (){}  
    public static final Singleton getInstance() {  
        return SingletonHolder.INSTANCE;
    }  
}
```

****
## 优点

* 由于在系统内存中只存在一个对象，因此可以节约系统资源，对于一些需要频繁创建和销毁的对象，单例模式无疑可以提高系统的性能。
* 单例模式提供了对唯一实例的受控访问。因为单例类封装了它的唯一实例，所以它可以严格控制客户怎样以及何时访问它。

## 缺点

* 由于单例模式中没有抽象层，因此单例类的扩展有很大的困难。

## 总结

* 单例模式确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例，这个类称为单例类，它提供全局访问的方法。单例模式的要点有三个：一是某个类只能有一个实例；二是它必须自行创建这个实例；三是它必须自行向整个系统提供这个实例。单例模式是一种对象创建型模式。
* 单例模式只包含一个单例角色：在单例类的内部实现只生成一个实例，同时它提供一个静态的工厂方法，让客户可以使用它的唯一实例；为了防止在外部对其实例化，将其构造函数设计为私有。
* 单例模式的目的是保证一个类仅有一个实例，并提供一个访问它的全局访问点。单例类拥有一个私有构造函数，确保用户无法通过new关键字直接实例化它。除此之外，该模式中包含一个静态私有成员变量与静态公有的工厂方法。该工厂方法负责检验实例的存在性并实例化自己，然后存储在静态成员变量中，以确保只有一个实例被创建。
* 单例模式的主要优点在于提供了对唯一实例的受控访问并可以节约系统资源；其主要缺点在于因为缺少抽象层而难以扩展，且单例类职责过重。
* 单例模式适用情况包括：系统只需要一个实例对象；客户调用类的单个实例只允许使用一个公共访问点。
