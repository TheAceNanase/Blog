# node(七)之express搭建简易论坛

### 1. 应用生成器
我们可以从0开始，一步步把系统搭建起来。不过express中还有一个`应用生成器`，使用这个应用生成器可以快速的创建一个应用的框架，然后我们再在这个框架中完善我们需要的内容。  

首先安装应用生成器：

```
$npm install -g express-generator  
```

运行`express --version`若能正常输出版本号，则安装成功。  

我们的论坛名称可以为`node_express_forum`，然后使用express创建一个框架：

```
$express node_express_forum
```

运行后，生成器会在这个目录下生成几个目录和文件：

```
 create : node_express_forum                
 create : node_express_forum/package.json   
 create : node_express_forum/app.js         
 create : node_express_forum/public         
 create : node_express_forum/public/javascri
 create : node_express_forum/public/images  
 create : node_express_forum/public/styleshe
 create : node_express_forum/public/styleshe
 create : node_express_forum/routes         
 create : node_express_forum/routes/index.js
 create : node_express_forum/routes/users.js
 create : node_express_forum/views          
 create : node_express_forum/views/index.jad
 create : node_express_forum/views/layout.ja
 create : node_express_forum/views/error.jad
 create : node_express_forum/bin            
 create : node_express_forum/bin/www        
                                           
 install dependencies:                     
   $ cd node_express_forum && npm install   
                                           
 run the app:                              
   $ DEBUG=node-express-form:* npm start  
```

已经生成成功。进入到这个目录：

```
$cd node_express_forum
```

我们来看下生成的这个框架，方便知道后面怎么进行填充。

```
.
├── app.js 
├── package.json  // 依赖的模块
├── bin
│   └── www
├── public  // 静态文件目录
│   ├── images
│   ├── javascripts
│   └── stylesheets
│       └── style.css
├── routes  // 路由，相当于控制器
│   ├── index.js
│   └── users.js
└── views  // 视图
    ├── error.jade
    ├── index.jade
    └── layout.jade
```

打开`package.json`后，我们看到还需要再安装几个模块才能运行：

```
$npm install --save-dev
```

好了，到现在框架已搭建完毕，我们来运行一下：

```
$npm start
```

然后在浏览器中输入`127.0.0.1:3000`，就可以看到了：Express Welcome to Express。

### 2. 准备工作

基本框架已经创建好了，现在开始我们论坛的准备工作。这里我们的准备工作有3个：模板引擎，模型，数据库，路由。

#### 2.1 模板引擎

express里默认使用的模板引擎是`jade`，不过我们也可以选择其他的模板引擎，我这里选择了`ejs`，因为感觉ejs更像是个html文件，方便维护，当然，每个人都有自己的喜好。

```
$npm install ejs --save-dev  
```

然后在`app.js`中修改模板引擎：

```
app.set('view engine', 'ejs'); // 原为jade，现改为ejs
```

这里我会将`views`目录中的.jade文件清空，后续使用.ejs编写模板。

#### 2.2 模型

这里我们说的模型是指`MVC`中的M，主要是进行数据库的连接和操作。创建`models`目录用来存放文件。

#### 2.3 数据库

我们使用mysql数据库来存放数据，数据库名称可以叫做`forum`。里面有3张表：user, list, reply。

* user表（用户）的字段有： id, username, password, regtime
* list表（主题）的字段有： id, uid(用户id), title, content, createtime
* reply表（回复）的字段有： id, pid(主题id), uid(用户id), content, createtime

#### 2.4 路由

上节我们是使用`app.use()`或`app.get()`等方式来实现路由，同时，express还提供了express.Router类来创建模块化。可挂载的路由。Router 实例是一个完整的中间件和路由系统，因此常称其为一个 “mini-app”。

在`routes/user.js`中（这里我将其users.js改为了user.js）：

```
var express = require('express');
var router = express.Router(); // 实例化router

// 定义主页的路由
router.get('/', function(req, res, next) {
     res.render('index', { title: 'user' }); // 加载index.ejs模板并传递数据给模板
});

router.get('/reg', function(req, res, next) {
      res.render('index', { title: 'reg' });
});

module.exports = router;  
```

然后在`app.js`中加载路由模块：

