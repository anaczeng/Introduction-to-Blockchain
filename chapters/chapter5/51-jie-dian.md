#### 5.1.1节点概述

节点运行Bitcoin Core程序，它们根据使用者的目的所执行的功能侧重点会有所不同，因此会选择运行程序的不同部分。Bitcoin Core程序的代码大致涵盖了三个方面的功能：钱包功能，即发起交易、查询交易、生成配对公私钥等；挖矿功能，也即是我们说的记账；验证并转发功能，验证交易和区块的合法性，并通过转发邻节点广播至全网。因此，节点根据其功能侧重点大致上分为全节点、挖矿节点和SPV节点。全节点和挖矿节点都拥有全账本，诚实的挖矿节点是在全节点的基础上再执行挖矿操作；它们也能执行钱包功能。SPV节点是后来产生的轻量级钱包节点，因为要满足用户移动端的需求。它们不存储全账本只下载区块头，所以依赖拥有全账本的对等节点提供所需的信息；此类节点不能执行挖矿和验证转发的功能。该节将依次对全节点，挖矿节点和SPV节点进行介绍。

表5-1：节点类型和描述![](/assets/fig-table-5-1.png)![](/assets/fig-5-1.png)

图5-1：不同节点可涵盖的功能。红圈是侧重的功能，蓝圈是可以执行的功能。

#### 5.1.2 全节点

全节点下载全账本，并对照比特币事先制定的规则验证区块和交易的合法性；合法的区块和交易将被转发。全节点是比特币骨干网的组成部分。此外，它也可以执行钱包功能。根据上述说法，挖矿节点也属于全节点；但平常说的全节点狭义指那些不执行挖矿功能，且主要执行验证转发功能的节点。

##### 5.1.2.1 全节点验证交易合法性

全节点根据事先制定好的比特币规则来验证交易的合法性。交易合法性验证主要包含以下几点：

（1）验证接收到交易的语法是否正确；

（2）验证接收到交易中的私钥签名是否与TXID所示的上一交易的地址公钥匹配；

（3） 验证接收到交易中的比特币是否是未花费的，技术上称为UTXO（unspent transaction outputs）；

（4）验证接收到交易中输入的比特币额度是否不小于输出中的额度；

（5）验证接收到交易是否不在主链区块中或在交易池内。交易池内存储尚未记录入区块但已接收到的合法交易。

##### 5.1.2.2 交易的公私钥脚本验证

验证交易合法性的重要一环是验证新交易的私钥签名和TXID所示的上一交易的地址公钥是否匹配。在比特币交易的输出中并没有直接显示公钥，而是其散列值；脚本和公钥哈希值相结合就成了输出中的脚本地址公钥（ScriptPublicKey）。事实上，私钥签名也是脚本，称为脚本签名（ScriptSig）。该验证过程就是将新交易的脚本签名和上一个交易的脚本地址公钥拼接在一起，看是否能不出错地成功运行。如果运行全部代码后不出现错误，则公私钥配对成功；否则该交易无效。该脚本语言是一种栈语言，常用命令及栈的介绍可回顾上一章的4.2.4.3节，本节重点阐述这些命令的执行过程。以下是全节点验证公私钥匹配的具体步骤：

![](/assets/step-1.png)

步骤（1）～（6）验证了公钥的正确性，是比特币发送者指定的；步骤（7）证明私钥是有效的。这六个步骤中只要有一步出现错误，交易就无效了。注意：加了“&lt;&gt;”符号的是数据，以“OP\_”开头的是命令。下面以交易“b18c…”和它上一个交易“3de4…”为例，省略非必要信息：

![](/assets/fig-5-2.png)

图5-2：交易“b18c…”验证公私钥匹配过程。红色字体标明了交易“b18c…”的输入来源于交易“3de4…”的输出。蓝色字体标明了在验证过程中所需要的数据。感兴趣的读者可以通过在线转换器验证②通过哈希计算得出和⑤一致的结果。

##### 5.1.2.3 全节点验证区块合法性

全节点根据事先制定好的比特币核心共识规则来验证区块的合法性。区块合法性验证主要包含以下几点：

（1）验证接收到区块的语法是否正确；

（2）验证Merkle树根是否正确；

