---
title: JavaScriptのES6☞Proxy
tags:
  - JavaScript
  - ES6
categories:
  - FrontEnd
  - JavaScript
date: 2019-03-07 22:24:31
---



代理可以理解为拦截层，外界想要访问目标对象的时候，需要通过这个拦截层。

起到控制和授权的作用



举个🌰:

例如在家想访问公司的内网

例如明星的经纪人，无法直接联系到明星，只能通过经纪人间接联系到明星。



Proxy(target,handleFunctionObject)

处理函数对象，

有getter和setter，分别拦截读取操作和写入操作

有has,deleteProperty，拦截in操作和delete操作



明星经纪人实例

```javascript
let famousStar = {
	name: "fanbingbing",
    age: "34",
    phone: "88888888",
    _time: "2019"
}
let starAgent = new Proxy(famousStar,{
    //target为目标，key为目标属性
    //读取操作
    get:function(target,key){
        if(key==="phone"){
            //经纪人不会告诉你明星自己的真正号码
            return "agent:1383838"
        };
        if(key==="price"){
            //明星本身没有price属性，经纪人弄了个100万出场费
            return 1000000
        };
        //不需要特殊处理的，直接返回target(明星)的key(本身属性)
        return target[key];
    },
    //value为设置的值
    //赋值操作
    set:function(target,key,value){
        if(value < 1000000){
            throw new Error("100万一口价，低了免谈！")
        }else{
            //当famousStar上有了新的属性，那么starAgent也会有这个属性
            target[key] = value;
            return true;
        }
    },
    //拦截in操作符，看明星是否有这个属性;特殊地,has并不会拦截for...in循环！
    has:function(target,key){
        //打印了这一句说明走了has拦截
        console.log("请联系agent:1383838")
        if(key === "customPrice"){
            return target[key];
            //其实会转为布尔值，所以可以写成 return true
            //除了customPrice以外的所有属性都被拦截了 都是false
        }else{
            return false;
        }
    },
    //拦截delete操作
    deleteProperty: function(target,key){
        console.log("听说你想删除属性?")
        //可删除_开头的属性
        if(key.indexOf("_")==0){
            delete target[key];
            //不写会返回false
            return true;
        }else {
            return false;
        }
    }
});

console.log(starAgent.phone);  //读取操作被proxy拦截了
console.log(starAgent.price);  //明星没有的属性但经纪人有拦截，也可以访问到经纪人提供的
console.log(starAgent.name); //经纪人没特殊拦截的属性，读到明星原本属性
console.log(starAgent.age); //经纪人没特殊拦截的属性，读到明星原本属性

starAgent.customPrice = 1080000;
console.log(starAgent.customPrice); //1080000

starAgent.customPrice = 666666;
console.log(starAgent.customPrice); //报错了! 100万一口价,低了免谈!

console.log("====has======")
console.log("customPri" in starAgent)

delete(starAgent[name]); //并没有删除成功 会打印false

delete(starAgent["_time"]); //删除成功了 特殊命名 所以必须加引号
//or delete(starAgent._time)

```



Proxy支持的所有操作，可在Reflect对象上看

所以Reflect中的操作都可以使用

例如:

let obj = {a:1,b:2}

console.log(Reflect.get(obj,‘a’));

console.log(Reflect.has(obj,‘a’));