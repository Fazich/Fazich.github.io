title: BFC与清除浮动
type:
  - tags
  - categories
date: 2016-03-12 00:38:20
tags: [css]
categories: [前端]
---
快投简历了，到时候肯定避免不了要笔试或者面试一些前端题目，所以呢有空把一些基础知识总结下

这次说到的是BFC和清除浮动

## BFC
首先说一下什么是BFC,引用下别人的概念
> BFC（Block Formatting Context）直译为“块级格式化范围”。它是指一个独立的块级渲染区域，只有Block-level BOX参与，该区域拥有一套渲染规则来约束块级盒子的布局，且与区域外部无关。

概念很抽象，根本看不懂，举个例子吧
```html
<div>
    <ul style="float:left">
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ul>
</div>
```
这段代码明眼人一看肯定是有问题的，打开浏览器审查下元素就能看到类似这个情况
![http://7xr8op.com1.z0.glb.clouddn.com/BFC.png](http://7xr8op.com1.z0.glb.clouddn.com/BFC.png)

div标签没有包住ul标签，原因很简单，因为div下的子元素浮动了，因此div的高度就塌陷了。

有了这个例子就可以更通俗的说下什么是BFC了。继续上面的例子，如果对div设置宽度是不影响到它的子元素的，只有div包住了它的子元素，设置的宽度才会作用到它的子元素上。当div包住它的子元素时，这个区域就是个BFC，该区域拥有一套渲染规则来约束块级盒子的布局。就像这样

![http://7xr8op.com1.z0.glb.clouddn.com/BFC2.png](http://7xr8op.com1.z0.glb.clouddn.com/BFC2.png)

## 清除浮动
好了，有了BFC的概念那么就说一下清除浮动。
### 原理
首先，说下清除浮动的原理，清除浮动的原理其实就是让浮动这块的元素形成BFC，就上面的例子，如果有办法让父元素包住子元素，那么这个区域就是BFC了，就好进行布局管理了，而这个方法就是清除浮动

### 方法
总的来说清除浮动的方法有3个，分别如下
#### 1、尾部添加div
```html
<div>
    <ul style="float:left">
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ul>
    <div style="clear:both"></div>
</div>
```
这个方法通俗点将就是，父元素下面的子元素都浮动了，如果有一个不浮动岂不是就能包住了？没错这个方法就是尾部添加div的方式思想。

#### 2、伪类消除

```css
<style>
    .clearfix{
        *zoom:1;
    }
    .clearfix:after{
        content:"";
        display:block;
        clear:both;
    }
</style>
<div class="clearfix">
    <ul style="float:left">
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ul>
</div>
```
这方法是第一种方法的优雅版本，加个div专门处理浮动总是不好，语义化上讲这个div是干什么的呢？所以有了伪类方式，其实核心思想也是和第一种一样。

伪类和伪元素有什么区别呢？有空专门写写吧，这里先附上一个概念
> * 伪类用于向某些选择器添加特殊的效果。
> * 伪元素用于将特殊的效果添加到某些选择器。

#### 3、BFC处理
最后就是BFC处理法总共归位四类
##### 3.1、设置父元素float的值不为none
```html
<div style="float:left">
    <ul style="float:left">
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ul>
</div>
```
这个意思就是，既然儿子们都浮动了，那么老子也跟浮吧

##### 3.2、设置父元素overflow的值不为visible
```html
<div style="overflow:hidden">
    <ul style="float:left">
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ul>
</div>
```
##### 3.3、display的值为inline-block、table-cell、table-caption
```html
<div style="display:inline-block">
    <ul style="float:left">
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ul>
</div>
```
用这个方法时注意ie8不支持inline-block
##### 3.4、position的值为absolute或fixed
```html
<div style="position:fixed">
    <ul style="float:left">
        <li>1</li>
        <li>2</li>
        <li>3</li>
    </ul>
</div>
```
## BFC触发条件
最后附上BFC的触发条件

> * 根元素
> * float的值不为none
> * overflow的值不为visible
> * display的值为inline-block、table-cell、table-caption
> * position的值为absolute或fixed

看到这里有没有发现，其实清除浮动就是围绕着BFC的触发条件再转啊。

