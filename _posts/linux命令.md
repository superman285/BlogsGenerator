---
title: linux命令
date: 2018-08-28 22:36:03
tags: linux命令
---





#### linux四个命令

***



1. ls ls即list的简写，意为罗列。罗列出当前目录下的文件信息。

   {% asset_img ls1.png ls1配图 %}

   常用参数为 -a -l  合写-al

   -a 为罗列所有文件，包括隐藏文件(命名以.开头的文件)和隐藏目录。

   -l 为罗列所有文件详细信息，包括大小、权限、时间等。

2. cat，concatenate的简写。

   连接文件并打印到显示设备上，一般用于显示文件的内容。

3. mv move的简写。用于移动或重命名文件。

   mv 文件名 目标路径 ；即移动文件  e.g. mv 1.txt dir2/

   mv 文件名 新文件名 ；即重命名文件  e.g. mv 1.txt 2.txt

4. touch 用于新建文件或改变文件更新时间。

   不存在文件1.txt时，touch 1.txt，意为新建1.txt文件；

   1.txt存在后，再使用touch 1.txt，意为改变文件的更新时间。



#### explainshell.com网站用法

***

打开网站在搜索框输入完整命令(可以带参数)

鼠标分别移动到命令处或某个参数处，即可看到对应指向框中的具体含义，如图所示。

{% asset_img exshell.png explainshell配图 %}

