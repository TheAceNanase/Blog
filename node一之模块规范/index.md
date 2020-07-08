# node(一)之模块规范

相信你也听说过CommonJS, AMD, CMD等概念，但是他们之间又有什么联系和区别呢，本文简要的介绍下这三者，为后面的node学习进行铺垫        




在讲解CommonJS,
 AMD, 
CMD这些概念之前，我们首先俩了解下js的模块化。模块化，顾名思义，就是将项目按照功能或者其他逻辑进行分解处理，每个部分只处理一个功能，进行功能的解耦处理，方便以后的开发和维护。那么模块化必须具有以下的能力，才能进行模块的拆分和组装：

1. 定义封装的模块；
1. 定义新模块对其他模块的依赖；
1. 可对其他模块的引入支持；

那么就需要一套规范准则来定义这些能力，于是就出现了CommonJS, AMD, CMD等。

### 1. CommonJS

CommonJS原先叫做`ServerJS`，是js在服务端的规范，node使用的就是这种规范。根据CommonJS规范，一个单独的文件就是一个模块，`require`用来加载一个模块，`exports`用来向外部暴露该模块里的方法或属性。  

例如：

```
// hello.js
function say(username){
    console.log( 'hello, '+username );
}

exports.say = say;  
```

=============

```
// main.js
var person = require('./hello');

person.say('wenzi'); // hello, wenzi
person.say('师少兵'); // hello, 师少兵
person.say('NUC'); // hello, NUC  
```

同时，`require`语句可以写在文件中的任何位置，只要使用之前引用之前即可，不一定要写在文件的最前面。不过，为了代码更易阅读，能直观地看到当前引用了哪些模块，最好是放在文件的最前面。

#### exports与module.exports的区别

可能有人见过直接使用exports的，有的是使用module.exports的，这里稍微的讲解下这两者的区别。  

先举个简单的例子：

```
var a = {name:'wenzi'};
var b = a;

console.log(a); // {name: "wenzi"}
console.log(b); // {name: "wenzi"}  
```

a和b输出的结果是一样的。现在我改变下b中name的值：

```
b.name = 'shaobing';

console.log(a); // {name: "shaobing"}
console.log(b); // {name: "shaobing"}  
```

a和b的输出结果都发生了改变。我再对b进行重新声明：

```
var b = {name:'师少兵'};

console.log(a); // {name: "shaobing"}
console.log(b); // {name: "师少兵"}  
```

这三个例子输出了三种结果：

1. 声明a对象，并把a赋值给b，然后a和b输出了相同的结果；
1. 改变了b中的name，那么a中的name也跟着改变；
1. 重新声明了b对象，那么a中的name则没有跟着b一起改变

**解释**：a
 是一个对象，b 是对 a 的引用，即 a 和 b 指向同一块内存，所以1中的输出是一样的。当对 b 作修改时，即 a 和 b 
指向同一块内存地址的内容发生了改变，a 也会体现出来，所以第2个例子输出也一样。当 b 被覆盖时，b 指向了一块新的内存，a 
还是指向原来的内存，所以最后输出会不一样。  

那么此时就可以引出`exports`和`module.exports`了：

1. module.exports 初始值为一个空对象 {}
1. exports 是指向的 module.exports 的引用
1. require() 返回的是 module.exports 而不是 exports

如果module.exports发生了新指向，则exports无效；若module.exports没有发生变化，则直接exports即可。

### 2. AMD与RequireJS

说到AMD，不得不说到`RequireJS`，AMD从CommonJS社区独立出来，单独成为了AMD社区，AMD的流行，很大程度上也是依托了RequireJS作者的推广。  

AMD规范中，默认推荐的模块格式是：

```
// hello.js
// 将需要引入的模块全部写入到数组中，然后传递参数进行调用
define(['a', 'b'], function(a, ,b){
    // do something

    return{
        hello : function(username){
            console.log( 'hello, '+username );
        }
    }
})
```

==========

```
// main.js
define(['./hello'], function(h){
    h.hello('wenzi');
})
```

也就是说，在AMD中，模块必须使用`define`定义，依赖通过函数参数传进来，这样的一个好处就是所有的依赖都能一目了然。

### 3. CMD与seajs

CMD规范是国内著名的玉伯大神提出来的，将就的就是`就近依赖`，什么时候用到，就在那个地方进行`require`。SeaJS就是使用的CMD规范：

```
// hello.js
define(function(require, exports, module){
    var a = require('a');
    // do a

    var b = require( 'b' );
    // do b

    module.exports.hello = hello; // 对外输出hello
})
```

从这里也能看到AMD和CMD的区别：

> AMD通常需要一次性引入全部的依赖，然后通过参数传递；而CMD则需要时才引入

不过，AMD也支持CMD这样的引入格式，但内部还是按照AMD的逻辑进行执行。

### 4. 总结

这篇文章里介绍了下CommonJS, AMD, CMD规范的相关区别与联系，这里再简要的总结下：

1. CommonJS： 每个文件就是一个模块，不用define进行定义，node使用此规范；
1. AMD： 使用define定义一个模块，讲究**提前依赖**；
1. CMD： 使用define定义模块，将就**就近依赖**

