# node(二)之搭建http服务器

     


首先我们从搭建一个http服务器，能运行简单的程序开始说起。

### 1. hello world

最经典的`hello world`。首先我们创建一个`server.js`来保存我们的代码：

```
console.log( 'hello world' );
```

在终端输入`node server.js`运行:

```
node server.js
```

终端就会输出 **hello world** 的字样。可是我们一个node服务器程序，总是要在浏览器上访问的呀，这里就要用到node里自带的`http`模块了：

```
var http = require('http'); // 引入http模块

// 创建http服务器
// request : 从浏览器带来的请求信息
// response : 从服务器返回给浏览器的信息
http.createServer(function(request, response){
    response.writeHead(200, {'content-type': 'text/plain'}); // 设置头部信息，输出text文本
    response.write('hello world'); // 输出到页面中的信息
    response.end(); // 返回结束
}).listen(3000);
console.log('server has started...');
```

我们再次在终端输入`node server.js`运行，终端里会有输出 **server has started...** 的字样，表示服务器已创建并正在运行，然后我们在浏览器上访问`127.0.0.1:3000`，就可以看到页面中输出了`hello world`。

### 2. form表单

刚才我们只是在页面中输出了一段简单的文本，现在我们要在页面中呈现一个表单，可以让用户输入信息并进行提交：

```
// server.js
var http = require('http');

http.createServer(function(request, response){
var html = '<html>\
    <head>\
    <meta charset=UTF-8" />\
    </head>\
    <body>\
    <form action="/" method="post">\
    <p>username : <input type="text" name="username" /></p>\
    <p>password : <input type="password" name="password" /></p>\
    <p>age : <input type="text" name="age" /></p>\
    <p><input type="submit" value="submit" name="submit" /></p>\
    </form>\
    </body>\
    </html>';

    response.writeHead(200, {'content-type': 'text/html'}); // 输出html头信息
    response.write(html); // 将拼接的html字符串输出到页面中
    response.end(); // 结束
}).listen(3000);
console.log('server has started...');
```

修改server.js中的内容，重新运行：

```
node server.js
```

刷新页面后，我们发现页面中输出了3个文本框和1个提交按钮。因为我们的程序只是呈现页面，并没有做任何其他的处理，因此在页面中提交数据只是刷新当前页面。  

**注意：** 我们每次修改node中的任何代码后，都要重新进行启动。

#### 2.1 获取表单GET方式提交的数据

我们上面的代码中使用的是POST方式，不过这里要先讨论使用`GET`方式提交过来的数据，我们先不考虑数据的安全性，只是学习如何获取使用get方式提交过来的form表单数据，将post改为get，重新运行。  

我们知道，使用get方式提交数据，会将数据作为URL参数传递过来，因此我们通过解析URL中的参数获取到数据，这里就用到了`url`模块中的方法：

```
// server.js
var http = require('http'),
url = require('url');

http.createServer(function(request, response){
    var html = '<html>\
        <head>\
        <meta charset=UTF-8" />\
        </head>\
        <body>\
        <form action="/" method="get">\
        <p>username : <input type="text" name="username" /></p>\
        <p>password : <input type="password" name="password" /></p>\
        <p>age : <input type="text" name="age" /></p>\
        <p><input type="submit" value="submit" name="submit" /></p>\
        </form>\
        </body>\
        </html>';
    
    var query = url.parse( request.url, true ).query;
    if( query.submit ){
        var data = '<p><a href="/">back</a></p>'+
            '<p>username:'+query.username+'</p>'+
            '<p>password:'+query.password+'</p>'+
            '<p>age:'+query.age+'</p>';
         
        response.writeHead(200, {'content-type': 'text/html'});
        response.write(data);
    }else{
        response.writeHead(200, {'content-type': 'text/html'});
        response.write(html);
    }
    response.end(); // 结束
}).listen(3000);
console.log('server has started...');
```

我们再次运行提交后就能在页面中显示出数据了。  

