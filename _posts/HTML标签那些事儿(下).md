---
title: HTML标签那些事儿(下)
date: 2018-09-05 10:43:43
tags:
- HTML
- 基础
---

## HTML标签那些事儿(下)

> 故事上篇，已经大致介绍了大部分HTML标签的用途。
>
> 故事下篇，我们来介绍下常用标签和常用用法，以及一些小tips。



> 常用标签&用法

`iframe标签`

属性：frameborder | src(嵌套页面的url地址) | name | scrolling

name常配合a标签使用



`a标签`

- **a标签`target`属性**

|     值      |                             描述                             |
| :---------: | :----------------------------------------------------------: |
|   \_blank   |                  在新窗口中打开被链接文档。                  |
|    _self    |            默认值。在相同的框架中打开被链接文档。            |
|    _top     | 在最大的整个浏览器窗口中(即最外层的祖先框架集)打开被链接文档。 |
|   _parent   |  在父框架集中打开被链接文档。(a自己所在框架集中的父框架集)   |
| *framename* |                在指定的框架中打开被链接文档。                |

💡 framename用法

```php+HTML
<iframe name="baidu_iframe" src="#" frameborder=0 width=100% height=500px></iframe>
<a href="http://www.baidu.com" target="baidu_iframe">iframe百度</a>
```

此栗子中，点击a链接，会在框架中打开百度首页。iframe的name需配合a链接使用。

`注`

若框架名字为属性关键字时，点击a链接**关键字属性生效**，franmename不生效。



- **a标签`download`属性**

a标签链接到网页|文本|图片，加上该属性后都会触发强制下载

download还可以指定下载文件的名字/后缀信息，不指定则为原本名称。

```html
<a href="https://www.baidu.com" download>下载</a>
<a href="http://js.jirengu.com/images/dave.min.svg" download="jsbin.svg">下载并重命名</a>
```

想让浏览器以下载形式处理该请求，两种方法

1. http响应中的Content-Type: application/octet-stream	
2. 给a链接加上download属性，指定强制下载



- **a标签`href`属性**

  - 🌀 **注意**

    \<a href="qq.com"\>QQ\</a\>

    qq.com是相对路径，并不是URL，这种写法无法访问qq官网，URL包括协议-域名-端口号-路径-查询参数-锚点等

    至少包含协议-域名才能正常定位过去，http://qq.com 才是URL。

    \<a href="//qq.com"\>QQ\</a\>

    //无协议，以当前协议的方式访问，即a所在的html的协议

  ------

  - href可以写`锚点`(页面内跳转)，打开代表html路径#锚点，不会发起请求 

    路径是127.0.0.1/index.html

    \<a href="#id1"\>QQ\</a\> 即127.0.0.1/index.html#id1



```
href还可以写`查询参数`(一定不能漏了`?`，打开代表html路径查询参数(紧跟在路径后)

打开时会发起一个get请求

\<a href="?name=superman"\>QQ\</a\> 即127.0.0.1/index.html?name=superman
```



```
href还可以写`伪协议`，javascript:xxxxx;

\<a href="javascript:alert(helloQQ)"\>QQ\</a\> 打开后会执行js代码，弹框显示helloQQ
```

------

- 🔨`一个小技巧` **完美实现点击a链接但是无任何反应**

  \<a href="javascript:;"\>QQ\</a\> (不能漏掉分号，否则会报错，加分号代表什么都不做)

  \<a href="javascript:void(0)"\>QQ\</a\> (作用同以上写法，但要小心)

  \<a href="##"\>QQ\</a\> (也可写###,#默认为#top,但##/###浏览器不认识,所以默认不处理)

  > https://www.cnblogs.com/lfan192517/p/7242998.html 2、3方法引自此，细节可看文章

  以下写法都有瑕疵

  1. \<a href="#"\>QQ\</a\> 页面内跳转不会发请求，但是点击会跳到顶部，因为#默认代表#top
  2. \<a href=""\>QQ\</a\> 默认跳转到自身，会刷新页面而且发送get请求

> 总结`href`可写形式

- //qq.com 无协议
- #xxx **|** ?name=superman **|** ./xxx.html
- javascript:alert(1); **|** javascript:; **|** javascript:void(0);



`form标签`

form跳转页面是HTTP POST请求，a标签跳转页面是HTTP GET请求

input的name很重要，写了name提交后服务器才能得到想要的信息

action属性指定请求路径

`相对路径 | 绝对路径 | 查询参数 | #`

#代表提交数据到本页

method指定请求方式

`GET | POST `

- form表单中没有input:submit按钮的话 无法提交这个form 除非用js

- form表单设置method参数则默认为get请求，实际使用一般都用POST方式。

- form表单提交后可在浏览器Form Data查看请求的第4部分内容

- form的method只支持post和get，

  当使用get时填入表单的数据会作为查询参数(Query String Parameters)

  当使用POST时填入表单的数据会作为Form Data(请求的第4部分的数据)

- form标签action属性可以写查询参数，让post请求也带查询参数，但是无法让get请求带第4部分数据

- 当form表单中只有一个button标签**且没声明type**，则自动升级为type-submit；input-button不会升级

- 有target属性，规则`与a标签一模一样` \_blank **|** \_self **|** \_parent **|** \_top **|** frameName 



*http协议支持POST和GET,file协议不支持POST和GET* 

*http协议，form data可以明文看到password,https协议会加密*



`input标签`

- input type=“search”	搜索框专用，比text的右侧多了一个×

  input type=“range”	滑块控件(水平滑动条)

input标签配合label使用，可以实现点击文字直接定位或选中对应input的效果。

1. label的for属性值得设置为input标签的id值

2. 💡 label把input标签嵌套起来(如\<label\>\<input type="text"\>\</label\>)

   ，这时候他们就有了关联关系！

input标签name属性的作用是：get方式请求时，提交后能看到这个input标签的值(作为查询参数中的key)，带有name才能看到，value会作为name的值



`button标签`

属性type取值 submit | button | reset

当form表单中只有一个button标签**且没声明type=button**，则自动升级为type-submit



`textarea标签`

textarea标签，style=resize:none;则无法拉动宽高。

实际上如果需要精确的宽高一般用css设置，而不时cols和rows属性。



`table标签`

表头为th，会加粗

可设定每列的宽度，可以不带单位，bgcolor设定列的颜色

```html
<clogroup>
	<col width=100>
	<col width=200 bgcolor=green>
</colgroup>
```

thead、tbody、tfoot可以不写

💡 边框合并但不加粗，单元格边框没间隙的表格，css写法

```css
table {
	border-collapse: collapse;
}
td {
	border: 1px solid black;
}
```



`div标签和span标签`

分别为块元素和行内元素无语义标签，可用于任何需要的地方。

div常用来包含内容/做布局，span常套文字。



`一些HTML5的新增语义化标签`

header | footer | main | nav | article | section | aside

等，默认为块元素，若怕有的浏览器不支持，可再声明一次display:block;



`一个HTML5全局属性`

contenteditable:内容可编辑的

e.g 

\<p contenteditable="true"\>我是可编辑的\</p\>

\<div contenteditable="true"\>我是可编辑的\</div\>

