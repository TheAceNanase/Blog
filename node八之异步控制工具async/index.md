# node(八)之异步控制工具async

我们在编写异步程序时，最头痛的就是不知道结果什么时候返回给我们，然后执行后面的操作，很多时候只能把后面的操作放到返回成功的函数里，或者使用计数器等方法。  

比较典型的两个就是：后面的操作需要依赖上一个异步操作的结果；多个异步操作并行执行，都执行完成后再执行接下来的操作。这两个操作中，第一个异步的程序我们可能会写成这样：

```
db.select(SQL1, function(res1){
    db.delete(SQL2, function(res2){
        db.insert(SQL3, function(res3){
            // ...
        })
    })
});
```

将后面的操作写到执行成功后的回调函数里。第2个并行的异步操作，可以使用计数器的方法，每个异步调用成功时，计数器加1，当所有的异步都调用成功后，再接着执行：

```
var count = 0;
var success = function(){
    count++;
    if(count>=3){ 
        console.log('执行完毕...');
    }
}

var select = function(){
    db.select(sql, function(res){
        success();
    })
}
var select2 = function(){
    db.select(sql, function(res){
        success();
    })
}
var select3 = function(){
    db.select(sql, function(res){
        success();
    })
}
select();
select2();
select3();
```

这些编写方式非常麻烦，而且代码逻辑比较混乱，调试起来也很不方便。那么就要用到异步控制的利器`async`了。

### 1. 介绍

async的作用是进行流程的控制，而且提供了非常多的方法可供调用。这些方法可以分为三大类：

* 集合类（Collections）
* 流程控制类（Control Flow）
* 工具类（Utils）

下面我们从这三个分类里分别挑出几个方法进行讲解。

### 2. 函数介绍

