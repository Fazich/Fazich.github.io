title: 几个常用的js函数实现
type:
  - tags
  - categories
date: 2017-03-19 21:22:21
tags: [基础]
categories: [js]
---
本文的翻译自[7 Essential JavaScript Functions](https://davidwalsh.name/essential-javascript-functions)

我记得之前由于浏览器厂商对于js的支持程度不同，我们需要自己写一些简单函数来实现一些边缘功能，甚至是一些基本功能例如`addEventListener`和`attachEvent`。随着发展，这些功能大部分的浏览器都已经支持了，但是仍有一些函数未实现，需要开发者们掌握，以便为了更容易的开发。
# debounce
debounce函数能够很好的提升事件处理的性能。如果你没有为`scroll`，`resize`，`key*`事件使用debounce函数的话，那是十分糟糕的。以下是debounce的实现，这能让你的代码更有效率
```javascript
// 返回一个函数，如果它被不间断地调用，它将不会得到执行。该函数在停止调用 N 毫秒后，再次调用它才会得到执行。如果有传递 ‘immediate’ 参数，会马上将函数安排到执行队列中，而不会延迟。
function debounce(func, wait, immediate) {
	var timeout;
	return function() {
		var context = this, args = arguments;
		var later = function() {
			timeout = null;
			if (!immediate) func.apply(context, args);
		};
		var callNow = immediate && !timeout;
		clearTimeout(timeout);
		timeout = setTimeout(later, wait); //当immediate为false时timeout是执行函数，为true时timeout执行为空，不会与下面的apply冲突
		if (callNow) func.apply(context, args);
	};
};

// Usage
var myEfficientFn = debounce(function() {
	// All the taxing stuff you do
}, 250);
window.addEventListener('resize', myEfficientFn);
```
`debounce`函数不允许回调函数在指定时间内执行多次。这对一些频繁触发事件但执行一次回调的函数十分重要。
个人备注：在一些浏览器事件中我们不希望事件触发后立刻执行的，例如scroll到浏览器底部加载数据这个时间，有时候用户触底可能是无意操作，或者触底后就会关闭页面并不会继续浏览接下来的内容，这就会带来不必要的开销。因此通常的措施是为这个响应时间加上setTimeout来延迟执行，在延迟期间在此触发并不会继续执行。
# poll
尽管上面提及了`debounce`函数，但如果时间不存在的话，你就不能插入一个事件来判断一个所需要的状态，你需要每隔一段时间来检查所需要的状态
```javascript
// The polling function
function poll(fn, timeout, interval) {
    var endTime = Number(new Date()) + (timeout || 2000);
    interval = interval || 100;

    var checkCondition = function(resolve, reject) {
		//当条件满足时resolve
        var result = fn();
        if(result) {
            resolve(result);
        }
		//若条件没有满足且没有超时则继续
        else if (Number(new Date()) < endTime) {
            setTimeout(checkCondition, interval, resolve, reject);
        }
        //超时且未获得所需状态, reject!
        else {
            reject(new Error('timed out for ' + fn + ': ' + arguments));
        }
    };

    return new Promise(checkCondition);
}

//使用：确保元素可见
poll(function() {
	return document.getElementById('lightbox').offsetWidth > 0;
}, 2000, 150).then(function() {
    // Polling 结束, 做其他事情
}).catch(function() {
    // Polling 超时, 处理错误
});
```

# once
有时候你想要一个函数只执行一次，类似于使用onload事件，这段代码可以提供给你想要的
```javascript
function once(fn, context) { 
	var result;

	return function() { 
		if(fn) {
			result = fn.apply(context || this, arguments);
			fn = null; //执行一次之后清空参数
		}

		return result;
	};
}

// Usage
var canOnlyFireOnce = once(function() {
	console.log('Fired!');
});

canOnlyFireOnce(); // "Fired!"
canOnlyFireOnce(); // nada
```
once函数能够保证函数只能执行一次，从而避免了多次初始化

#getAbsoluteUrl
通过一个字符串变量获得一个绝对URL并不如你想象的简单。对于URL构造器来说如果你不提供需要的参数的话，它就不能正常工作，然后又时候你都不能提供。这里有一个优雅的技巧，只需要传递一个字符串就能得到绝对URL
```javascript
var getAbsoluteUrl = (function() {
	var a; //通过闭包缓存

	return function(url) {
		if(!a) a = document.createElement('a');
		a.href = url;

		return a.href;
	};
})();

// Usage
getAbsoluteUrl('/something'); // https://davidwalsh.name/something
```
看起来href的处理和url看起来毫无意义，但是通过return提供了一个可靠的绝对URL
个人备注：通过创建a标签，因为a标签的href属性会有当前页面的绝对路径，因此就能获得绝对路径
# isNative
如果你想知道一个函数是否是原生的，并且不知道能否覆盖他。下面这段代码就能给你答案
```javascript
;(function() {

  // Used to resolve the internal `[[Class]]` of values
  //用来处理内部[[Class]]的值
  var toString = Object.prototype.toString;
  
  // Used to resolve the decompiled source of functions
  //解析函数的反编译代码
  var fnToString = Function.prototype.toString;
  
  // Used to detect host constructors (Safari > 4; really typed array specific)
  //用来检测宿主构造器
  var reHostCtor = /^\[object .+?Constructor\]$/;

  // Compile a regexp using a common native method as a template.
  //用一个通用的原生方法作为模版来编译一个正则表达式
  // We chose `Object#toString` because there's a good chance it is not being mucked with.
  //我们选择`Object#toString`因为它一般不会被污染
  var reNative = RegExp('^' +
    // Coerce `Object#toString` to a string
	//将`Object#toString`强制转换为字符串
    String(toString)
    // Escape any special regexp characters
	//转义任何特殊的正则字符
    .replace(/[.*+?^${}()|[\]\/\\]/g, '\\$&')
    // Replace mentions of `toString` with `.*?` to keep the template generic.
	//用 '.*?' 替换提及的 'toString' ，以保持模板的通用性
    // Replace thing like `for ...` to support environments like Rhino which add extra info
    // such as method arity.
	//将 'for ...' 之类的字符替换掉，以兼容 Rhino 等环境，因为这些环境添加了额外的信息，如方法参数数量
    .replace(/toString|(function).*?(?=\\\()| for .+?(?=\\\])/g, '$1.*?') + '$'
  );
  
  function isNative(value) {
    var type = typeof value;
    return type == 'function'
      // Use `Function#toString` to bypass the value's own `toString` method
      // and avoid being faked out.
	  // 用 'Function#toString' （fnToString）绕过了值（value）本身的 'toString' 方法，以免被伪造所欺骗。
      ? reNative.test(fnToString.call(value))
      // Fallback to a host object check because some environments will represent
      // things like typed arrays as DOM methods which may not conform to the
      // normal native pattern.
	  // 回退到宿主对象的检查，因为某些环境（浏览器）将类型数组（typed arrays）之类的东西当作 DOM 方法，此时可能不遵循标准的原生正则表达式
      : (value && type == 'object' && reHostCtor.test(toString.call(value))) || false;
  }
  
  //导出函数
  module.exports = isNative;
}());

