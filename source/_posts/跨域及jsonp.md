title: 跨域及jsonp
type:
  - tags
  - categories
date: 2016-04-15 20:15:23
tags: [跨域,jsonp,CRFS]
categories: [ajax]
---
昨天参加了网易的前端开发实习生面试，分为两次技术面试和一次HR面试，PS.网易的校招和面试效率还是挺高的。这两次技术面试都问到了跨域及jsonp的问题，所以呢就为此写一下吧，这也是前端开发中绕不过的一个重点。
## 什么是跨域？
要解释跨域，就要先说明下什么是域？域的英文名是Domain，百度百科给的定义是:
> 域(Domain)是Windows网络中独立运行的单位，域之间相互访问则需要建立信任关系(即Trust Relation)

众所周知，网址还有个名字叫做域名。当我们去访问这个域名时，实际上在做的事情是与这个域名下的一个或多个服务器建立起链接，去获取想要的信息。在这个域名下的一个或多个服务器就共同组成了一个域，就类似于国家一样，一个或多个省份组成了一个国家。

当一个域去访问获取另一个域的信息的时候就是跨域了，举个例子就是就是当你打开baidu.com的时候，在这个域名下想获取taobao.com下的某个数据，那么这就是一个跨域请求。跨域访问是有限制，例如ajax就是不支持跨域请求的。就好比你要出国,你需要护照需要签证，不然别的国家不会让你入境。跨域问题也是一样，如果你的请求不合法是不允许你跨域访问的。
## ajax为什么不支持跨域？
这是个蛮有意思的问题，可以先想一下为什么去别的国家需要签证需要护照的这么麻烦。有一个很重要的原因就是国家的安全，如果不加以筛选和限制，来了些不好的人，那么这个国家也不安全，访问这个这个国家的人也不安全。

不允许ajax访问也是一个道理，在web安全中有一个很重要的安全策略就是[同源策略](http://baike.baidu.com/link?url=lGZEuvgETv4FCc7dnWkevdYkQd61x2unzO11nHXhpD03tkQpjn-rChGbP9kBNbSSN55fVdKCUUDsckmqyciQBq)，同源这个源也可以理解成域，这里翻译下[Understanding the Web-Same Origin Policy, Restrictions, Purpose, and Workarounds](https://blog.logicboost.com/2012/12/13/understanding-the-web-same-origin-policy-restrictions-purpose-and-workarounds/)这篇文章的部分段落，简单介绍下什么是同源策略（若不准确建议看下原文）
> ### DOM 同源策略:

> 不同源的页面下，是不允许对页面的DOM进行读写操作的。这可以阻止大量的对用户信息窃取的攻击，一个简单的例子如下：

> 1. MaliciousSite.com这个页面下有个链接指向mybank.com

> 2. 用户点击这个链接的时候，MaliciousSite.com就会通过`window.open`这个JavaScript脚本在一个新窗口里打开mybank.com这个网页

> 3. 通过这个打开mybank.com的窗口，MaliciousSite.com能够读写mybank.com网页里的信息。

> ### XmlHttpRequest(ajax)同源策略

> XmlHttpRequest的同源策略是，不允许通过JavaScript的xmlhttp想不同域名的网站建立起http的requests请求，所以“正常”的ajax请求是不能跨域的。 与DOM的同源策略相比，XmlHttpRequest同源策略是为了阻止一种不同的攻击方式，这是十分重要的。XmlHttpRequest是为了防止Cross Site Request Forgery（CSRF）攻击。当HTTP请求建立的时候，浏览器将会发送属于这个请求域名的cookies。实际上，当你访问一个网站时，这个网站会有一个授权的cookie来让你访问。基于此，如果没有XmlHttpRequest同源策略，那么一个CSRF攻击就可能会实现，例子如下：

> 1. 用户通过浏览器访问了一个恶意网站MaliciousSite.com

> 2. MaliciousSite.com在不让用户知道的情况下悄悄的访问另一个网站MyBank.com

> 3. MyBank.com通过web服务将信息传递给使用者

> 4. MaliciousSite.com获取了MyBank.com传给用户的信息。MyBank.com之所以会响应请求，是因为用户是被授权的，并且通过ajax发送了这个授权的cookie。

翻译的肯定不如原文清楚，这也是为什么有时候看英文的能看懂看翻译的就看不懂了，如果还是不明白，建议看下原文吧，我的英文翻译水平有限见谅啦。

## jsonp跨域原理及过程
### 原理
跨域不可避免，既然ajax无法跨域，那肯定要通过别的途径了。想一下写页面的时候，有时候我们引入的css或者js文件并不是本地服务器上的，可能就是某个cdn上的。所以突破口就这里，如果标签里src放的不是资源文件，放的是个请求，岂不是就可以实现跨域了？要知道url也是可放请求的，当然这还需要后端的配合。
### 过程
jsonp的实现过程大体分为3个阶段。
1. 生成get请求的url。为什么是get不是post，因为src属性发起的HTTP请求就是get方式，至于get和post有什么区别，这个自己在补补课吧，就不赘述了。
2. 创建script标签，将src的属性设为请求的url，并插入到页面中。这里一定要放入到页面中才可，因为不放入到页面中的话，浏览器不会渲染，因此也不会去执行。
3. 获取跨域请求获得的信息。这里要注意下，有许多跨域请求时，怎么知道信息返回给谁呢？所以又有个识别码在url里的。

附上个简单的伪代码
```javascript
//获取url
function getURL(){
	//dom操作
	var url = "http://domain.com/somejson?name=test?id=1";
	return url;
}
//建立标签
function createScript(src){
	var Scrip=document.createElement('script');
	Scrip.src=src;
	document.body.appendChild(Scrip);
}
function readJson() {
	//获取标签里的信息
    console.log(json);//Object { number="1111"}
}
```
最后注意下，使用jsonp有个问题就是不知道这个请求是否成功还是失败，因为没有状态码，因此还需要设置个定时器。
## 问题
面试的时候考官问了个问题，就是为什么src能跨域，我的推测就是个w3c的标准，但是没有找到相关的信息，所以有谁知道就请告诉我吧，谢谢啦。



