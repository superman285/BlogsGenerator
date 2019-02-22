---
title: Solidity学习笔记
tags:
  - Solidity
  - Ethereum
  - BlockChain
  - 智能合约
categories:
  - BlockChain
  - Solidity
date: 2019-01-26 20:22:26
---



##### 通识

一个字节对应8位(8个二进制位) 1B(Byte)对应8b(bit)

一个十六进制位对应4个二进制位(2^4^=16)

所以==一个字节对应2个十六进制位==



bytes32 32字节长，对应64位十六进制数 即256bit

address 20字节长，对应40位十六进制数【160位二进制，相当于uint160】



地址是 address 长度为40位(加上开头0x就42位)

私钥|公钥 bytes32 长度为64位



##### 基本类型

string 字符串，utf-8编码

bool

int 有符号整数

uint 无符号整数

address 16进制，一共有40位数(不包括开头0x)



##### 整数

uint8 8位无符号整数，0~2^8^-1(255)

uint16

...

uint256

以8为步长，uint相当于uint256

int类似



##### 引用类型

int[8] 定长数组

type[] 动态数组(多种类型)，length长度，push加入元素

struct 结构体 **可见性只能为internal,不支持自己声明**

mapping(key => value) 映射表 (即kv键值对) mapping(type1=>type2) map; map[key]对应value

> 「如何判断某个key是否存在mapping中」，其实是为了输入不合法的key时能给与提示，否则只是返回缺省值或者没反应 肯定不合适。
>
> 具体可见合约HMS/InfoManagement.sol
>
> 有个比较耗gas的方法
>
> 把mapping的value也做一个数组，value[] values;数组中需要有map的key(即id)
>
> 传入参数为input_id
>
> for(uint i=0;i<values.length;i++){
>
> ​    if(input_id==values[i].id){则说明input_id确实存在于mapping中的}
>
> }



> 💡特别注意：
>
> ==!!!!入参和出参支持结构体，结构体数组，数组，映射!!!!==  0.5.0  666
>
> 想支持结构体或结构体数组 开头要添加pragma experimental ABIEncoderV2;
>
>
>
> 传入或返回映射的写法 returns(mapping(uint=>uint) memory(或storage))
>
> 切记传入或返回映射只能在==internal方法==或==library库方法==中
>
>
>
> 传入或返回数组returns(uint[] memory) 当可见性为internal时，可为storage(少用)
>
>
>
> 传入或返回结构体returns(structName memory)  当可见性为internal时，可为storage(少用)
>
> 传入或返回结构体数组returns(structName[] memory) 当可见性为internal时，可为storage(少用)
>
>
>
> 具体可见HMS/InfoManagement.sol，拿方法getCustomerIDs()来试验



==结构体==

目前可能性只能为internal，而且不能显示地写出来!

struct CustomerInfo

mapping(uint=>CustomerInfo) cinfos



切记数组添加元素必须用push，不能array[1]=xxx,array[2]=xxx,不是mapping,是序列化的，一定要一个个来！

结构体添加可以structA.name=xxx

mapping添加可以 address[name]=“myName”



结构体两种赋值方式：

```javascript
CustomerInfo memory hotelAdmin = CustomerInfo({
    customerName:"Administrator",
    customerID:customers.length,
    customerAge:666,
    isCrime:false});
CustomerInfo memory hotemAdmin = CustomerInfo("Admin",1,666,false);
```

新建结构体必须加memory，因为新建的结构体未属于状态变量。

若是将已经存在的状态变量结构体赋值给新声明的结构体，可以用storage

如 CustomerInfo storage newguest = customers[0];



💡tips: 新建结构体不是storage，无所谓啊，把他添加进属于状态变量的某个数组里头，他就成状态变量啦。



solidity结构体初始化 不需要new 不像js

Person ap = Person(172,‘Satoshi’);



==bytes== 动态分配大小的字节数组

bytes byt; byt.length byt[0]，可访问长度和某个下标



