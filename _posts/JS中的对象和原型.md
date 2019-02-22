---
title: JS中的对象和原型
date: 2018-11-02 10:53:30
tags: [JavaScript,前端知识]
---

## JS里的对象



### 对象

> 对象就是一组键值对(key-value)集合,是无序的复合数据集合。类似map结构。

一个对象的键值对之间，用逗号分隔。



💫键名如果是数值，会被自动转为字符串。



如果键名不符合标识名的条件（比如第一个字符为数字，或者含有空格或运算符），且也不是数字，则必须加上引号，否则会报错。

```js
// 报错
var obj = {
  1p: 'Hello World'
};

// 不报错
var obj = {
  '1p': 'Hello World',
  'h w': 'Hello World',
  'p+q': 'Hello World'
};
```

对象两种调用属性方式，obj.pro | obj[‘pro’]。在上面这个栗子，只能用[‘’]的方式调用。



对象的每一个键名又称为“属性”（property），它的“键值”可以是任何数据类型。如果一个属性的值为函数，通常把这个属性称为“方法”，它可以像函数那样调用。

```js
var obj = {
  p: function (x) {
    return 2 * x;
  }
};

obj.p(1) // 2
obj["p"](1) // 2 ，第二种调用方法
```



#### 表达式还是语句？

如果行首是一个大括号，它到底是表达式还是语句？

```js
{ foo: 123 }
```

JavaScript 引擎读到上面这行代码，会发现可能有两种含义。第一种可能是，这是一个表达式，表示一个包含`foo`属性的对象；第二种可能是，这是一个语句，表示一个代码区块，里面有一个标签`foo`，指向表达式`123`。



<font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;">外面加上圆括号，理解为对象；不加圆括号，理解为代码块。</font>

这种差异在`eval`语句（作用是对字符串求值）中反映得最明显。

```js
eval('{foo: 123}') // 123
eval('({foo: 123})') // {foo: 123}
```

没有圆括号，`eval`将其理解为一个代码块；加上圆括号以后，就理解成一个对象。



#### 属性的操作

##### 读取属性

点运算符和方括号运算符。

```
var obj = {
  p: 'Hello World'
};

obj.p // "Hello World"
obj['p'] // "Hello World"

```

上面代码分别采用点运算符和方括号运算符，读取属性`p`。

请注意，如果使用方括号运算符，键名必须放在引号里面，否则会被当作变量处理。

```js
var foo = 'bar';

var obj = {
  foo: 1,
  bar: 2
};

obj.foo  // 1
obj[foo]  // 2
```



方括号更加灵活多样，方括号运算符内部还可以使用表达式，点号不行。

```js
obj['hello' + ' world']
obj[3 + 3]
```



数字键可以不加引号，因为自动转为字符串。数字键不能用点运算符，会被当成小数点，报错。

```js
var obj = {
  0.7: 'Hello World'
};

obj['0.7'] // "Hello World"
obj[0.7] // "Hello World"
obj.0.7 //报错，不可用点运算符。
```



##### 属性的赋值

点运算符和方括号运算符，除了读取，还可用于赋值。

```js
var obj = {};

obj.foo = 'Hello';
obj['bar'] = 'World';
```

JavaScript 允许<font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;">属性的“后绑定”</font>，也就是说，你可以在任意时刻新增属性，没必要在定义对象的时候，就定义好属性。



##### 查看所有属性

`Object.keys`，查看一个对象本身的所有属性(所有‘键’)，返回的是一个数组(Array)。

💡 技巧：想查看一个对象中有多少个键，使用`Object.keys(objxx).length`方法即可。

(或Object.keys(objxx)[‘length’])

```js
var obj = {
  key1: 1,
  key2: 2
};

Object.keys(obj);
// ['key1', 'key2']
```



##### delete命令

用于删除对象的某个属性，删除成功后返回true

删除存在或不存在的属性，都会返回true；只有一种情况，该属性存在而且不得删除，delete才会返回false。

```js
var obj = { p: 1 };
Object.keys(obj) // ["p"]

delete obj.p // true
obj.p // undefined
Object.keys(obj) // []
```



```js
var obj = Object.defineProperty({}, 'p', {
  value: 123,
  configurable: false
});

obj.p // 123
delete obj.p // false
```

