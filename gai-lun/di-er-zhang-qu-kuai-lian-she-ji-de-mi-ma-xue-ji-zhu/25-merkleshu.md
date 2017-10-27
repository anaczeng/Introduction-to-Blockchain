我们的老朋友子贡又遇到了新的问题：

> 子贡有上千本账本。当仆人清扫完书房后，他如何确认这些账本都没有被翻动过呢？

用封条，子贡想；可是检查上千份封条是否破损也是件令人头疼的事情。有捷径吗？有，那就是把这上千部打封条的账本捆起来再在封条上打个大封条。只需要检查大封条的完整性，就可以判断账本是否被翻动过。

在虚拟世界中，Merkle树这种数据组织方式就可以把所有文件通过哈希指针链接起来，它根部的散列值就相当于上文提及的大封条。Merkle树是Ralph Merkle在1979年获得专利的数据结构，其直观图如下。除了叶节点外，每个节点都存储着指向节点的散列值，也即是图中的箭头都是2.3节介绍的哈希指针。

图2-23：Merkle树。叶子节点的文件1，文件2等是原文件。散列值11是文件1经过哈希计算后的散列值输出，同理可得散列值12,21,和22.散列值1是散列值11和散列值12作为消息输入得出的散列值输出；同理可得散列值2.最后的Merkle树根是散列值1和2作为消息输入的散列值输出。

由于哈希指针的大量存在，Merkle树防篡改的能力非常强大。只要叶子节点中的文件出现了一丝改动，该文件的哈希值就会发生变动反映在它的后继节点上；最终，这个改动会通过哈希指针层层传递到根节点。

图2-24：a.叶子节点的文件1被篡改成了文件1（1），它的散列值11也发生了变化；b.散列值11的变化导致了散列值1的变化；c.文件1被篡改的影响最终传递到Merkle树的根节点。红色部分表示篡改后发生变化的地方。从左边红色箭头可看出篡改的影响是自底向上的。

也即是说，只需要对比一下过去时刻和现在时刻的Merkle树根，即可知道它所涵盖的文件是否遭到篡改。除此之外，通过Merkle树还能快速定位出被修改的文件。具体过程只需要将新的Merkle树和旧的做对比。现在让我们回到子贡的场景：

子贡离开书房前，按照Merkle树结构给账本打上了哈希封条，并将产生的散列值依次记        下。

其中大封条也即是Merkle树根值为

76 ED 87 27 98 A0 B3 CB 3F 48 81 94 EF 09 0F 4E 21 02 4A EF。

当他回来时，对比Merkle树根值，如果依然是：

76 ED 87 27 98 A0 B3 CB 3F 48 81 94 EF 09 0F 4E 21 02 4A EF，

子贡可以放心地相信在他离开期间，账本没被翻动。

假如在子贡离开期间，仆人翻动了账本1。Merkle树根值变成了

68 9E F0 7F 79 39 4F 65 1E 8A B4 94 AB 23 63 28 3B CD 69 E0。

发现Merkle树根值不对的子贡现在需要找出这个被篡改的文件：

（1）对比根的两个前驱节点，发现散列值1不一致；

（2）对比散列值1的两个前驱节点，发现散列值11不一致。至此，可判定账本1被篡改。

图2-25：定位被篡改的文件。右边是Merkle树，黄色部分代表被子贡对比过散列值的节点；左边的散列值对应着右边树上黄色的节点，红色标记出与原来散列值不同的新散列值。可以从左边的箭头可看出，对篡改文件的定位是自顶向下的。