```
var user = require('./routes/user');
//...
app.use('/user', user);
```

这样就可以访问`/user`和`/user/reg`页面了。如果需要增加其他的路由，则依照此方式创建添加即可。

### 3. 注册与登录

我们论坛的功能：注册、登录、发布主题和回复主题。这4个功能的实现都需要连接数据库。

#### 3.1 数据库连接

引入mysql模块，然后使用`mysql.createPool()`创建连接：

```
$npm install mysql --save-dev
```

在models目录下创建db.js，其他需要操作数据库的，首先引入db.js:

```
var mysql = require('mysql');

var pool = mysql.createPool({
    host : '127.0.0.1',
    user : 'root',
    password : '123',
    database : 'forum'
});

module.exports = pool;  
```

#### 3.2 注册功能

注册功能的流程我们非常熟悉了：

1. 加载注册页面；
1. 用户输入数据后提交；
1. 处理表单数据然后进行数据库操作

我们在`routes/user.js`中创建一个`reg`的get方式的路由用来加载注册页面：

```
// routes/user.js

// get方式
router.get('/reg', function(req, res, next) {
      res.render('reg', { errmsg:'' }); // 加载reg.ejs模板
});
```

在`views`目录下创建`reg.ejs`：

```
<!DOCTYPE html>
<html>
  <head>
    <title>注册</title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
    <style type="text/css">
      .tip{color: #f00;}
    </style>
  </head>
  <body>
  <div class="container">
    <p><a href="/">回到首页</a></p>
    <h1>注册</h1>
    <form action="/user/reg/" method="post">
      <% if(errmsg){ %>
      <p class="tip">*<%= errmsg %></p>
      <% } %>
      <p>用&nbsp;&nbsp;户&nbsp;&nbsp;名： <input type="text" name="username" required="required"></p>
      <p>密&emsp;&emsp;码： <input type="password" name="password" required="required"></p>
      <p>重复密码： <input type="password" name="password2" required="required"></p>
      <p><input type="submit" name="submit" value="submit"></p>
    </form>
    <p>已有帐号？ <a href="/user/login">点击登录</a></p>
  </div>
  </body>
</html>
```

运行程序，并用浏览器访问`127.0.0.1:3000/user/reg`，注册页面就出来了。  

然后再在`routes/user.js`中创建一个`reg`的post方式的路由用来处理提交过来的数据，post方式过来的数据并不能使用req.query变量获取，而应该使用`req.body`：

```
// routes/user.js

// post方式
router.post('/reg', function(req, res, next) {
      var username = req.body.username || '',
        password = req.body.password || '',
        password2 = req.body.password2 || '';

    if(password!=password2){
        res.render('reg', {errmsg:'密码不一致'});
        return;
    }
    var password_hash = user_m.hash(password), // 对密码进行加密
        regtime = parseInt(Date.now()/1000);

    // 数据库处理
});
```

凡是设计到数据库处理的，我们都将其放到models中。这里，我们在`models`中创建一个`user.js`：

```
// models/user.js

var pool = require('./db'), // 连接数据库
    crypto = require('crypto'); // 对密码进行加密

module.exports = {
    // 对字符串进行sha1加密
    hash : function(str){
        return crypto.createHmac('sha1', str).update('love').digest('hex');
    },

    // 注册
    // 因数据库操作是异步操作，则需要传入回调函数来对结果进行处理，而不能使用return的方式
    reg : function(username, password, regtime, cb){
        pool.getConnection(function(err, connection){
            if(err) throw err;

            // 首先检测用户名是否存在
            connection.query('SELECT `id` FROM `user` WHERE `username`=?', [username], function(err, sele_res){
                if(err) throw err;

                // 若用户名已存在，则直接回调
                if(sele_res.length){
                    cb({isExisted:true});
                    connection.release();
                }else{
                    // 否则将信息插入到数据库中
                    var params = {username:username, password:password, regtime:regtime};
                    connection.query('INSERT INTO `user` SET ?', params, function(err, insert_res){
                        if(err) throw err;

                        cb(insert_res);
                        connection.release();
                        // 接下来connection已经无法使用，它已经被返回到连接池中 
                    })
                }
            })
        });
    }
}
```