上面代码之中，对象`obj`的`p`属性是不能删除的，所以`delete`命令返回`false`（关于`Object.defineProperty`方法的介绍，请看《标准库》的 Object 对象一章）。

```js
var obj = {};
delete obj.toString // true
obj.toString // function toString() { [native code] }
```

另外，需要注意的是，`delete`命令只能删除对象本身的属性，无法删除继承的属性（关于继承参见《面向对象编程》章节）。

上面代码中，`toString`是对象`obj`继承的属性，虽然`delete`命令返回`true`，但该属性并没有被删除，依然存在。这个例子还说明，即使`delete`返回`true`，该属性依然可能读取到值。



##### in运算符

`in`运算符用于检查对象是否包含某个属性（注意，检查的是键名，不是键值），如果包含就返回`true`，否则返回`false`。

```js
var obj = { p: 1 };
'p' in obj // true
```

不能识别哪些属性是对象自身的，哪些属性是继承的。`toString`方法不是对象`obj`自身的属性，而是继承的属性。但是，`in`运算符不能识别，对继承的属性也返回`true`。

```js
var obj = {};
'toString' in obj // true
```



##### for...in循环

该循环用来遍历一个对象的全部属性。

```js
var obj = {a: 1, b: 2, c: 3};

for (var i in obj) {
  console.log(obj[i]); | console.log(i)
}
// 1  |  a
// 2  |  b
// 3  |  c
```

下面是一个使用`for...in`循环，提取对象属性名的例子。

```js
var obj = {
  x: 1,
  y: 2
};
var props = [];
var i = 0;

for (var p in obj) {
  props[i++] = p
}

props // ['x', 'y']
```



对象`obj`继承了`toString`属性，该属性不会被`for...in`循环遍历到，因为它默认是“不可遍历”的。



<font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;">hasOwnProperty</font>

判断某个属性是不是对象自身的属性(还是继承的?)，返回真假。

```js
var person = { name: '老张' };

for (var key in person) {
  if (person.hasOwnProperty(key)) {
    console.log(key);
  }
}
// name
```



以上总结From 『js标准参考教程』

------





全局对象 global

在浏览器中，global为window



<font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;">window的属性分为两类</font>

- ECMAScript规定 (标准化) parseInt | parseFloat
- 私有的(只有某些浏览器有，没有标准) 
  - alert 弹框提示
  - prompt 用户填写
  - confirm 确认
  - console 开发者
  - document （文档） DOM
  - history （浏览器） BOM



window的属性或方法，使用时可以省略window

window常用API

Window.Number() | String() | Boolean() | Object()

setTimeout (控制台打出的数字是指计时器序号)



var a = new Number(1); 这时 a的类型为object对象，而不是数字

#### 常用API

> **Number()**

直接声明1和new Number的区别

var n1 = 1;	         数字1  栈内存

var n2 = new Number(1);  对象1  栈内存存地址，堆内存有各种属性方法和值



temp = new Number(n1); temp.toString();

然后把temp干掉，把temp.toString()返回表达式n1.toString()

js之父上述后 让n1简单数字 也可以用对象的属性和方法了



切记：临时对象temp会对抹杀，普通类型.xxx时 是引入了一个临时对象temp，这次用完立马抹杀



把简单类型当成对象使用时，都是转成了临时对象temp，用完立刻抹杀。



charAt(index) 获取index索引的字符

charCodeAt(index) index索引字符对应的Unicode编码

Var s = “abcd”; s0 = s.charAt(0); s1 = s.charCodeAt(0);

则s0为“a”,s1为97,s2=s.charCodeAt(0).toString(16),为97的十六进制数61



> **String()**



> **trim**

**裁剪左右两边的多余空格**

s1 = ‘ heell  ’

s1.trim() - s1 = ‘heell’

> **concat**

**连接两个字符串**

s1 = ‘hello’; s2 = ‘world’;

console.log(s1.concat(s2));‘helloworld’

> **slice**

**切片**

slice(start,num)，从索引start号开始，切出num片

s1 = “hello”

s1.slice(1,2) - “el”

> **replace**

**替换**

s1.replace(‘e’,‘o’) - s1 = hollo (替换第一个？)



常用API可见MDN useful string methods



> **Boolean()**

5个falsy值，0 | NaN | “” | null | undefined 

其他全都是true，<font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;">所有对象均为true</font>，包括false对象





⚠️注意：