bytes32 字节数组

bytes32 写一个的时候可以简写0x1234，必须为==偶数位==，后面自动补0





💡初始化数组方法：

uint[] memory values = new uint\[](3);

内存数组必须用长度参数创建，创建一个长度为3的数组

需要试一试5.0中的最新改动



##### 数据位置

storage 保存在状态树中，成员变量一般位于此处|全局变量

memory 参数、局部变量一般位于此处



有的变量可以使用修饰符来改变变量位置，但是==状态变量不可改变位置==。

两个不同位置的引用变量，会导致深拷贝警告



memory 值传递，拷贝，不改变源数据

storage 引用传递，相当于指针，会改变源数据



强制指定的数据位置：

- 外部函数的参数（不包括返回参数）： calldata
- 状态变量： storage

默认数据位置：

- 函数参数（包括返回参数）： memory
- 所有其它局部变量： storage



> 函数参数默认为memory，想显示声明数据位置的只能为struct|array|mapping
>
> 三种数据类型，其他数据类型都不可加memory或storage声明
>
> 最新版本！string类型的函数参数需要显示增加memory声明



注意💡: ==函数中结构体和数组的声明和赋值==需要显示标为storage或者memory，不声明会报warning(默认为storage),而其他类型(包括mapping)都不能带storage或memory声明。



函数中无法声明临时mapping，mapping一定会作为状态变量存在的，函数中声明mapping也需要初始化而且要将一个状态变量mapping(A)赋值给他，这时用声明的mapping相当于在用mappingA了。



合约中函数外的声明都是状态变量，都存在storage中，不能显示加上memory或storage声明。



Person p = persons[_index];
p.age = _age;

//改变age会影响原来的值



Person memory p = persons[_index];
p.age = _age;

//改变age不会影响原来的值





例子：https://cryptozombies.io/zh/lesson/3/chapter/12



批量删除数组元素，有两种方法，第一种符合直觉，但是特别费gas

- 方法需要传入storage数组参数，数组的删除项后面的每个项都往前挪一位(即重新赋值)，然后再将数组长度减1。

  因为需要修改storage变量，每动一项都要消耗gas，贵

- 方法二:由于外部调用view函数免费，可以用for循环遍历整个数组把符合要求的挑出来构建出数组，这样会便宜的多得多。(需要做if判断)



批量赋值

```javascript
uint a;
uint b;
uint c;
// 这样来做批量赋值:
(a, b, c) = (1,2,3)
```

##### 内建对象

==**block**==

block.blockhash(uint blockNumber) returns (bytes32) 指定块的哈希

block.coinbase (address) 当前块矿工地址

block.difficulty (uint) 当前块难度值

block.gaslimit (uint) 当前块gaslimit

block.number (uint) 当前块块号(区块高度)

block.timestamp (uint) 当前块时间戳



==**msg**==

调用方法时，会给方法传递一个msg属性

msg.data (byte) 完整的calldata

msg.gas (uint) 剩余gas

msg.sender (address)当前消息的发送者

msg.sig (bytes4) 呼叫数据的前四个字节(即功能标识符)

msg.value (uint) 发送的消息的数量(发送的token数量)



==now==

即block.timestap的别名 当前块时间戳



==address==

\<address\>.balance

\<address\>.transfer(uint256 amount) 发送指定数量ether到地址，失败时抛异常，



\<address\>.send(uint256 amount) returns (bool)  

发送指定数量ether到地址，失败时返回false



\<address\>.call(...) returns (bool)

\<address\>.callcode(...) returns (bool)

\<address\>.delegatecall(...) returns (bool)



getBalance()方法

return this.balance;

更安全的写法是return address(this).balance;



this代表当前合约的地址，合约地址！

msg.sender代表当前调用人的地址，外部账户地址！



==tx==

- **tx.gasprice (uint)**  gas价格

