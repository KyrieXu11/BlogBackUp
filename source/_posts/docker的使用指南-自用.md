---
title: docker的使用指南(自用)
date: 2020-02-13 22:05:09
tags: docker
categories: docker
---

1. 运行镜像：docker run <image-name>，值得一说的是，docker在接收到这条指令之后，首先会在本地寻找有没有镜像，如果有的话，就直接运行，如果没有的话，就去docker hub上寻找这个镜像，默认是`lastest`也就是最新版本的镜像。
2. 查看本地的镜像：docker images； 查看本地所有镜像的id ：docker images -qa；查看本地镜像的摘要信息：docker images --digests
3. 从docker hub拉取镜像：docker pull <image-name>
4. 在docker hub上搜索镜像：docker search <image-name>，搜索可以使用一些条件搜索，就比如赞star数大于多少的条件搜索，指令的书写形式为：docker search -s 30 <image-name>意思就是star数大于30的镜像搜索结果。
5. 删除镜像：docker rmi `image-name`，强制删除：docker rmi -f <image-name|| image-id >，批量删除镜像(强制)：docker rmi -f `image-name1` `image-name2`....，如果想要全部删除docker rmi -f $(docker images -aq)
6. 列出当前所有正在运行的镜像：docker ps，查看当前所有正在运行的镜像的id：docker ps -qa
7. 退出==容器==：exit 停止并退出；ctrl+p+q 不停止退出