对象和对象一般都不相等，因为他们地址不一样。即使指向的东西的值一样。



## JS里的原型

#### 公用属性——原型

共用属性 toString | valueOf

为了减少内存浪费，不用每个对象里头都存一个toString



对象不存 toString和valueOf,但是可以调用

有一个隐藏的key，\_\_proto\_\_



\_\_proto\_\_ 存的是一个地址,指向那些共用属性 toString|valueOf 



⚠️注意：

Number更复杂，\_\_proto\_\_中还套了一层\_\_proto\_\_，第二层\_\_proto\_\_才是指向的所有对象共有的属性，第一层\_\_proto\_\_是含有Number独有的属性

String和Boolean与Number类似，\_\_proto\_\_中还套了一层\_\_proto\_\_



结构像一棵树，Object的共有属性(Object.prototype)相当于树根，



Object.prototype-> 对象的共有属性，也就是实例对象的\_\_proto\_\_对应的值

原型即共有属性的意思

var o1 = new Object();

o1.\_\_proto\_\_ === Object.prototype ✅ 

其实对象也有两层\_\_proto\_\_,第二层\_\_proto\_\_为null(注意不是undefined)

o1.\_\_proto\_\_.\_\_proto\_\_ === null



var n1 = new Number(1);

n1.\_\_proto\_\_ === Number.prototype

n1.\_\_proto\_\_.\_\_proto\_\_ === Object.prototype



<font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;line-height:28px">某个实例访问属性，如果该实例的构造函数的prototype没有这个属性，就会往更下一层的\_\_proto\_\_(即Object的共有属性)去找，有的话就可调用成功，如果还是没有就返回undefined。</font>

例如上文的 n1.hasOwnProperty就是往下一层的\_\_proto\_\_中找的属性，是对象才有的公共属性。



var \_\_ = new \_\_

var后的为对象，new后面的为函数对象

\_\_proto\_\_是对象的属性，prototype是函数的属性

对象.\_\_proto\_\_ === 函数.prototype



> **总结**
>
>
>
> **<font color=dared>对象.\_\_proto\_\_ == 该对象的构造函数.prototype</font>**
>
>
>
> \_\_proto\_\_是对象的属性，prototype是函数的属性
>
> `Object`| `String`| `Number`| `Boolean`| `Function`都是构造函数
>
> 否
>
> Object.\_\_proto\_\_ === Function.\_\_proto\_\_
>
> 原型对象prototype的所有属性和方法，都能被实例对象共享。
>
> 原型对象的作用，就是定义所有实例对象共享的属性和方法。
>
>
>
> 实例对象的\_\_proto\_\_指向原型对象；
>
> 构造函数的prototype指向原型对象；
>
> 原型对象的constructor指向构造函数
>
> (即Object===Object.prototype.constructor)
>
>
>
> 将一个东西当成函数，则用xx.prototype去获取他的原型
>
> 将一个东西当成对象，则用xx.\_\_proto\_\_去获取他的原型。
>
> (functionxx.prototype是一个对象,obj.\_\_proto\_\_也是一个对象)
>
> 使用\_\_proto\_\_相当于使用一个访问器，
>
> e.g. obj.\_\_proto\_\_ 相当于 Object.getPrototypeOf(obj)



 <font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;line-height:2">特殊的Function</font>

Fuction的构造函数也是Function，

Function.constructor === Function

> 所以Function.\_\_proto\_\_ === Function.prototype
>
> 即Object.getPrototypeOf(Function)===Function.prototype

全等号左侧的Function被当成对象，而右侧的Function被当做函数。

Function是Function的构造函数，

相当于var obj = new Object();

obj.\_\_proto\_\_===Object.prototype

Object是obj的构造函数

> Function.prototype.\_\_proto\_\_ = Object.prototype

Function.prototype也是一个对象，对象的\_\_proto\_\_属性即对象的构造函数Object的prototype。



⚠️注意：

<font color=dared>**Object是所有所有对象的构造函数，而Function是所有所有函数对象(typeof xx为function)的构造函数。**</font>

所以Object(Object是一个函数对象)的构造函数也是Function，则

> Object.\_\_proto\_\_ === Function.prototype 
>
> Object.\_\_proto\_\_ === Function.\_\_proto\_\_



附上一张图总结：

![prototype](prototype.jpg)



引自思否https://segmentfault.com/a/1190000014717972