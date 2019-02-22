---
title: npm常用知识点
tags:
  - Node.js
  - NPM
  - Web
categories:
  - Web
  - NPM
date: 2019-01-22 19:34:23
---



第三方模块商店

www.npmjs.com



npm文档

docs.npmjs.com

www.npmjs.cn



别人用node或者javascript写的工具模块



包版本格式一般为

主版本号.次版本号.修正版本号

重大更新 | 功能升级 | bug修复



==最常用命令==



- npm search | 例如: npm search jquery
- npm install(npm i) npm i jquery@3.3.1 有多种模式
  - 本地模式
  - 本地开发模式
  - 全局模式
- npm update xx 更新xx包到当前主版本号的最新版本，一般不会更新主版本号



==npm安装模式==

默认npm i 模块名 等同于 npm -save|-S 模块名

 | 本地模式  => 信息记录到package.json的dependencies

npm i -D|-develop 模块名 | 本地开发模式=> 信息记录到package.json的devDependenies

npm i -g 模块名 | 全局安装 

全局安装后在 AppData\Roaming\npm\node_modules windows

或/usr/local/lib/node_modules unix或linux

项目代码中要使用的包使用本地模式



在项目开发过程中使用的而且非代码本身使用的包使用的模式为开发模式，比如各种辅助工具(例如babel、webpack、eslint等)



> 💡注意事项
>
> npm i 模块xx (-S);
>
> 对模块xx而言
>
> - 模块xx的dependencies会随模块一起下载，模块xx的devDependencies不会随模块一起下载
>
> 对自身项目所在文件夹而言
>
> - 自身项目的packagejson中会将模块xx加入dependencies
>
>
>
> ------
>
>
>
> npm i 模块yy -D
>
> 对模块xx而言
>
> - 模块xx的dependencies会随模块一起下载，模块xx的devDependencies不会随模块一起下载
>
> 对自身项目所在文件夹而言
>
> - 自身项目的packagejson中会将模块xx加入devDependencies
>
> ------
>
>
>
> npm i
>
> ==当前所在项目文件夹下的packagejson中的无论dependencies或devDependencies全都一股脑下载下来==
>
> 所以，如果平时下载的第三方模块没有包含依赖的工具库内容时，你可以直接到第三方模块目录底下打命令npm i ,所有的依赖包括dependencies和devDependencies都会被下载下来，供你学习或使用。
>
> 或者你用npm i -D 结果是一样的





npm run xxx



xxx是package.json中的scripts字段中的某个对象，是一些较长命令的缩写，可以简洁地运行命令又避免了麻烦地书写一长串带参数的命令。

相当于运行script中的命令



##### package-lock.json 和 package.json



###### package-lock.json 

加速已安装过的包的安装速度

锁定安装版本，避免直接改package.json，利于包管理

package.json是锁定大版本号(第一版本号),每次npm install仍然会更新最新的小版本号

这时使用package-lock.json就锁定全部版本号,不会因为npm install导致包的版本号变动

如果还是想更新npm包，就只能用这种方法，npm install packxx@x.x.x(或者@latest)



###### package.json

该文件是用来描述目录类型的模块或第三方模块(第三方模块也是按照目录进行组织的)

如果一个模块下有package.json文件，首先读取package.json的文件，读取这个文件中的main字段对应的文件路径，

如果没有package.json文件，则默认读取该文件夹路径下的index.js



安装或卸载完第三方模块，会同时更新package.json的dependencies项中的内容，增加或删除。



```json
{
    "name":
    "version":
    "main":
    "dependencies":{
        "jquery": "^3.3.1"
    }
    
    
}
```



npm run xxx



xxx是package.json中的scripts字段中的某个对象，是一些较长命令的缩写，可以简洁地运行命令又避免了麻烦地书写一长串带参数的命令。

相当于运行script中的命令



创建方式：

- 手动创建
- npm init(交互式) | npm init -y(直接生成)



name和version两个字段是==必须必须有的==





##### 常用工具

path库

livereload 不用手动刷新页面 自动重启前端

supervisor 不用手动重新打指令开服务器 自动重启后端

nrm 切换下载源

http-server -c-1 启动简易服务器







##### 发布一个npm包

==package.json是必须的==

npm官网注册账号

npm login | 输入用户名 密码

定位到当前目录然后 npm publish

在官网就可以搜索到啦

npm unpublish 下架指定的包