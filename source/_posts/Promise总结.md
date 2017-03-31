title: Promise总结
type:
  - tags
  - categories
date: 2017-03-31 14:13:45
tags: [es6,promise]
categories: [js]
---
在js的异步编程中，一个非常麻烦的情况的回调陷阱，当回调函数嵌套着回调函数的时候，那这个代码基本上就不能看了。例如:
```javascript
asyncFun1(param, function (er, data) {
    if (er) return cb(er);
    asyncFun2(data,function (er,data) {
      if (er) return cb(er);
      asyncFun3(data, function (er, data) {
        if (er) return cb(er);
        cb(data);
      })
    })
  })
```
不能看的原因很简单，因为它不符合人们正常的阅读习惯和思维方式，所以为了解决这个回调陷阱，promise就诞生了。通过promise改造上面的例子就变得清晰了不少
```javascript
asyncFun1(param).then(asyncFun2(data).then(asyncFun3(data).then(cb(data))).catch(cb(er))
```
通过例子能够看出，promise把纵向的回调函数变成横向的链式调用，这让回调函数变得更容易的书写阅读和可维护。

# Promise语法
promise的语法十分简洁
```javascript
new Promise(
    /* executor */ 
    function(resolve, reject){...}
);
```
## 参数
promise内部有一个executor，executor是一个执行器函数，当调用这个promise函数时它会被promise立即执行。在executor中会传递给其resolve和reject两个函数，在executor内部，如果resolve被调用则意味着该promise被成功解析，而当reject被调用时代表着该异步代码调用失败，promise被拒绝进入到reject函数中处理。
值得一提的是promise通常是用来对一个异步函数做一个封装，使其更容易的被调用和使用，例如:
```javascript
var promiseAjax = function(url){
  return new Promise(function(resolve,reject){
    $.ajax({
      url: url,
      success: resolve(data),
      error:reject(e)
  })
}
//通过promise包装后ajax使用起来会更加的得心应手，看起来就像是在使用同步函数一样
promiseAjax('getUserInfo').then(showData(data)).catch(uploadErr(e))；
```

# 细节
总的来看promise对象是一个代理对象，它代理了放在promise里面的异步函数，并为其执行结果的成功和失败分别绑定了相应的处理方法。这让异步方法可以像同步方法那样使用，虽然结果并不是立即返回的。
## 状态
promise有以下三个状态：
- pending: 初始状态, 未完成或拒绝。
- fulfilled: 意味着操作成功完成。
- rejected: 意味着操作失败。

pending状态的Promise对象可能被填充了（fulfilled）值，也可能被某种理由（异常信息）拒绝（reject）了。当其中任一种情况出现时，Promise对象的then方法绑定的处理方法（handlers ）就会被调用(then方法包含两个参数：onfulfilled和onrejected，它们都是Function类型。当值被填充时，调用then的onfulfilled方法，当Promise被拒绝时，调用then的onrejected方法， 所以在异步操作的完成和绑定处理方法之间不存在竞争)
promise的状态变化图:
![promise的状态变化图](http://7xr8op.com1.z0.glb.clouddn.com/promises.png)
**注意的是primise里的pending状态可以转变为fulfilled或rejected，而fulfilled与rejected之间无法相互转换，转变的过程是不可逆的，转变一旦完成promise对象就不能被修改。**

# 源码细节
primise如此神奇，那具体代码是怎么实现的呢？
## 源码实现
```javascript
/*
我们要满足状态只能三种状态：PENDING,FULFILLED,REJECTED三种状态，且状态只能由PENDING=>FULFILLED,或者PENDING=>REJECTED
*/
var PENDING = 0;
var FULFILLED = 1;
var REJECTED = 2;
/*
value状态为执行成功事件的入参，deferreds保存着状态改变之后的需要处理的函数以及promise子节点，构造函数里面应该包含这三个属性的初始化
 */
function Promise(callback) {
  this.status = PENDING;
  this.value = null;
  this.defferd = [];
  setTimeout(callback.bind(this, this.resolve.bind(this), this.reject.bind(this)), 0);
}

Promise.prototype = {
  constructor: Promise,
  //触发改变promise状态到FULFILLED
  resolve: function (result) {
    this.status = FULFILLED;
    this.value = result;
    this.done();
  },
  //触发改变promise状态到REJECTED
  reject: function (error) {
    this.status = REJECTED;
    this.value = error;
  },
  //处理defferd
  handle: function (fn) {
    if (!fn) {
      return;
    }
    var value = this.value;
    var t = this.status;
    var p;
    if (t == PENDING) {
      this.defferd.push(fn);
    } else {
      if (t == FULFILLED && typeof fn.onfulfiled == 'function') {
        p = fn.onfulfiled(value);
      }
      if (t == REJECTED && typeof fn.onrejected == 'function') {
        p = fn.onrejected(value);
      }
      var promise = fn.promise;
      if (promise) {
        if (p && p.constructor == Promise) {
          p.defferd = promise.defferd;
        } else {
          p = this;
          p.defferd = promise.defferd;
          this.done();
        }
      }
    }
  },
  //触发promise defferd里面需要执行的函数
  done: function () {
    var status = this.status;
    if (status == PENDING) {
      return;
    }
    var defferd = this.defferd;
    for (var i = 0; i < defferd.length; i++) {
      this.handle(defferd[i]);
    }
  },
  /*储存then函数里面的事件
  返回promise对象
  defferd函数当前promise对象里面
  */
  then: function (success, fail) {
    var o = {
      onfulfiled: success,
      onrejected: fail
    };
    var status = this.status;
    o.promise = new this.constructor(function () {

    });
    if (status == PENDING) {
      this.defferd.push(o);
    } else if (status == FULFILLED || status == REJECTED) {
      this.handle(o);
    }
    return o.promise;
  }
};
```
## 源码流程
附上一张源码流程图
![promise流程图](http://7xr8op.com1.z0.glb.clouddn.com/2873136445-5799d21591606_articlex%20%281%29.png)

# 参考
1. [promise源码分析](https://segmentfault.com/a/1190000006103601)
2. [Promise 对象](http://es6.ruanyifeng.com/#docs/promise)
3. [Promise](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)
4. [闲话Promise机制](http://www.cnblogs.com/dojo-lzz/p/4340897.html)