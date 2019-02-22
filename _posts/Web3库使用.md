---
title: Web3库使用
tags:
  - Web3
  - Ethereum
  - JavaScript
categories:
  - BlockChain
  - Ethereum
date: 2018-12-02 20:29:14
---



##### Web3的provider提供器

- HttpProvider: HTTP服务提供器已经被弃用，因为它不支持订阅。不支持subscribe?
- WebsocketProvider: Websocket服务提供器是用于传统的浏览器中的标准方法。
- IpcProvider: 当运行一个本地节点时，IPC服务提供器用于node.js下的DApp环境，该方法提供最安全的连接。



var Web3 = require(‘web3’);

var web3 = new Web3(new Web3.providers.HttpProvider(‘http://localhost:8545’))



httpprovider更稳，错误更少



web3的public属性的状态变量 会生成getter方法

这个getter方法 的返回值一般为string格式 例如uint或address的 不过也不一定

然后web3调用合约方法 的from 的账户地址 一般为 string类型

js中拿到的地址 一般为string类型



###### 判断是否为地址类型

```javascript
web3.utils.isAddress(addr)
```



###### 判断账户类型

getCode返回的是一个promise对象，需要对结果进行处理才可以

web3.eth.getCode(address \[, defaultBlock\]\[, callback\])

参数：

- `address`：String - 要读取代码的地址
- `defaultBlock`：Number|String - 可选，使用该参数覆盖web3.eth.defaultBlock属性值
- `callback`：Function - 可选的回调函数，其第一个参数为错误对象，第二个参数为结果

返回值：

一个Promise对象，其解析值为指定地址处的代码字符串。

```javascript
var addrCorrect = false;
web3.eth.getCode(transTarget.value, function (err, result) {
                if (!err) {
                    var code = result;
                    console.log("代码:" + code);
                    if (code != "0x0") {
                        console.log("代码不为空");
                        console.log(code);
                        addrCorrect = false;
                    } else {
                        addrCorrect = true;
                    }
                } else {
                    console.log("错误:" + err);
                }
}).then(判断addrCorrect然后进行后续步骤)
```



###### 构造合约实例

```javascript
var web3 = new Web3(new Web3.providers.HttpProvider("http://127.0.0.1:7545"));
var showhandContract = new web3.eth.Contract(abi,contractAddr);
```





调用方法使用send或call



所有view或pure方法使用call



所有非view或非pure方法使用send



==定期检测账户激活的代码实例==

```javascript
var accountInterval = setInterval(function() {
  // 检查账户是否切换
  if (web3.eth.accounts[0] !== userAccount) {
    userAccount = web3.eth.accounts[0];
    // 调用一些方法来更新界面
    updateInterface();
  }
}, 100);
```





###### 事件event相关



好资料：https://cryptozombies.io/zh/lesson/6/chapter/9

http://cw.hubwiz.com/card/c/web3.js-1.0/1/4/13/



事件的参数都是可选项



==订阅合约事件和订阅区块链上的特定事件==

💡订阅合约声明的事件(event声明 emit触发)

contractObj.events.EventA() 

订阅指定的合约事件EventA(事件名为EventA)



💡订阅区块链的特定事件

http://cw.hubwiz.com/card/c/web3.js-1.0/1/3/5/

web3.eth.subscribe(type,[,options]\[,callback])



e.g. web3.eth.subscribe(‘logs’,{address:_from})

订阅来自_from的日志



subscribe订阅类型type有:

- logs 订阅日志 一定一定要options,callback可选
- syncing 订阅同步事件 无options,callback可选
- newBlockHeaders 订阅新区块头生成事件 无options,callback可选
- pendingTransactions 订阅处于pending状态的交易 无options,callback可选



==indexed关键字==

如果需要筛选数据，需要添加indexed关键字

event Transfer(address indexed _from,address indexed _to);

event Test(uint indexed _num)

使用如下：

contractObj.events.Transfer({filter:{_to:userAccount}})

过滤出_to为userAccount的所有事件

contractObj.events.Test({filter:{_num:[12,13]}})

过滤出_num为12或13的所有事件



==回调函数==

contractObj.events.Test(function(err,event){

console.log(err)//第一个参数为错误

console.log(event)//第二个参数为事件对象

})



==事件触发器 on==

- data 接收到新事件时触发
- changed 事件从区块链上移除时触发
- error 发生错误时触发

可链式调用



contractObj.events.EventA().on(‘data’,function(event){

​    console.log(event)

})



> 还有一个很好用的库推荐，Ether.js 适用于钱包、交易等场景，后续会做个笔记



Web3 签名交易要配合 EthereumJS-tx使用

推荐使用Ether.js或者truffle家的 truffle-HDWallet-Provider