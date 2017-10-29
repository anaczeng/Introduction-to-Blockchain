本章耗费大量笔墨介绍了拜占庭将军问题，是因为比特币、以太坊这样的先驱，以及Swan提出的区块链2.0和3.0的远景之所以受人瞩目，都是基于去中心化这个大命题。既然如此，作为其源头的拜占庭将军问题自然值得深入探究一番。Lamport原文中的一般描述，经典描述，以及口头协议和书面协议都作了科普介绍；此外，并将拜占庭将军问题的去中心化场景和雅典公民大会的中心化场景作了对比。

POW和POS都是为了抵制故障或恶意节点通过傀儡身份进行女巫攻击而采用的协议。Hashcash是比特币区块链实际采用的POW技术，它通过穷举输入中的变量值来遍历哈希函数的大空间输出，以寻找符合验证要求开头部分碰撞的哈希值。头部部分碰撞要求的位数越多，需要遍历的空间越大，需要耗费的计算时间（工作量）也越大。在比特币系统中，Hashcash采用的哈希函数是SHA-256；POS系统中有采用Scrypt的。

POS是POW的低能耗替代方案。核心思想是复用包含在股权中的工作量来减少未来挖矿所需的哈希计算，并以此降低能耗。相比之下，POS更能抵挡51％攻击，但它也有自身局限。

在众多区块链介绍的文章和书里，时间戳似乎是个被遗忘的角落，但实际上这是区块链的雏型。"为了在点对点的网络中实现分布式的时间戳服务器，我们将采用类似Adam.Back提出的Hashcash"，也许这句原文能让你在本章结束时依稀看到比特币区块链的真容。

#### 参考文献：

\[1\]Andrew S.Tanenbaum，David J.Wetherall.计算机网络\[M\].北京：清华大学出版社，2012：400-403.

\[2\]Thomas H.Cormen等. 算法导论（原书第三版）\[M\].北京：机械工业出版社，2013.

\[3\]何书元.概率论\[M\].北京：北京大学出版社，2015.

\[4\]Melanie Swan. Blockchain:Blueprint for a New Economy\[M\]. USA:O’Reilly Media,Inc,2015.  

\[5\]Andreas Antonopoulos.Mastering Bitcoin:Unlocking Digital Crypto-Currencies\[M\].USA:O’Reilly Media,Inc,2015.  

\[6\]L Lamport,R Shostak, M Pease. The Byzantine Generals Problem. http://159.226.251.229/videoplayer/byz.pdf?ich\_u\_r\_i=1b3cff6be30a17805e8304843ef161a3&ich\_s\_t\_a\_r\_t=0&ich\_e\_n\_d=0&ich\_k\_e\_y=1645118921751263372424&ich\_t\_y\_p\_e=1&ich\_d\_i\_s\_k\_i\_d=4&ich\_u\_n\_i\_t=1, 2016/11/21. 

\[7\]Satoshi Nakamoto. Bitcoin: A Peer-to-Peer Electronic Cash System. https://bitcoin.org/bitcoin.pdf, 2016/11/21.

\[8\]Sunny King, Scott Nadal. PPCoin: Peer-to-Peer Crypto-Currency with Proof-of-Stake. https://peercoin.net/assets/paper/peercoin-paper.pdf, 2016/11/21. 

\[9\]Pavel Vasin. BlackCoin’s Proof-of-Stake Protocol v2. http://blackcoin.co/blackcoin-pos-protocol-v2-whitepaper.pdf, 2016/11/21. 

\[10\]NXT Whitepaper. http://bravenewcoin.com/assets/Whitepapers/NxtWhitepaper-v122-rev4.pdf, 2016/11/21. 

\[11\]S. Haber. W,S, Stirbetta. How to Time-Stamp a Digital Document\[J\]. Journal of Cryptology, vol3, no2,1991:99-111. 

\[12\]D. Bayer, S. Haver, W.S. Stornetta. Improving the Efficiency and Reliability of Digital Time-stamping. Sequences II: Methods in Comminication, Security and Computer Science, 1993:329-334. 

\[13\]H.Massias, X.S. Avila, And J.-J. Quisquater. Design of a Secure Timestamping Service with Minimal Trust Requirements. 20th Synmposium on Information Theory in the Benelux, 1999. 

\[14\]Jogn R. Douceur. The Sybil Attack. https://www.freehaven.net/anonbib/cache/sybil.pdf, 2016/11/21. 

\[15\]A.Back. Hashcash-A Denial of Service Counter-measure. http://www.hashcash.org/papers/hashcash.pdf, 2016/11/21.

