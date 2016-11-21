title: xetex.def not found的解决办法
type:
  - tags
  - categories
date: 2016-11-21 20:56:57
tags: [debug]
categories: [latex]
---
临近毕业，在研究如何使用markdown和latex写毕业论文，毕竟对latex早有耳闻，正好借这个机会来尝试下使用latex感觉怎样。我使用的是mactex2016，版本较新在使用的过程中遇到了不少坑，其中最耗时间的坑就是题目这个坑，所以具体来写一下。
## 错误现象
在使用pandoc将markdown结合latex模版转换成pdf时遇到了这么一个报错，报错如下
```
(/usr/local/texlive/2016/texmf-dist/tex/latex/graphics/graphics.sty
(/usr/local/texlive/2016/texmf-dist/tex/latex/graphics/trig.sty)
(/usr/local/texlive/2016/texmf-dist/tex/latex/graphics-cfg/graphics.cfg)

! LaTeX Error: File `xetex.def' not found.

Type X to quit or <RETURN> to proceed,
or enter new name. (Default extension: def)
```
百度谷歌和很久都没有解决啊，好像其他人没有遇到这个错误似的，也研究了一阵子，结果解决方式真是巨坑啊！！！
## 解决方法
在这个报错前几行有一串的文件记录，突破口就是这里，说明latex在编译的时候在这个文件夹下找不到“xetex.def”文件，顺藤摸瓜在查看latex宏包时发现graphics-def被强制删除了，查看这个包的信息时发现这个包里就有缺少的“xetex.def”！！

真是日了狗了，解决方案直接出来了，安装graphics-def这个被强制删除的包就好了.
