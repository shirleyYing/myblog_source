title: 用hexo搭建自己的个人博客
date: 2015-10-07 21:39:15
tags: [hexo,pacman,blog]
categories: tools
---

之前用gitbook发布到github上写笔记，没能找到怎么高亮显示代码块，总觉得不爽，最后还是用了hexo，没想到非常方便，网上教程很多。搭建完成后，也来写一篇当做纪念。

<!-- more -->

# 准备工作
1. git安装 ` v 1.9.5` 
2. node安装 ` v 0.4.1`
3.  hexo安装 `v 3.1.1 `

# 搭建

## 初始化文件夹

安装完成后，根据自己喜好建立目录（如`D:Git\hexo`），进入`Git Shell`切换到该路径下`D:Git\hexo`执行以下指令,`Hexo` 将会在指定文件夹中新建所需要的文件,若未指定文件夹，将在当前路径中完成初始化。

``` bash
$ hexo init <folder>
$ cd <folder>
$ npm install
```

启动本地服务器 ` hexo server `  本地查看效果,输入`http://localhost：4000`
 
## 更换主题
使用了pacman的主题 [pacman主题介绍](http://yangjian.me/pacman/hello/introducing-pacman-theme/)

### 遇到的问题

1.  博客根目录下的config.yml中 没有`stylus`属性.    在文件最后添加
``` bash
    stylus:
  compress: true
```
2. 代码片段不会高亮显示。在` theme/pacman/_config.yml` 中添加
```
highlight:
  enable: true
  line_number: true
  auto_detect: true
  tab_replace:
```
3. author.jpg 找不到。路径加一级 `author_img: ../img/author.jpg ` 

### 主题修改

#### 创建标签和归档

menu 默认没有启用` /tags` 和 `/categories` 
页面，如果需要启用请在博客目录下的source文件夹中分别建立`tags` 和 `categories`文件夹每个文件夹中分别包含一个index.md文件。内容为：
``` 
layout: tags (或categories)
title: tags (或categories)
---
```
## 编写博客

1. 进入bolg文件夹下  `hexo new " article"`
2. 去source文件下中找到article.md 
3. 编写tags 和catagory
4. 写完后，hexo generate
5. 发布 hexo deploy
 eg：
 ```
tags: [css3, html5, mobile, terminal]
categories: css3 

 ```

常用命令
``` bash
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #将.deploy目录部署到GitHub

```
发布显示更多,在你觉得适合的位置插入

```
<!-- more -->
```
 

## 插件安装


``` bash
# 一键发布到github上
$ npm install hexo-deployer-git --save
# rss 插件
$ npm install hexo-generator-feed
# 站点地图插件
$ npm install hexo-generator-sitemap
```
## 插件设置
### rss设置
1. 编辑根目录下的`_config.yml`,添加
```
plugins:
- hexo-generator-feed
```
2. 在`themes/pacman/_config.yml`中，编辑 `rss: /atom.xml`
3. 在`themes/pacman/layout/_partial/header.ejs`中，
```
<ul></ul>之间，添加一样代码<li> <a href="/atom.xml">RSS</a> </li>
```
### sitemap设置
1. 编辑根目录下的`_config.yml`,添加
```
plugins:
- hexo-generator-sitemap
```
## 发布到github
1. 在github上建立仓库，仓库名必须为『your_user_name.github.io』
2. 安装`hexo-deployer-git `插件，在根目录下的`_config.yml`,添加
```
deploy:
  type: git
  repository: git@github.com:shirleyYing/shirleyYing.github.io.git
  branch: master
```
**注意**: `type:git` 不能是` github`
插件只是将publish文件夹的内容发布到github对应仓库中去。

## 参考

[https://hexo.io/zh-cn/]

	
