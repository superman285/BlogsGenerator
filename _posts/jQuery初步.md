---
title: jQuery初步
tags:
  - jQuery
categories:
  - FrontEnd
  - jQuery
date: 2018-12-15 19:26:29
---



## 简单嗦嗦



jq的方法多是对集合内多元素自动循环然后 挨个执行的，很多方法都可批量操作，省得再套个for循环



jq对象：w.fn.init[x]



💡注意：jq事件的this指向的还是原生的dom元素，而不是jq包装对象，需要用jq方法的话再加个$包装下，这样用：\$(this),这样就让this外加了层jq包装,就可以用jq方法了。

jq和原生混用是没问题的，不存在冲突。



jQuery最最常用的

| 常用方法                         | 作用                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| on                               | 类似原生中的addEventListener                                 |
| (‘p’).index(‘*’)                 | 类似原生的indexOf,p在所有元素中的索引号                      |
| eq(i)\|get(i)                    | 获取索引为i的元素,eq为jq包装的,get为原生获得的               |
| .click\|.hover (function(){})    | 点击触发事件，鼠标移入触发事件                               |
| text()\|html()\|val()            | 类原生的innerText,innerHTML,value(表单用),可get和set         |
| attr(‘s1’[,‘news1’])\|prop(‘s2’) | 获取或设置某标签的某属性,可以写成对象形式设置多个值<br>例如attr({“href”:“baidu.com”,“title”:“baidu”})<br>attr可用于所有属性，prop用于固有属性 |
| append(ctx)\|prepend(ctx)        | 在元素内部的末尾/元素内部开头 插入内容                       |
| after(ctx)\|before(ctx)          | 在元素前面/元素后面 插内容 「元素外部」                      |
|                                  |                                                              |
|                                  |                                                              |

on 类似原生js的addEventListener





\$.each(arr,(index,val)=>{}) 类似forEach,jQ中第一个参数是索引第二个参数是值,原生js中反过来,第一个是值第二个是索引









jq的动画使用的是一种队列形式

jq内部会维护一个动画队列，当我们调用动画相关方法时，并非立即执行这个动画，而是会把这个方法添加到动画队列中，当队列中某个动画执行完成以后，该动画会从队列中删除，并从队列取出下一个动画。



如果写两个animate方法，相当于调用两次，串行而不是并行的

$(‘div’).animate({width:200},1000).animate({height:100},1000)



用stop方法可以停止当前正在运行的动画,不带参数的话,stop()

stop(param1,param2) 第一个参数是 是否清空动画队列(清空后所有后续动画都不再播放) 默认false

第二个参数是 是否到达动画结束状态点,填是则stop后立即到那个状态(若param1为true后续动画不再播放所以不会到后续状态) 默认true









eq(i) 代表索引 就同[i]



jq 数值可以不带单位



jq中的csshooks，jQuery对象下有一些静态属性:jQuery.cssNumber

保存了一些设置好的属性，每一个属性都是布尔值，

当jq要给元素的样式添加数字类型的值时，jq会现在jQuery.cssNumber中查找要添加的属性是否存在，如果存在而且值为true，name添加的属性处理成不带单位的数字值



为true不带单位的话会自动补单位，为false一定要带单位



也可以自定义？



$.cssHooks:当我们通过jq去给css设置属性的时候回执行的钩子函数

有点像对象的defineProperty

$.cssHooks.cssAttr = {

​    get(element){},

​    set(element,value){}

}

方便扩展，类似cssNumber，出现新属性后可针对不同浏览器进行兼容





jQuery插件写法：过程式转为对象式,写一个单独的js文件

给原型链上增加方法 jQuery.fn == jQuery.prototype == \$.fn == \$.prototype

两种扩展，一种给原型扩展，二种给jQuery类扩展(静态扩展,e.g jQuery.),

给jQuery类的原型对象上添加新方法(不直接操作原型),jq提供了专门用于扩展原型的方法,$.fn.extend



```
$.fn.extend({
//对象中的每一项都会被添加到jquery原型上
a(){console.log('a function')},
b(){},
drag(){
    //this指向调用这个方法的jq对象,例如$('.box').drag的this就是$('.box')
    this.on('mousedown',function(e){
        //因为不是箭头函数,this指向当前触发mousedown的原生元素
        let x = e.clientX - this.offsetLeft;
        let y = e.clientY - this.offsetTop;
        $(document).on('mousemove',event=>{
            $(this).css({
                left: e.clientX - x,
                top: e.clientY - y
            });
        });
        $(document).on('mouseup',event=>{
            //off类似removeEventListener
            $(document).off('');
        });
        
    })
}
})
```

$(‘.box’).drag();



静态扩展：不是在protype中扩展了，直接在jQ类上扩展

\$.extend({

​    qq(str){

​        

​    }

});



使用： \$.qq(‘12419312’)



==事件问题==



addEventListener原生匿名函数，无法直接removeEventListener,因为其实两个地方的代表的是两个不同的匿名函数。



jQ有个好用的东西，on 和 off

xx.on(‘click’,()=>{});

//取消所有click观察者对象数组中的函数,所有click事件都清除了！

xx.off(‘click’)

click: {

observes:[],

a:{observes:[]},

b:{observes:[]}

}



如果想只取消某一个click事件，给事件之下再加个属性

xx.on(‘click.event1’,()=>{})

xx.off(‘click.event1’) 这样就只是清除event1点击事件，不影响其他点击事件了



