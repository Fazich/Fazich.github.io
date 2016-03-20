title: 用Nodejs做爬虫
type:
  - tags
  - categories
date: 2016-02-25 22:56:58
tags: Nodejs
categories: Nodejs
---
## 引言
提起爬虫，最容易想到的就是python，因为python给人的感觉就是啥都能干，但是之前用python做爬虫的过程还是很不爽的，主要问题来自这么几个方面：第一个是对爬来的网页dom操作上，第二个就是编码的处理，第三就是多线程，所以用python做爬虫其实并不是很爽，有没有更爽的方式呢？当然有那就是node.js！
## Nodejs做爬虫的优劣
### 首先说一下node做爬虫的优势

第一个就是他的驱动语言是JavaScript。JavaScript在nodejs诞生之前是运行在浏览器上的脚本语言，其优势就是对网页上的dom元素进行操作，在网页操作上这是别的语言无法比拟的。

第二就是nodejs是单线程异步的。听起来很奇怪，单线程怎么能够异步呢？想一下学操作系统的时候，单核cpu为什么能够进行多任务处理？道理也是类似，在操作系统中进程对CPU的占有进行时间切片，每一个进程占有的时间很短，但是所有进程循环很多次，因此看起就像是多个任务在同时处理。js也是一样，js里有事件池，CPU会在事件池循环处理已经响应的事件，未处理完的事件不会放到事件池里，因此不会阻塞后续的操作。在爬虫上这样的优势就是在并发爬取页面上，一个页面未返回不会阻塞后面的页面继续加载，要做到这个不用像python那样需要多线程。

### 其次是node的劣势

首先是异步并发上。处理的好很方便，处理的不好就会很麻烦。例如要爬取10个页面，用node不做异步处理话，那返回的结果可不一定是按1、2、3、4……这个顺序，很可能是随机。解决的办法就是增加一个页面的序列戳，让爬取的数据生成csv文件，然后重新排序。

第二个是数据处理上的劣势，这点是不如python的，如果只是单纯的爬数据，用node当然很好，但是如果用爬来的数据继续做统计分析，做个回归分析聚类啥的话，那就不能用node一步到底了。

## 如何用nodejs做爬虫
下面就要说一下如何用nodejs做爬虫了
### 1、初始化项目文件
在对应的项目文件夹下执行`npm init`来初始化一个package.json文件
### 2、安装request和cheerio依赖包
request听起来很熟悉吧，跟python里request功能一样。它的功能就是建立起对目标网页的链接，并返回相应的数据，这个不难理解。

cheerio的功能是用来操作dom元素的，他可以把request返回来的数据转换成可供dom操作的数据，更重要的cheerio的api跟jquery一样，用$来选取对应的dom结点，是不很方便？对一个前端程序员来说，这比python的什么xpath和beautisoup方便了不知道多少啊哈哈

安装命令也很简单，分别是`npm install request --save`和`npm install cheerio`
### 3、引入依赖包并使用
接下来就用request和cherrio写一个爬虫吧！

首先引入依赖
```javascript
var request = require("request");
var cheerio = require("cheerio");
```
接下来就以爬取我们学校的新闻页为例吧，我们学校的新闻页面链接是[http://news.shu.edu.cn/Default.aspx?tabid=446](http://news.shu.edu.cn/Default.aspx?tabid=446)

然后调用request的接口
```javascript
request('http://news.shu.edu.cn/Default.aspx?tabid=446',function(err,result){
    if(err){
        console.log(err);
    }
    console.log(result.body);
})
```
运行一下结果就是这样的

![http://7xr8op.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20160226182051.png](http://7xr8op.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20160226182051.png-width)

是不是很激动哈哈，html返回回来了。这样还是不够的，接下就是要处理下返回的数据，并提炼出我们想要获得的信息，这就轮到cheerio登场了

将request返回的结果传入cheerio中,并获得想要获取的信息，看代码是不是想在写脚本的感觉？
```javascript
request('http://news.shu.edu.cn/Default.aspx?tabid=446',function(err,result){
    if(err){
        console.log(err);
    }
    var $ = cheerio.load(result.body);
   $('a[id^="dnn"]').each(function(index,element){
       console.log($(element).text());
   })
})
```
运行下结果如下：

![http://7xr8op.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20160226191031.png](http://7xr8op.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20160226191031.png-width)

这样一个简单的爬虫就完成啦，是不是很简单啊，当然这远远是不够的。
### 4、设置请求头
众所周知，http协议里，建立连接要发送请求头header，对于一些动态网页的爬取有时候需要设置user agent、cookies等等，那么这些设置如何使用呢？
具体事例代码如下：
```javascript
var options = {
    url: startUrl+'?page=1',
    method: 'GET',
    charset: "utf-8",
    headers: {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/45.0.2454.93 Safari/537.36",
        "cookie": cookies
    }
};
request(options,function(err,response,body){
//...
})
```

### 5并发控制
爬取一个页面还好，要是页面多了就是无限制并发了，那肯定就要被封了，所以就要有一个并发控制，这里要介绍的就是async。跟上述一样要通过`npm install async --save`来安装并通过`var async = require("async")`来引入。

具体以一个限制并发的方式来示例一下
```javascript
async.mapLimit(5,function(url,callback)){
//...
fetch(url,callback)
})
```
这里面的5就是限制的并发数量，可以自由发挥，最后千万不要忘了执行完后callback，因为如果没有的话就会阻塞了，async并不知道他限制的函数是否执行完毕，因此不会释放掉。

## 总结
至此呢，Nodejs爬虫的核心就已经介绍完毕了，剩下就完全可以自由发挥了，最后附上一个自己做的简单的新浪微博的爬虫吧[https://github.com/Fazich/nodeSpider](https://github.com/Fazich/nodeSpider)




