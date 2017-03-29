title: css之box-sizing
type:
  - tags
  - categories
date: 2017-03-27 14:26:25
tags: [box-sizing]
categories: [css]
---
前一阵子逛论坛社区看看最近春招的面试题，以此来观察下前端发展和变化的趋势，一般看到问题时我都会过一下思路，如果不通的话就会记一下。在其中有个问题时box-sizing的理解，当时一看愣了一下，回头一查全都想了起来，所以在此记一下毕竟好记性不如烂笔头啊。
# 概念
还是概念先行，看下MDN对box-sizing的解释吧
> box-sizing 属性用于更改用于计算元素宽度和高度的默认的 CSS 盒模型。可以使用此属性来模拟不正确支持CSS框模型规范的浏览器的行为。

# 盒模型
在概念中可以很容易的看出来这个属性是用来更改盒模型计算的，那么这里就自然而然的需要介绍两个盒模型一个是标准盒模型，一个是IE盒模型
## 标准盒模型
无论什么盒模型它都有这几个属性值，从外到分别是:
1. margin
2. border
3. padding
4. content

对于标准盒模型来说，当修改了这个dom的width或height属性时，例如修改为200px，这个修改只是修改了content的width，其总宽度是content+padding+border+margin。这个宽度是要大于等于200px的。这就是标准盒模型的长宽计算过程，如图所示:
![标准盒模型](http://7xr8op.com1.z0.glb.clouddn.com/%E6%A0%87%E5%87%86%E7%9B%92%E6%A8%A1%E5%9E%8B.png)

## IE盒模型
有了标准盒模型自然也有非标准的，那就是IE盒模型，在怪异模式下css的盒模型就是这个模式，同标准盒模型一样它也有margin、border、padding、content这4个属性，但是与标准盒模型不一样的地方在于，它的width计算可与标准盒模型不一样，这里做个对比:
- 标准盒模型的width为content的width，总宽高 = content(width)+padding+border+margin
- ie盒模型的width为content+padding+border的width，因此总宽高为 = (content+padding+border)(width)+margin

ie盒模型的计算如图所示:
![ie盒模型](http://7xr8op.com1.z0.glb.clouddn.com/ie%E7%9B%92%E6%A8%A1%E5%9E%8B.jpg)

## 两者对比
一张图就可以清晰的看出两者的差异，主要差异在width的计算上，如图所示:
![盒模型对比](http://7xr8op.com1.z0.glb.clouddn.com/%E7%9B%92%E6%A8%A1%E5%9E%8B.gif)
# box-sizing
接下来再回到box-sizing这个属性上就十分容易理解了，box-sizing有两个重要的属性，分别是：
1. content-box。这是默认值，使用标准盒模型计算
2. border-box。使用ie盒模型计算

至此就可以灵活的切换css盒模型的计算方式来应对设计师的需求啦