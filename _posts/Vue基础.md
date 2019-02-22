---
title: Vue基础
tags:
  - JavaScript
  - Vue
  - 基础
categories:
  - FrontEnd
  - Vue
date: 2019-01-04 19:31:57
---



##### 设计模式 mvvm

m: model 数据 界面中展示的或应用中使用的数据

v: view 视图 用户看到的界面

vm: view-model 中间人 处理数据与界面之间的相关逻辑(处理v和m的交互)





new Vue得到一个vue对象 后续的数据管理和视图的渲染更新这些操作都是通过这个Vue对象来完成的



初始化Vue中的属性
el:指定当前vue对象处理的范围，通过el指定的内容将被当前Vue对象管理，

可使用选择器指定，要在这个范围内才能管理和使用data的数据

data:存放当前Vue实例管理的应用中的所有数据





##### 模板语法

插值语法 `双花括号-插值**表达式**-双花括号` Mustache写法双大括号

表达式:能产生结果(值)的公式 ==变量|函数调用|数学运算==等

语句: 一个行为，例如if for之类

`双花括号-val-双花括号` 如果val是对象 vue会默认将val转为JSON格式

相当于`双花括号-JSON.stringfy(val)-双花括号`



##### vue指令

指令填充的是表达式

v-开头的一种特殊属性 行间 vue内置了很多指令 我们还可以自定义指令



v-for = “(val,idx) in array”



v-show是改变display属性(存在于页面上) 需要频繁切换时使用

v-if是是否渲染元素(不满足时这个元素直接不存在)



> v-text和v-html

v-text="title" 这个title代表的是变量(表达式) 是data中title的值

如果想要title字符串 可以写为 v-text=“‘title’”



v-html更新元素的innerHTML 危险 容易导致xss攻击 可以识别html语法

v-html=“content” //data中content=“`\<strong>I am strong\</strong>`”



v-text相当于更新元素的innerText 纯文本



> v-bind 

想把数据作为标签的某个特定属性



把表达式的值绑定到指定的参数属性中

v-bind:value=“title” //title是表达式 是data中定义的变量

==简写形式== 只有冒号 去掉v-bind

:value=“title”



当想传入一个对象而不是字符串时，用v-bind写法

:value = “{title:‘super’}”



针对class和style使用v-bind时又有特殊用法

v-bind动态改变class 使用三目运算符

或用对象写法 作用等同三目



对于class和style还有特殊待遇，可用数组形式和对象形式

v-bind:

v-bind:class=“[class1,class2,class3]”

v-bind:style=“{color:activeColor,background:bgColor}”

或“[colorObject,bgObject]” colorObject和bgObject是定义在data中的对象



> v-on

v-on:click= 简写 @click=



vue中的click相当于js中的onclick 后面跟函数名 不带() 与原生html的onclick(带括号)区分



vue中的函数默认都指向当前vue对象

原因是在函数中用this可以很方便的去调用vue实例对象下的其他数据，例如data中的数据

data中的数据和methods中的函数，都可以通过实例对象进行直接访问

Vue在初始化时把data、methods中的属性和方法都直接挂载到了实例对象上

把data中的methods中的数据直接挂在了实例对象上

this.dataProp1(不用this.data.dataProp1)

this.methodFn1()(不用this.methods.methodFn1())



想拿到事件元素event怎么拿?通过第一个参数

methods中的方法的第一个参数就是事件对象



==特殊地==

v-on:click=“fn4()” 并不会立即调用 vue会智能地判断 还是会等到点击时才触发

如果在v-on处用了带括号的写法，必须显式地声明事件参数 否则不会获取到事件对象

v-on:click=“fn4($event)”



假设Vue对象中methods:function click(p1,p2){console.log(p1,p2)};

v-on不同写法有以下几种情况:顺序很重要

- v-on:click=‘click’ 这时会自动将第一个参数视为事件对象 //MouseEvent{} undefined
- v-on:click=‘click(1)’ 这时就没事件对象啥事了 当做正常参数了 //1 undefined
- v-on:click=‘click($event)’ 显示调事件参数 //MouseEvent{} undefined
- v-on:click=‘click(1,$event) 两个参数都生效 //1 MouseEvent{}



==事件修饰符== 不同指令有不同修饰符 有的指令无修饰符

@click.修饰符=“函数名” 也可以不跟函数 只是触发事件修饰符

@contextmenu.prevent 阻止右键弹出菜单的默认行为



stop 阻止冒泡 相当于stopPropagation()

