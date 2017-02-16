title: javascript设计模式之代理模式
type:
  - tags
  - categories
date: 2016-11-24 19:35:19
tags: [设计模式,javascript]
categories: [js]
---
# 代理模式
## 定义
为其他对象提供一种代理以控制对这个对象的访问。在某些情况下，一个对象不适合或者不能直接引用另一个对象，而代理对象可以在客户端和目标对象之间起到中介的作用。

代理模式的关键是，当客户不方便直接访问一个对象或者不满足需要的时候，提供一个替身对象来控制对这个对象的访问，客户实际上访问的是替身对象。替身对象对请求做出一些处理之后，再把请求转交给本体对象。

关键在于本地和代理之间对外提供了一致的接口，在客户看来，代理对象和本体是一致的，代理接受请求的过程对于用户来说是透明的，用户并不清楚代理和本体的区别。

代理模式主要由三部分组成，分别是：

1. 客户类。也就是请求发起方。
2. 代理类。作为客户请求的承接，将请求转发给本体。
3. 本体类。也就是指令的接收方。

## 示例代码
使用代理预加载图片
```javascript
//本体
var myImage = (function() {
    var imgNode = document.createElement('img');
    document.body.appendChild(imgNode);
    return function(src) { //代理与本体之间提供了一致的接口调用
        imgNode.src = src;
    }
})();
//代理
var proxyImage = (function() {
    var img = new Image;
    img.onload = function() {
        myImage(this.src); //图片加载完成后替换地址
    }
    return function(src) { //代理与本体之间提供了一致的接口调用
        myImage('file:///C:/Users/svenzeng/Desktop/loading.gif'); //先使用本地图片做瑜伽在
        img.src = src;
    }
})();
//客户调用
proxyImage('http://imgcache.qq.com/music//N/k/000GGDys0yA0Nk.jpg');
```
虚拟代理合并http请求
```javascript
var synchronousFile = function(id) { //本体与代理之间接口一致
    console.log('开始同步文件，id 为: ' + id);
};

var proxySynchronousFile = (function() {
    var cache = [], // 保存一段时间内需要同步的 ID
        timer; // 定时器
    return function(id) { //本体与代理之间接口一致
        cache.push = []; //中间的代理缓存区
        if (time) { //保证不会覆盖
            return
        }
        time = setTimeout(function() {
            synchronousFile(cache.join(','));
            clearTimeout(timer); // 清空定时器
            timer = null;
            cache.length = 0; // 清空 ID 集合
        }, 2000)
    }
})()

var checkbox = document.getElementsByTagName('input');

for (var i = 0, c; c = checkbox[i++];) {
    c.onclick = function() {
        if (this.checked === true) {
            proxySynchronousFile(this.id);
        }
    }
};
```
计算输出
```javascript
/**************** 计算乘积 *****************/
var mult = function() {
    var a = 1;
    for (var i = 0, l = arguments.length; i < l; i++) {
        a = a * arguments[i];
    }
    return a;
};

/**************** 计算加和 *****************/
var plus = function() {
    var a = 0;
    for (var i = 0, l = arguments.length; i < l; i++) {
        a = a + arguments[i];
    }
    return a;
};

/**************** 创建缓存代理的工厂 *****************/
var createProxyFactory = function(fn) {
    var cache = {}; //缓存队列
    return function() {
        var args = Array.prototype.join.call(arguments, ',');
        if (args in cache) {
            return cache[args]; //如果请求曾经计算过直接吐出结果
        }
        return cache[args] = fn.apply(this, arguments);
    }

};

var proxyMult = createProxyFactory(mult),
    proxyPlus = createProxyFactory(plus);

alert(proxyMult(1, 2, 3, 4)); //计算输出24
alert(proxyMult(1, 2, 3, 4)); //缓存输出24
alert(proxyPlus(1, 2, 3, 4)); //计算输出10
alert(proxyPlus(1, 2, 3, 4)); //缓存输出10
```
