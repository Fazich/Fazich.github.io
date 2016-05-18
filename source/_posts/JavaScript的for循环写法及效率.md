title: JavaScript的for循环写法及效率
type:
  - tags
  - categories
date: 2016-05-18 20:31:54
tags: [for循环,效率,写法]
categories: [js]
---
for循环，再常用不过了。但是这回说下for循环是因为看代码时我居然没有看明白一个for循环的意思，真是不应该啊。这个for循环是这么写的：
```javascript
for (var i = 0, rule; rule = rules[i++];) {
    //do something
}
```
这个写法是什么意思呢？后面再说，现卖个关子，这个写法我感觉还是挺好的。

## for循环写法对效率的影响
说上面那段代码之前，先说一下for循环的效率问题。在接触js时关于for循环的写法和对效率影响的文章挺不少的。但总的来说对于for循环的写法有这么两种：

1. 不写声明变量的写法：`for(var i = 0;i<arr.length;i++){}`
2. 写声明变量的写法：`for(var i = 0,len = arr.length;i < len;i++){}`

除了for循环还有forEach()，也有文章说forEach()效率最高，推荐用forEach()写法，那么到底哪个效率高呢？做个测试来看看吧。

### 测试方案
总的测试方案如下：

1. 做一个容纳4千万的测试数组变量。
2. 分别用两种写法的for循环和foreach对这个测试变量进行遍历。
3. 在同一台稳定机器上，进行10次测试，最后取平均值。
4. 测试环境：CPU:Inter(R) Core i5-3210M,RAM:12GM,system:win10(x64)

### 测试流程

#### 制作测试变量
先用while循环做个测试变量出来，这个很简单，具体如下：
```
var testArrs = [],
	i = 0;
while(i<40000000){
	testArrs.push(i);
	i++;
}
```
#### 编写相应测试函数
测量和执行时间的代码，我用的是console.time()和console.timeEnd()来进行测试。

针对这个三个for循环，先做出三个函数出来，他们分别是

- foreach循环测试:
```javascript
function testForeach(testArrs){

	console.time('foreach');
	var newArrs = [];
	testArrs.forEach(function(i){
		newArrs.push(i);
	});
	console.timeEnd('foreach');
}
```

- 没有声明变量的for循环:
```javascript
function testNoDeclare(testArrs){

	console.time('no declare');
	var newArrs = [];
	for(var i = 0;i<testArrs.length;i++){
		newArrs.push(i);
	}
	console.timeEnd('no declare');
}
```

- 有变量声明的写法
```javascript
function testUseDeclare(testArrs){

	console.time('use declare');
	var newArrs = [];
	for(var i = 0,len = testArrs.length;i<len;i++){
		newArrs.push(i);
	}
	console.timeEnd('use declare');
}
```
#### 执行测试函数
执行测试函数这里很简单啦，就是调用函数就可以了
```javascript
testForeach(testArrs);
testNoDeclare(testArrs);
testUseDeclare(testArrs);
```
### 测试结果
经过10次测试，得到了以下结果

foreach|不写声明|写声明
-------|--------|------
2372.891ms|672.530ms|743.974ms
2431.821ms|710.275ms|805.676ms
2422.448ms|729.287ms|741.014ms
2330.894ms|730.200ms|755.390ms
2423.186ms|703.255ms|769.674ms
2379.167ms|689.811ms|741.040ms
2372.944ms|712.103ms|710.524ms
2316.005ms|726.518ms|726.522ms
2535.289ms|733.826ms|747.427ms
2560.925ms|793.680ms|817.098ms
平均值|平均值|平均值
2414.56ms|720.15ms|755.83ms

不知道结果有没有让你出乎意料呢？没想到最平常的写法效率最高，为什么？我也没想明白，谁知道就告诉我吧，但我估计写声明的写法是没有意义的。因为`len = arr.length`这个`arr.length`可能已经缓存起来了，所以我们在声明个len变量来存储是没有意义的。

最后附上全部测试代码，复制到自己的电脑上直接就可以测试了，要是有不合理的地方请告诉我吧
```javascript
var testArrs = [],
	i = 0;
while(i<40000000){
	testArrs.push(i);
	i++;
}

function testForeach(testArrs){

	console.time('foreach');
	var newArrs = [];
	testArrs.forEach(function(i){
		newArrs.push(i);
	});
	console.timeEnd('foreach');
}

function testNoDeclare(testArrs){

	console.time('no declare');
	var newArrs = [];
	for(var i = 0;i<testArrs.length;i++){
		newArrs.push(i);
	}
	console.timeEnd('no declare');
}

function testUseDeclare(testArrs){

	console.time('use declare');
	var newArrs = [];
	for(var i = 0,len = testArrs.length;i<len;i++){
		newArrs.push(i);
	}
	console.timeEnd('use declare');
}

testForeach(testArrs);
testNoDeclare(testArrs);
testUseDeclare(testArrs);
```

## for循环的特殊写法
下面说下文章刚开始说的那个我没看懂的代码，说之前先温习下再熟悉不过的for循环语法。for循环的基本语法是:
```
for (语句 1; 语句 2; 语句 3)
{
被执行的代码块
}
```

- 语句1：在循环（代码块）开始前执行
- 语句2：定义运行循环（代码块）的条件
- 语句3：在循环（代码块）已被执行之后执行

如果我们用for循环要输出1到10，我们可以这么写:
```
for(var i=0;i<10;i++){
console.log(i);
}
```
但是！根据上面的语法说明，我们也可以写成这样
```
for(var i=10;i--;){
console.log(i);
}
```
刚开始看的时候我也很疑惑，怎么能这么写？语句2放的是循环条件，i--是什么判断条件。其实不然，在语句2中，如果返回true循环会继续执行。在js中0,null,undefined,false,'',""作为条件判断时，其结果为false，也就说当i--到0的时候就是false，循环就终止了。

再回到文章开头的代码
```
for (var i = 0, rule; rule = rules[i++];) {
    //do something
}
```
这个`rule = rules[i++]`就是判断条件，当成为undefined时就会终止循环啦。所以这段代码换成普通写法就是这样的：
```
for(var i = 0;i < rules.length;i++){
	var rule = rules[i]
}
```
其实就是把判断和赋值放到一起了，一边循环一边赋值。是不是挺简单？