url.parse是用来解析URL字符串的，并返回解析后的URL对象。若我们只输出一下 url.parse(request.url) ：

```
url.parse(request.url);
```

result:

```
{
    protocol: null,
    slashes: null,
    auth: null,
    host: null,
    port: null,
    hostname: null,
    hash: null,
    search: '?username=111113&password=123&age=122&submit=submit',
    query: 'username=111113&password=123&age=122&submit=submit',
    pathname: '/',
    path: '/?username=111113&password=123&age=122&submit=submit',
    href: '/?username=111113&password=123&age=122&submit=submit'
}
```

如果将第2个参数设置为true，则会将返回结果中的query属性解析为一个对象，其他属性不变；默认值为false，即query属性是一个字符串：

```
url.parse(request.url, true);
```

result:

```
{
    ...
    query: {
        username: '111113',
        password: '123',
        age: '122',
        submit: 'submit' },
    ...
}
```

因此我们可以通过如下语句判断是否有提交数据并获取提交数据，然后再输出到中即可：

```
var query = url.parse( request.url, true ).query;
/*
{
    username: '111113',
    password: '123',
    age: '122',
    submit: 'submit'
}
*/
```

#### 2.2 获取表单POST方式提交的数据

现在我们使用post方式来提交数据。因为POST请求一般都比较“重”
 
（用户可能会输入大量的内容），如果用阻塞的方式来处理处理，必然会导致用户操作的阻塞。因此node将post数据拆分为很多小的数据块，然后通过data事件（表示新的小数据块到达了）和end事件传递这些小数据块（表示所有的数据都已经接收完毕）。
 所以，我们的思路应该是：在data事件中获取数据块，在end事件中操作数据。

```
// server.js
var http = require('http'),
querystring = require('querystring');

http.createServer(function(request, response){
    var html = '<html>\
        <head>\
        <meta charset=UTF-8" />\
        </head>\
        <body>\
        <form action="/" method="post">\
        <p>username : <input type="text" name="username" /></p>\
        <p>password : <input type="password" name="password" /></p>\
        <p>age : <input type="text" name="age" /></p>\
        <p><input type="submit" value="submit" name="submit" /></p>\
        </form>\
        </body>\
        </html>';
    
    if( request.method.toLowerCase()=='post' ){
        var postData = '';

        request.addListener('data', function(chunk){
            postData += chunk;
        });

        request.addListener('end', function(){
            var data = querystring.parse(postData);
            console.log( 'postData: '+postData );
            console.log(data);
    
            var s = '<p><a href="/">back</a></p>'+
                '<p>username:'+data.username+'</p>'+
                '<p>password:'+data.password+'</p>'+
                '<p>age:'+data.age+'</p>';

            response.writeHead(200, {'content-type': 'text/html'});
            response.write(s);
            response.end();
        })
    }else{
        response.writeHead(200, {'content-type': 'text/html'});
        response.write(html);
        response.end();
    }
}).listen(3000);
console.log('server has started...');
```

这段代码与上段代码项目，主要有的几个变化是：

1. 不再引入url模块， 改用引入querystring模块。因为我们不再对URL进行操作了，也没必要引入了；
1. 使用`request.method.toLowerCase()=='post'`判断当前是否有数据提交；
1. 在data事件进行数据的拼接，在end事件中进行的处理；
1. `response.end()`写在了`end`事件内部，因为end事件是异步操作，因此必须得数据输出完成之后才能执行`response.end()`

我们在控制台中可以看出，postData是这样的一个字符串：

```
'username=123&password=123&age=23&submit=submit';
```

因此我们使用`query.parse`将postData解析为对象类型，以便获取提交过来的数据。

### 3. 路由

现在我们所有的逻辑都是在根目录下进行的，没有按照url区分，这里我们按照功能进行路由拆分。以上面的post请求为例，我们可以拆分为：页面初始化和form提交后的处理。  

页面初始化：