（3）验证区块头的散列值是否不大于nBits；

（4）验证时间戳是否不早于前面十一个区块的中位数时间，且不晚于该区块链接的上一个区块未来两小时的时间；

（5）验证包含交易的有效性：输入输出非空，交易大小不超过规定的字节数，以及交易的币额在有效的货币单位（例如非负数）；

##### 5.1.2.4 全节点的钱包功能

比特币的钱包功能非常完备，可以生成配对公私钥、发起交易、查询交易等等。下面两个例子涵盖了常用的钱包功能。为了让读者明白在比特币系统中真正发生的过程，此处直接用Bitcoin Core程序中的命令来讲解。

###### 例子1：

子贡先从鲁国钱庄处获得了8BTC，然后他转移4BTC给卫国钱庄，剩下的3.99BTC找零转回给自己。

（1）子贡先使用getnewaddress命令获取地址，然后发给鲁国钱庄用于接收8BTC；

> 输入：bitcoin-cli getnewaddress  
> 输出：14cYAhJRknBkXDtG2r3VEMS8b6UeozngrQ

（2）过一段时间后，鲁国钱庄告诉子贡已经转账了；子贡使用getreceivedbyaddress命令并设置该地址和6为参数来查看该地址经过6个区块确认的接收额度；

> 输入：bitcoin-cli getreceivedbyaddress 14cYAhJRknBkXDtG2r3VEMS8b6UeozngrQ 6  
> 输出：8.00000000

（3）子贡使用listunspent命令查看自己的未花交易UTXO；

> 输入：bitcoin-cli listunspent  
> 输出：
>
> ```
> [
>   {
>       "tixd": "48aa4e2a01668e5ebab8988c3fae709e5a720c7bd3014ad1cb23187678cd92bb",
>       "vout": 1,
>       "address": "14cYAhJRknBkXDtG2r3VEMS8b6UeozngrQ",
>       "amount": 8.00000000
>       "confirmation": 6
>   }
> ]
> ```

（4）子贡使用createrawtransaction命令创建与卫国钱庄的交易，他将与鲁国钱庄交易的外指，卫国钱庄的地址公钥和支付4BTC额度，自己的公钥地址和3.99BTC零钱额度作为参数输入；获得一个原始十六进制的加密交易字符串；

> 输入：bitcoin-cli createrawtransaction '\[{"txid":"48aa4e2a01668e5ebab8988c3fae709e5a720c7bd3014ad1cb23187678cd92bb","vout":1}\]' '{"1D7twkUwiZxYPQanhi5PXpRYmMznhC3B46":4.00000000, "14cYAhJRknBkXDtG2r3VEMS8b6UeozngrQ":3.99000000}'  
> 输出：0100000001bb92cd78761823cbd14a01d37b0c725a9e70ae3f8c98b8ba5e8e66012a4eaa480100000000ffffffff01c0897b0d000000001976a91427a0f3fa9836fae94672b95f6abbcc03e43203ba88ac00000000

（5）子贡使用decoderawtransaction命令，以（4）获得的字符串作为参数输入，查看    交易；

> 输入：bitcoin-cli decoderawtransaction 0100000001bb92cd78761823cbd14a01d37b0c725a9e70ae3f8c98b8ba5e8e66012a4eaa480100000000ffffffff01c0897b0d000000001976a91427a0f3fa9836fae94672b95f6abbcc03e43203ba88ac00000000  
> 输出：\(略\)

（6）子贡确认（5）中交易内容无误后，使用signrawtransaction命令进行签名，以（4）中获得的字符串作为参数输入，获得新一串十六进制的加密交易字符串；

> 输入：bitcoin-cli signrawtransaction 0100000001bb92cd78761823cbd14a01d37b0c725a9e70ae3f8c98b8ba5e8e66012a4eaa480100000000ffffffff01c0897b0d000000001976a91427a0f3fa9836fae94672b95f6abbcc03e43203ba88ac00000000  
> 输出：0100000004d5f9ea32d69973218d64a72992afec398eff25004432bf32dc269c253d96038e010000006a47304402204…

（7）子贡可以再次使用decoderawtransaction命令来确认交易内容，之后她使用sendrawtransaction命令，以（6）中获得的字符串作为参数输入，将交易发布到比特币网络，获得该交易的散列值。

