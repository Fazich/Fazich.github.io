title: javascript设计模式之单例模式
type:
  - tags
  - categories
date: 2016-11-24 19:31:59
tags: [设计模式,javascript]
categories: [js]
---
#  单例模式
## 定义
保证一个类仅有一个实例，并提供一个访问它的全局访问点。

也就是说一个类只能有一个接口，且只能通过这个接口来访问他，通过这种方式可以节约内存，实现类中实例的最大复用。

在单利模式中，惰性单例是单例模式的重点。惰性单例是指在需要的时候才创建对象实例。

## 代码示例
单例实例
```javascript
var ProxySingletonCreateDiv = (function(){
  var instance; // 实例保存对象
  return function(html){
    if ( !instance ){
      instance = new CreateDiv(html); //需要的时候再去创建
    }
    return instance; //再次调用时返回该实例
  }
})();
  var a = new ProxySingletonCreateDiv('sven1');
  var b = new ProxySingletonCreateDiv('sven2');
  alert ( a === b ); //true
```
