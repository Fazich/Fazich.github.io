title: 如何判断指定dom元素是否在屏幕内
type:
  - tags
  - categories
date: 2016-10-08 20:49:57
tags: [元素可见]
categories: [js]
---
刷网页的时候，有时会遇到这样一个情景，当某个dom元素滚到可见区域时，它就会展现显示动画，十分有趣。那么这是如何实现的呢？
## 实现原理
想要实现这个功能，就要知道具体的实现原理。下面直入主题。
我们通过浏览器在浏览一个网页时候是这个样子的，如图所示
![浏览网页原理](http://7xr8op.com1.z0.glb.clouddn.com/%E5%85%83%E7%B4%A0%E5%8F%AF%E8%A7%81.png)
页面的长宽，以及各dom的坐标都是静止的，动的是显示窗口坐标而已。所以明白了这个，那么判断一个dom元素是否可见时，就十分简单了。
我们需要知道三个坐标就可知道当前dom是否在可见区域内，分别是
1. 显示窗口的顶部坐标
2. 显示窗口的底部坐标
3. dom元素的中心坐标

其判断规则就是，**当dom元素的中心坐标的X及Y坐标均小于显示窗口的顶部，且大于显示窗口的底部坐标时，那么就可以判断该坐标在可见区域**。
OK，那么接下来就是要知道这三个坐标怎么计算了。
首先是窗口的顶部坐标，顶部坐标就是页面的滚动条滚动的距离。
其次是底部坐标，底部坐标就是滚动条的距离加上当前可视窗口的高度。
最后dom元素的中心距离，就是这个dom元素到最顶端的高度加上自身高度的一般。
原理就是那么的简单有木有。
## 具体实现
明白了原理，具体实现起来就很简单啦。下面直接贴上一个简单的dom代码做下示例，在实际的生产中还是要优化的，比如初次的首屏显示等等，这里就不赘述了。
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<style type="text/css">
    .box {
        width: 100%;
        height: 200px;
        background: #ff0000;
        margin-bottom: 10px;
        text-align: center;
        color: #fff;
        line-height: 200px;
        font-family: microsoft yahei;
        font-size: 40px;
        
    }
	.animate{
		animation: showText 1s;
	}
    @keyframes showText
    {
	    from {
	    	font-size: 20px;
	    }
	    to {
	    	font-size: 40px;
	    }
    }
</style>
<body>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
</body>

<script type="text/javascript">
    
    var box = document.getElementsByClassName('box');


        document.addEventListener('scroll',function(){
        	
        	//滚动条高度+视窗高度 = 可见区域底部高度
        	var visibleBottom = window.scrollY + document.documentElement.clientHeight;
        	//可见区域顶部高度
        	var visibleTop = window.scrollY;

            for (var i = 0; i < box.length; i++) {
                var centerY = box[i].offsetTop+(box[i].offsetHeight/2);
                if(centerY>visibleTop&&centerY<visibleBottom){
                    box[i].innerHTML = '区域可见'
                    box[i].setAttribute("class",'box animate')
                    console.log('第'+i+'个区域可见');
                }else{
                	box[i].innerHTML = '';
                	box[i].setAttribute("class",'box')
                	console.log('第'+i+'个区域不可见');
                }
            }
        })

</script>
</html>
```