> 输入：bitcoin-cli sendrawtransaction 0100000004d5f9ea32d69973218d64a72992afec398eff25004432bf32dc269c253d96038e010000006a47304402204…  
> 输出：fdc2d201e9b8cbd2b7ffa0104323914725609dae30937f62f559a459cdc97676

至此，子贡在交易中的操作完成。他或卫国钱庄，或者是任何人都可以使用getrawtransaction命令，以（7）中获得的散列值作为参数输入来获取加密的交易信息；再使用之前的decoderawtransaction命令获取解密后具体的交易信息。

> 输入：bitcoin-cli getrawtransaction fdc2d201e9b8cbd2b7ffa0104323914725609dae30937f62f559a459cdc97676  
> 输出：0100000001e15a001dbcd129a5c3b79c6305636ef2be0ee314f0c629612d441815cd53f065000000006a4730440220028234f1f7edb646a77…

值得注意的是，上面的过程并没有出现私钥，是因为signrawtransaction封装了私钥的操作：该例中的公钥地址是钱包生成的，所以钱包中有其对应的私钥。私钥代表了比特币的所有权，所以需要保护好私钥。私钥可以从钱包中导出，无加密的钱包直接执行dumpprivkey命令即可。有加密的钱包可以先执行walletpassphrase命令，并以设定好的密码和解锁秒数作为参数值；随后再执行dumpprivkey命令。加密钱包则使用encryptwallet命令，以自己设置的密码作为参数。比特币的钱包功能非常强大，还有查询区块信息，更改本地设置等等的命令。更详细的内容可以通过系统中的help命令查阅，附录3中收集了命令的条目。

> 输入：bitcoin-cli dumpprivkey 14cYAhJRknBkXDtG2r3VEMS8b6UeozngrQ  
> 输出：（略）
>
> 输入：bitcoin-cli encryptwallet 123456  
> 输出：（系统提示）
>
> 输入：bitcoin-cli walletpassphrase 123456 60  
> 输出：无

值得一提的另一点是交易中的手续费。细心的读者发现比特币在转移的过程中总值似乎越来越少。鲁国钱庄转了8TC给子贡，但在子贡和鲁国钱庄的交易中，输入总值为8BTC，输出的总值却变为4BTC+3.99BTC=7.99BTC。那么还有0.01BTC的比特币去哪里了呢？这部分输入与输出的差价作为手续费奖励给了记账的矿工，所以总量是不变的。关于手续费，有两个有趣的地方：

1. 比特币交易的手续费并不是强制的。但是含有手续费的交易比不含手续费的交易有更高的优先级，能更早的被矿工打包到区块里被确认；而且手续费越多，优先级越高。在早期交易少的情况下，手续费并不影响交易被确认；但在现在交易多的情况下，没有手续费的交易有较高的可能性需要等上很长时间才被确认。这是因为每个区块有1M的限容，所以能打包的交易数量有上限。1M限容限制了交易的速度，也导致了交易费用的飙升，这与中本聪低手续费的初衷就背道而驰了。

2. 由于交易是不可逆的，一旦忘记将差额打回到自己的公钥地址，那么这部分差额就会自动成为手续费。比特币历史上出现过天价手续费，例如交易4BTC比特币却付了15BTC比特币的手续费，估计就是哪位粗心的家伙忘记了。

###### 例子2：

子贡和仆人建立了一个可共同管理资金的多重签名地址。

（1）子贡和仆人分别使用getnewaddress命令获取两个地址 `13Cz2ZRYVF4TLDqppGtXhzWWtZChuQyMSb`和 `13BNvWASTJDiMfyTnK1phuMfiT4R5kjaev`；

（2）子贡和仆人分别使用validateaddress命令得到地址对应的公钥；

> 输入：bitcoin-cli validateaddress 13Cz2ZRYVF4TLDqppGtXhzWWtZChuQyMSb
>
> 输出：
>
> ```
> {
>   "isvalid": true,
>   "address": "13Cz2ZRYVF4TLDqppGtXhzWWtZChuQyMSb",
>   "scriptPubKey": "76a91418346e483142fa281b1439c4e7ea9554936db47588ac",
>   "ismine": true,
>   "iswatchonly": false,
>   "isscript": false,
>   "pubkey": "033db818156b4484e1b18f39f6cfe5b405559760e67c65e5dedc0106db7506f276",
>   "iscompressed": true,
>   "account": ""
> }
> ```

