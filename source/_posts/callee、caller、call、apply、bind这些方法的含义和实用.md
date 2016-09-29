title: callee、caller、call、apply、bind这些方法的含义和使用
type:
  - tags
  - categories
date: 2016-09-27 21:09:04
tags: [callee,caller,call,apply,bind]
categories: [js]
---
在学校时间比较充裕，所以利用这段时间好好回顾下了js的基础。这回说下这五个函数属性方法，它们分别是：callee、caller、call、apply、bind
这5个方法在刚学js的时候一直理解不透，在实际的开发中也一直避免使用这5个方法，原因嘛当然很简单，那就是不理解，怕埋坑。
## Function类型
在讲这5个方法之前，首先先讲一下储备知识，能更加方便的理解这5个方法。
众所周知js的数据类型有两大类型，它们分别是基本类型和引用类型。基本类型就是那5个基本的js数据类型：string、number、boolean、null、undefined。对于这5个基本数据类型可以用typeof来进行判断。
引用类型说的通俗点就是用typeof不能区分的，也就是object。对于Array、RegExp、Function等等这些都是引用类型。
说这些有什么用呢？那是因为**由于函数是对象，因此函数名实际上也是一个指向函数对象的指针，不会与某个函数绑定。**举个例子:
```javascript
function add(num){
	return num+100;
}
function add(num){
	return num+200;
}
var result = add(100) //300
```
为什么是后面覆盖了前面呢？因为一开始函数名add指向的是num+100的函数，后来的又写了一个名为add的函数，因此这个函数名add指向了num+200的函数。其实上面的代码也等同于下面的代码：
```javascript
var add = function(num){
	return num+100;
}
add = function(num){
	return num+200;
}
var result = add(100) //300
```
这也js没有重载的原因所在。
## callee,caller
上面那5个方法可以大体分为两类，一类是callee,caller，另一类是call,apply,bind。先从callee,caller开始。
### callee
首先从一个斐波那契数列开始
```javascript
function factorial(num){
	if(num<=1){
		return 1;
	}else{
		return num*factorial(num-1)
	}
}
```
这么看很完美，但是有一个问题就是递归的时候函数的执行与函数名紧紧的耦合在一起了，例如:
```
var testFactorial = factorial;
factorial = function(){
	return 0;
}
testFactorial(5); //0
```
之所以是0的原因是factorial这个变量指向了`return 0`的这个匿名函数，在原来这个函数内部的`num*factorial(num-1)`会去指向factorial变量新指向的函数，因此就是0。听起来很绕，谨记着函数是对象，函数名是指针的概念看下面的图就能明白了。
![解释图片](http://7xr8op.com1.z0.glb.clouddn.com/callee.png)
接下来我们用callee来对这个斐波那契数列进行解耦，目的很简单让函数里return指向自身，代码如下
```javascript
function factorial(num){
    if(num<=1){
        return 1;
    }else{
        return num*arguments.callee(num-1)
    }
}

var testFactorial = factorial;
factorial = function(){
    return 0;
}
console.log(testFactorial(5)); //120
```
这样以来就解决了刚才的问题，原理如下图所示：
![解释图片2](http://7xr8op.com1.z0.glb.clouddn.com/callee2.png)
那么就可以用一句话来概括callee了，那就是**callee是一个指针，指向拥有这个argument对象的函数**
### caller
caller含义，一句话就是**caller这个属性保存着调用当前函数的函数的引用，如果是在全局作用域中调用当前函数，它的值为null**。还是举个例子：
```javascript
function outer(){
    inner();
}
function inner(){
    console.log(inner.caller);
}
outer(); //显示outer的源代码
```
因为outer()调用了inner()，所以inner.caller就指向outer()。为了实现松散的耦合也可以把inner.caller换成arguments.callee.caller。
## call,apply,bind

### call,apply
首先是call和apply，这两个完全可以放一块，因为他们的作用完全是一样的，区别也仅在于接受参数的方式不同。apply第二个参数可以是Array的实例，也是一个argument对象，而call方法接收的第二个参数必须逐个列出来。
call和apply是改变函数执行的作用域的，说的通俗点就是改变函数体内this的指向。举个例子
```javascript
window.color = 'red'
var o = {
	color:'blue'
}
function sayColor(color){
	console.log(this.color+' param:'+color)
}
sayColor.call(window,'black'); //red param:black
sayColor.apply(window,['black']);//red param:black

sayColor.call(o,'black') //blue param:black
sayColor.apply(o,['black') //blue param:black
```
这里面color存在在两个环境里，分别全局环境中和对象o中。使用了call或apply方法后，接收的第一个参数就是改变this的指向，将this指向参数传入的作用域中去，因此输出了不同环境下的color值。
使用apply和call的最大好处就是，对象不需要与方法有任何耦合关系。
### bind
bin方法是ECMAscript5定义的一个方法。这个方法会创建一个函数的实例，其this值会被绑定到传给bind()函数的值，还是举个例子：
```javascript
window.color = 'red'
var o = {
	color:'blue'
}
function sayColor(color){
	console.log(this.color)
}
var objSayColor = sayColor.bind(o);
objSayColor(); //blue
```
bind方法会创建一个函数实例，因此需要有变量指向这个函数实例。使用bind的好处，除了能够解耦对象和方法外，在全局作用域中调用这个函数，也能够将this指向所对应的环境。
