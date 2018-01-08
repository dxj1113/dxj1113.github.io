---
title: 用hexo+github搭建个人博客
date: 2017-07-12 13:45:51
tags: [Markdown,Hexo,nodejs,git]
categories: 技术
---

# 搭建hexo

即使扒了很多大佬的搭建教程，还是踩了很多坑，所以打算记录下来自己的搭建过程。本文主要介绍win7下hexo3.3.7搭建，并发布到GitHub上。并且实现页面源文件与发布页面分离。

<!--more-->

# 环境搭建

 安装nodejs（必须）

- 作用：用来生成静态页面的
- 方法：到[node.js](http://nodejs.cn/)官网下载并安装。我是用的是win10下v6.9.1版本。

 安装git（必须）

- 作用：把本地的hexo内容提交到github上去。
- 方法：进入git官方下载即可，速度较慢。

 申请GitHub账号（必须）

- 作用：是用来做博客的远程创库、域名、服务器之类的。
- 方法：到[github](https://github.com/)官网自行注册，如果想深入学习请看[pro git](http://iissnan.com/progit/)教程



# GITHUB库创建

1. 创建仓库，https://github.com/dxj1113/dxj1113.github.io/

2. 创建两个分支：master 与 hexo；

3. 设置hexo为默认分支（setting---branch---default branch）；

4. 使用

>   git clone git@github.com:dxj1113/dxj1113.github.io.git

   拷贝仓库；

5. 在本地dxj1113.github.io.git文件夹下通过Git bash依次执行


   

> npm install hexo-cli -g

   

> hexo init blog

   

> cd blog

   

> npm install 

   

> npm install hexo-deployer-git

   （此时当前分支应显示为hexo）;

6. 修改_config.yml中的deploy参数，设置如下

> deploy:

>   type: git   

>   repo: git@github.com:dxj1113/dxj1113.github.io.git

>   branch: master



7. 依次执行 

>   git add .、

>   git commit -m "..."、

>   git push origin hexo

   提交网站相关的文件；

8. 执行
   
>   hexo g -d

   生成网站并部署到GitHub上。

# 关于日常的改动流程

在本地对博客进行修改（添加新博文、修改样式等等）后，通过下面的流程进行管理。

1. 依次执行
> git add .

> git commit -m "..."

> git push origin hexo

指令将改动推送到GitHub（此时当前分支应为hexo）；

2. 然后才执行
  
>   hexo g -d
   
   发布网站到master分支上。虽然两个过程顺序调转一般不会有问题，不过逻辑上这样的顺序是绝对没问题的（例如突然死机要重装了，悲催....的情况，调转顺序就有问题了）。


# 本地资料丢失后的流程

当重装电脑之后，或者想在其他电脑上修改博客，可以使用下列步骤：

1. 使用git clone git@github.com:dxj1113/dxj1113.github.io.git拷贝仓库（默认分支为hexo）；


2. 在本地新拷贝的dxj1113.github.io文件夹下通过Git bash依次执行下列指令：

>   npm install hexo、
    
>   npm install、
  
>   npm install hexo-deployer-git

  （记得，不需要hexo init这条指令）
----EOF----