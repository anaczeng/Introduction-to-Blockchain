公证链致力于使用已有的比特币区块链设施来实施新型的数据记录和管理方案。去中心化、不可篡改和可追溯的三大性质让区块链天生适用于公证业务。人们希望用这项技术来记录土地注册、艺术品奢侈品所有权转移、选民投票等等。公证链系统相当于一个中间层，对上为具体的应用（例如投票系统）提供接口，对下将数据锚定进比特币区块。下面将从数据结构、网络和工作流程三方面介绍公证链系统。

![](/assets/fig-6-10.png)

图6-10：比特币系统、Factom系统和具体应用关系示意图。

#### 6.3.1 数据结构

![](/assets/fig-6-11.png)

图6-11：Factom数据结构一览图。

Factom的数据结构是分层的，由目录区块、条目区块和条目三层存储结构构成。Factom的链（Chain）是个逻辑结构，是一组同属特定程序的有序条目。

##### 6.3.1.1 目录区块（Directory Block）

目录区块是Factom数据结构的第一层，每分钟产生一个。约十个连续的目录区块组成一棵Merkle树，其根会被锚定在新产生的比特币区块中。目录区块主要为了存储条目区块的链ID（ChainID）和其对应的键Merkle树根（KeyMR）。一个目录区块可以存储上百万条这样的记录，并且链ID都是唯一的。目录区块包含头和主体两部分。

表6-5：目录区块头每部分大小和功能的具体介绍。

![](/assets/fig-table-6-5.png)

BodyMR是该区块身体所有元素构造的Merkle树的根，类似于比特币系统将交易构造成Merkle树，并将树根存储在区块头。KeyMR树根是区块头散列值和BodyMR这对键值的散列值，它在目录区块中的作用类似于比特币区块头中的前区块头散列值。

目录区块的主体包含了条目区块的链ID及其对应的KeyMR。除此之外，它还包含辅助链条区块的信息。Factom有三条辅助管理的链条，它们分别是管理链、条目信用链和Factoid链。感兴趣的读者可以查阅Factom开发者指南。

![](/assets/fig-6-12.png)

图6-12：目录区块示意图。

##### 6.3.1.2 条目区块（Entry Block）

条目区块是Factom数据结构的第二层。每个条目区块对应一个链ID，存储一段时间内对应该链ID产生的新条目（Entries）。最后这些新条目构成一棵Merkle树，其链ID及KeyMR将打包进目录区块。条目区块也包括头和主体两部分。

表6-6：目录区块头每部分大小和功能的具体介绍。

![](/assets/fig-table-6-6.png)

条目区块的主体部分按接收前后的顺序打包了条目的散列值。

![](/assets/fig-6-13.png)

图6-13：条目区块示意图。

##### 6.3.1.3 条目（Entries）

条目是Factom数据结构的最后一层，它包含了由用户提供的自定义数据。用户可对要上传至Factom系统的数据先进行加密或哈希计算来保护隐私，对不敏感数据也可以直接上传普通文本文件。数据可以是视频，音频，图像或是文本等。条目包括了头和负载。

表6-7：条目每部分大小和功能的具体介绍。

![](/assets/fig-table-6-7.png)

负载主要是用户上传的数据。若外部ID大小不为0，负载还包含外部ID的数据。

![](/assets/fig-6-14.png)

图6-14：条目示意图。

##### 6.3.1.4 链（Chain）

链是一个逻辑结构，是一组拥有相同链ID的有序条目和条目区块。对于特定应用来说，它只需关注属于它的链ID相关的数据，可忽略其他的数据；一组拥有相同链ID的有序条目和条目区块可近似看成一条区块链的结构。

#### 6.3.2 网络

Factom拥有一个类似于比特币系统的点对点网络。该网络中包含三类节点：全节点（Full Node），联合服务器（Federated Server）和审计服务器（Auditor Server）。值得一提的是，Factom本身是不具备审查条目真伪的功能。如果审计过程可以程序化，审查条目真伪的代码由应用提供并上传到Factom系统；否则，将经过可信任的审计人员审查签名再输入到Factom系统。

