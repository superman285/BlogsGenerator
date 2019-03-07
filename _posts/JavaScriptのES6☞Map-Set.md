---
title: JavaScriptのES6☞Map&Set
tags:
  - ES6
  - JavaScript
  - 数据结构
categories:
  - FrontEnd
  - JavaScript
date: 2019-03-05 22:29:29
---



### Map映射 类似对象

对象的问题:

var m = {}

var x = {id:1},

​    y = {id:2};



m[x] = ‘foo’;m[y]=‘bar’

console.log(m[x]);

console.log(m[y]);

打印出来啥? 这就是对象的问题

所以有了Map



Map不会将键转为string([object Object])



#### 初始化

var map = new Map([‘name1’,‘zhangsan’],[‘name2’,‘lisi’]);

//相当于 map.set(‘name1’,‘zhangsan’).set(‘name2’,‘lisi’);



实现方式: 用到了对象解构/数组解构

var items = [[‘name1’,‘zhangsan’],[‘name2’,‘lisi’]];

var m = new Map;

items.forEach(([key,value])=>m.set(key,value));

数组元素item为[xx,yy] 对应 [key,value] key value相当于解构



#### set方法支持链式操作

let map = new Map()

map.set(‘key1’,‘value1’).set(‘key2','value2')



#### 遍历方式 (具备Symbol.iterator接口)

for(let v of map){console.log(v)} v的格式是[key,value] 所以可解构

相当于for(let [key,value] of map){console.log(key,value)}

相当于for(let [key,value] of map.entries())



v of map  用的迭代方式是 v of map.entries()

Map.prototype[Symbol.iterator] === Map.prototype.entries //true

注意对比 set用的是 set.values()方法 (因为set只需要值，map需要键值)





> #### WeakMap

**特征**

- 不存在遍历方法，无法遍历
- 键名只能是对象，值可以是其他类型
- 弱引用:如果没有其他地方引用WeakMap的键(对象)，就会被系统回收



#### 坑🕳

注意对象和对象不相等，所以

map.set([5],555);//map.get([5]) 得到的是undefined!

map.set({k:‘v’},666); //map.get({k:‘v’}) 得到的是undefined!

如果想拿到值，需要指定为同一地址

var a = [5]; map.set(a,555); map.get(a) //这样就可以拿到555



### Set集合 类似数组

#### Set初始化

var set = new Set(); set.add(5); set.add(7);

相当于 var set = new Set([5,7]); //将数组作为参数



#### 数组去重

var set = new Set(array);

var newArray = Array.from(set);

//var newArray = Array.from(new Set(array));



或者

var newArray = [...new Set(array)];



Set变数组的方法:

1. Array.from(set)

2. [...set]


#### add方法支持链式调用

var set = new Set();

set.add(1).add(2); //add方法返回的仍然是set结构，所以可链式调用。



注意💡

delete和clear方法 不支持链式调用

delete返回布尔值 删除成功或失败



set.has(1) //判断是否含有元素1

set.clear() //清空集合

set.delete(v) //删除集合中某个元素



#### 遍历方式 (具备Symbol.iterator接口)

for(let i of set) //1 2 3 “g”

for(let i of set.keys()) // 1 2 3 “g”

for(let i of set.values()) // 1 2 3 “g”

for(let i of set.entries()) //\[1,1] \[2,2] \[3,3] \['g',‘g’]

Set集合中 元素的键名和键值相当于是相同的 都是元素的值



Set.prototype[Symbol.iterator] === Set.prototype.values //true

说明i of set调用的是 set.values()的方法



set.forEach((value,key,arr)=>console.log(value,key,arr))

value和key是一致的 而不是像数组索引(0,1,2,3)



#### 求交集，并集，差集

let a = new Set([1,2,3])

let b = new Set([2,3,4])



并集:

let union = new Set([...a,...b])



交集:

let intersect = new Set([...a].filter(v=>b.has(v))) b有才丢入filter



差集:

a差b

let difference = new Set([...a].filter(v=>!b.has(v))) b没有丢入filter



#### 注意问题

var set = new Set([undefined,undefined,null,null,true,1,NaN,NaN,{},{},[],[]])

es6中 认为两个NaN是相等的 

注意{}和{},[]和[] 由于地址不同 是不相等的 所以都是唯一的

所以set中含有

```javascript
Set{{},{},[],[]}
```



<font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;line-height:28px">操作是实时的,例如在console.log打印后再删除元素，但是看控制台可能却是删除元素后的结果，说明操作是实时影响到结果的。</font>

所以后面的操作可能影响到前面使用时的结果，要注意。





> #### WeakSet

**特征**

- 没有遍历方法，无法遍历
- 成员只能是对象，只能add对象
- 弱引用:如果没有其他地方引用WeakSet的元素(对象)，就会被系统回收



**冷**🕳

let arr = [1,2,3,4]

console.log(arr.map(parseInt)) //打印出[1,NaN,NaN,NaN]

接收的参数是 1,0 2,1 3,2 4,3

即parseInt(1,0) | parseInt(2,1) 将1转为0进制

将2转为1进制 将3转为2进制 将4转为3进制



