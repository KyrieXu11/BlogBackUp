---
title: 如何更改VSCODE的插件安装位置
date: 2019-07-05 20:45:40
tags: Blog
categories: 博客
---
# 用vscode来写博客真的爽啊，vscode的颜值不用多说，还可以直接在vscode中打开终端，进行博客的操作，所以今天来说一下vscode的扩展安装位置怎么改。
### 1.`vscode`的扩展默认安装位置是在C:\Users\用户名\.vscode\extensions.
### 2.我们无法在vscode中直接改，我们只能指定vscode的启动位置
### 3.在桌面上建立个快捷方式，右键快捷方式,点击属性
![awda](attribute.png)
### 4.在`目标`后面加上`--extensions-dir `再加你想放扩展的文件夹，我的文件夹是`D:\Program Files (x86)\vscode extensions`
### 5.打开这个文件夹康康
![](place.png)

---
### 另外说一下，vscode写markdown文件的时候不知道为什么全是黄色线线...
![](vscode.png)
### 还有如果博客图片(整个站点的图片)太多的话上传有点慢...所以最好还是用图床来上传图片。