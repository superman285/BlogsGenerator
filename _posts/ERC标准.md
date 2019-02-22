---
title: ERC标准
tags:
  - Ethereum
  - ERC
  - Solidity
categories:
  - BlockChain
  - Ethereum
date: 2018-11-22 20:24:48
---



### ERC20标准

一个合约

包含6个未实现的方法(都带返回值)，包含两个事件



==方法==

totalSupply 返回token总发行量



balanceOf 查看_owner的余额(不是以太余额，而是你发行token的余额)



transfer 转出value数量的token给目标to,参数没有from(from其实是调用者msg.sender)

返回值为转账成功或失败



approve (==spender==,value^1^) 调用者(msg.sender)授权一个人spender(挥霍者)可以从自己账中调用value数量的token(即代表自己去使用value数量的token)。spender可以是普通账户，也可以是智能合约。



transferFrom (from,to,value^2^) ，供==spender==调用的函数(其实代币拥有者owner也可以调用?)。spender可将from(approve时的授权方,即approve时的msg.sender，一般是部署合约者)授权的value^1^数量内的value^2^(value^2^<=value^1^)数量的token转给to；from与调用transfefFrom的msg.sender不同,from是aprrove时的msg.sender

from为token的当前所有人，



> 核心围绕着spender挥霍者，挥霍者在approve被授权，在transferFrom可以调用花钱



allowance (owner,spender) 查看owner授权给spender的剩余token数(返回uint)，若授权100，spender用掉了50(把这50转走了)，再查allowance就为50。owner一般为合约部署者



Transfer事件，转账的from、to和value，成功调用转账时触发该事件

Approval事件，授权时的owner(拥有者)、spender(挥霍者)和value，成功调用approve时触发该事件



看下文解释：

https://www.jianshu.com/p/a5158fbfaeb9

https://www.wdzj.com/hjzs/ptsj/20180909/773772-1.html



transfer和transferFrom和approve的返回值bool，其实在实现中只用到true，require来判条件，因为用if来判断true或false，被web3调用是无法取到返回值的(JVM可以在log看到(decoded ouput{0}),web3就无法在log看到了)，用了function(err,result)的话肯定是没有err的，if判断不成立返回false并不会导致err，所以相当于判断失效了，仍然要用require，具体范本实现见下面例子。

==特别地== 如果方法是pure或view的 web3是可以取到返回值的，因为相当于从本地获取



即使用了if判断 但是result并不是返回值 而是这笔交易的相关信息，

blockHash,blockNumber,contractAddress,status,transactionHash等等信息



规范的实现erc20接口的例子：

[openzeppelin]https://github.com/OpenZeppelin/openzeppelin-solidity/blob/9b3710465583284b8c4c5d2245749246bb2e0094/contracts/token/ERC20/ERC20.sol

[consensys]https://github.com/ConsenSys/Tokens/blob/fdf687c69d998266a95f15216b1955a4965a0a6d/contracts/eip20/EIP20.sol



> 转账
>
> - 判断余额符合条件
> - 溢出检查
> - 事件通知





### ERC721

ERC721为非同质化代币，唯一(不可分割)资产

Non-Fungible Tokens”，翻译为不可互换的Token, 英文简写为"NFT"



ERC721不同于ERC20，他是接口而不是合约

erc20中的转移数量value到了erc721中，变成了tokenId(唯一标识)

```javascript
interface ERC721 {
    //查看owner拥有的token数量
    function balanceOf(address _owner) external view returns (uint256);
	//返回某tokenId对应的账户地址，一个tokenId对应唯一资产
    function ownerOf(uint256 _tokenId) external view returns (address);

    function safeTransferFrom(address _from, address _to, uint256 _tokenId, bytes data) external payable;
	
    function safeTransferFrom(address _from, address _to, uint256 _tokenId) external payable;
	//将from的tokenId转移到to，此处不是数量，而是某一个tokenId
    function transferFrom(address _from, address _to, uint256 _tokenId) external payable;
	//授予地址_approved具有tokenId的控制权
    function approve(address _approved, uint256 _tokenId) external payable;
	//将调用者所有资产都授权或取消授权(授权给)
    function setApprovalForAll(address _operator, bool _approved) external;
	//查询tokenId授权给了谁，若无授权会返回地址0？
    function getApproved(uint256 _tokenId) external view returns (address);
	//查看owner是否将所有资产都授权给operator
    function isApprovedForAll(address _owner, address _operator) external view returns (bool);

    event Transfer(address indexed _from, address indexed _to, uint256 indexed _tokenId);
    event Approval(address indexed _owner, address indexed _approved, uint256 indexed _tokenId);
    event ApprovalForAll(address indexed _owner, address indexed _operator, bool _approved);

}
```



设计数据结构时，考虑要实现的方法中传入参数、传出参数之间的关系，参数的个数，从而设计数据结构，是无映射关系呢还是一重映射关系，或是多重映射关系。