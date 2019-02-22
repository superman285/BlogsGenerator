---
title: 聊聊geth和私链节点
date: 2019-01-20 20:59:51
tags:
- BlockChain
- Ethereum
---

##### 创世区块

```json
{
  "config": {
        //区块链的ID，你随便给一个就可以
        "chainId": 21,
        //下面三个参数暂时不知道干啥的
        //等我知道了补上，或者有哪位大神知道
        //可以在评论里指点我，谢谢
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
  //用来预置账号以及账号的以太币数量，应该也就是所谓的预挖
  //我这里不需要预挖，所以给了个空对象
  //如果需要可以这样加
  //"alloc": {
  //"0x0000000000000000000000000000000000000001": {"balance": "111111111"},
  //"0x0000000000000000000000000000000000000002": {"balance": "222222222"}
  //}
  "alloc"      : {},
  //币基地址，也就是默认的钱包地址，因为我没有地址，所以全0，为空
  //后面运行Geth后创建新账户时，如果Geth发现没有币基地址，会默认将第一个账户的地址设置为币基地址
  //也就是矿工账号
  "coinbase"   : "0x0000000000000000000000000000000000000000",
  //挖矿难度，你可以随便控制哦，这里设置的难度比较小，因为我喜欢钱来得快
  "difficulty" : "0x20000", //可以用小点的0x4000
    //0x2ffffd大概 一分钟5~10个
  //附加信息，随便填个文本或不填也行，类似中本聪在比特币创世块中写的报纸新闻
  "extraData"  : "",
  //gas最高限制，以太坊运行交易，合约等所消耗的gas最高限制，这里设置为最高
  "gasLimit"   : "0x2fefd8",  //0xffffff为最高
  //64位随机数，用于挖矿，注意他和mixhash的设置需要满足以太坊黄皮书中的要求
  //直接用我这个也可以
  "nonce"      : "0x0000000000000042",
  //与nonce共同用于挖矿，注意他和nonce的设置需要满足以太坊黄皮书中的要求
  "mixhash"    : "0x0000000000000000000000000000000000000000000000000000000000000000",
  //上一个区块的Hash值，因为是创世块，石头里蹦出来的，没有在它前面的，所以是0
  "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
  //创世块的时间戳，这里给0就好
  "timestamp"  : "0x00"
}
```



初始化创世节点：

geth --datadir ./data init genesis.json



启动节点

geth --datadir ./data --networkid 15 --port 30303 --rpc --rpcaddr 0.0.0.0 --rpcport 8545 --rpcapi 'db,net,eth,web3,personal' --rpccorsdomain '*' --nat "any" --nodiscover console

💡 <font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;line-height:28px">千万注意引号是英文的 特别坑</font>



##### GETH命令参数详解

| geth命令参数    | 参数含义                                                     |
| --------------- | ------------------------------------------------------------ |
| console         | 指启动节点后启用交互式js命令行                               |
| datadir         | 指定数据存放目录                                             |
| networkid       | 以太坊网络标识符，1代表主网，3Ropsten，4Rinkeby，34都为测试网，<br />默认为1，填个大点的代表私有链 |
| port            | 网卡监听端口号，不同计算机节点通过这个连接                   |
| --rpc           | 启用http-rpc服务器，允许远程指令访问                         |
| rpcaddr 0.0.0.0 | 允许任意有效的ip地址连接                                     |
| rpcport         | http-rpc服务器监听端口，即geth启动rpc服务的端口为8545(默认值) |
| rpcapi          | 提供的可供调用的api模块                                      |
| rpccorsdomain   | 可以跨域访问的域名列表 (浏览器想连接上geth需要有此项)，<br />*代表所有 |
| nat             | 端口映射机制，默认any                                        |
| nodiscover      | 禁用节点发现机制(手动添加节点)                               |
| --identity      | 自定义节点名‘name’                                           |
| --dev           | 开发者模式，自动分配一个不需要解锁的账户而且会得自动挖矿     |



<font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;line-height:28px">注意</font>

--dev 使用POA共识网络，默认预分配一个开发者账户并且会自动开启挖矿。--dev可以不用创世块初始化

创世块中调节挖矿难度

--dev.period value | value为开发者模式下挖矿周期(0 = 仅在交易时)(默认: 0)



> geth --datadir ./data --networkid 15 --port 30303 --rpc --rpcaddr 0.0.0.0 --rpcport 8545 --rpcapi ‘db,net,eth,web3,personal’ --rpccorsdomain ‘*’ --nat "any" --nodiscover --identity "superman285" console 2>gethprint.log

信息不打印在命令行中，而是输出到gethprint.log文件中，

这时如果用miner.start() 会打出null而不是true，但是也开始挖矿了



geth命令参数详解：http://www.cnblogs.com/tinyxiong/p/7918706.html



geth的控制台可以定义变量，用js语法，可用var



<font style="color:white;background:mediumseagreen;padding:3px 6px;font-weight:bold;line-height:28px">常用：</font>

eth.accounts | eth.accounts[index]

eth.blockNumber

eth.getBalance(eth.accounts[0])

personal.newAccount(“password”)

personal.unlockAccount(address) | 可以address = eth.accounts[0]

miner.start() | miner.stop()



转账

eth.sendTransaction({from:user1,to:user2,value:web3.toWei(10,“ether”)})

转账10个eth，需要挖矿才能确认交易

单位换算

web3.toWei(10,“ether”)



geth指令 启动节点时如果带了 --dev 开发者模式 可以不用创世块genesis block来初始化 ?