我们将检测用户名和插入数据两个功能放到一起处理了，实际应用中，最好是在用户提交数据之前就对用户名进行检测。注册功能的model写好之后，就可以调用了，承接上面的代码，从数据库处理接着编写。

```
// routes/user.js

var user_m = require('../models/user'); // 引入model

// post方式
router.post('/reg', function(req, res, next) {
      // 与上面的代码一样

    // 数据库处理
    user_m.reg(username, password_hash, regtime, function(result){
        if(result.isExisted){
            res.render('reg', {errmsg:'用户名已存在'}); // 重新加载注册模板，并提示用户名已存在
        }else if(result.affectedRows){
            // 注册成功
            res.redirect('/');
        }else{
            // console.log('登录失败');
            res.render('reg', {errmsg:'注册失败，请重新尝试'});
        }
    });
});
```

页面若跳转到首页，则说明注册成功了，查看下数据库是否将数据正确的插入了。  

到这里，注册功能完成了，完成了吗？还没呢，我们这里注册完成后仅仅是跳转到了首页，还缺少的操作是：

* 若直接跳转到首页，则默认是已经登录了，这里就需要记录用户的登录状态；
* 若不跳转到首页，则注册成功后要跳转到登录页面让用户登录

我们这里使用第1种方式，稍后讲解如何记录用户的登录状态。

#### 3.2 登录功能

登录过程与注册是非常类似的，而且比注册还要简单，只需要查询数据库中是否存在对应的用户名和密码即可。  

首先编写一个登录页面：  

// views/login.ejs

```
<!DOCTYPE html>
<html>
  <head>
    <title>登录</title>
    <link rel='stylesheet' href='/stylesheets/style.css' />
    <style type="text/css">
        .tip{color: #f00;}
    </style>
  </head>
  <body>
  <div class="container">
      <p><a href="/">回到首页</a></p>
      <h1>登录</h1>
      <form action="/user/login/" method="post">
          <% if(errmsg){ %>
          <p class="tip">*<%= errmsg %></p>
          <% } %>
          <p>用户名： <input type="text" name="username" required="required"></p>
          <p>密&emsp;码： <input type="password" name="password" required="required"></p>
          <p><input type="submit" name="submit" value="submit"></p>
      </form>
      <p>还没帐号？<a href="/user/reg">点击注册</a></p>
  </div>
  </body>
</html>
```

然后在`model/user.js`中添加上对数据库的登录操作：

```
module.exports = {
    // ...

    // 登录
    login : function(username, password, cb){
        pool.getConnection(function(err, connection){
            if(err) throw err;

            connection.query('SELECT `id` FROM `user` WHERE `username`=? AND `password`=?', [username, password], function(err, result){
                if(err) throw err;

                cb(result);
                connection.release();
                // 接下来connection已经无法使用，它已经被返回到连接池中 
            })
        });
    }
}
```

最后在`routes/user.js`中添加上登录的路由：

```
// routes/user.js 

// 进入到登录页面
router.get('/login', function(req, res, next) {
  res.render('login', {errmsg:''});
});

// 处理登录请求
router.post('/login', function(req, res, next) {
    var username = req.body.username || '',
            password = req.body.password || '';

    var password_hash = user_m.hash(password);

    user_m.login(username, password_hash, function(result){
        if(result.length){
            // console.log( req.session );
            // req.session.user = {
            //     uid : result[0].id,
            //     username : username
            // }
            // res.redirect('/');
            res.send('登录成功');
        }else{
            // console.log('登录失败');
            res.render('login', {errmsg:'用户名或密码错误'});
        }
    });
});
```

登录功能也编写好了。

#### 3.3 保存登录状态

我们通常是使用session来保存用户的登录状态，express框架没有对session处理的功能，需要我们引入额外的模块`express-session`：

```
$npm install express-session --save-dev
```

然后在`app.js`中引用：

```
var session = require('express-session')

app.use(session({
  secret: 'wenzi', // 建议使用 128 个字符的随机字符串
  cookie: { maxAge: 60*60*1000 }, // 设置时间
  resave : false,
  saveUninitialized : true
}));
```

设置完成后，就可以使用session保存数据了。以登录成功后保存数据为例：

