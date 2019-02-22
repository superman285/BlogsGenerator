---
title: git常用命令3
date: 2018-08-29 00:30:32
tags: 
- git命令
- Hexo Blog
---



### 3个git命令

------

①git init  

将所在目录初始化为git仓库。

会在目录生成一个.git子文件夹，包含git操作所需要的内容。

{% asset_img init.png init配图 %}



②git add 将文件内容改动添加到暂存区，但文件改动并没有真正进入版本库中，而是处于中间状态。

{% asset_img add.png add配图 %}



③git commit -v  提交时显示内容差异对比。

提交时：

{% asset_img commitv-1.png commit配图 %}



{% asset_img commitv-2.png commit配图 %}



提交后：

{% asset_img commitv-3.png commit配图 %}

