---
layout: post
title:  "rust学习笔记"
categories: rust
tags:  rust
author: malefo
---

* content
{:toc}

## *前言*

我是怎么接触rust的呢，想想好像是在19年底的时候，一个同事和我说:“你知道rust吗？巨吊，没有gc。”。我当时只是左耳进右耳出。
后面新冠爆发，在家办公，看了一点区块链的东西，然后想到了facebook的libra，看到libra是用rust实现的，突然一下想到rust还在哪里听到过。
一下子醒悟了，这是之前同事说的东西，然后开始进行了解，学习。

这段时间刚好离职了，离去下份工作入职还有差不多半个月的时间，有了些时间，更新一下博客，同时给自己进行一个学习总结和充电。

<!-- more -->

***

* [学习回顾](#学习回顾)

* [制定目标](#制定目标)

* [实现目标](#实现目标)

***


##### 学习回顾

***

真正开始学习是从四月份开始，那时候闲了下来，上家公司好像因为财务问题即将GG，项目少了很多很多。
时间空了出来，同时老同学说他自己的公司经常7月份要报表10月份才出得来，因为数据收集问题，统计问题等等，
于是乎开始给他进行开发，开发一个报表管理系统，技术使用java+vue，vue是别人来搞，我负责后台和运维，
搭建服务器，搭建代码管理gitlab，搭建自动化部署工具jenkins，由于就两个服务，就不上k8s了，大体就这些。

刚开始拿自己的主机装ubuntu server来做的服务器，淘宝搞了一个内网映射的工具，但是卡的不行啊，果断放弃选用阿里云。
然后开始紧密锣鼓的搭建，部署，开发。
在这之中我选用了java的异步web开发，但是我们要保存数据的要用到数据库，我看数据库，mysql没有异步的，mongodb有，就选用了mongodb，
这里为什么我要去看看数据库是否支持异步呢？举个例子

>老张爱喝茶，废话不说，煮开水。
 出场人物：老张，水壶两把（普通水壶，简称水壶；会响的水壶，简称响水壶）。
 1 老张把水壶放到火上，立等水开。（同步阻塞）
 老张觉得自己有点傻
 2 老张把水壶放到火上，去客厅看电视，时不时去厨房看看水开没有。（同步非阻塞）
 老张还是觉得自己有点傻，于是变高端了，买了把会响笛的那种水壶。水开之后，能大声发出嘀~~~~的噪音。
 3 老张把响水壶放到火上，立等水开。（异步阻塞）
 老张觉得这样傻等意义不大
 4 老张把响水壶放到火上，去客厅看电视，水壶响之前不再去看它了，响了再去拿壶。（异步非阻塞）
 老张觉得自己聪明了。
 所谓同步异步，只是对于水壶而言。
 普通水壶，同步；响水壶，异步。
 虽然都能干活，但响水壶可以在自己完工之后，提示老张水开了。这是普通水壶所不能及的。
 同步只能让调用者去轮询自己（情况2中），造成老张效率的低下。
 所谓阻塞非阻塞，仅仅对于老张而言。
 立等的老张，阻塞；看电视的老张，非阻塞。
 情况1和情况3中老张就是阻塞的，媳妇喊他都不知道。虽然3中响水壶是异步的，可对于立等的老张没有太大的意义。所以一般异步是配合非阻塞使用的，这样才能发挥异步的效用

异步就是相对于调用方，被调用方要能主动通知，因此我web使用的是异步，我去调用数据库，数据库成了被调用方，如果他不支持异步，那不一样变成了同步阻塞。

在使用java异步开发有个很有意思的地方，不在是同步获取，判断是否有数据；现在是基于流的感念，通过api去处理抽象的数据--数据流。

很快就把这个系统的java版本写完了，然后开始用go进行了一遍重构，看到go没有mongodb的异步框架，就不写了，
虽然go本身就是异步形式的，但是mongodb不是异步的，不一样编程同步阻塞了；不过在编码层可以规避这样的同步阻塞，直接新启一个go程就好。

既然很闲，就开始了rust的研究之路，这一路便是对知识的不停探索。

>了解一遍基本语法

这个过程我使用了两周去看网上的文档，有了一个对语法的初步认识，记住了一些关键字，字符串String，字符串的字面量&str，可变数组Vec等等，
记住这些常用的关键字可方便你在后面遇到不懂的时候直接百度用法写法，因为这些都是最常用的。

然后开始知道rust中文社区rust.cc，开始每天逛逛论坛，看看别人的学习笔记。

这时候我犯了一个错误，*刷题*，然后被链表的只能指针搞得晕头转向，用了几天缓过来，继续向下学习。
这时候收到了挫折，因为确实这个语言的学习曲线是比较复杂的，设计者不适用gc，直接把变量的逃逸分析搬到了编码层，你在写代码的时候就要明白变量的去处和使用。

这时候的我又犯了另外一个错误，拿公司项目进行重构，由于对各种基础知识都掌握不够的情况下，开始各种找包来使用，为了能便宜通过，也不知道自己写的代码的意思，
最后编译通过了，产生了运行时错误，又搜不到类似的问题答案，直接导致我放弃学习一个月多。

后面鬼使神差又学了起来，买了张汉东的《rust编程之道》，然后开始去做了一堆例子，算是把基础的东西大体记住了。
自此，开始对自己的项目进行了重构，这次我选择一点点的前进，搞明白我为什么要这么写，搞明白每一句话的含义，遇到不懂就查，
之后就像拨云见日一样，一切都很明朗，就算编译不过也能清楚怎么回事了。那种感觉很棒。

后面进了群，看到大家都在说异步的东西，然后看了一下招聘的信息也要使用异步的知识，这时候开启了rust的第二段旅程。
这段旅程让我对rust，go，java这三者的优缺点还有各自对异步的支持模式都有了一个大体的了解。

rust的异步使用的是无栈协程，使用异步就是为了解决c10k的问题，用单线程或者少量的线程去解决高并发。

学习回顾先说这些吧，接下来定制目标。

##### 制定目标

***

算了算，11月13离职，12月1入职。除去周六日，有10天的时间让我来完成这个目标。

* 目标：使用rust构建简单的区块链并部署
* 包含：
    1. 熟练掌握rust语法
    2. 熟练掌握区块链基本知识
    3. 熟练在linux部署rust项目

之后我会每天做一个学习记录和分享

##### 实现目标

***

> 2020.11.16

# 学习记录

>ps. 视频地址 https://www.bilibili.com/video/BV1NE41137zt
>一共82集，大概每天5集这样来刷

* **block ：区块**

* * *

```rust
pub struct Block {
    pub timestamp: i64,
    pub prev_block_hash: BigUint,
    pub data: Vec<u8>,
    pub hash: BigUint,//大数
    pub nonce: u64 //是一个随机值，找到这个随机值，就是循环了几次找到的hash
}
```
区块是最基本的数据结构，，由多个区块相连成区块链


* **block-chain ：区块链**

* * *

```rust
pub struct BlockChain {
    pub blocks: Vec<Box<Block>>,
}
```

区块链结构，有一个区块可变数组的字段

* **pow proof of work ：工作量证明**

* * *

```rust
pub struct ProofOfWork{
    pub block:Box<Block>,//要进行工作的区块
    pub target:BigUint,//目标值
}
```
工作量证明的类，此类是根据区块来生成一个目标值，然后要让此区块合法有效，就必须要调用此类的内部方法，来算取这个区块的hash

* **target_bits：难度值**

* * *

即hash前面有几个0，这几个0看似不多，但是在hash计算起来是非常消耗资源的，因此区块链对于能源的浪费也是非常严重的，但是反过来看；一个人在开车前，要花很多时间和金钱去练车，也花费了很多资源就为了获得一个驾照，但驾照有没有才是很重要的，花费的资源会心疼会可惜，但是结局是符合自己的预期的。

* **max_nonce：nonce计算范围，我选用了MAX的U64**
这个就是计算次数，就好比，驾校只给你4次机会，4次考试都没过就作废此次你的驾校活动，要想重新进驾校，又要重新交钱。


# 学习难点
**难点1：**

* * *

在学习这三个的时候，我觉得难点在于工作量证明里的target的取值
再看视频，是使用go的，直接就有支持，rust要去找包
我尝试使用了两个大数包

>num-bigint
>bigint

第二个似乎不维护了，功能也比第一个少，因此转用第一个
这两个的大叔结构体，第一个是维护了一个vec，第二个是一个[u64;4]，两者的区别
最后我使用了第一个，把block中的前一个区块hash和当前区块hash都换成了这个包里的num_bigint::BigUint

在视频里，go的大数包，打印大数的结构体出来时，是变成一个16进制的字符串打印，就比较清晰
我在使用rust的大数包，打印的是10进制数值，我就纳闷，这个数怎么是这样的。。。

经过一番思考，在二进制，十进制和十六进制之间来回切换，最后终于想明白了
先是创建一个移位256-难度值的位数的二进制大数
这个数就是pow中的target
然后block区块中的hash和prev_hash也是这个二进制数，方便打印和转换
如果存的是vec<u8>还要考虑是从后往前排还是从前往后排，要不打印的数值是不一样的，有点麻烦



**难点2：**

* * *
在格式化打印时，像前置位补0，使用这个

```
    // 你可以按指定宽度来右对齐文本。
    // 下面语句输出 "     1"，5 个空格后面连着 1。
    println!("{number:>width$}", number=1, width=6);
```
我照猫画虎，写了，然后就是空格，没有0，我就思考是不是字符串不补0，只有数值才行
然后自己写了个循环

```rust
pub fn leading_zero_to_string(str:String,width:u32) -> String{
    let mut leading_zero = width - (str.len() as u32);
    let mut leading_zero_str = String::new();

    loop {
        if leading_zero != 0 {
            leading_zero = leading_zero - 1;

            leading_zero_str.push_str("0");
        } else { break; }
    }

    leading_zero_str.push_str(str.as_str());
    leading_zero_str
}
```

> 2020.11.17

# 学习记录

* **数据库**

今天的学习时间都花在了一块。
先是选择tikv作为保存区块链的库，现在自己的云服务器上把tidb装了，然后在编译tikv的client包时，在编译grpcio出错
第一次出错，cmake没装
第二次出错，nasm没装
第三次出错，nasm装成了32位
第四次出错，就在win10上搞不过去，汇编代码出问题？

我选择放弃了，赶紧去换下一个，去选择使用redis，也是在云服务器上搞了一个实例，然后在datagrip上搞了一个，发现是真的不好用，又换了redinav，配好了连上了


* **序列化**

序列化，反序列化，一对的，即把结构体变成[u8]
我选择使用json的方式来序列化

```rust
#[derive(Debug,Deserialize,Serialize)]
pub struct BlockChain {
    pub blocks: Vec<Box<Block>>,
}

#[derive(Debug, Clone,Deserialize,Serialize)]
pub struct Block {
    pub timestamp: i64,
    pub prev_block_hash: Vec<u8>,//还是不能用BigUint，考虑到序列化问题，这个结构没有实现Deserialize,Serialize
    pub data: Vec<u8>,
    pub hash: Vec<u8>,//大数
    pub nonce: u64 //是一个随机值，找到这个随机值，就是循环了几次找到的hash
}
```

在derive里边加了两个字段 Deserialize,Serialize
在第一天的时候，hash和prev_block_hash我是用的BigUint的，但是这个结构没有实现序列化和反序列化，我只能又换回了Vec<u8>

```rust

    pub async fn serialize(&self) -> Vec<u8>{
        let block_chain_str = serde_json::to_string(self).expect("block to string err.");
        block_chain_str.into_bytes()
    }

```
序列化代码

# 学习难点

无

> 2020.11.18

# 学习记录

* **数据库**

在设置完redis之后发现不好用，于是乎我不纠结了，不再去搞了，索性直接换mongodb
毕竟之前做的项目改造有用到mongodb

mongodb是类似sql数据库的，只不过把表结构改成了document
设置两个字段在document里边，一个key，一个value，key=hash，value=[u8]

关键点就是block需要价格方法，转换成doc格式

```rust
impl Block {

    pub async fn serializer(&self) -> Vec<u8>{
        let block_chain_str = serde_json::to_string(self).expect("block to string err.");
        block_chain_str.into_bytes()
    }

    pub async fn to_doc(&self) -> bson::Document{
        let mut doc = bson::Document::new();
        let block_serialize = self.serializer().await;
        let block_hash = BigUint::from_bytes_be(self.hash.as_slice()).to_str_radix(16);

        // let b = bson::to_bson(block_serialize.as_slice()).expect("[u8] to bson err");
        let mut b = bson::Binary{ subtype: spec::BinarySubtype::Generic, bytes:  block_serialize };
        doc.insert("key",block_hash);
        doc.insert("value",b);
        doc
    }
}
```

之后就是存进mongo就好
```json
{
    "_id": ObjectId("5fb4ecc300f640b900a25c94"),
    "key": "8e650124c1e4ee1dc3f98232caacb79ed577d8f61cd0568e8ce9243923559a",
    "value": BinData(0, "eyJ0aW1lc3RhbXAiOjE2MDU2OTI2MTEsInByZXZfYmxvY2tfaGFzaCI6WzBdLCJkYXRhIjpbNzEsMTAxLDExMCwxMDEsMTE1LDEwNSwxMTUsMzIsNjYsMTA4LDExMSw5OSwxMDddLCJoYXNoIjpbMCwxNDIsMTAxLDEsMzYsMTkzLDIyOCwyMzgsMjksMTk1LDI0OSwxMzAsNTAsMjAyLDE3MiwxODMsMTU4LDIxMywxMTksMjE2LDI0NiwyOCwyMDgsODYsMTQyLDE0MCwyMzMsMzYsNTcsMzUsODUsMTU0XSwibm9uY2UiOjUxfQ==")
}

// 2
{
    "_id": ObjectId("5fb4ecc300551f8900a25c95"),
    "key": "3874114fbc6319b43a013611fda5a8821cc034d865615a03bd19d3eca72771",
    "value": BinData(0, "eyJ0aW1lc3RhbXAiOjE2MDU2OTI2MTEsInByZXZfYmxvY2tfaGFzaCI6WzAsMTQyLDEwMSwxLDM2LDE5MywyMjgsMjM4LDI5LDE5NSwyNDksMTMwLDUwLDIwMiwxNzIsMTgzLDE1OCwyMTMsMTE5LDIxNiwyNDYsMjgsMjA4LDg2LDE0MiwxNDAsMjMzLDM2LDU3LDM1LDg1LDE1NF0sImRhdGEiOls4MywxMDEsMTEwLDEwMCwzMiw1MCw0OCw0OCwzNiwzMiw4NCwxMTEsMzIsNjUsMzIsNzAsMTE0LDExMSwxMDksMzIsNjZdLCJoYXNoIjpbMCw1NiwxMTYsMTcsNzksMTg4LDk5LDI1LDE4MCw1OCwxLDU0LDE3LDI1MywxNjUsMTY4LDEzMCwyOCwxOTIsNTIsMjE2LDEwMSw5Nyw5MCwzLDE4OSwyNSwyMTEsMjM2LDE2NywzOSwxMTNdLCJub25jZSI6OH0=")
}

// 3
{
    "_id": ObjectId("5fb4ecc3000debc400a25c96"),
    "key": "cf22df6211439b8f6c4db79d9e69c7cf8b62f2d4627424237dc0b3cdef69fb",
    "value": BinData(0, "eyJ0aW1lc3RhbXAiOjE2MDU2OTI2MTEsInByZXZfYmxvY2tfaGFzaCI6WzAsNTYsMTE2LDE3LDc5LDE4OCw5OSwyNSwxODAsNTgsMSw1NCwxNywyNTMsMTY1LDE2OCwxMzAsMjgsMTkyLDUyLDIxNiwxMDEsOTcsOTAsMywxODksMjUsMjExLDIzNiwxNjcsMzksMTEzXSwiZGF0YSI6WzgzLDEwMSwxMTAsMTAwLDMyLDU2LDQ4LDQ4LDM2LDMyLDg0LDExMSwzMiw2NSwzMiw3MCwxMTQsMTExLDEwOSwzMiw2N10sImhhc2giOlswLDIwNywzNCwyMjMsOTgsMTcsNjcsMTU1LDE0MywxMDgsNzcsMTgzLDE1NywxNTgsMTA1LDE5OSwyMDcsMTM5LDk4LDI0MiwyMTIsOTgsMTE2LDM2LDM1LDEyNSwxOTIsMTc5LDIwNSwyMzksMTA1LDI1MV0sIm5vbmNlIjo0MjN9")
}

// 4
{
    "_id": ObjectId("5fb4ecc30088ecf200a25c97"),
    "key": "ce0527c545d1c91b2933f192a5e4b11420bf031ca3e1402e44383bc9c4d78a",
    "value": BinData(0, "eyJ0aW1lc3RhbXAiOjE2MDU2OTI2MTEsInByZXZfYmxvY2tfaGFzaCI6WzAsMjA3LDM0LDIyMyw5OCwxNyw2NywxNTUsMTQzLDEwOCw3NywxODMsMTU3LDE1OCwxMDUsMTk5LDIwNywxMzksOTgsMjQyLDIxMiw5OCwxMTYsMzYsMzUsMTI1LDE5MiwxNzksMjA1LDIzOSwxMDUsMjUxXSwiZGF0YSI6WzgzLDEwMSwxMTAsMTAwLDMyLDUwLDU3LDQ4LDM2LDMyLDg0LDExMSwzMiw2NiwzMiw3MCwxMTQsMTExLDEwOSwzMiw2N10sImhhc2giOlswLDIwNiw1LDM5LDE5Nyw2OSwyMDksMjAxLDI3LDQxLDUxLDI0MSwxNDYsMTY1LDIyOCwxNzcsMjAsMzIsMTkxLDMsMjgsMTYzLDIyNSw2NCw0Niw2OCw1Niw1OSwyMDEsMTk2LDIxNSwxMzhdLCJub25jZSI6MTkzfQ==")
}
```

这就是存进去的样式

* **改造区块链的结构**

在使用完了数据库之后，我要开始改造区块链的结构，之前的区块链里边村的是一个vec可变数组，现在只用存数据库中最后一个区块的hash

>这里我有个问题，如果数据都是放在库里，而区块链不会删除数据，那数据不是会超级多？整个curd会很慢把。


改造目标

1. 新建区块链时，新生成创世区块，入库，同时把生成的hash给到blockchain中
2. 添加新区块到链中，生成的区块入库，同时新的hash给到blockchain中
3. 遍历区块链


***1***

* * *
区块链的新结构

```rust
pub struct BlockChain {
    // pub blocks: Vec<Box<Block>>,
    pub tip:String,//最后一个区块的hash，即vec<u8> to hex string
}
```

生成blockchain的方法
```rust
///创建区块链，带有创世区块
pub async fn new_block_chain() -> Box<BlockChain> {
    //创造创世区块
    let block = new_genesis_block().await;

    //获取mongodb，collection
    let mongodb_client = super::MONGODB_INSTANCE.get().unwrap();
    let db = mongodb_client.database(super::DATA_BASE);
    let collection = db.collection(super::DATA_COLLECTION);

    //插入创世区块
    collection.insert_one(block.to_doc().await,InsertOneOptions::default());

    //生成创世区块
    let block_hash = BigUint::from_bytes_be(block.hash.as_slice()).to_str_radix(16);
    
    let block_chain = BlockChain{ tip: block_hash.to_string() };
    Box::new(block_chain)
}
```


***2***

* * *

新增区块到链中

```rust
    pub async fn add_block(mut self, data: String) -> Box<BlockChain> {
        //获取mongodb collection
        let mongodb_client = super::MONGODB_INSTANCE.get().unwrap();
        let db = mongodb_client.database(super::DATA_BASE);
        let collection = db.collection(super::DATA_COLLECTION);

        //获取区块的bytes
        let mut doc = bson::Document::new();
        doc.insert("key",self.tip.clone());

        //这里知识界解包了，会出错，找不到会有none
        let find_doc:bson::Document = collection.find_one(doc,FindOneOptions::default()).unwrap().unwrap();

        //解析，反序列化
        let bs = find_doc.get("value").cloned().unwrap();
        let binary = bson::from_bson::<bson::Binary>(bs).unwrap();
        let prev_block = serde_json::from_slice::<Block>(binary.bytes.as_slice()).expect("Deserialize block err");

        //生成新区快
        let mut block = new_block(data,prev_block.hash).await;

        //工作量证明
        let pow = new_proof_of_work(*block).await;
        block = pow.run().await;

        //存新块到库中
        collection.insert_one(block.to_doc().await,InsertOneOptions::default());
        //计算新区快的hash [u8] to hex
        let block_hash = BigUint::from_bytes_be(block.hash.as_slice()).to_str_radix(16);

        //替换blockchain中的最后区块的hash
        self.tip = block_hash;

        //返回一个blockchain的指针
        Box::new(self)
    }
```

***3***

* * *

遍历

```rust
impl Iterator for BlockChain{
    //饭会的类型是Block
    //在这里使用的是内联？我记得是叫类型内联把，我也记不清了
    //代替了泛型，内置一个返回的type，这个type具体什么类型，自己定义
    //可能我学的还不深，内在的哲学我没get到
    type Item = Block;

    fn next(&mut self) -> Option<Self::Item> {
        
        //连接mongodb
        let mongodb_cli = super::MONGODB_INSTANCE.get().unwrap();
        let db = mongodb_cli.database(super::DATA_BASE);
        let collection = db.collection(super::DATA_COLLECTION);

        //获取blockchain的hash来从库中找寻block
        let hash = self.tip.as_str();
        let op :Option<bson::Document> = collection.find_one(doc!["key":hash], FindOneOptions::default()).unwrap();

        //找不到直接返回none，迭代结束
        return match op {
            None =>Option::None,
            Some(find_doc)=>{
                //解析bytes
                let bs = find_doc.get("value").cloned().unwrap();
                let binary = bson::from_bson::<bson::Binary>(bs).unwrap();
                let block = serde_json::from_slice::<Block>(binary.bytes.as_slice()).expect("Deserialize block err");
                Option::Some(block)
            }
        }

    }

}

//这里是遍历的过程，写的很搓，后面再重构吧（当码农的乐趣不就是重构么[捂脸]）
let mut iter = *block_chain.clone().into_iter();
loop {
	match iter.next(){
		None => break,
		Some(b) => {
			println!("{}",b);
			let mut blc = *block_chain.clone();
			blc.tip = BigUint::from_bytes_be(b.prev_block_hash.as_slice()).to_str_radix(16);
			iter = blc.into_iter();
		}
	}

}
```

# 学习难点

**难点1：**
* * *
在把vex<u8>装换成document的时候，发现没有方法，只能新建一个结构体，然后给字段赋值，有点扯

在得到查询结果才是最坑的，能获取出来，但是是一个bson的枚举值
bson的枚举中有Binary
然后要把bson转成Binary需要这么写
```rust
let binary = bson::from_bson::<bson::Binary>(bs).unwrap();
```
感觉挺坑的

**难点2：**
* * *

再写迭代器的时候，发现只会不停的循环获取最后一个区块然后不停的输出最后一个区块
发现问题，1没有加个中断none，2第一次写的时候返回的是blockchain，然后里边的hash都没有改过

> 2020.11.19

> 2020.11.20

> 2020.11.23

> 2020.11.24

> 2020.11.25

> 2020.11.26

> 2020.11.27

> 2020.11.28

> 2020.11.29

> 2020.11.30