```
user_m.login(username, password_hash, function(result){
    if(result.length){
        // 将数据保存到名为user的session中
        req.session.user = {
            uid : result[0].id,
            username : username
        }
        res.redirect('/');
    }else{
        // console.log('登录失败');
        res.render('login', {errmsg:'用户名或密码错误'});
    }
});
```

还有一个问题：如何把session中的数据传递给模板呢，比如没有登录时，显示“注册，登录”连接，登录后显示“用户名，登录”信息。  

在`app.js`中添加：

```
app.use(function(req, res, next){
    // 如果session中存在，则说明已经登录
    if( req.session.user ){
        res.locals.user = {
            uid : req.session.user.uid,
            username : req.session.user.username
        }
    }else{
        res.locals.user = {};
    }
    next();
})
```

然后在模板中就可以使用`user`变量了：

```
<% if(user.uid){ %>
    <!-- 登录状态下 -->

<% }else{ %>
    <!-- 非登录状态下 -->

<% } %>
```

### 4. 首页及详情页

我们在首页能够展示主题列表并能发表主题，点击链接进入详情页后能该主题进行回复。当然发表主题和对主题进行回复都是在已经登录的状态进行的。

#### 4.1 首页

在`models`目录创建`list.js`，从数据库中获取主题列表：

```
// models/list.js

var pool = require('./db');

module.exports = {
    // 获取首页的主题
    getIndexList : function(cb){
        pool.getConnection(function(err, connection){
            if(err) throw err;

            // 连表查询，获取到作者的用户名
            connection.query('SELECT `list`.*, username FROM `list`, `user` WHERE `list`.`uid`=`user`.`id`', function(err, result){
                if(err) throw err;

                cb(result);
                connection.release();
                // 接下来connection已经无法使用，它已经被返回到连接池中 
            })
        });
    }
}
```

在`routes`中的`index.js`中调用**getIndexList**获取数据，并调用index.ejs模板：

```
// routes/index.js

var list_m = require('../models/list');

router.get('/', function(req, res, next) {
    list_m.getIndexList(function(result){
        res.render('index', { data:result }); // 选择index模板并传递数据
    })
});
```

在`views/index.ejs`创建首页模板：

```
<h1>列表</h1>
<table>
  <tr>
    <td>标题</td>
    <td>作者</td>
    <td>创建时间</td>
  </tr>
  <% for(var i=0, len=data.length; i<len; i++) { %>
    <tr>
      <td><a href="/list/<%=data[i].id %>.html" title="<%=data[i].title %>"><%=data[i].title %></a></td>
      <td><%=data[i].username %></td>
      <td><%=data[i].createtime %></td>
    </tr>
  <% } %>
</table>
<% if(user.username){ %>
<!-- 在登录状态展示输入框 -->
<div class="add">
    <p><input type="text" class="title"></p>
    <textarea class="content" cols="100" rows="10"></textarea>
    <p><input type="button" class="submit" value="提交"><span class="tip"></span></p>
</div>
<% }%>
```

展示数据完毕。

#### 4.2 发表主题

我们在首页上添加了可以输入标题和内容的两个输入窗口，可以使用ajax的方式提交数据。

```
<script type="text/javascript" src="http://mat1.gtimg.com/libs/jquery/1.12.0/jquery.min.js"></script>
<script type="text/javascript">
  var running = false;
  $('.submit').on('click', function(){
    if(running) return;
    running = true;
    $('.tip').text('');

    var title = $('.add .title').val();
        content = $('.add .content').val();
    if(!title || !content){
      $('.tip').text('*输入不能为空');
      return;
    }
    $('.tip').text('数据正在提交中...');

    $.ajax({
      url : '/list/addtopic', // 提交接口
      data : {title:title, content:content},
      dataType : 'json',
      type : 'get'
    }).done(function(result){
      if(result.code==0){
        var html = '<tr><td><a href="'+result.data.url+'" title="'+result.data.title+'">'+result.data.title+'</a></td><td>'+result.data.author+'</td><td>'+result.data.createtime+'</td></tr>';
        $('table').append(html);
        $('.tip').text('');
        $('.title, .content').val('');
      }else{
        $('.tip').text('添加失败');
      }
      running = false;
    })
  })
</script>
```