- **tx.origin (address)** 交易的发送者（全调用链）

  Solidity有一个全局变量tx.origin，它遍历整个调用堆栈，并返回原先发送调用（或事务）的帐户地址。

  如果一个函数的调用者规定必须为外部账户，然而这个合约B是被其他合约A部署的，这时用msg.sender会获得外部账户地址而不是合约地址，如果使用tx.origin则可以获得最先部署合约A的外部账户的地址了。

  不过要注意安全问题，一般较少使用。

  有一个判断require(msg.sender==tx.origin)什么含义呢？

##### 内置函数

addmod(uint x,uint y,uint k) returns (uint) (x+y)%k

mulmod(uint x,uint y,uint k) returns (uint) (x*y)%k

keccak256(...) returns(bytes32)

变参，可传多个参数

sha3 keccak256别名



assert(bool condition)

require(bool condition)

revert();



assert比较自信，断言此事不发生，发生会惩罚 扣光所有gas

require比较温和，如果条件不满足，退回剩余gas，用的更多

revert主动退回gas，if/else判断后使用

assert多用于判断非状态变量

assert可考虑放在函数结尾部分用于验证之前的操作结果的正确性



solidity内部有this，address(this)

selfdestruct(address) 销毁合约，address是收益人(销毁后钱转给)



##### 随机数生成方法

keccak256函数：

uint random = uint(keccak256(block.difficulty,now));



blockhash法:

uint random = uint(block.blockhash(block.number-1)); //？待查





不像js，变量的赋值|函数的执行 不能直接写在合约中的，而是写在函数中，这些操作是可能需要消耗gas的。或者写在js中？



#### 函数



##### 函数声明

定义

修饰

调用

返回值(可以多个，***最多7个***，可省略名字) 可return (v0,v1,...,vn)

fallback(匿名函数)



##### 函数修饰符

💡 切记区分可见性(可调用的位置)和能不能看见函数内容是不同的

调用其他合约(除继承外)需要先获得一个其他合约实例c,然后c.functionxx()



> 💡
>
> - 函数声明时默认为**`public`**类型，和显示声明为**`public`**类型的函数一样，都可供外部访问。
> - 构造函数大部分情况下都声明为`public`类型。
> - 所有在合约内的东西对外部的观察者来说都是可见的，将某些东西标记为**`private`**仅仅阻止了其它合约来进行访问(调用)和修改，但并不能阻止其它人看到相关的信息[可以看到函数是干啥的，函数体里头有啥内容，但是没法调用，没法改]。
> - 可见性的标识符的定义位置，==对于变量声明来说是在类型的后面==，==对于函数声明来说是在参数列表和返回关键字(returns)中间==。
> - 状态变量声明时，默认为**`internal`**类型，只有显示声明为**`public`**类型的状态变量才会自动生成一个和状态变量同名的**`get`**函数以供外部获取当前状态变量的值。状态变量无法声明为`external`类型。



###### 可见性 不刻意声明时有默认值

函数可见性默认为public

状态变量可见性默认为internal 无法声明为external

结构体默认为internal 且无法加任何其他的可见性声明

| 修饰符       | 意义                                                         |        分类        |
| ------------ | ------------------------------------------------------------ | :----------------: |
| public       | 任何人(包括儿子)可以调用(同一个sol文件内部可以直接调用,其他文件需要import引入)该函数，和private互斥 |     调用控制类     |
| private      | 只有该合约能调用(儿子都不可)，和public互斥                   |     调用控制类     |
| external     | 外部函数。内部不可访问(可this)，外部正常访问，子类可继承。   |     调用控制类     |
| internal     | 内部函数。内部正常访问，外部无法访问，子类可继承。           |     调用控制类     |
| view         | 合约不修改状态变量，和constant一样                           | 状态变量访问控制类 |
| constant     | 合约不修改状态变量，和constant一样                           | 状态变量访问控制类 |
| pure         | 合约不但不修改，也不读取状态变量                             | 状态变量访问控制类 |
| payable      | 涉及eth(其他token不算)的转移操作需要加上这个                 |     资金控制类     |
| 自定义修饰符 | 自定义修饰符号modifier                                       |    自定义控制类    |