async中提供了非常多的方法可供使用，我们仅仅是讲解其中几个比较有代表性的，其他的可以访问官方文档：[http://caolan.github.io/async/docs.html](http://caolan.github.io/async/docs.html)。

#### 2.1 集合类

集合类中的方法主要有`some`, 'map', 'each', 'every'等，这些方法是对数组或组合进行某个相同的操作后，统一执行回调函数。  

我们以map为例，map对集合中的每一个元素，执行某个**相同的异步操作**，得到结果。所有的结果将汇总到最终的callback里。  

使用方法，map接收三个参数，分别是：

| 参数名称 | 类型 | 说明 |
| - | - | - |
| coll | iteratee | callback |
| Array | Iterable | Object | function | function |
| 需要处理数组，集合或其他可迭代的类型 | 迭代方法，用来对集合中的每一项进行处理。该方法接收两个参数(item, callback)；item为集合中的每一项, callback为回调函数。callback需要带有err(有时可能为null)和处理后的数据，callback(err, data) | 最终回调函数，当集合处理完毕后调用此函数，传递两个参数err和result，result为之前处理后的所有的结果的集合 |

注意：中间处理函数iteratee对coll中的每一项都是`并发`处理的，因此并不能保证iteratee按照顺序完成。不过，如果coll是个数组，最后的结果集results会按照coll中的顺序排列；如果coll是个集合（Object）类型，results会是数组类型，结果将大致按照coll的键的顺序排列（但是不同在不同的JavaScript引擎中会有可能发生变化）。

我们来举个例子，使用map获取几个文件中的内容：

```
var files = ['./file/cnode_1.txt', './file/cnode_2.txt', './file/cnode_3.txt'];

// 读取文件内容
// 第1个参数 文件名称列表的数组
// 第2个参数 传入数组中的每一项和回调函数
// 第3个参数 results为所有结果的集合
async.map(files, function(file, cb){
    fs.readFile(file, 'utf-8', function(err, data){
        cb(err, data);
    })
}, function(err, results){
    console.log( results );
})
```

而且，如果中间的处理函数比较大，不想写在map中，也可以单独写成一个函数，然后传递进去，不过参数传递还是要符合规则的：

```
var files = ['./file/cnode_1.txt', './file/cnode_2.txt', './file/cnode_3.txt'];

var read = function(file, cb){
    fs.readFile(file, 'utf-8', function(err, data){
        cb(err, data);
    })
}
async.map(files, read, function(err, result){
    console.log( result );
})
```

这里还有一个`mapLimit`，可以传递一个参数limit，用来限制并发的数量：mapLimit(coll, limit, iteratee, callbackopt)：

```
// 并发数量为2
async.mapLimit(files, 2, read, function(err, result){
    console.log( result );
})
```

同时，集合类中还有其他的方法，我们也稍微了解下：

* each : 与map类似，但是最后的回调函数里没有results，each只循环不负责处理结果
* every : 中间处理函数iteratee的参数(err, boolean)需要传递一个boolean值，若所有选项的结果都为true，则results为true
* some : 与every类似，只是只要其中一个选项的结果为true，则results为true
* filter : 对coll进行筛选，筛选出结果为true的结果
* reject : 与filter正好相反，筛选出结果为false的结果
* concat : 将每个异步操作的结果合并为一个数组

#### 2.2 流程控制类

上面的集合类是对一个集合进行相同的处理，集合中的每一项都处理完后，再对结果进行回调处理。而多个回调方法执行时，则需要对这几个回调方法进行控制了。  

多个回调方法执行时，通常有这么几个流程：

1. 串行且无关联，即执行完一个后再依次执行下一个，且相互之间无数据交互，都执行完后，再执行最后的回调函数。可以使用`async.series`
1. 串行且有关联，即执行完一个后再依次执行下一个，且上一个回调函数的结果会作为下一个回调函数的参数。可以使用`async.waterfall`
1. 并行，这几个回调函数同时并发执行，都执行完成后，再执行最后的回调函数。可以使用`async.parallel`

当然还有其他更复杂的流程，这里也只聊上面的三种情况。  

`async.series`，`async.waterfall`和`async.parallel`的语法都是一样的：

```
async.Method(coll, function(err, results){

})
```

其中coll既可以是数组，也可以是json格式的，而且results的类型与coll对应。  

串行且无关联`async.series`：

```
// 串行且无关联，数组格式
async.series([
    function(cb){
        getAllList(function(result){
            cb(null, result);
        });
    },
    function(cb){
        getAllUser(function(result){
            cb(null, result);
        });
    }
], function(err, result){
    console.log(result);
})
```

同时串行的异步可以是json格式的：

```
// 串行且无关联，json个数
async.series({
    one: function(cb){
        getAllList(function(result){
            cb(null, result);
        });
    },
    two: function(cb){
        getAllUser(function(result){
            cb(null, result);
        });
    }
}, function(err, result){
    console.log(result);
})
```

串行且有关联`async.waterfall`：

```
// 串行且上一个结果作为下一个的参数
async.waterfall([
    function(cb){
        getListById(1, function(result){
            cb(null, result);
        });
    },
    function(params, cb){
        console.log(params);
        getAllUser(function(result){
            cb(null, result);
        });
    }
], function(err, result){
    console.log(result);
})
```

并行`async.parallel`：

```
// 并行，getAllList与getAllUser同时执行
async.parallel([
    function(cb){
        getAllList(function(result){
            cb(null, result);
        });
    },
    function(cb){
        getAllUser(function(result){
            cb(null, result);
        });
    }
], function(err, result){
    console.log(result);
})
```

关于并行的异步操作，这里还有一个`async.parallelLimit`，限制并发的数量：

```
// 并发数量为2
async.parallelLimit([
    iteratee1, iteratee2, iteratee3, ...
], 2, function(err, results){
    
})
```

#### 2.3 工具类

async中也提供了不少的工具方法可供使用，比如`async.log`可以输出回调方法中的值，第1个参数为函数，后面的参数为传递给函数的参数：

```
var hello = function(name, callback) {
    setTimeout(function() {
        callback(null, 'hello ' + name);
    }, 1000);
};

// 将'world'传递给hello方法
async.log(hello, 'world'); // 'hello world'
```

这里面还有apply, dir, timeout等方法。

### 3. 总结

使用async控制异步流程非常的方便，而且也可以在前端使用，比如可以操作多个ajax请求等。~~~~

