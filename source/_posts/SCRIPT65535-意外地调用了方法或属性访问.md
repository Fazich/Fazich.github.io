title: 'SCRIPT65535:意外地调用了方法或属性访问'
type:
  - tags
  - categories
date: 2016-03-09 22:08:52
tags: [debug,ie]
categories: [js]
---
本文以这个bug报错信息命名，来阐述下这个问题的解决办法。

## 问题概述
首先说一下报错问题来自哪里。

这是一条脚本的报错信息，是我在KISSY中遇到了，报错模块在base.js中，这是KISSY里的一个核心模块。当然在jQuery中也一样会遇到，因为这是dom操作中遇到的bug。初次看到这个bug很无奈，居然是框架里的报错信息不是自己写的代码，完全无从下手。

其次就是什么时候报错。这个报错产生在ie6、7、8中，在ie8以上没有报错，坑爹的ie啊，FUCK！！

## 解决办法
下面直奔主题来说一下解决的办法
先说一下为什么会出现这个错误，根本原因就是在ie8以下的浏览器里，操作一个DOM的非法属性时会出现，举例说明下

第一种情况，操作了一个DOM不该有的属性

```html
<input type="text" id="test">
```
如果要改变这个dom的值应该是修改它的value属性
```javascript
$('#test').val('hello');
```
但是如果写成了
```javascript
$('#test').html('hello');
```
在ie8以上的浏览器也OK，给你识别纠正了，但是在ie8以下就不行了

第二种情况，标签的闭合上也就是我出错的地方
```html
<input type="text" id="test"></input>
<p>hello</p>
```
由于编辑器的自动补全，往往会忽略一些细节上的错误例如为input自动加了闭合标签，如果只操作这个dom的值无影响，但是如果操作下个同级元素就会报错了例如
```javascript
$('#test').next().html('hello');
```
当debug到test的下一级标签时，便会发现这个dom的nodeName是input而不是p，input操作html属性是不合法的，因此就报错啦。解决方法就是把input的闭合标签干掉