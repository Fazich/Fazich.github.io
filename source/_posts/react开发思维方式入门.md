title: react开发思维方式入门
type:
  - tags
  - categories
date: 2016-09-08 12:29:06
tags: [react]
categories: [js,react]
---
很久没写文章了，前一阵子比较疲惫，一直没有时间更新。
之前开发过一个基于react redux的项目，总的来看用react来开发，其效率是指数型提高的，初期虽然很慢，但是到了后期维护起来真实比jquery系爽太多了。在学习react和redux的过程中，遇到的最大的障碍莫过于思想上的转变了，如果之前jquery用多了，产生了思维僵固，那么转换到react的开发思想上来还是很费劲的。所以在这里写一下怎样快速的转换到react开发思维方式上来。

## 从一个需求开始
让我们从一个开发需求开发，分别用react和jquery进行开发。当然不用担心，我不会通过代码形式的来讲述其中的差别，这样看起来还是会很费劲的，我会通过伪代码的形式来进行说明。
产品现在给了一个需求，要开发这么一个界面：
![开发需求](http://7xr8op.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20160908212645.png)
具体细节如下：
- 右上角动态统计字数
- 字数最大限制在140字之内，超过后提示用户
- 内容为空时，发布按钮不可点击啊
- 不为空且140字之内，发布按钮可点击

### jquery思维方式的开发
下面开始用jquery进行开发，在上面的需求中有三个交互dom，分别是
`<div id="count"></div>`
`<textarea id="content"></textarea>`
`<button id="btn"></button>`
这其中涉及的主要逻辑如下:
- `$('#count')`的内容等于`$('#content')`的内容length
- 如果`$('#content')`内容length大于140时，提示用户超出最大内容
- 如果`$('#content')`内容的length为0时，`$('#btn')`为不可点击
- 如果`$('#content')`内容的length不为为0且小于140时，`$('#btn')`为可点击

这里面的具体代码就不在展开了，思路明确了，剩下的就是码逻辑了。至此用jquery的开发过程就告一段落了，这其中各dom之间的关系如下图所示：
![dom间关系](http://7xr8op.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20160908233749.png)

### react思维方式的开发
下面就是react的思维方式的开发，当然这里我先不用react的代码进行编写，换一种思路来实现上面的过程。
首先进行分析下，我们可以看出来上面的需求可以抽象成以下几个状态：
1. 没有内容时页面是一个状态
2. 有内容且符合140字之内时页面是一个状态
3. 有内容且超过140字时是一个状态

那么我们可以抽象出一个对象来维护这个状态，例如：
```javascript
var state = {
	content: "", //countent dom里的内容
	btnDisabled:false //按钮状态
}
```
这之后呢，我们让dom事件改成对这个状态变量的修改，例如：
- 对于keydown事件，让`$('#content')`里的内容赋值到'state.content'里
- 当内容长度不为空且小于140时，btnDisabled为true，其他情况皆为false

接下来就是dom里的内容皆是对state变量里的内容的映射，为了方便书写，我将{}表示为显示state里的内容，例如：
`<div id="count">{state.content.length}</div>`
`<textarea id="content">{state.content}</textarea>`
`<button id="btn" disabled="{state.btnDisabled}"></button>`

使用这种思维方式，下面的代码结构就变成如下图所示了：
![react思维方式](http://7xr8op.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20160909000559.png)

通过此图就能看出，dom显示的内容不过是state里映射而已，而事件变成了对state更改。

## 改需求
这么做有什么好处呢？怎么感觉更麻烦了。那么从一个场景切入来看一下。现在产品给你说要改需求了，添加一个图片按钮，且内容里面增加了添加图片的功能，并且一个图片占20个字。
### jquery的思维方式维护
下面就用jquery的思维方式进行维护，首先添加一个按钮，这个按钮是用来添加图片的
`<div id="addImg">添加图片</div>`
然后呢，编写以下逻辑：判断content里是否有图片，如果有图片字数+20
这个之前的dom结构就变成以下这个样子了：
![jquery思维方式维护](http://7xr8op.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20160909002729.png)
可以看出来感觉有点乱了呢，维护起来读代码的时间开始变长了。

### react的思维方式维护
接下来就是react的思维方式进行维护。
首先来为我们的state变量增加一个属性来维护图片的
```javascript
var state = {
	content: "", //countent dom里的内容
	btnDisabled:false, //按钮状态
	imgCount:0 //图片数量
}
```
然后还是要添加一个按钮，`<div id="addImg">添加图片</div>`，添加图片时对imgCount进行++
最后对显示字数的dom进行一个微调，变成：
`<div id="count">{state.content.length+state.imgCount*20}</div>`
意思就是，字数等于content的内容长度加上图片数量*20
这样以来我们之前的结构变成如下图所示：
![react思维方式维护](http://7xr8op.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20160909003556.png)

## 最后
写的比较仓促，不知道看到这里是否有点启发没有。
其实简单点来说就是，在jquery时代，我们写交互时，会思考各dom间的依赖关系，但是随着相关dom的增多以及交互的复杂，这之间的关系维护起来越来越吃力，好的设计模式就成为了其中的关键，而且在开发和维护中，我们还要避免破坏了其中的设计模式。
到了react这里，react将一层层的交互抽象成了几个不同的状态，不同的状态映射出不同的界面样子，我们要做的就是维护这个状态，因为dom的显示会自然的state关联起来，这样以来就可以从复杂dom依赖关系中解脱了出来。
没有写react的使用方式，因为react的api都很少也很简单，但是如果思维方式转变不过来，看起来仍是十分吃力的，如果想了解react的使用可以看下react的官方文档[React](http://reactjs.cn/)，如果想更深层次的看一下jquery与react代码层面的差异，那么就强烈建议看一下这篇文章[React.js Introduction For People Who Know Just Enough jQuery To Get By](http://reactfordesigners.com/labs/reactjs-introduction-for-people-who-know-just-enough-jquery-to-get-by/?utm_source=javascriptweekly&utm_medium=email)。如果在学习react看的第一篇文章是这个，而不是官方文档的话，我想我的学习之路会更加平坦一些。个人感觉react的难就难在这个思维方式转变上。