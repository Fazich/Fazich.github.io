title: 运用hexo建立博客的几个坑
date: 2016-02-25 15:36:12
type: [tags,categories]
tags: [hexo,博客]
categories: 跳坑
---
经过差不多4个多小时的折腾终于完成了hexo+github pages的建站，申请的域名还在审核所以先暂用github上的域名吧。
建立博客的过程十分曲折，比我预计的多出了不少时间，因为遇到了一些小碎坑，在此把这些碎坑整理一下，由于错误没有截图，所以全凭记忆力的报错信息吧。
## 1、hexo更新导致npm包缺少
在建立过程中第一个问题出现在部署到github上，在敲入`hexo deploy `后会报出缺少`node_modules`的错误，这是由于hexo跟新到3.0导致的，解决办法：执行`npm install hexo-deployer-git --save`安装缺少的依赖包，并将`hexo`下的`_config.yml`的`type`改为`git`，这样就解决了hexo更新带来的无法自动部署到github上的问题
## 2、spawn ENOENT问题
此问题是在`hexo deploy `后出现的，原因是hexo会调用git的命令，如果环境变量中没有git的话就会出现这个错误，解决方法：在环境变量path中添加git的环境变量`C:\Program Files (x86)\Git\bin;C:\Program Files (x86)\Git\libexec\git-core`
## 3、git相关错误
### 3.1无法获取到github仓库
这是由于`hexo init`之后hexo文件夹里缺少`.git`文件夹，hexo在执行`git commit`时没有远端地址导致。解决方法：`git init`一下初始化git项目
### 3.2远端拒绝访问
这种问题八成是由于公钥导致，先执行`ssh -T git@github.com`确定是否能够访问github，若出现拒绝则需要配置公钥，要公钥添加至自己的github主页下。
### 3.3修改博客后执行部署提示无修改
这种问题出现在最初不使用hexo部署，使用git命令部署后，再使用hexo部署后出现，因为hexo项目下.gitignore忽略了public下所有文件，而hexo编译后的静态文件都在pulic下。解决方法：执行`hexo generate`后执行`git add .`然后再次执行`hexo d`即可部署到github上
### 3.4以上操作不要使用cmd用git bash操作
## 4、github pages css及js路径错误
如果初次部署不是通过hexo的话就会出现这种错误，解决方案如下：
1、遵守github pages的命名规则，相关链接:[https://pages.github.com/](https://pages.github.com/)。在官方说明第二条中明确写到
> Head over to GitHub and create a new repository named username.github.io, where username is your username (or organization name) on GitHub.

仓库命名规则要按照`username.github.io`规则，username是自己的github名字，不可以自己随便取名
2、初次部署使用`hexo d`来部署，分支名在`_config.yml`设置

最后总结下比较顺畅的建站思路：

```flow
st1=>start: start
st=>operation: 检查github公钥是否正常
condStart=>condition: Yes or No?
opStart=>operation: 配置公钥
op1=>operation: 初始化hexo项目
op2=>operation: git init hexo项目
op3=>operation: npm install并安装缺少的依赖包
op4=>operation: 按照github pages规则建立仓库
op5=>operation: 配置_config.yml
op6=>operation: 通过hexo部署到github上
e=>end

st1->st->condStart
condStart(yes)->op1
condStart(no)->opStart
op1->op2->op3->op4->op5->op6->e
```
by the way 谁能告诉我为什么markdown的流程图编译不了 T_T~~