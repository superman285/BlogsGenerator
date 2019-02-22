---
title: Promise手札
tags:
  - JavaScript
  - Promise
categories:
  - FrontEnd
  - JavaScript
  - Promise
date: 2019-01-02 19:30:31
---



##### 同步异步

script标签加载方式为同步阻塞方式，所以一般放在body最后

最新es版本 script标签增加了async属性 可设置为true 则为异步加载

createElement创建script对象加载 为异步非阻塞模式



##### 先记住写法

new Promise(function(resolve,reject){dosomething(异步代码)})

==等价于==

new Promise((resolve,reject)=>{dosomething(异步代码)})



Promise为js内置的构造函数，构造的Promise对象是==专门用于处理异步任务的管理工具==



> Promise并不是将异步变为同步
>
> 而是提供了一种异步代码的管理方式，将嵌套关系(回调地狱)变为平行关系





##### Promise状态

> **Promise的状态是不可逆的，一旦状态发生改变就永远无法还原(你手动也改不了)**

Promise如何管理异步代码？

它为每一个任务都维护了一个内部状态，我们可根据异步代码的执行结果去动态改变Promise的内部状态，从而实现后续代码的调用

(是我们手动改变状态，不是Promise自己维护的，如果我们不去手动改，**永远都是初始状态pending**)

- pending - 初始状态 既非成功 也非失败 在等待
- resolved - 操作成功 通过resolve(xx)方法 之后调用then方法
- rejected - 操作失败 通过reject(yy)方法 之后调用catch方法

resolve和reject函数调用类似事件对象event，需要传入进new Promise的参数中才可调用！

Promise的状态被修改后 就会调用后续的then、catch方法 



Promise中的异步任务什么时候 可以继续向下执行 全凭你吩咐 看你什么时候改Promise的状态

(看你什么时候调resolve或reject方法)



特殊写法：return Promise.resolve(xx) | return Promise.reject(yy)

这个写法既改变了PromiseStatus也改变了PromiseValue



##### Promise对象的then方法

then方法可接收两个函数作为参数，第一个表示resolved，第二个表示rejected

如果只接收了一个函数 那么就是成功

then方法的参数函数只能接收一个值

let p1 = new Promise((resolve,reject)=>{dosomething;resolve();})

p1.then(()=>{},()=>{})

如果p1是resolved状态则走then方法中的第一个函数体，

如果p1是rejected状态则走then方法中的第二个函数体。

💡注:

如果在调用resolve(par1)或reject(par2)方法时传入了参数值par，par可以被传递到then方法中使用哦，par就赋给了Promise内部的PromiseValue

p1.then(par1=>{},par2=>{})par1为resolve状态的PromiseValue,par2为rejected状态的PromiseValue



==同一个Promise对象==的then方法都是同级(相当于并发或并行)的，类似addEventListener事件注册机制

p1.then(xx=>{yy1});p1.then(xx=>{yy2})



> **then方法也是有默认返回值的**
>
> ------
>
> p1对象的then方法返回一个新的Promise对象p2(不同于p1)
>
>
>
> ***大坑:***
>
> 一旦你调用了then或catch方法而且里头有传函数作为参数(即使函数体为空！)，但你没有手动return new Promise和在里头更改status和value的话
>
> 那then或catch之后一定会给你返回这么一个Promise: <resolved>: undefined
>
> 因为then第一个参数代表resolve,而catch其实相当于then(undefined,xxx)
>
> 不信你去试试 例如p2 = p1.then(()=>{},x=>{console.log(x)})
>
> p3=p2.catch(y=>{console.log(y)})
>
> ==小坑==
>
> 如果你在函数体结尾写了return 2;返回值并不是2,而是
>
> Promise {<resolved>: 2} 没想到吧.
>
>
>
> 所以可以动手造一个new Promise作为返回值，在这个new Promise中根据需求手动设定状态
>
> 切记这个手动造的新Promise得传入参数resolve或reject



then方法可以链式调用，最好每个then中都动手构造new Promise作为返回值，根据需求来改变状态，

但是最后一个then不需要手动构造了，因为后续没有要执行的代码了



###### catch

catch相当于then的一种特殊情况的一个语法糖 也是可以链式调用滴

p1.then(()=>{},x=>{y}) => p1.then(undefined,x=>{y})

=> p1.catch(x=>{y}) 都是等价的

