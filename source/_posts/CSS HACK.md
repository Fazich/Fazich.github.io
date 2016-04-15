title: CSS HACK
type:
  - tags
  - categories
date: 2016-03-28 21:13:47
tags: [浏览器兼容性,hack]
categories: [css]
---
这次说一下css的hack

前两天出来个新闻，淘宝将不再支持ie6、7。不得不说这是一个非常好的消息，为web开发行业树立了一个标杆，让我们众多前端开发工程狮们不用再为ie6、7来抓狂了。而且随着移动互联网开发的蓬勃发展，再加上各种前端开发工具和框架的出现，web的开发环境也是越来越好，很多曾经pc端开发时慎用的css3的属性在开发中也能够放肆的使用，从而提高开发的质量和效率。但是这并不意味着css hack就可以退出历史的舞台了，因为不同浏览器也有hack，而且ie8、9也是要支持的嘛。

## 什么是css hack?
引用下百度百科的定义
> CSS hack由于不同厂商的浏览器，比如Internet Explorer,Safari,Mozilla Firefox,Chrome等，或者是同一厂商的浏览器的不同版本，如IE6和IE7，对CSS的解析认识不完全一样，因此会导致生成的页面效果不一样，得不到我们所需要的页面效果。 这个时候我们就需要针对不同的浏览器去写不同的CSS，让它能够同时兼容不同的浏览器，能在不同的浏览器中也能得到我们想要的页面效果。

简单点说就是通过css hack可以让不同版本的浏览器有不同的样式，可以运用它来为不同的浏览器制定不同的样式，也可以运用它来解决浏览器的兼容性问题
## 为什么会有css hack？
这个说起来就长了，具体可以参考下其他相关书籍，但总的来主要是由于浏览器大战导致的。第一次浏览器大战发生在上个世纪90年代，是微软的IE浏览器，和网景公司的Netscape Navigator，这次大战的结果是ie获胜，最后网景将公司卖给AOL一走了之。这次大战确立JavaScript在浏览器的绝对统治地位，另外也推动了web开发标准化的进程，这次大战也可以说是一次标准大战。第二次大战从04年firefox的推出一直到现在，这次大战的特点是一些新的基于不同引擎的浏览器也加入了阵营，例如Opera Chrome 等等，这次大战可以说是一次内核大战，这次大战的结果胜负未出，但是已经有了一些共识，例如chrome是js运行最快的浏览器，Nodejs用的就是Chrome的V8引擎，还有IE最不受开发者欢迎，因为有很多独立的api而且自己运行也慢，这告诉我们没有实力就不要搞特殊。
## css hack情况介绍
下面就是主题了，有哪些css hack和怎么用？这里按照以下几个方面来区分。
### 1、区别IE和非IE浏览器
```
#tip{ 
	background:blue; /*非IE背景蓝色  因为所有浏览器都能解释*/ 
	background:red\9; /*IE6、IE7、IE8、IE9背景紅色 因为\9在IE6.7.8.9中可以识别，覆盖上面样式 IE10跟11应该不识别，IE11测试确定*/ 
} 
```
### 2、IE浏览器下的hack
```
element{
	color：#666\9； //IE8 IE9
	* color：#999；   //IE7
	_color:#EBEBEB； //IE6
}
//若要区分IE8 和IE9可以使用伪类来识别
:root element{color：#666\9;}//IE9
//:root”伪类IE系列只有IE9支持，其他主流浏览器均支持，利用这一点来区分IE8和IE9。另外考虑到opera部分支持，完全支持:root,所以不使用。
```
### 3、其他主流浏览器css hack
#### 1.Gecko内核，以Firefox为代表
```
//使用-moz-前缀
-moz-box-shadow: inset 0 -4px 0 #2a6496;
```
#### 2.Webkit内核，以Chrome为代表
```
//使用-webkit-前缀
-webkit-box-shadow: inset 0 -4px 0 #2a6496;
```
#### 3.Presto内核，以Opera为代表
```
//使用-O-前缀
-o-box-shadow: inset 0 -4px 0 #2a6496;
```
#### 4.Trident内核，以IE 360等众国产浏览器为代表
```
//使用-ms-前缀
-ms-box-shadow: inset 0 -4px 0 #2a6496;
```
### 4、大汇总
```
.element{
	color:#000;             /*w3c标准*/
	[;color:#f00;];         /*Webkit(chrome和safari)*/
	color:#666\9;           /*IE8*/
	*color:#999;            /*IE7*/
	_color:#333;            /*IE6*/
}
-moz-box-shadow: inset 0 -4px 0 #2a6496;       /*Firefox*/
-webkit-box-shadow: inset 0 -4px 0 #2a6496;    /*Chrome*/
-o-box-shadow: inset 0 -4px 0 #2a6496;         /*Opera*/
-ms-box-shadow: inset 0 -4px 0 #2a6496;        /*IE*/

```
参考文章:[CSS hack大全&详解（什么是CSS hack）](http://www.kwstu.com/Admin/ViewArticle/201409011604277330)