（3）使用createmultisig命令获得一个多重签名地址。

> 输入：bitcoin-cli createmultisig 2 '''\["'033db818156b4484e1b18f39f6cfe5b405559760e67c65e5dedc0106db7506f276'","'03a0e1b4f5925d7e15aa0e9dab167665e85ef62ac9898a48c5da94c43310568712'"\]'''
>
> 输出：
>
> ```
> {
>   "address": "34PgLUSorh3fnk9Ee72BwniSPc5KqSPDTQ",
>   "redeemScript": "5221033db818156b4484e1b18f39f6cfe5b405559760e67c65e5dedc0106db7506f2762103a0e1b4f5925d7e15aa0e9dab167665e85ef62ac9898a48c5da94c4331056871252ae"
> }
> ```

至此，获得了一个多重签名地址。若要动用该地址内的资金，必须同时使用子贡和仆人分别持有的私钥签名。子贡和仆人只需使用例子1的命令将资金打入该地址即可以开始共同管理。多重签名地址是构建闪电网微支付通道的开始，这部分内容将在第七章介绍。

##### 比特币小知识：比特币的第一笔实物交易

北京时间2010年5月23日，美国佛罗里达州的程序员Laszlo Hanyecz在论坛上发布帖子，要用10, 000BTC换50美金的披萨，最后由一名英国的志愿者在网上为Laszlo订购了披萨并获得这10, 000BTC的比特币。这是比特币价值第一次与物理世界的商品价值挂钩；在这交易中出现的地址也成为了比特币的著名地址之一。

##### 5.1.2.5 全节点状况

