# node(三)之文件操作

对于文件和文件夹的操作，文件系统提供了不少的api接口，这里我们从几个样例稍微讲解下文件接口的使用        

node中对文件和目录的操作，我们不一个个讲每个api的使用，只是从几个例子来了解下下文件系统。

### 1. 文件操作

在文件操作里，主要是有文件读写，创建、移动文件等。

#### 1.1 读取文件

读取文本文件时，如.txt, .js, .json等文件，直接使用`readFile`就可以获取文件的内容。

```
// server.js
var fs = require('fs');

fs.readFile('./data.txt', 'utf-8', function(err, data){
    if(err) throw err;
    console.log(data);
});
```

读取图片时，我们是不能直接输出到控制台中的，是需要创建一个服务器，然后在浏览器上进行查看。其实在上节中，我们已经了解过显示图片的过程了。

```
// server.js
var http = require('http'),
    fs = require('fs');

http.createServer(function(request, response){
    // 使用二进制方式读取图片
      fs.readFile('./img/test.png', 'binary', function(err, file){
        if( err ) throw err;
        // 当前数据以image/png方式进行输出
        response.writeHead(200, {"Content-Type": "image/png"});
        response.write(file, 'binary');
        response.end();
      });
}).listen(3000);
console.log('server has started...');
```

打开浏览器：`127.0.0.1:3000`，就能看到图片了。

#### 1.2 写入文件

将字符串写入到文件文件中，是非常简单的操作，使用`writeFile`即可搞定：

```
var fs = require('fs');

var data = '从一开始，就选择了做前端开发，因为觉得前端开发更贴近用户，能够倾听用户的声音，更好玩，更有意思，美的更直观。我们总是在尝试最新的技术，尝试更炫的效果，希望更能优化用户的体验效果！';

fs.writeFile('./test.txt', data, function(err){
      if(err) throw err;
      console.log('写入数据成功...');
});
```

writeFile方法，在没有文件时会创建文件并写入；若文件存在则内容被覆盖。

#### 1.3 创建或文件重命名

根据writeFile的特性，可以使用`writeFile`写入空字符串的方式创建文件。  

同时，`fs.open`也可以创建文件：

```
// 打开模式可以使用 w | w+ | a | a+
// 这些模式在打开不存在的文件时，会创建文件
// fd为一个整数，表示打开文件返回的文件描述符，window中又称文件句柄
fs.open(Date.now()+'.txt', 'a+', function(err, fd){
      if(err) throw err;
      console.log(fd);
})
```

在文件系统中，有一个`fs.rename`的方法，顾名思义，对文件（文件夹）进行重命名。

```
fs.rename(oldname, newname, callback(err));
```

特性：  

将oldname文件（目录）**移动**至newname的路径下，并重新命名；如果oldname和newname是同一个路径，则直接进行重命名。

### 2. 文件夹操作

通常对目录的操作比较简单一些。

#### 2.1 读取文件夹中的文件和文件夹列表

使用`fs.readdir(path, callback)`可以获取path路径下的文件和目录列表，而且只能读取直接目录下的文件和文件夹，子目录里的是获取不到的。

```
fs.readdir('./', function(err, files){
      if(err) throw err;
      console.log( files );
});
```

输出结果：

```
[
      'img',
      'msg.txt',
      'node_modules',
      'package.json',
     'server.js',
      'test.js',
      'tmp'
]
```

node_modules和tmp是文件夹，剩下的是文件，而且是获取不到node_modules和tmp里面的数据。获取一个目录下所有的文件，后面会讲解，稍等。

#### 2.2 删除文件夹

使用`fs.rmdir(path, callback)`可以删除文件夹，**但只能删除空文件夹**，如果当前路径不是文件夹或当前文件夹不为空，则删除失败；删除的为空文件夹时，可以删除成功。

```
fs.rmdir('./tmp', function(err){
      if(err){
        console.log('删除文件夹失败');
        throw err;
      }else{
        console.log('删除成功');
      }
})
```

如何删除不为空的目录，后面会讲解，稍等。

#### 2.3 获取文件或文件夹的信息

