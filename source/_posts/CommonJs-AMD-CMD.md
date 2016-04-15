title: 'CommonJs,AMD,CMD'
type:
  - tags
  - categories
date: 2016-03-21 21:12:29
tags: [模块化,标准]
categories: [js]
---
前端项目做大了做复杂了免不了要模块化，说起模块化自然就要提到这三者，因为这3个是前端模块化的标准，没有它们js不会像现在这样流行，也不会有npm这个包管理工具，更不会有如此多的前端开发框架。下面简要概述下这三个模块化标准吧。
## CommonJs
CommonJs服务器端的模块化规范，典型的例子就是Nodejs。根据CommonJS规范，一个单独的文件就是一个模块。加载模块使用require方法，该方法读取一个文件并执行，最后返回文件内部的exports对象。

CommonJS 加载模块是同步的，所以只有加载完成才能执行后面的操作。像Nodejs主要用于服务器，加载的模块文件一般都已经存在本地硬盘，所以加载起来比较快，不用考虑异步加载的方式，所以CommonJS规范比较适用。但如果是浏览器环境，要从服务器加载模块，这是就必须采用异步模式。所以就有了另外两个规范，也就是AMD，CMD解决方案。

一个CommonJs规范的代码如下:
```
//以下代码在一个js文件里，一个文件就是一个模块

//私有变量 
var test = "private"; 

//公有方法 
function example () { 

    this.foo = function () { 
        // do someing ... 
    } 
    this.bar = function () { 
        //do someing ... 
    } 
} 

//exports对象上的方法和变量是公有的 
var example = new example(); 
exports.foobar = foobar; 
```

所以CommonJs的关键就是，异步，还有一个文件是一个模块，另外天生内置export、require、require方法，至于为什么？推荐下朴灵的《深入浅出Nodejs》，简单来说就是给包装了一下。

## AMD
接下来就是AMD，可不是那个显卡厂商AMD，而是Asynchromous Module Definition，直接翻译下就是异步模块定义。这是requireJS提出的一个标准，其典型的特点就是异步，另一个特点就是模块加载前置，提前执行，AMD标准的特点就是define方法，举个AMD例子吧：

```
//通过数组引入依赖 ，回调函数通过形参传入依赖 
define(['someModule1', ‘someModule2’], function (someModule1, someModule2) { 

    function foo () { 
        /// someing 
        someModule1.test(); 
    } 

    return {foo: foo} 
}); 
```

AMD也兼容CommonJS规范，写法如下:

```javascript
define(function (require, exports, module) { 

    var reqModule = require("./someModule"); 
    requModule.test(); 
     
    exports.asplode = function () { 
        //someing 
    } 
}); 
```

## CMD
CMD是SeaJS推广的规范，这个标准也是中国人提出来的，SeaJS的其中一个作者就是玉伯哈哈。 

CMD和AMD的区别有以下几点： 

 * 对于依赖的模块AMD是提前执行，CMD是延迟执行。不过RequireJS从2.0开始，也改成可以延迟执行（根据写法不同，处理方式不通过）。 

 * CMD推崇依赖就近，AMD推崇依赖前置。

例子如下：

```javascript
//AMD 
define(['./a','./b'], function (a, b) { 

    //依赖一开始就写好 
    a.test(); 
    b.test(); 
}); 
```

```javascript
//CMD 
define(function (requie, exports, module) { 
    
    //依赖可以就近书写 
    var a = require('./a'); 
    a.test();  
    ... 
    //软依赖 
    if (status) { 

        var b = requie('./b'); 
        b.test(); 
    } 
}); 
``` 

虽然 AMD也支持CMD写法，但依赖前置是官方文档的默认模块定义写法。 

另外AMD的api默认是一个当多个用，CMD严格的区分推崇职责单一。例如：AMD里require分全局的和局部的。CMD里面没有全局的 require,提供seajs.use()来实现模块系统的加载启动。CMD里每个API都简单纯粹。

