title: javascript设计模式之迭代器模式
type:
  - tags
  - categories
date: 2016-11-24 19:41:31
tags: [设计模式,javascript]
categories: [js]
---
# 迭代器模式
## 定义
迭代器模式是提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示。

迭代器可分为内部迭代器和外部迭代器
## 内部迭代器
内部迭代器是指迭代器内部已经定义好了迭代规则，它完全接手整个迭代过程。例如java中的each函数，javascript里的forEach函数等

## 外部迭代器
外部迭代器是指必须显式的请求迭代下一个元素，这跟es6的generator函数很像

外部迭代器代码实例
```javascript
var Iterator = function(obj) {
    var current = 0; //可看作是指向obj内部属性的一个指针，通过改变current的值来对内部的属性进行迭代

    var next = function() {
        current += 1;
    };

    var isDone = function() {
        return current >= obj.length;
    };

    var getCurrItem = function() {
        return obj[current];
    };

    return {
        next: next,
        isDone: isDone,
        getCurrItem: getCurrItem
    }

};
```