`fs.stat(path, callback)`能够获取path路径的信息，比如创建时间，修改时间，文件大小，当前是否为文件，当前是否为文件夹等信息；如果path路径不存在，则抛出异常。

```
fs.stat('./test.js', function(err, stats){
    if( err ){
        console.log( '路径错误' );
        throw err;
    }
    console.log(stats);
    console.log( 'isfile: '+stats.isFile() ); // 是否为文件
    console.log( 'isdir: '+stats.isDirectory() ); // 是否为文件夹
});
```

结果：

```
{
    dev: -29606086,
      mode: 33206,
      nlink: 1,
      uid: 0,
      gid: 0,
      rdev: 0,
      blksize: undefined,
      ino: 2251799813687343,
      size: 2063,  // path路径为文件夹时，size为0
      blocks: undefined,
      atime: Thu Jan 12 2017 21:12:36 GMT+0800 (中国标准时间),
      mtime: Sat Jan 14 2017 21:57:26 GMT+0800 (中国标准时间),
      ctime: Sat Jan 14 2017 21:57:26 GMT+0800 (中国标准时间),
      birthtime: Thu Jan 12 2017 21:12:36 GMT+0800 (中国标准时间)
}
isfile: true // 是否为文件
isdir: false // 是否为文件夹
```

关于这几个时间属性的理解，可以参考我之前写的博文：【[对gulp-changed插件的一点思考](https://www.xiabingbao.com/gulp/2016/01/20/gulp-changed-ponder.html#mtime-atime-ctime)】

stats中的size属性就是当前文件的大小（单位：字节，除以1024即为kb），stats还有下面方法可供使用：

* stats.isFile()
* stats.isDirectory()
* stats.isBlockDevice()
* stats.isCharacterDevice()
* stats.isSymbolicLink() (only valid with fs.lstat())
* stats.isFIFO()
* stats.isSocket()

fs.stat(path, callback)是异步执行的，对应的还有同步执行版本：`fs.statSync(path)`，这个方法返回的就是`fs.stats`实例。

### 3. 综合运用

我们在上面的讲解中，还留着两个功能没实现，这里实现一下它的过程。

#### 3.1 遍历目录中所有的文件

我们已经知道使用`readdir`只能获取当前目录里的文件和文件夹名称，为了获取这个目录里所有的文件名称，只能是读取当前目录里所有的文件夹里的文件。这里我们使用递归的方法，如果当前资源是文件，则进行存储，是文件夹则进行递归进一步检索，直到把所有的文件夹遍历完毕。

```
// 获取文件夹中所有的文件
function readDirAll(path){
    // 获取字符串的最后一个字符
    var getLastCode = function(str){
        return str.substr(str.length-1, 1);
    }

    var result = []; // 存储获取到的文件
    var stats = fs.statSync(path); // 获取当前文件的状态
    if( stats.isFile() ){
        result.push(path);
    }else if( stats.isDirectory() ){
        // 若当前路径是文件夹，则获取路径下所有的信息，并循环
        var files = fs.readdirSync(path);

        for(var i=0, len=files.length; i<len; i++){
            var item = files[i],
                itempath = getLastCode(path)=='/'  ? path+item : path+'/'+item; // 拼接路径
            var st = fs.statSync(itempath);
            if( st.isFile() ){
                result.push(itempath);
            }else if( st.isDirectory() ){
                // 当前是文件夹，则递归检索，将递归获取到的文件列表与当前result进行拼接
                var s = readDirAll( itempath );
                result = result.concat( s );
            }
        }
    }
    return result;
}
console.log( readDirAll('./') );
```

使用此程序获取当前目录中所有的文件（展示的为部分文件）：

```
[ 
    './bing.doc',
      './img/1484234634801.png',
      './img/1484234660592.png',
      './img/test.png',
      './inter.js',
      './msg.txt',
      './node_modules/formidable/.npmignore',
      './node_modules/formidable/.travis.yml',
      './node_modules/formidable/index.js',
      './node_modules/formidable/lib/file.js',
      './node_modules/formidable/lib/incoming_form.js',
      './node_modules/formidable/lib/index.js',
      ...
]
```

如果想要输出一种树形的结构，就可以对当前的递归程序进行改造，比如我想要输出如下的这种结果，那么，就要分析这种结构的特点：

```
bing.doc                                    
img                                         
    |---1484234634801.png                   
    |---1484234660592.png                   
    |---test.png                            
inter.js                                    
msg.txt                                     
node_modules                                
    |---formidable                          
        |---.npmignore                      
        |---.travis.yml                     
        |---index.js                        
        |---lib                             
            |---file.js 
            |---incoming_form.js 
            |---index.js
```

可以看出的规律：

1. 第一层级的文件和文件夹前面是没有空格和字符的；
1. 第一级子目录中的文件或文件夹前面是1组空格和1个字符；
1. 第二级子目录中的文件或文件夹前面是2组空格和1个字符；
1. 依次类推...

我们可以再传递一个depth来表示当前目录的层级，然后计算出前面空格的数量：

```
// depth为递归的深度，可根据递归的深度输出文件名称前面的格式
function readDirAll(path, depth){
    // 获取字符串
    var getLastCode = function(str){
        return str.substr(str.length-1, 1);
    }

    depth = depth || 0; // 默认为0
    var fir_code = '';

    // 计算文件名称前面的字符，4个空格为1组
    for(var j=0; j<depth; j++){
        fir_code += '    ';
    }
    depth && (fir_code += '|---');

    var stats = fs.statSync(path);
    if( stats.isFile() ){
        console.log( fir_code+path );
    }else if( stats.isDirectory() ){
        var files = fs.readdirSync(path);
        for(var i=0, len=files.length; i<len; i++){
            var item = files[i],
                itempath = getLastCode(path)=='/'  ? path+item : path+'/'+item,
                st = fs.statSync(itempath);

          console.log( fir_code+item );
          if( st.isDirectory() ){
              var s = readDirAll( itempath, depth+1 );
          }
      }
  }
}
console.log( readDirAll('./') ); 
```

#### 3.2 删除目录

使用`fs.rmdir(path)`是有局限性的，只能删除空目录，如果是个非空目录，我们可以根据上面的思路，写出一个能删除当前目录下所有的文件。`递归`，只要找到里面的文件夹就递归寻找，直到找到最底层，把最底层的文件删除，然后再逐级向上删除文件夹，直到删除到当前目录。

```
// 删除path下所有的文件和文件夹，包括path自己
function rmDirAll(path){
    // 获取字符串
    var getLastCode = function(str){
        return str.substr(str.length-1, 1);
    }

    var stats = fs.statSync(path); // 获取当前文件的状态
    if( stats.isFile() ){
        fs.unlinkSync(path);
        console.log( '删除成功： '+path );
    }else if( stats.isDirectory() ){
        // 若当前路径是文件夹，则获取路径下所有的信息，并循环
        var files = fs.readdirSync(path);

        for(var i=0, len=files.length; i<len; i++){
            var item = files[i],
                itempath = getLastCode(path)=='/'  ? path+item : path+'/'+item; // 拼接路径
            var st = fs.statSync(itempath);
            if( st.isFile() ){
                fs.unlinkSync(itempath);
                console.log( '删除成功： '+itempath );
            }else if( st.isDirectory() ){
                // 当前是文件夹，则递归检索
                rmDirAll( itempath );
            }
        }
        // 现在可以删除文件夹
        fs.rmdir(path);
        console.log( '删除成功： '+path );
    }
}
rmDirAll('./img'); 
```

则删除时输出的信息如下，先把内部的文件和文件夹删除干净，最后删除 './img'：

```
删除成功： ./img/1484234634801.png
删除成功： ./img/1484234660592.png
删除成功： ./img/gggg/est.txt
删除成功： ./img/gggg
删除成功： ./img/test.png
删除成功： ./img
```

当然，你也可以试着实现这样的程序：

1. 删除path内部所有的内容，同时能保留下path目录
1. 只删除文件，将所有的空文件夹保留下来
1. 将内部所有的文件都移动到path的根目录下，并删除空文件夹

等等，都可以试着实现一下。