```
// starter.js  页面初始化

function start(request, response){
    var html = '<html>\
        <head>\
        <meta charset=UTF-8" />\
        </head>\
        <body>\
        <form action="/show" method="post">\
        <p>username : <input type="text" name="username" /></p>\
        <p>password : <input type="password" name="password" /></p>\
        <p>age : <input type="text" name="age" /></p>\
        <p><input type="submit" value="submit" name="submit" /></p>\
        </form>\
        </body>\
        </html>';
    
    response.writeHead(200, {"Content-Type":"text/html"});
    response.write( html );
    response.end();
}
exports.start = start;
```

展示获取的数据：

```
// uploader.js 展示获取的数据
var querystring = require('querystring');

function upload(request, response){
    var postData = '';

    request.addListener('data', function(chunk){
      postData += chunk;
    });
    
    request.addListener('end', function(){
        var data = querystring.parse(postData);
        console.log( 'postData: '+postData );
        console.log(data);

        var s = '<p><a href="/">back</a></p>'+
            '<p>username:'+data.username+'</p>'+
            '<p>password:'+data.password+'</p>'+
            '<p>age:'+data.age+'</p>';

        response.writeHead(200, {'content-type': 'text/html'});
        response.write(s);
        response.end();
    })
}
exports.upload = upload;
```

然后在server.js中进行路由选择

```
// server.js
var http = require('http'),
url = require('url');

http.createServer(function(request, response){
    var pathname = url.parse(request.url).pathname;
    console.log(pathname);
    response.end();
}).listen(3000);
console.log('server has started...');
```

我们任意改变URL地址，会看到输出的每个地址的pathname（忽略/favicon.ico）：

```
http://127.0.0.1:3000/ // 输出： /
http://127.0.0.1:3000/show/ // 输出： /show/
http://127.0.0.1:3000/show/img/ // 输出： /show/img/
http://127.0.0.1:3000/show/?username=wenzi // 输出： /show/
```

因此我们就根据pathname进行路由，对路由进行方法映射：

```
// server.js
var http = require('http'),
url = require('url'),
starter = require('./starter'),
uploader = require('./uploader');

http.createServer(function(request, response){
    var pathname = url.parse(request.url).pathname;
    var routeurl = {
        '/' : starter.start,
        '/show' : uploader.upload
    }

    if( typeof routeurl[pathname]=== 'function' ){
        routeurl[pathname](request, response);
    }else{
        console.log('404 not found!');
        response.end();
    }
}).listen(3000);
console.log('server has started...');
```

如果匹配到路由 `/` ，则执行 starter.start(request, response) ；如果匹配到路由 `/show` ，则执行 uploader.upload(request, response) 。如果都没匹配到，则显示404。

### 4. 图片上传并显示

在上面我们已经能成功提交数据了，这里来讲解如何进行图片上传并显示。使用node自带的模块处理起来非常的麻烦，这里我们使用别人已经开发好的`formidable`模块进行编写，它对解析上传的文件数据做了很好的抽象。

```
npm install formidable --save-dev
```

在starter.js中，我们添加上file控件：

```
// starter.js
function start(request, response){
    var html = '<html>\
        <head>\
        <meta charset=UTF-8" />\
        </head>\
        <body>\
        <form action="/upload" method="post" enctype="multipart/form-data">\
        <p>file : <input type="file" name="upload" multiple="multiple" /></p>\
        <p><input type="submit" value="submit" name="submit" /></p>\
        </form>\
        </body>\
        </html>';

    response.writeHead(200, {"Content-Type":"text/html"});
    response.write( html );
    response.end();
}
exports.start = start;
```

#### 4.1 图片上传

首先我们进行的是图片上传操作，首先我们要确保当前目录中存在tmp和img目录。在 uploader.js 中：

