# 学习小组 第3周 | 共识层

EPFsg的第三周深入探讨了以太坊的共识层。

观看由[Alex Stokes](https://twitter.com/ralexstokes)主讲的关于共识层概述的演讲，视频可在[StreamEth](https://streameth.org/watch?event=&session=65e9f54579935301489a01eb)和[Youtube](https://www.youtube.com/watch?v=FqKjWYt6yWk)上观看。演讲幻灯片[在此处可用](https://github.com/eth-protocol-fellows/protocol-studies/tree/main/docs/eps/presentations/week3_presentation.pdf)。

[录音](https://streameth.org/embed/?playbackId=66a30awapcuiok0z&vod=true&streamId=&playerName=Consensus+Layer+Overview+%7C+Alex+Stokes+%7C+Week+3 ':include :type=iframe width=100% height=520 frameborder="0" allow="fullscreen" allowfullscreen')

查看第3周演讲的文字总结，请参见[笔记](https://github.com/eth-protocol-fellows/protocol-studies/files/14850973/Week.3.EPFsg.Consensus.Layer.Overview.pdf)

对于讲座期间的讨论存档，请查看我们[Discord 服务器](https://discord.gg/epfsg)中的[this thread](https://discord.com/channels/1205546645496795137/1214219045205835776/1214219052969492541)。

## 预阅读

在开始第3周的内容之前，请先熟悉[第2周](/eps/week2.md)的资源。

此外，您还可以通过阅读以下文档为演讲做好准备：
- [Ethereum.org关于权益证明（PoS）的文档](https://ethereum.org/developers/docs/consensus-mechanisms/pos)及其子主题
- [Beacon Chain 解释](https://ethos.dev/beacon-chain)
- [PoS和太阳朋克未来，Dany Ryan 2022](https://www.youtube.com/watch?v=8N10a1EBhBc)，这是一场关于合并之前的演讲，提供了关于合并和Beacon Chain开发与测试的深入见解

## 大纲

- 区块链实现了数字稀缺性，但它需要确保其完整性的安全性
- 分布式网络需要解决拜占庭容错（BFT）问题
- 比特币首次通过PoW解决了BFT问题
- 以太坊转向权益证明，切换了系统外部信号用于Sybil保护，改为系统内信号
- 使用BFT多数来确定链的状态
    - 拜占庭错误可以被协议观察到，并且股权可以被`削减`
    - 分叉选择规则总结为LMD-GHOST
    - 它通过Casper确保活跃性
- 提供更多的加密经济安全性

## 额外阅读和练习

- [Gasper论文](https://arxiv.org/pdf/2003.03052.pdf)
- [Bitwise LMD GHOST：一种高效的CBC Casper分叉选择规则](https://medium.com/@aditya.asgaonkar/bitwise-lmd-ghost-an-efficient-cbc-casper-fork-choice-rule-6db924e57d1f)
- [Eth2book，注释规范](https://eth2book.info/)
- [关于PoS的知识点，Domothy](https://domothy.com/proof-of-stake/)
- [Dankrad Feist的Slashing场景解释](https://dankradfeist.de/ethereum/2022/03/24/run-the-majority-client-at-your-own-peril.html)
- [Justin Drake的Beacon Chain设计错误](https://www.youtube.com/watch?v=10Ym34y3Eoo)
- [Devcon 3的Casper和共识](https://archive.devcon.org/archive/watch/3/casper-and-consensus/?tab=YouTube)
- [一个插槽的解剖学](https://www.youtube.com/watch?v=EswDO0kL_O0)

在学习了执行层（EL）和共识层（CL）之后，尝试运行一个客户端对。启动一个执行客户端和一个共识客户端，并阅读它们的日志，了解它们的操作方式。
