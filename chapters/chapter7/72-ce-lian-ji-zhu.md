侧链技术也是社区中常被提及的概念，在其白皮书中被定义为能使比特币和其他记账资产在多条区块链中转移的技术。下面介绍该技术中两个关键的概念以及双向式对称楔形侧链与父链的交互流程。

确认时期（confirmation period）是“币在能被转移到侧链前需被锁定在父链中的时期”。这是为了确保作为转移输入的比特币存在。竞争时期（contest period）是“刚转移到侧链中的币需要锁定以免被“双花”的时期” 。这是为了确保作为转移输出的比特币不会因为软分叉而被“双花”。这两个时期本质上都是常说的“6个区块确认”以确保包含与侧链相关交易的区块能被比特币主链包含。确认时期和竞争时期的时长视具体侧链安全要求而定，是在安全性和时效性间权衡。

![](/assets/fig-7-18.png)

图7-18：确认时期和竞争时期示意图。TXID2是在父链中锁定币转移到侧链的交易，以“6个区块确认”为例。交易TXID1中的输出是交易TXID2的输入，如果包含交易TXID1的区块不被包含在主链中，那么引用交易TXID1的交易TXID2也将无效，所以为确保交易TXID2的输入有效，需要从区块1到区块6的确认时期等待。交易TXID2如果不被包含在主链中，即意味着包含更多工作量的竞争链成为了主链，交易TXID2无效，也无法在父链中锁定币以供侧链交易。

侧链使用SPV验证来与父链交换信息，SPV验证的介绍可回顾5.1.4节。下面是双向式对称楔形侧链与父链的交互流程。

![](/assets/fig-7-19.png)

图7-19：币从父链转移到侧链的流程示意图。

![](/assets/fig-7-20.png)

图7-20：币从侧链转移到父链的流程示意图。

币从父链转移到侧链：

（1）	交易TXID1经过了确认时期，作为交易TXID2的输入；

（2）	交易TXID2在父链中锁定了币，币通过SPV验证转移到侧链；

（3）	币经过竞争时期后在侧链中交易；

币从侧链转移回父链：

（4）	交易TXID3经过了确认时期，作为交易TXID4的输入；

（5）	交易TXID4将币转移回父链，通过SPV验证解锁父链的币；

（6）	币经过竞争时期后可在父链中再次交易。

从上面的过程可以很清楚看到，币在父链和侧链中双向转移的步骤是一样的，这也是该类侧链被冠以“双向式对称”的原因。运行侧链的客户端只下载父链的区块头，靠SPV验证与父链交互；这类似于第五章介绍的SPV客户端，只是侧链可以独立进行锁定币的交易。

闪电网技术可归类为侧链的一种，但Factom公证链不是。尽管Factom也依赖于父链，但其交易流动是单向的：从Factom链流向比特币区块链。此外，Factom数据在比特币区块链中的锚定方式也迥异于侧链，锚定介绍可回顾6.2节。