表6-8：节点类型和描述。

![](/assets/fig-table-6-8.png)

三类节点都存有系统所有的数据，其中联合服务器和审计服务器都是全节点，但并非所有的全节点都是联合服务器或审计服务器。联合服务器和审计服务器的角色并非一层不变，它们每四小时会根据特定的规则重新分配角色。

![](/assets/fig-6-15.png)

图6-15：三类节点的关系示意图。

#### 6.3.3 工作流程

现在我们假设有一款应用可以将用户在Twitter上的推文记录在Factom系统，而子贡是该款应用的用户之一。

```
输入：factom-cli generateaddress fct ftest
输出：FA3FMuvEXxiSQeUnGUkwVCXprWazAzEDvmDAV94eRXTRa2fpgCFG

输入：factom-cli generateaddress ec ectest
输出：EC3JQNanBTQmJkfBqcqZfkx8tiLVVvkHfrZuJvqzkLGr35GQq4XP

输入：factom-cli balances
输出：Factoid Addresses
        ftest FA3FMuvEXxiSQeUnGUkwVCXprWazAzEDvmDAV94eRXTRa2fpgCFG 4
      Entry Credit Addresses
        ectest EC3JQNanBTQmJkfBqcqZfkx8tiLVVvkHfrZuJvqzkLGr35GQq4XP 0

输入：factom-cli newtransaction testTx
输出：Success building a transaction

输入：factom-cli addinput testTx ftest 3
输出：Success adding Input

输入：factom-cli addecoutput testTx ectest 3
输出：Success adding Entry Gredit Output

输入：factom-cli getfee testTx
输出：The fee due for this transaction is 0.010908

输入：factom-cli addinput testTx ftest 3.010908
输出：Success adding Input

输入：factom-cli sign testTx
输出：Success signing transaction

输入：factom-cli transactions
输出：testTx:  (略)

#Check the transaction and if it is OK, countinue.

输入：factom-cli submit testTx
输出：Success Submitting transaction

#Pass for a while…

输入：factom-cli balances
输出：Factoid Addresses 
        ftest FA3FMuvEXxiSQeUnGUkwVCXprWazAzEDvmDAV94eRXTRa2fpgCFG 0.989092
      Entry Credit Addresses
        ectest EC3JQNanBTQmJkfBqcqZfkx8tiLVVvkHfrZuJvqzkLGr35GQq4XP 3
```

（1）    子贡购买了Factoids，并将其转化成用以计算允许写入数据大小的条目信用；

此步骤与比特币系统进行交易的过程十分类似，下面用Factom真实的命令来执行上述过程：

（2）    子贡提交了包含公钥和其条目信用的支付；

（3）    一台联合服务器接受了支付并将该支付接受的消息广播全网；

Factom主要的贡献在下面过程：

（4）    子贡确认了支付接受后提交了推文，推文被系统处理成条目；

（5）    由于子贡是首次使用Factom系统，一台联合服务器为其创建了新的链ID；否则将省略链ID创建的步骤。服务器将条目加入进程名单和打包进对应链ID的条目区块；此后，它将条目确认消息广播全网。

（6）    其他联合服务器收到该条目确认消息后，更新并验证了自己进程名单的视图，以及更新了对应链ID条目区块的视图；

（7）    该分钟的目录区块包含了这分钟内所有联合服务器打包的条目区块的链ID和其对应的Merkle树根；

（8）    子贡在10分钟内发布多条推文。若她仍有剩余的条目信用，每条新推文在Factom的上传都重复步骤（2）-（7）；否则从步骤（1）开始。

（9）    当Factom系统产生10个连续尚未锚定的目录区块时，产生这10个目录区块的Merkle树根并将该结果锚定在比特币系统中。

至此，子贡这10分钟发布的所有推文就被永久性地记录在Factom系统中了。

![](/assets/fig-6-16.png)

图6-16：步骤（4）到步骤（7）的流程图示意图。

