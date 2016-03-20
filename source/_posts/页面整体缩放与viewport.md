title: 页面整体缩放
type:
  - tags
  - categories
date: 2016-02-25 18:47:37
tags: [前端,meta]
categories: 前端
---
前两天遇到一个问题，把PC的页面整体缩放到pad上，意思如下以：

在PC下看到的页面：![http://7xr8op.com1.z0.glb.clouddn.com/%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87.png](http://7xr8op.com1.z0.glb.clouddn.com/%E5%8D%9A%E5%AE%A2%E5%9B%BE%E7%89%87.png-width)

期望在移动设备下看到的：![http://7xr8op.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20160225192221.png](http://7xr8op.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20160225192221.png-height)

然而实际的样子是：![http://7xr8op.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20160225192248.png](http://7xr8op.com1.z0.glb.clouddn.com/QQ%E6%88%AA%E5%9B%BE20160225192248.png-height)


查了查发现其实这还是个挺有意思的问题，所以在此整理下有关页面整体缩放的技术和方法
## 1、js方式
故名思议，通过脚本来控制html标签的css样式，使其能够整体按比例缩放，这种方式修修bug还好，要是一个复杂页面通过这种方式来控制css显然是本末倒置了，因为css才是控制页面样式的啊
## 2、在body上加上zoom属性进行控制
这种方法也不是很可取，使用zoom会倒置页面布局错乱，尤其是float布局时最明显。父标签设定了固定宽度，当使用zoom时，父标签宽度也会改变，如果宽度变小，float的子元素无法容下，那么就会到第二行去了，因此页面也就乱了。
## 3、rem方式
这种方式是用来解决手机屏幕大小不一致时文字无法依据屏幕大小进行适配，可能会出现小屏幕手机文字图片比例刚好，但是到大屏幕手机上比例就失衡了。

rem通俗点就是以根元素为标准设置元素的尺寸。例如为html标签设置`font-size:12px`,其下面的一个子元素span标签设置为`font-size:1.25rem`，那么这个span的font-size就是12*1.25=15px。这么做看起来很麻烦，属性值还需要重新计算一边，但是如果再为html元素的font-size加上响应式就不一样了，如果在大屏幕上，将html下的font-size的值变大，那么凡是以rem为单位的元素都会按比例进行缩放，这样一来就不会出现屏幕大小不同而导致的比例失衡的问题了。
## 4、通过meta标签的viewport来改变
在移动设备上，viewport就是设备的屏幕上能用来显示我们的网页的那一块区域，但viewport又不局限于浏览器可视区域的大小，它可能比浏览器的可视区域要大，也可能比浏览器的可视区域要小。当viewport小于页面大小的时候就会出现横向滚动条，因此通过调整viewport也能实现我们整体缩放页面的目的，但这个方式又与上面不同，上面是改变了css元素，而这个只是改变了视角。换句话说就是，如果你发现一副画很大，一眼看不全，那么就离这幅画远一点，这样你就可以看全了，viewport也是这个道理。
那么要怎么修改能够实现呢?在移动页面上我们都能看到这个标签

`<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">`
解释下就是我们的viewport宽度为设备宽度，初始缩放百分比为1，最大缩放百分比为1，用户不能进行页面的缩放
这样的方式通过移动设备打开pc页面就会只能看到局部并出现横向滚动条，如果想要实现看到全部的页面，也就是站远点看到全部的画，那么要做的就是把`initial-scale=1.0`这个值干掉，这样就不是按初始缩放1:1打开这个页面而是看到全部页面的视角打开，接着就是把`user-scalable=0`干掉，使用户能够自行缩放，至此就算是解决啦。