capture 捕获 相当于把原生addEventListener中第三个参数设置为true 事件设为捕获

prevent 阻止默认行为

once 绑定一次

self 只有点自己才会触发 

passive(消极的) true表示永不调用preventDefault 为false即可以preventDefault



self:给父亲设置@click.self后，点击儿子是不会触发父亲方法的(没设置之前是可以触发的)



> v-model 双向绑定

简单滴说：

双向绑定 = 单向绑定 + UI 事件监听

例子:

http://js.jirengu.com/duzo/1/edit?html,console,output



> 表单💡

数据单向绑定(v-bind)  model -> view 

当js中的数据变化了,视图上显示的数据也会重新渲染



数据双向绑定(v-model)  model <-> view 

当js中的数据变化，视图上的数据也会变化

当视图的数据发生变化，js中的数据也发生变化



不同的标签 v-model绑定的标签属性不一样 智能地绑

input:text v-model 绑定的是value

input:checkbox v-model 绑定的是checked

input:radio v-model 绑定的是checked



input单个checkbox v-model 绑定checked 布尔值

input多个checkbox v-model绑定数组 value出现在了数组中则被选中

input多个radio v-model绑定字符串 哪个radio被选中 就显示哪个radio的value值

select多个option v-model绑定字符串 哪个option被选中 就显示哪个option的innerText(文本内容)





.lazy修饰符 input变化时不会实时更新 而是失去焦点时再更新

.number 用户输入的值转为数值类型(原本默认为string)

.trim 去除首尾空白字符





###### 自定义指令

指令是以属性方式出现在元素上的，有特殊前缀，v-指令名称

指令时作用于 使用当前指令的元素上的 当一个元素使用某个指令以后 该指令会对当前使用它的元素产生一些特定效果 具体的效果由当前指令的具体代码去实现



Vue.directive(id,[definition]) 方法定义全局指令

id:指令名称 使用时需要带v-前缀，定义时不用带v-前缀

defnition:指令定义 具体定义、指令配置

钩子函数：指令的执行过程中不同阶段调用的不同函数

==5个钩子==: bind inserted update componentUpdated unbind

https://cn.vuejs.org/v2/guide/custom-directive.html

注意💡 bind函数只执行一次，在初始化时指令绑定到元素上的瞬间执行，即使值再改 不会重新绑定的

第一次绑定并不算update 一般大逻辑写在bind中 update改变小值

如果bind函数和update函数想共享变量 可以把变量挂载这些函数的第一个参数上(即dom元素对象)

或者通过dataset来共享数据



bind函数的第一个参数为元素 第二个参数binding为对象 里头有一些属性

根据binding来配置自定义指令的值



如果想自动重新绑定类似v-model那样 就不用bind函数钩子 用update钩子



focus不生效的原因：

focus不能放在bind和update中，要放在inserted狗子中

bind vue还没解析完



##### data中的数据

data中一般放原始数据 

派生数据一般放 computed中 是通过计算或其他值换算来的



计算属性 会动态地受其他值影响 其他值变了 它动态地改变 很智能

==注意==

computed对象中为方法，返回计算后的结果

对应的html中`双花括号--双花括号`中的数据为方法名

html: 

`\<p>双花括号-methodA-双花括号\</p>`



js:

data:{message:‘hello’}

computed: {

methodA: return this.message.substr(1)

}



如果用v-model绑定了数据 computed方法中必须配上get()和set()

因为v-model是双向绑定 用户也可能改数据 导致computed属性发生改变



如果是v-bind就说明只是读取值 不会改值 就不用写get()和set() 默认为get()

直接return xxxxx



注💡 

一般来说 computed属性都是派生来的 由其他属性计算得来的 而不是直接手动改





##### 侦听器

watch

监听的属性名要与data中的数据同名

watch:{

​    a(){ //a为data中的属性}

}

当有的数据 是异步变化而不是立即得到结果的可能要用到监听watch







##### vue-router 前端路由

注💡 页面切换的请求千万不要让浏览器发请求(这样会让浏览器来渲染了)

要让vue来渲染



模式 url的# hash是不会导致页面刷新发请求的



单页面SPA和多页面MPA SPA通过#hash 后端通过x1.html/x2.html

https://juejin.im/post/5a0ea4ec6fb9a0450407725c



历史记录方式要处理比较多情况 比如判断别人是浏览器直接打开访问的 比如 localhost/a

可能要加个header 然后判断方式不是ajax时

 可能还需要后端支持 

因为不带hash了 所以可能需要后端路由



可以局部刷新 局部渲染 用户体验比较流程