全节点的分布现在可以通过Bitnodes项目（网址：[https://bitnodes.21.co）查询。现在全节点的数量正在逐渐的减少，这对比特币系统的网络来说并不是一件好事。全节点减少主要有以下两个原因：](https://bitnodes.21.co）查询。现在全节点的数量正在逐渐的减少，这对比特币系统的网络来说并不是一件好事。全节点减少主要有以下两个原因：)

（1）全账本的存储空间过大让一部分全节点退出网络。从区块链产生一直到现在（2016年9月5日），全账本已有65GB的数据，而且还会不断增加。全节点运行并不像挖矿节点有激励机制，因此越来越多的全节点退出了网络。

（2）一部分全节点运行并不完全，它们的8333端口被防火墙关了。这意味着这些节点最多只能连接8个其他节点，不能成为网络中的骨干节点。

#### 5.1.3 挖矿节点

诚实的挖矿节点除了要验证交易和区块的合法性外，它还需要计算出新的区块，也即是常说的记账或挖矿；挖矿节点被称为矿工。随着难度的飙升，矿工也完成了从最初普通电脑CPU到如今专用矿机的更新换代，组织形式也从最初的个人管理变为了如今的矿池管理。但无论是最开始的个人电脑CPU还是现在的矿池专业矿机，它们所做的挖矿工作并无区别，仅仅是在性能上后者能更快地进行哈希运算。本节将介绍比特币系统挖矿的具体流程，并从环境保护的角度出发对现在的挖矿行业进行反思。

##### 5.1.3.1 挖矿流程

挖矿的本质是用哈希计算来证明算力（计算能力），Hashcash是其具体的实现方法。它在比特币系统中解决了两个问题：

（1）用算力作为度量来实现"一脑一票"，避免了整个系统会被某个组织或个人轻易操控；

（2）解决了去中心化的全账本"谁来记账"的问题：最快者记账；当最快者大于一时，选择蕴含算力最多的链条。

POW和Hashcash的介绍可以回顾3.4节的内容。

![](/assets/fig-5-4.png)

图5-4：术语关系说明。

概括地说，挖矿过程通过穷举随机数来构成完整的区块，然后将完整的区块通过SHA-256计算出散列值，最后将这个散列值与nBits比较。一个挖矿节点要找到合适的随机数使其构成的完整区块头的散列值小于nBits是极其耗时的，要经过成千上万次的试错过程；但是一个全节点检验一个完整区块头的散列值是否不大于nBits却很容易的事情，只需要一次哈希计算和比较。

**挖矿程序流程**：

（1）时间设定为开始挖矿的Unix时间，也即是发现合法新区块的时间。主链一旦被延长，矿工马上在其后计算新区块；

（2）将收到的合法交易都打包进区块，计算出区块头中的Merkle树根；

（3）首次穷举将随机数设置为全0，其后每次加1。得到备选的完整区块头；

（4）将备选区块头进行SHA-256计算，得出散列值；

（5）（4）中得到的散列值与nBits作比较，若不大于nBits则马上将新的区块发送给邻节点；否则跳回步骤（3）。

（6）得到符合要求的散列值后，马上将新区块转发出去以得到全网确认。

事实上因为现在难度的增加，很有可能穷举完区块头32位的随机数依然没能找到合适的散列值，这时候矿工就开始穷举币基交易中的随机数，也即是使用币基脚本中的字节，币基结构可回顾上一章的4.2.4.2节。

![](/assets/fig-5-5.png)

图5-5：挖矿流程图。

![](/assets/fig-5-6.png)

图5-6：步骤3分解：图一显示的二进制数字表示操作的具体部分；图二是一直没出现合适散列值时步骤3的细化。若从币基脚本开始穷举，工作量将指数级上升。每在币基脚本穷举一位，nonce的32位都要重新穷举一次；所以币基脚本的穷举比nonce部分的穷举工作量大。

在比特币世界，一次哈希运算是算力度量的最小粒度。一般用单位H（Hash）/s来衡量挖矿机器的算力，也即是每秒能进行哈希计算的次数。以4.2.3节的创世块为例，它的current\_target左起有8个零（十六进制），那么要找到符合要求的区块散列值，期望的哈希计算次数是H。假设一个普通的台式或手提一秒钟能做500MH，那大概8.6秒可以找到这个散列值。时至今日（2016年9月），难度值已经发生了天翻地覆的变化，current\_target左起已有18个零；若仍用算力500MH/s的机器挖矿，将需要约30万年的时间才能找到一个符合要求的区块散列值。

##### 5.1.3.2 比特币区块链抗攻击能力

通过上节的介绍，我们了解了挖矿的具体的流程以及算力的衡量方法；现在我们来探讨下挖矿和比特币共识机制的关系，以及比特币系统是如何抵抗攻击的。

比特币共识机制的结果是要让全网诚实节点只认一条主链。拟人化比喻就是节点对过去发生的历史达成一致结论，包括交易发生与否、发生的前后顺序以及具体的内容。达成该结果的方法是在区块和交易都合法的基础上，让节点选择蕴含计算量最大的链条。

![](/assets/fig-5-7.png)

图5-7：区块中的数字为难度值，难度值越大工作量越大。以算力为度量的投票结果：长的链条不一定蕴含的计算量大；图一比图二的链条短，但是却蕴含更多的工作量。如果以长度作为主链选择的标准，那么就很容易伪造一条长链成为主链。

挖矿的难度（Difficulty）可以让节点对链条蕴含的计算量做出判断。此外，它还让攻击共识机制需要付出极大的代价：除了庞大的算力外还需要足够的时间，或者运气。下面就看看常说的“双花”攻击和51%攻击在比特币系统中是怎样操作的；比特币系统又是如何抵抗攻击的。

##### “双花”攻击

“双花”问题的概念可以回顾上一章的4.1节，“双花”攻击在比特币中的具体实现让我们以下面例子作说明：

> 交易一：盗跖从子贡处获得了3BTC的比特币。该交易已有6个以上的主链区块确认。
>
> 交易二：盗跖以从子贡处获得的3BTC比特币换取张记的西瓜。尚无区块确认。
>
> 交易三：盗跖发起交易二后马上再以子贡处获得的3BTC比特币换取李家的黄酒。尚无区块确认。

在比特币网络中，就有以下交易的存在：

> 交易一（b14f8b28eb460d9d25a51addf90872f28fbd39385d839e8988130b8d0a620706）：
>
> 输入：略
>
> 输出：
>
> ```
> 价值：3BTC
> 地址：1Gg8ZkeyzeZoifqbDceJrXiLsFWV86wEEk
> ```
>
> 交易二（a00208d56051fe501a6c454c202b7498cfa334e0ad535acd9cc7dd92a73a6453）：
>
> 输入：
>
> ```
> TXID：b14f8b28eb460d9d25a51addf90872f28fbd39385d839e8988130b8d0a620706
> 索引：0
> 签名：L5E4aVE19Kyv5Cf9Akbh9zGJxoqqteHYLgZQ7D13VMqqkqNj4Q13
> ```
>
> 输出：
>
> ```
> 价值：3BTC
> 地址：12mbpvSoCfSd4HTxuFV6g3WUfs2nMcnEJK
> ```
>
> 交易三（022dcce262019cfc357b160743294357c80a8a4dafa22a453bdebf4b18f989eej）：
>
> 输入：
>
> ```
> TXID：b14f8b28eb460d9d25a51addf90872f28fbd39385d839e8988130b8d0a620706
> 索引：0
> 签名：L5E4aVE19Kyv5Cf9Akbh9zGJxoqqteHYLgZQ7D13VMqqkqNj4Q13
> ```
>
> 输出：
>
> ```
> 价值：3BTC
> 地址：16knopFn5ou7R9GZ39fEoaszoFpVtBKJSB
> ```

通过之前的学习，我们知道：

（1）交易一已在账本上而且是有效的，所以盗跖已有3BTC比特币的所有权；

（2）对单个无论是主节点还是矿工节点来说，它们只会接受交易二或交易三并拒绝另一个。

在大多数情况下，一个时刻只有一个区块产生，并在其后面接上后续的区块。那么无论被打包在区块中的是交易二还是交易三，盗跖的3BTC比特币也只能被花费一次，"双花"攻击是无效的。

![](/assets/fig-5-8.png)

图5-8：以交易二被包含在主链区块为例，交易三就会被认为是非法交易被所有节点摒弃。反之亦然。

在少数情况下，一个时刻恰好有两个区块产生，而这两个区块又恰好一个包含交易二另一个包含交易三。如果张记和李家都是心急的人，他们都没再等后续区块的出现就把商品交给了盗跖。那么至此，“双花”攻击就成功了。

![](/assets/fig-5-9.png)

图5-9：张记看到了包含交易二的账本，李家看到了包含交易三的账本。

即使出现了上述的小概率事件，只要张记和李家能耐心等待6个区块的交易确认，盗跖的"双花"也无法凑效，因为区块链即使发生了分叉也能很快收敛：只要其中的一条链更快地被新区块延长，它就被认为是主链，另一边孤零零的区块就成了孤儿块。所以，约定俗成地认为交易被至少6个区块确认了比特币才转移到新的地址。

![](/assets/fig-5-10.png)

图5-10：以包含交易二的链条被延长为例，它后面又跟了6个新区块；那么3BTC比特币就可认为已转移到张记的钱包。包含在孤儿块中的交易三是无效的。反之亦然。

从上面例子可知，在正常情况下"双花"攻击对比特币系统是无效的；而且在比特币系统中，"双花"并不会增加货币的发行量。对于倒霉的张记或李家来说，是到手的钱又被转移到它处了。

##### 51%攻击

盗跖非常固执地要成功实现她的"双花"攻击，并且现在她掌握了系统部分的算力；于是对之前的计划做了如下修改：

> 盗跖在正常发起交易二后开始秘密挖矿：计算包含交易三的区块，并在该区块后继续计算后续区块。当张记看到交易二已被6个区块确认，他就发货了。这时，盗跖广播了她的重置链条。由于重置链条比原来的蕴含更多计算量（长），所以原来的就成了无效的旁链，重置链条成为主链一部分。于是李家看到交易三被6个以上区块确认，他也发货了。至此，盗跖的"双花"攻击成功完成。

![](/assets/fig-5-11.png)

图5-11：在区块n后的主链部分原本是蓝色包含交易二的链条，现在被Mallory红色包含交易三更长的重置链条所取代了。

看起来很完美的计划，而且没有后顾之忧：诚实的节点会接受这条重置的链条，它是合法的。但盗跖要掌握多少的算力才能计算出这样一条重置链条呢？根据中本聪在原文中的公式：

![](/assets/fig-table-5-2.png)表5-2：在6个区块产生时间——大约一小时，占不同百分比算力可计算出重置链条的可能性。

根据上面数据可以看出，少于51％的算力不能保证完成这个任务的，除非你是狛枝凪斗。这就是常说的51％攻击：**利用对网络算力的掌控，将过去达成共识的交易推翻置换成新交易。**

再考虑一个实际点的问题，怎样才能拥有比特币网络51％的算力呢？现在全网的算力约1600PH／s。一台先进的蚂蚁-S9矿机算力为11,850GH／s，售价11,300元人民币。你需要至少135,021台这样的矿机才能在加入后拥有超过50％的算力，光矿机的购置就需要约15.3亿人民币。除此之外，还需要考虑场地、制冷设备、电费等一系列的开支。

![](/assets/fig-5-12.png)

图5-12：2016年9月18日的全网算力图。图片来源[http://bitcoin.sipa.be。](http://bitcoin.sipa.be。)

![](/assets/fig-5-13.png)图5-13：2016年9月18日矿机信息。图片来源[http://mining.btcfans.com/?isappinstalled=0&from=singlemessage。](http://mining.btcfans.com/?isappinstalled=0&from=singlemessage。)

在4.1节已解释过一个理性的人在拥有51％或以上算力的情况下是没有发动51％攻击的动机的；但是若存在像盗跖这样固执的人，他最好先去一趟银行。

##### 脑洞：成功51％攻击的后续

成功的51%攻击能改变比特币世界的时间线，它能重置诚实节点对过去历史的一致结论。以上述场景为例，对比特币世界的节点来说，盗跖与张记之间的交易不存在了，在那一时刻只发生了盗跖和李家的交易；但对上帝视角的用户来说，两笔交易都发生过，所以事后张记要找盗跖打官司索求赔偿，只要一查比特币的全账本，盗跖的"双花"行为便不可抵赖了。也即是比特币系统配合完善的法制，即使Mallory成功地完成了51％攻击，她依然无法成功完成真正能获得收入的"双花"攻击。

##### 5.1.3.3 激励危机

在2100万比特币尚未被挖完前，激励机制包括新产生的比特币和区块中包含的手续费；但当比特币全部挖完后，激励机制就仅剩下手续费了。激励机制犹如整个系统的心脏。若无利益驱使，人们是不愿意将自己机器的存储能力和算力贡献出来挖矿；没人挖矿，那么也就不会有新的区块产生，比特币系统也就无法运行。当比特币挖尽后，手续费是否就能维持系统的运行现在还未有定论，毕竟到理论上的2040年还有24年，比特社区还有充足的时间应付这个问题。

##### 比特币小知识：为什么比特币总发行量的上限是2100万BTC？

比特币不仅是数字货币，还是虚拟货币。所谓虚拟，就是无中生有造出来的。这也就是为什么它要有一个数字模型来阐述它的发行量。比特币发行的数学模型是一个公比为1／2的等比数列，每四年产出减半。

初始四年的产币量：

比特币总产量：

##### 5.1.3.4 挖矿的能耗问题

挖矿耗电主要在两个方面：矿机挖矿和制冷设备降温。两者都是将电能转化为不可复用热能的过程。由于现在挖矿队伍日益庞大，消耗能源的问题就日益凸显了。以蚂蚁-S9为例，它的功率是1.172千瓦。如果全网都采用这种相对低耗的矿机，一小时耗电约16万度，一年耗电14亿度。一个10万千瓦电厂一年的发电量约为5亿度，那么比特币系统光算力一年就消耗了差不多3个这样电厂的发电量。除此之外，制冷设备的耗电也不可小觑。有研究声称，到了2020年整个比特币网络的耗电量将与丹麦一国的耗电量相当。这些数据都显示挖矿的能耗问题不容忽视。

那么，挖矿对区块链来说是必需的吗？

如果按照本书"区块链一定是去中心化系统"的说法，恐怕挖矿就目前来说是必需的。因为计算能力是整个共识机制运作的基础，也是比特币系统抵抗"双花"和51％攻击的利器；挖矿恰恰就为算力提供了证明。仅管社区对替换算力做出了尝试，提出了POS，具体内容可回顾3.3节；但POS的起源还是挖矿，它只是在系统积累了一定工作量证明（即算力证明）的前提下以股权的形式不断复用之前的证明。社区另一方面的尝试，就是改变挖矿的方式。哈希计算仅能证明算力，那么寻找最大素数的过程是否就又能证明算力，又能解决数学问题呢？这是算力另一种类型的复用。这种类型的“算力复用”其实在POW提出的原文中就被赋予了名称，叫做面包布丁协议。

围绕挖矿能源消耗问题的争论很多。既然挖矿暂时不能避免，那么相比之下，传统的货币系统是否就更节能？事实也未必如此。Arvind Narayanan就提出了这样的观点：传统货币系统也耗能，例如铸造硬币，印刷纸币，运行ATM，分类硬币，注册货币，处理交易，还有货币的运输以及为了制造运输货币的专用车等等。这类的能源消耗也是不可逆的，而且除了维护该货币系统之外别无他用。

能源问题对新生事物是否能长久发展至关重要，特别在当今世界能源紧缺的大背景下。比特币，更确切说区块链系统，虽不至于比传统的解决方案差，但今后是否能有更多的应用场景，能提供更好的服务，寻找降低能耗的途径是必不可少的。

#### 5.1.4 SPV节点

SPV节点是为了方便用户使用移动端而产生的。它只下载固定大小80字节的区块头，所以需要的存储空间约是全账本的千分之一。按照现在65GB（2016年9月16日）大小的全账本，SPV节点只需要下载约65MB的数据即可，这对现在的手机或平板来说并不是特别大的存储需求。

SPV节点只运行钱包功能，但部分功能的实现会与全节点有所区别。全节点可以验证交易，SPV节点只是验证支付。全节点可以根据全账本的信息维护一个UXTO数据库，用于验证交易中的输入是否已经花费过；但SPV节点不行，它要通过尽可能多的连接拥有全账本的节点来验证支付是否成功。下面就让我们来看看SPV节点是如何验证支付的。

**高度 V.S. 深度**

高度和深度是两个相反的概念。高度是从创世块开始计算区块的个数，深度是从最新的区块开始计算。一个区块的深度加高度等于主链区块个数的总和。

![](/assets/fig-5-14.png)

图5-14：区块深度和高度，最后高度＋深度＝主链区块个数总和。以上图区块3为例，它的深度为2，高度为3，整条链子含有5=3+2个区块。深度也即使包含在区块3中交易被确认的次数。

全节点的交易都可以追溯到最初的币基交易来验证合法性，SPV节点的交易需要通过包含交易区块后续的主链区块个数验证支付成功。概略地阐述，前者通过高度，后者通过深度。

**简单支付验证（SPV）过程**

SPV节点从连接的拥有全账本的节点中获取相关的交易和区块信息。SPV节点通过交易的散列值查询到其所在的区块，通过该区块是否在主链中且后面链接上了6个主链区块来确认支付是否成功。仍以5.1.1.2的交易2和交易3为例：

![](/assets/fig-5-15.png)

图5-15：SPV节点A通过获取全节点B的交易树链和区块流链验证了交易2是否支付成功。

由于SPV节点只能验证已知交易是否存在，但它不能发现"隐藏"的交易，所以潜在"双花"攻击的危险。回想一下之前盗跖发起的"双花"攻击，一些小额的交易，比如一个西瓜，交易双方就不会等待6个区块的确定。如果SPV节点只连接全节点A，它就很可能发现不了盗跖跟其他人的小额交易。所以，SPV节点要尽可能多的连接其它节点以获取更多的信息。此外，大额交易尽量使用全账本的节点来保证安全。

![](/assets/fig-5-16.png)

图5-16：SPV节点A受到“双花”攻击，它只跟节点B连接。在只有一个确认的情况下，节点A发现不了交易3。也即是张记持有SPV节点A，只看到了交易2，就把西瓜给了盗跖；他没有发现盗跖将同样的3BTC发给李家的交易3。盗跖的“双花”就实现了。