函数中有transfer、send等方法，则需要加上payable修饰

合约中的struct数组的可见性只支持internal(默认值，不需要写)，不能声明其他可见性！！(新版本也看下)



==external调用注意==

合约内或后代合约(继承)需要用this方式调用，此外合约外部直接用合约实例调即可。b.externalFn();



==函数返回值return用法==

1. function .. returns (type \_result){...;\_result = xxx;}

   定义出参时加上变量名，函数体不写return

2. function .. returns (type){...;return result;}

   定义出参时不加变量名，函数体写return

函数若声明成private私有的，remix右侧工具栏是无法看到和调用的；声明成external和public才可以。

view和constant在remix中为蓝色的，pure可用于计算(只是传参，不关心别的)

public变量会自动生成一个同名查询函数，蓝色。



查询最好带上view，因为不消耗gas啊，给力



函数多个返回值，只想取后面的返回值的方法：

> (,,,,,result) = functionA();



==return在哪看==

1. 在控制台debug的decoded output字段看
2. 在右侧工具窗口debugger页签的Solidity State看所有状态，这儿可以看到
3. 如果带上view修饰符，直接在右侧界面可以看，调用方法后就在函数名下可以看到



==！注意注意！==

view和pure的函数 被合约外部(即外部账户)调用时不花费gas

但它们被内部其他函数调用时 会耗费gas (取决于主调函数)





##### require assert revert



if(msg.sender==owner){

//dosomething

}else{

​    revert();

}

不满足条件会revert回滚

更好的写法

require(msg.sender\==owner)//或assert(msg.sender==owner)

//dosomething

效果一样，但更简洁更语义化



##### 事件

可用于打日志

在logs项中的event字段可以看到事件中定义了的参数



声明：

Event BidEvent(address \_myAddr,uint \_myMoney)

使用：

在函数中发射 emit BidEvent(myAddr,myMoney); 



##### 数组

push方法只能对变长数组使用

如果想用delete删数组元素，也用变长数组吧



##### 字符串

abi.encodePacked(a,b) 连接两个字符串



##### web3.js

RPC方式访问以太坊网络

> 资料：
> http://cw.hubwiz.com/card/c/web3.js-1.0/1/4/7/



view蓝色的 用call来调用？

红色的不带view的 用send来调用？



==调用关键点==

连接到以太坊

已知合约ABI

已知合约地址(通过ABI也可以部署新合约) 知道ABI和合约地址可以获得合约实例

以太坊客户端支持rpc



1. 创建合约对象

2. 通过合约地址和合约abi获得合约实例

3. 调用合约中的方法

   实例.methods.functionName().call()



或者methods.functionName().send()



call可以不用from和gas，send必须有from和gas

call(function(){})



send({},function(){})



###### At Address 加载已部署合约

之前部署过的合约，有数据，



##### import引入的用法

- import “./xxx.sol”
- 

##### 库library的用法

1. import {Console} from "./Console.sol"; //Console为library名字

   使用时Console.log(xxx) 调用库名.方法

2. import "./AddressUtils.sol";

   合约内 using AddressUtils for address; 然后address类型自带方法isContract

   使用时isContract(addrxxx) 或者 addrxx.isContract()?





##### solidity 0.5.0那些事

https://www.colabug.com/4931922.html



- 地址分为address和address payable,必须显式声明了payable才可以transfer send
- external 的函数参数(普通类型数组!类型)需显式声明为 calldata,其他按各自规则来。
- keccak256(abi.encodePacked(a,b,c)) 必须加上abi.encodePacked
- bytes32无法转为uint8,但是可以转为uint(即uint256)
- uint和uint8 一起运算可能会被禁止
- selfdestruct参数的地址a必须为address payable,因为要把钱打到a那儿,payable才可以接收钱。
- 最新版本！string类型的函数参数需要显示增加memory声明