---
title: node简单基础
tags:
  - Node.js
  - 基础
categories:
  - BackEnd
  - Node.js
date: 2019-01-15 19:34:12
---



##### webserver

Nginx 下载好对应系统版本安装，命令行开启，就相当于启动服务器了

nginx配置conf文件

root html路径修改下即可

nginx -c conf/myselfconf.conf

这时就自己可以通过localhost或127.0.0.1访问



查下自己公网ip 其他机器可通过这个访问



##### node

操作文件系统/网络/数据/进程 等 而不是bom和dom



###### 模块化

require方法返回的是module.exports对象(与exports是一样的，全等，相当于省略了module的写法)

如果不给module.exports添加内容，那返回的就是{} 空对象



==module.\_\_dirname(目录)== | ==module.\_\_filename(目录及文件名)== | module.require

模块内的module就相当于浏览器中的window，可以省略

数据冲突问题，依赖问题

node的顶层全局对象为global,模块顶层对象为module,global.a 全部模块都可以访问



每一个文件都是一个模块，每个模块有自己的独立作用域，模块内的数据自己私有

每个独立模块都有一个内置对象 module，该对象存储和提供与当前模块有关的一些信息

每一个模块对象module下有一个属性exports,默认为空对象,该对象为导出对象,可以把模块中的局部变量等数据挂载到该对象下,require方法返回的值就是被加载模块的exports对象



exports.a = a; 这时返回值就是{a:2}(exports对象)



require



每个模块下都有一个模块对象module，同时该模块下有一些属性和方法：

exports: 导出模块私有数据

require: 加载模块



commonJS 模块化



##### ==模块加载==

require(‘./2’)或require(‘./2.js’) 会执行2.js,同时会返回一个值,



require一个json返回 对象？ 学习下



模块也可以通过目录形式来组织, let dir = require(‘./miaov’) 默认加载的是./miaov/index.js

默认情况下会加载目录模块下的index.js 作为目录模块的入口,如果不想的话,准备一个package.json文件



==package.json== 模块说明文件

name 模块名称 | version 模块版本 | main 模块入口文件

{

​    “name”: “miaov”,

​    “version”: “1.0.0”,

​    “main”: “fn.js”    //改变默认加载的入口文件

}



###### 使用require时导入模块的顺序

如果一个模块下有package.json文件，首先读取package.json的文件，读取这个文件中的main字段对应的文件路径，

如果没有package.json文件，则默认读取该文件夹路径下的index.js

💡注意：如果既没有index.js也没有package.json(或main字段),则会报错:cannot find module



==两种路径的方式==

自己的文件/文件夹模块 require形式为./|../之类打头 let my = require(‘./mydir’)

第三方模块 require形式不以./|../之类路径符号打头 let jquery = require(‘jquery’)

./导入的是本地文件/文件夹模块

不带./导入的是核心模块(例如require(‘fs’))或第三方模块(在node_modules)



为防止自己的项目模块和第三方模块混合，node中约定了一个特殊目录，mode_modules目录存放第三方模块



> 第三方模块查找逻辑：
>
> 先在本层目录的node_modules目录找，找不到的话到父亲的node_modules目录
>
> 直到找到最顶层的node_modules目录
>
> 最终会找到 /users/superman285/.node_modules目录下
>
> 如果还找不到就要找node源码了(即核心模块)