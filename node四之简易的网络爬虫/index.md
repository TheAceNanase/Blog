# node(四)之简易的网络爬虫

爬虫的思路很简单：

1. 确定要抓取的URL；
1. 对URL进行抓取，获取网页内容；
1. 对内容进行分析并存储；
1. 重复第1步

在这节里做爬虫，我们使用到了两个重要的模块：

* request : 对http进行封装，提供更多、更方便的接口供我们使用，request进行的是异步请求。更多信息可以去[[request-github](https://github.com/request/request)]上进行查看
* cheerio : 类似于jQuery，可以使用$(), find(), text(), html()等方法提取页面中的元素和数据，不过若仔细比较起来，cheerio中的方法不如jQuery的多。

### 1. hello world

说是hello world，其实首先开始的是最简单的抓取。我们就以cnode网站为例（[https://cnodejs.org/](https://cnodejs.org/)）,这个网站的特点是：

* 不需要登录即可访问首页和其他页面
* 页面都是同步渲染的，没有异步请求的问题
* DOM结构清晰

代码如下：

```
var request = require('request'),
    cheerio = require('cheerio');

request('https://cnodejs.org/', function(err, response, body){
    if( !err && response.statusCode == 200 ){
        // body为源码
        // 使用 cheerio.load 将字符串转换为 cheerio(jQuery) 对象，
        // 按照jQuery方式操作即可
        var $ = cheerio.load(body);
        
        // 输出导航的html代码
        console.log( $('.nav').html() );
    }
});
```

这样的一段代码就实现了一个简单的网络爬虫，爬取到源码后，再对源码进行拆解分析，比如我们要获取首页中第1页的 问题标题，作者，跳转链接，点击数量，回复数量。通过chrome，我们可以得到这样的结构：  

每个div[.cell]是一个题目完整的单元，在这里面，一个单元暂时称为$item

```
{
    title : $item.find('.topic_title').text(),
    url : $item.find('.topic_title').attr('href'),
    author : $item.find('.user_avatar img').attr('title'),
    reply : $item.find('.count_of_replies').text(),
    visits : $item.find('.count_of_visits').text()
}
```

因此，循环div[.cell]，就可以获取到我们想要的信息了：

```
request('https://cnodejs.org/?_t='+Date.now(), function(err, response, body){
    if( !err && response.statusCode == 200 ){
        var $ = cheerio.load(body);

        var data = [];
        $('#topic_list .cell').each(function(){
            var $this = $(this);
            
            // 使用trim去掉数据两端的空格
            data.push({
                title : trim($this.find('.topic_title').text()),
                url : trim($this.find('.topic_title').attr('href')),
                author : trim($this.find('.user_avatar img').attr('title')),
                reply : trim($this.find('.count_of_replies').text()),
                visits : trim($this.find('.count_of_visits').text())
            })
        });
        // console.log( JSON.stringify(data, ' ', 4) );
        console.log(data);
    }
});

// 删除字符串左右两端的空格
function trim(str){ 
    return str.replace(/(^\s*)|(\s*$)/g, "");
}
```

### 2. 爬取多个页面

上面我们只爬取了一个页面，怎么在一个程序里爬取多个页面呢？还是以CNode网站为例，刚才只是爬取了第1页的数据，这里我们想请求它前6页的数据（别同时抓太多的页面，会被封IP的）。每个页面的结构是一样的，我们只需要修改url地址即可。

#### 2.1 同时抓取多个页面

首先把request请求封装为一个方法，方便进行调用，若还是使用console.log方法的话，会把6页的数据都输出到控制台，看起来很不方便。这里我们就使用到了上节文件操作内容，引入`fs`模块，将获取到的内容写入到文件中，然后新建的文件放到file目录下（需手动创建file目录）：

```
// 把page作为参数传递进去，然后调用request进行抓取
function getData(page){
    var url = 'https://cnodejs.org/?tab=all&page='+page;
    console.time(url);
    request(url, function(err, response, body){
        if( !err && response.statusCode == 200 ){
            console.timeEnd(url); // 通过time和timeEnd记录抓取url的时间

            var $ = cheerio.load(body);

            var data = [];
            $('#topic_list .cell').each(function(){
                var $this = $(this);

                data.push({
                    title : trim($this.find('.topic_title').text()),
                    url : trim($this.find('.topic_title').attr('href')),
                    author : trim($this.find('.user_avatar img').attr('title')),
                    reply : trim($this.find('.count_of_replies').text()),
                    visits : trim($this.find('.count_of_visits').text())
                })
            });
            // console.log( JSON.stringify(data, ' ', 4) );
            // console.log(data);
            var filename = './file/cnode_'+page+'.txt';
            fs.writeFile(filename, JSON.stringify(data, ' ', 4), function(){
                console.log( filename + ' 写入成功' );
            })
        }
    });
}
```

CNode分页请求的链接：[https://cnodejs.org/?tab=all&](https://cnodejs.org/?tab=all&)`page=2`，我们只需要修改page的值即可：

```
var max = 6;
for(var i=1; i<=max; i++){

    getData(i);
}
```

这样就能同时请求前6页的数据了，执行文件后，会输出每个链接抓取成功时消耗的时间，抓取成功后再把相关的信息写入到文件中：

```
$ node test.js
开始请求...
https://cnodejs.org/?tab=all&page=1: 279ms
./file/cnode_1.txt 写入成功
https://cnodejs.org/?tab=all&page=3: 372ms
./file/cnode_3.txt 写入成功
https://cnodejs.org/?tab=all&page=2: 489ms
./file/cnode_2.txt 写入成功
https://cnodejs.org/?tab=all&page=4: 601ms
./file/cnode_4.txt 写入成功
https://cnodejs.org/?tab=all&page=5: 715ms
./file/cnode_5.txt 写入成功
https://cnodejs.org/?tab=all&page=6: 819ms
./file/cnode_6.txt 写入成功
```

我们在file目录下就能看到输出的6个文件了。

#### 2.2 控制同时请求的数量

我们在使用for循环后，会同时发起所有的请求，如果我们同时去请求100、200、500个页面呢，会造成短时间内对服务器发起大量的请求，最后就是被封IP。这里我写了一个调度方法，每次同时最多只能发起5个请求，上一个请求完成后，再从队列中取出一个进行请求。

```
/*
  @param data []  需要请求的链接的集合
  @param max  num 最多同时请求的数量
*/
function Dispatch(data, max){
    var _max = max || 5, // 最多请求的数量
        _dataObj = data || [], // 需要请求的url集合
        _cur = 0, // 当前请求的个数
        _num = _dataObj.length || 0,
        _isEnd = false,
        _callback;

    var ss = function(){
        var s = _max - _cur;
        while(s--){
            if( !_dataObj.length ){
                _isEnd = true;
                break;
            }
            var surl = _dataObj.shift();
            _cur++;

            _callback(surl);
        }
    }

    this.start = function(callback){
        _callback = callback;

        ss();
    },

    this.call = function(){
        if( !_isEnd ){
            _cur--;
            ss();
        }
    }
}
```

var dis = new Dispatch(urls, max);
dis.start(getData);

然后在 getData 中，写入文件的后面，进行dis的回调调用：

```
var filename = './file/cnode_'+page+'.txt';
fs.writeFile(filename, JSON.stringify(data, ' ', 4), function(){
    console.log( filename + ' 写入成功' );
})
dis.call();
```

这样就实现了异步调用时控制同时请求的数量。不过现在已经有很多类似的框架可以控制并发的数量了，直接使用即可，比如`async`中的parallelLimit方法也是用来控制并发数量的。

### 3. 抓取需要登录的页面

比如我们在抓取CNode，百度贴吧等一些网站，是不需要登录就可以直接抓取的，那么如知乎等网站，必须登录后才能抓取，否则直接跳转到登录页面。这种情况我们该怎么抓取呢？  

使用cookie。 用户登录后，都会在cookie中记录下用户的一些信息，我们在抓取一些页面，带上这些cookie，服务器就会认为我们处于登录状态，程序就能抓取到我们想要的信息。  

先在浏览器上登录我们的帐号，然后在console中使用`document.domain`获取到所有cookie的字符串，复制到下方程序的cookie处（如果你知道哪些cookie不需要，可以剔除掉）。

```
request({
    url:'https://www.zhihu.com/explore',
    headers:{
        // "Referer":"www.zhihu.com"
        cookie : xxx
    }
}, function(error, response, body){
    if (!error && response.statusCode == 200) {
        // console.log( body );
        var $ = cheerio.load(body);

        
    }
})
```

同时在request中，还可以设定referer，比如有的接口或者其他数据，设定了referer的限制，必须在某个域名下才能访问。那么在request中，就可以设置referer来进行伪造。

### 4. 保存抓取到的图片

页面中的文本内容可以提炼后保存到文本或者数据库中，那么图片怎么保存到本地呢。  

图片可以使用request中的`pipe`方法输出到文件流中，然后使用`fs.createWriteStream`输出为图片。  

这里我们把图片保存到以日期创建的目录中，mkdirp可一次性创建多级目录（./img/2017/01/22）。保存的图片名称，可以使用原名称，也可以根据自己的规则进行命名。

```
var request = require('request'),
    cheerio = require('cheerio'),
    fs = require('fs'),
    path = require('path'), // 用于分析图片的名称或者后缀名
    mkdirp = require('mkdirp'); // 用于创建多级目录

var date = new Date(),
    year = date.getFullYear(),
    month = date.getMonth()+1,
    month = ('00'+month).slice(-2), // 添加前置0
    day = date.getDate(),
    day = ('00'+day).slice(-2), // 添加前置0
    dir = './img/'+year+'/'+month+'/'+day+'/';

// 根据日期创建目录 ./img/2017/01/22/
var stats = fs.statSync(dir);
if( stats.isDirectory() ){
    console.log(dir+' 已存在');
}else{
    console.log('正在创建目录 '+dir);
    mkdirp(dir, function(err){
        if(err) throw err;
    })
}

request({
    url : 'http://desk.zol.com.cn/meinv/?_t='+Date.now()
}, function(err, response, body){
    if(err) throw err;

    if( response.statusCode == 200 ){
        var $ = cheerio.load(body);
        
        $('.photo-list-padding img').each(function(){
            var $this = $(this),
                imgurl = $this.attr('src');
            
            var ext = path.extname(imgurl); // 获取图片的后缀名，如 .jpg, .png .gif等
            var filename = Date.now()+'_'+ parseInt(Math.random()*10000)+ext; // 命名方式：毫秒时间戳+随机数+后缀名
            // var filename = path.basename(imgurl); // 直接获取图片的原名称
            // console.log(filename);
            download(imgurl, dir+filename); // 开始下载图片
        })
    }
});

// 保存图片
var download = function(imgurl, filename){
    request.head(imgurl, function(err, res, body) {
        request(imgurl).pipe(fs.createWriteStream(filename));
        console.log(filename+' success!');
    });
}
```

在对应的日期目录里（如./img/2017/01/22/），就可以看到下载的图片了。

