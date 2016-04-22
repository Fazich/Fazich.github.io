title: git本地提交未推送又复位到了其他版本怎么办
type:
  - tags
  - categories
date: 2016-04-22 18:27:07
tags: [代码丢失]
categories: [git]
---
先声明个版权，这篇文章参考自海蛟童鞋的这篇文章[git 本地提交未推送 不小心迁出、删除后 的找回方法！](http://blog.csdn.net/fo11ower/article/details/45893557)

这是在使用GitExtention时遇到的问题，现在已经把工具切换到sourcetree了，这个问题可以说是能够避免了，但是还要说以下，因为前一阵子因为这个问题吓得不轻啊哈哈，回炉了一下午的代码以为就这么没了。
## 问题原因
首先来简单的复位一下问题

1. 修改代码并提交，但是没有推送。
2. 迁出之前的代码。
3. 然后再看GitExtention的分支树就会发现，咦？？代码没了，WTF！！！

## 解决方法
1. 打开gitbash命令行。
2. 输入`git reflog`就能看到如下
![git](http://7xr8op.com1.z0.glb.clouddn.com/git.png)
记住head前面的那串数字，那是恢复代码的关键
3. 例如要恢复到1d95984这个分支，那么就输入`git reset 1d95984 –hard`,这样就硬恢复到这个版本啦。
4. 最后看下你的GitExtension就能看到你之前“丢失”的代码了。

## 最后
建议使用sourcetree吧，比GitExtension好用太多了，当然不管什么工具，命令行才是王道啊哈哈。

