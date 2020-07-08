# node(六)之express初识

### 1. 介绍

什么是express，为什么要使用express？根据官方网站的说法，express是一个基于 Node.js
 
平台的极简、灵活的web应用开发框架，它提供一系列强大的特性、丰富的API接口，对web应用的接口进行了二次的封装，提供了MVC模式，方便我们可以快速地创建各种web和移动应用。
  

Express 框架核心特性：

* 可以设置中间件来响应 HTTP 请求。
* 定义了路由表用于执行不同的 HTTP 请求动作。
* 可以通过向模板传递参数来动态渲染 HTML 页面。

本文也只是简单的了解下express框架的内容，希望大家能比较快速的入门，更多详细的内容还是阅读官网并查看相关的API。  

express的中文官方网站：【[Express](http://www.expressjs.com.cn)】

### 2. 入门

创建一个目录**myapp**，进入到myapp后，使用命令`npm express --save-dev`把express安装到本地，然后创建app.js（或server.js）作为程序的入口。

```
// app.js
var express = require('express');
var app = express();

app.get('/', function(req, res){
  res.send('hello world');
});

app.listen(3000, function(){
  console.log('server has running at port 3000');
})
```

运行app.js文件：

```
$ node app.js
server has running at port 3000
```

在浏览器中访问`http://127.0.0.1:3000/`就能看到页面上输出的hello world。说明基本的express程序可以正常运行了。

#### 2.1 app

引入express模块后，执行`express()`得到一个app实例，app实例中有get, post, use, listen等方法。  

app.get(path,
 handler)： 
当使用get方法访问路径path时，执行handler指定的方法，而且handler方法还带有req和res两个参数供我们使用。req是请求过来时带的信息，比如参数query,
 body, 头部header等； res是我们作为服务器想要返回给浏览器的信息设置。res.send('hello 
world')表示是向页面中发送'hello world'字符串。  

当然，如果想要接收post过来的请求，可以使用 app.post(path, function(req, res){}) 接收post到path的请求。  

app.listen用来监听本地的端口后运行web程序，监听成功后执行回调函数。

#### 2.2 路由

我们在之前讲解《[从0到1学习node(二)之搭建http服务器](https://www.xiabingbao.com/node/2017/01/12/node-http-server.html)》也说过一点路由的内容，不过那时候我们制定的路由规则非常简单，而且只是处理了3个左右的页面而已。而express则对路由功能进行丰富。

* app.get(path, handler) : get方式访问path路径
* app.post(path, handler) : post方式访问path路径
* app.put(path, handler) : put方式访问path路径
* app.delete(path, handler) : delete方式访问path路径
* app.all(path, handler) : 任何方式访问path路径

同时，我们也应该注意的是： `/`是表示根路径下，`/user`是表示user路径下，如果访问`/user/login`时，是直接访问`/user/login`路由的，前面的两个路由是不访问的。

```
// 根路径下的请求
app.get('/', function(req, res){
    console.log('hello world');
    res.send('hello world');
});

// /user路径下的请求
app.get('/user', function(req, res){
     console.log('user');
    res.send('huser');
});

// /user/login下的请求
app.get('/user/login', function(req, res){
    console.log('user/login');
    res.send('user/login');
});
```

而且，path路径还可以通过字符串匹配和正则匹配的方式进行路由选择。

#### 2.3 res响应方法

我们在刚上面的例子中，使用`res.send()`向页面中输出一段'hello world'的纯文本字符串，而且`res.send()`也可以输出其他类型的数据，比如html字符串（浏览器可以解析），Buffer类型，Object类型，Array类型等。  

比如我们要输出一段html字符串。

```
var html = '<!DOCTYPE html>\
            <html lang="en">\
                <head>\
                    <meta charset="UTF-8" />\
                    <title>Document</title>\
                </head>\
                <body>\
                    <div>\
                        <p style="color:#f00;">hello world</p>\
                        <p><input type="text" /></p>\
                    </div>\
                </body>\
            </html>';

app.get('/', function(req, res, next){
    res.send(html);
});
```

我们可以在浏览器上一个红色的hello world和一个文本输入框。但是若html的代码比较长，我们可以把这些代码都放到一个单独的html文件里，然后使用`res.sendFile()`方法，将html文件里的内容输出到页面中。 

在根目录下创建一个index.html文件，把完整的html代码放进去，然后：

```
app.get('/', function(req, res, next){
    res.sendFile('index.html');
});
```

这样就能在浏览器中看到一个完整的页面了。  

此外，res中还提供了一些别的方法供我们使用：

| 方法 | 描述 |
| - | - |
| res.download() | 下载文件。 |
| res.end() | 终结响应处理流程。 |
| res.json() | 发送一个 JSON 格式的响应。 |
| res.jsonp() | 发送一个支持 JSONP 的 JSON 格式的响应。 |
| res.redirect() | 重定向请求。 |
| res.render() | 渲染视图模板。 |  |
| res.send() | 发送各种类型的响应。 |
| res.sendFile | 以八位字节流的形式发送文件。 |
| res.sendStatus() | 设置响应状态代码，并将其以字符串形式作为响应体的一部分发送。 |

### 3. 中间件

上面我们执行`app.get('/', function(){})`时，里面的回调函数就是中间件。中间件其实就是一个函数，在使用`app.get`, `app.post`, `app.use`等方法时，都是在调用中间件作为回调函数。 中间件都可以调用req和res对象，如果多个中间件顺序向下执行的话，上一个中间还需要一个next变量，来调用下一个中间件。   

这里`app.use`的使用方法与`app.get`一样，都是有两个参数：path和回调函数，而在这里，path参数是可以忽略不写的（忽略不写则每个请求都会执行该中间件）。

```
// 任何的请求，该中间件都会响应
app.use(function(req, res, next){
    console.log('index m url: '+req.url);
    next(); // 若没有next()，则请求就会被挂起，一直等待
})

// /topic 下的请求都会响应，包括 /topic/1.html, /topic/c/1.html等
app.use('/topic', function(req, res, next){
    console.log('topic m url: '+req.url);
    next();
})

// 处理/根目录下的请求
app.get('/', function(req, res, next){
    res.send('index');
});

// 处理 /topic/1.html 这种类型的请求
app.get('/topic/:id.html', function(req, res, next){
    res.send('topic');
});
```

我们在浏览器中输入一些不同的url看看：

| url | 控制台输出 | 浏览器输出 | 说明 |
| - | - | - | - |
| 127.0.0.1:3000 | index m url: / | index |  |
| /user | index m url: /user | Cannot GET /user | 中间件响应了不存在页面的请求 |
| /topic/1.html | 
                index m url: /topic/1.html<br/>
                topic m url: /1.html
             | 
                topic
             | 
                两个use中间件都响应了请求
             |
| /topic/c/1.html | 
                index m url: /topic/c/1.html<br/>
                topic m url: /c/1.html
             | 
                Cannot GET /topic/c/1.html
             | 
                两个use中间件都响应了请求，只是没有路由来对该url进行处理
             |

同时，`app.use()`和`app.get()`等方法，可以调用多个中间件依次执行，使用`next()`将控制权交由下一个中间件。多个中间件既可以依次作为传输传递进去，也可以都放到数组中，也可以两者混用（app.get等同理）：

```
app.use(path, m1, m2, m3, m4...);
app.use(path, [m1, m2, m3, ...]);
app.use(path, [m1, m2, m3, ...], m7, m8, ...);
```

在上面代码的基础上，我们编写多个中间件。

```
// 作为数组方式
app.use([
    function(req, res, next){
        console.log('index m 1');
        next();
    }, function(req, res, next){
        console.log('index m 2');
        next();
    }, function(req, res, next){
        console.log('index m 3');
        next();
    }
])

// 每个中间件作为一个参数
app.get('/topic/:id.html', function(req, res, next){
    // res.send('topic');
    console.log('topic get 1');
    next();
}, function(req, res, next){
    console.log('topic get 2');
    next();
}, function(req, res, next){
    console.log('topic get 3');
    res.send('topic');
});
```

当我们访问`127.0.0.1/topic/1.html`时，在控制台则会输出：

```
index m 1
index m 2
index m 3

topic get 1
topic get 2
topic get 3
```

说明中间件是依次向下执行的。我们可以在每个中间件都做不同的处理，不过要记得使用`next()`方法，不然页面就挂了。

我们在上面看到`res`中的方法，至少需要调用一个，不然请求就会被挂起，一直等待或404。如果对外没有任何的回复，也可以使用`res.end()`结束。同时，如果在某个中间件中使用了res中的方法，则后面的中间件不再调用。