```
// uploader.js
var formidable = require('formidable'),
util = require('util'),
fs = require('fs');

function upload(request, response){
    if( request.method.toLowerCase()=='post' ){
        var form = new formidable.IncomingForm();

        form.uploadDir = './tmp/';
        form.parse(request, function(err, fields, files) {
            var oldname = files.upload.name,
                newname = Date.now() + oldname.substr(oldname.lastIndexOf('.'));
            fs.renameSync(files.upload.path, "./img/"+newname ); // 上传到 img 目录

            response.writeHead(200, {'content-type': 'text/plain'});
            response.write('received upload:\n\n');
            response.end(util.inspect({fields: fields, files: files}));
        });
        return;
    }
}
exports.upload = upload;
```

我们上传图片后跳转到upload路径，然后显示出相应的信息：

```
received upload:

{
    fields: { // 其他控件，如input, textarea等
    submit: 'submit'
},
files:{ // file控件
    upload:{
            domain: null,
            _events: {},
            _maxListeners: undefined,
            size: 5097,
            path: 'tmp\\upload_b1f7c3e83af224e9f3a020958cde5dcd',
            name: 'chrome.png',
            type: 'image/png',
            hash: null,
            lastModifiedDate: Thu Jan 12 2017 23:09:50 GMT+0800 (中国标准时间),
            _writeStream: [Object]
        }
    }
}
```

我们再查看img目录时，就会发现我们刚才上传的照片了。

#### 4.2 图片显示

将图片上传到服务器后，怎样才能把图片显示在浏览器上呢。这里我们就使用到了`fs`模块来读取文件，创建一个`shower.js`来专门展示图片：

```
// shower.js
var fs = require('fs'),
url = require('url');

function show(request, response){
    var query = url.parse(request.url, true).query,
        imgurl = query.src;

    // 读取图片并进行输出
    // 这里读取链接中的src参数，指定读取哪张图片  /show?src=1484234660592.png
    fs.readFile('./img/'+imgurl, "binary", function(err, file){
         if(err) throw err;
        response.writeHead(200, {"Content-Type": "image/png"});
        response.write(file, "binary");
        response.end();
    })
}
exports.show = show;
```

然后在 server.js 中添加上 show 的路由映射：

```
var routeurl = {
    '/' : starter.start,
    '/upload' : uploader.upload,
    '/show' : shower.show // 添加
};
```

最后在 upload.js 中进行图片的引用：

```
form.parse(request, function(err, fields, files) {
    var oldname = files.upload.name,
        newname = Date.now() + oldname.substr(oldname.lastIndexOf('.'));
    fs.renameSync(files.upload.path, "./img/"+newname ); // 同步上传图片

    response.writeHead(200, {'content-type': 'text/html'});
    var s = '<p><a href="/">back</a></p><p><img src="/show?src='+newname+'" /></p>'; // 显示刚才的图片
    response.write(s);
    response.end();
});
```

### 5. 综合

刚才学习了上传数据和上传图片，这里我们将其综合一下，拟定一个题目：“设定用户名密码，并上传头像”。希望可以自己实现一下。

### 6. 接口的实现

在第2部分学习了GET和POST请求，那么在这里写一个简单json或jsonp接口应该不是什么难事儿了吧。  

创建一个 inter.js ：

```
// inter.js
var url = require('url');

function init(request, response){
    if( request.method.toLowerCase()=='get' ){
        var query = url.parse(request.url, true).query;

        var data = {"code":0, "msg":"success", "data":[{"username":"wenzi", "age":26}, {"username":"bing", "age":25}]};
        if( query && query.callback ){
            // jsonp
            response.end( query.callback + '(' + JSON.stringify(data) + ')' );
         }else{
            // json
            response.end( JSON.stringify(data) );
        }
    }
}
exports.init = init;
```

在server中添加inter的引用和路由映射：

```
var routeurl = {
    '/' : starter.start,
    '/upload' : uploader.upload,
    '/show' : shower.show,
    '/inter' : inter.init // 添加
};
```

然后对 `http://127.0.0.1:3000/inter` 进行json请求或jsonp请求即可。