// Usage
isNative(alert); // true
isNative(myCustomFunction); // false
```
这个函数虽然不完美但是可用
# insertRule
我们都知道我们可以通过选择器（例如`document.querySelectorAll`）来获取一个NodeList，并且给其中的每一个元素赋一个样式，但是有什么更高效的方式来处理这个事情呢（就像在css里那样）
```javascript
var sheet = (function() {
	// 创建style标签
	var style = document.createElement('style');
	//如果需要媒介类型，这里需要添加下面的代码
	// style.setAttribute('media', 'screen')
	// style.setAttribute('media', 'only screen and (max-width : 1024px)')

	// WebKit hack :(
	style.appendChild(document.createTextNode(''));

	// 将style标签添加到页面里
	document.head.appendChild(style);

	return style.sheet;
})();

// Usage
sheet.insertRule("header { float: left; opacity: 0.8; }", 1);
```
这对与动态的重度依赖Ajax的网站是十分游泳的。如果你为一个选择器设置样式，那么你就不需要为每个匹配到的元素都设置样式（现在或将来）。
# matcheSelector
我们经常会在进行下一步操作前进行输入校验，以确保是一个可靠值，或确保表单数据是有效的，等等。但我们平时是怎么确保一个元素是否有资格进行进一步操作呢？如果一个元素有给定匹配的选择器，那么你可以使用 matchesSelector 函数来校验：
```javascript
function matchesSelector(el, selector) {
	var p = Element.prototype;
	var f = p.matches || p.webkitMatchesSelector || p.mozMatchesSelector || p.msMatchesSelector || function(s) {
		return [].indexOf.call(document.querySelectorAll(s), this) !== -1;
	};
	return f.call(el, selector);
}

// Usage
matchesSelector(document.getElementById('myDiv'), 'div.someSelector[some-attribute=true]')
```