这里的提交接口是`/list/addtopic`，因此我们需要再创建一个这样的路由：在`routes`目录下创建list.js：

```
// routes/list.js

router.get('/addtopic', function(req, res){
    // 在登录状态下可以添加主题
    if(req.session.user){
        var title = req.query.title,
            content = req.query.content,
            uid = req.session.user.uid,
            createtime = parseInt(Date.now()/1000);

        var params = {uid:uid, title:title, content:content, createtime:createtime};

        list_m.addTopic(params, function(result){
            // console.log(result);
            if(result.affectedRows){
                res.json({code:0, msg:'添加成功', data:{url:'/list/'+result.insertId+'.html', title:title, author:req.session.user.username, createtime:createtime}});
            }else{
                res.json({code:2, msg:'添加失败，请重新尝试'})
            }
        });
        
    }else{
        res.json({code:1, msg:'您还未登录'})
    }
})
```

这里用到了`list_m.addTopic`，因此需要在`models/list.js`中添加 addTopic 方法：

```
// models/list.js

/*
    添加主题
    uid, title, content, createtime
*/
addTopic : function(params, cb){
    pool.getConnection(function(err, connection){
        if(err) throw err;

        connection.query('INSERT INTO `list` SET ?', params, function(err, result){
            if(err) throw err;

            cb(result);
            connection.release();
            // 接下来connection已经无法使用，它已经被返回到连接池中 
        })
    });
}
```

#### 4.3 详情页

在首页列表中，可以看到，我们将详情页的链接设置为了`/list/1.html`的方式，也可以设置成其他的方式（比如 /list?pid=1 等），只要设置好路由就行。

```
// routes/list.js

// http://127.0.0.1:3000/list/1.html
router.get('/:pid.html', function(req, res) {
    var pid = req.params.pid || 1;

    console.log(pid);
});
```

在详情页中需要获取到这个主题的详细信息和对这个主题的回复，因此在list_m中：

```
// models/list.js

// 根据id查询主题的详情信息
getListById : function(id, cb){
    pool.getConnection(function(err, connection){
        if(err) throw err;

        connection.query('SELECT * FROM `list` WHERE `id`=?', [id], function(err, result){
            if(err) throw err;

            cb(result);
            connection.release();
            // 接下来connection已经无法使用，它已经被返回到连接池中 
        })
    });
},

// 某主题的回复
getReplyById : function(pid, cb){
    pool.getConnection(function(err, connection){
        if(err) throw err;

        connection.query('SELECT * FROM `reply` WHERE `pid`=?', [pid], function(err, result){
            if(err) throw err;

            cb(result);
            connection.release();
            // 接下来connection已经无法使用，它已经被返回到连接池中 
        })
    });
}
```

然后在路由中进行调用，这里使用`async`简单的控制了下两个异步的流程问题：

```
// http://127.0.0.1:3000/list/1.html
router.get('/:pid.html', function(req, res) {
    var pid = req.params.pid || 1;

    async.parallel([
        function(callback){
            list_m.getListById(pid, function(result){
                callback(null, result[0]);
            })
        },
        function(callback){
            list_m.getReplyById(pid, function(result){
                callback(null, result);
            })
        },
    ], function(err, results){
        // console.log( results );
        // res.json(results);
        res.render('list', { data:results });
    })
    
});
```

稍微解释下`async.parallel`的功能，下节我们会详细的讲解。 async.parallel([f1, f2, f3,..., fn], fb); 是f1到fn所有的异步都执行完了就会执行fb函数。这里我们是主题的详情和对主题的回复都请求完成了，就可以调用模板渲染。  

// views/list.ejs

```
<p><a href="/">返回到首页</a></p>
<h1>详情</h1>
<p>标题： <%=data[0].title %></p>
<div><%=data[0].content %></div>
<div class="reply">
  <h2>评论</h2>
  <div class="reply_con">
    <table>
      <% for(var i=0, len=data[1].length; i<len; i++) { %>
        <tr>
          <td><%=(i+1) %></td>
          <td><%=data[1][i].content %></td>
          <td><%=data[1][i].createtime %></td>
        </tr>
       <% } %>
    </table>
  </div>
</div>
```