想链式调用多个catch 中间一定要手动返回new Promise且reject(val)

p1.catch(x=>{}).catch(x=>{}).catch(x=>{});



###### finally

如果你想在 promise 执行完毕后无论其结果怎样都做一些处理或清理时，可以在链式调用末尾使用finally

p1.then().then().finally(()=>{})



##### Promise多任务处理

- Promise.all

  当参数中所有Promise都到达了成功状态(resolved)才可以执行后续的then

  一个任务依赖多个任务的处理结果时可用

  let p1 = new Promise(xx;resolve(p1v)); let p2 = new Promise(yy;resolve(p2v));

  Promise.all([p1,p2]).then(val=>{//拿到p1和p2结果后才执行的代码})

  then中的val值为数组，数组元素为前面异步任务的值(即p1和p2 传给resolve函数的值)PromiseValue

  val->[p1v,p2v]

- Promise.race

  当参数中的Promise但凡有一个到达了成功状态(resolved)就可以执行后续的then

  一个任务依赖于多个任务中任意一个处理结果时可用

  Promise.race([p1,p2]).then(v=>{})

  then中的v代表的是最先完成的Promise对象的PromiseValue(p1和p2中先完成的那个传给resolve函数的值)



------



then中处理返回值方案选择(需不需要手动return新Promise呢?)：

1. then中没有异步代码|逻辑处理或不需要向下传值了，我们只需要用then默认返回的promise即可

   默认返回的promise的状态继承自上个promise的状态(new Promise过来的或then过来的)

2. 如果then中有异步代码 或者需要向下传值，我们就需要手动返回new Promise

- 如果仅仅需要传个值 简洁地用return Promise.resolve(val)或Promise.reject(val) 即可

- 如果除了传值可能还有更复杂的异步任务和逻辑要处理，就使用

  return new Promise(resolve=>{

  //dosomething;

  resolve(val)

  })



> 几段代码读懂promise特性 https://www.jb51.net/article/119630.htm







###### 💡补充知识点:js内置属性[[prop]] 双方括号

[[PromiseStatus]]和[[PromiseValue]]

均为js内置属性，这种双方括号属性无法直接进行读或写操作，只能通过特定方法来进行读写操作。

特定方法来了:

Promise.resolve(1) 就相当于把promise的status设置为resolved,PromiseValue设为1

想读取PromiseValue只能 Promise.resolve(‘hello’).then(res=>{console.log(res)})

then中才可以打印出‘hello’

或者这种写法:

```javascript
(async ()=>{
    let res = await Promise.resolve('hello');
    console.log(res);
})()
//注意console.log(res)要紧跟在await后才有效
```



Promise.resolve() 则 Promise为 resolved:undefined



==注意==

var er = Promise.reject(‘err2’) 会改变PromiseStatus为rejected和PromiseValue为‘err’

但是会报错 Uncaught (in promise) err2

还是得处理下错误



##### async和await 很甜的语法糖

若将函数声明为async，一般没写return的函数原本默认返回undefined，加了async声明后默认返回值为Promise对象: Promise {<resolved>: undefined}

若在async函数末尾return x ,返回的仍然是Promise对象

Promise {<resolved>: x}



await后面是一个Promise对象

等待Promise的状态发生改变(且需要是resolve) 才进行后续操作 await后面的值 会被赋值给‘=’左侧的变量

await不能单独使用，需要在async函数中使用



await后的Promise对象如果reject了 那整个async函数会中断执行

若可能变为reject，需要将await所在语句放在try catch语句中



回调函数 (err,result)=>{}

换await写的话 用try catch来处理

例子：

```javascript
async function f() {
    try {
     await Promise.reject('出错了');
     console.log('上面已经出错了');
     return await Promise.resolve('hello world');
    } catch(e) {
        console.log(e);
    }
  }
```

打印出‘出错了’ 后面代码不再执行；如果不适用try catch语句 会爆红错误

❌ Uncaught (in promise) 出错了



注意💡

let foo = await getFoo();

let bar = await getBar();

这种写法是会顺序执行的 bar会等foo拿到之后再拿 

(如果then是这种写法 p1.thenxxx;p1.thenyyy;则是并发执行)



这时就靠Promise.all了

let [foo, bar] = await Promise.all([getFoo(), getBar()]);

这个时候foo和bar就是会并发执行了 两个Promise包成了一个

但是会等到两个结果都拿到