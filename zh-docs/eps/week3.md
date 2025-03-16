# 学习小组 第二讲 | 共识层（Consensus Layer）

第二天的 EPFsg 讲座深入探讨了以太坊的共识层（Consensus Layer）。

可以通过以下链接观看 Alex Stokes 讲解的共识层概述演讲：
- [StreamEth](https://streameth.org/watch?event=&session=65e9f54579935301489a01eb)
- [YouTube](https://www.youtube.com/watch?v=FqKjWYt6yWk)

演讲的幻灯片 [可在此查看](https://github.com/eth-protocol-fellows/protocol-studies/tree/main/docs/eps/presentations/week3_presentation.pdf)。

[录播视频](https://streameth.org/embed/?playbackId=66a30awapcuiok0z&vod=true&streamId=&playerName=Consensus+Layer+Overview+%7C+Alex+Stokes+%7C+Week+3 ':include :type=iframe width=100% height=520 frameborder="0" allow="fullscreen" allowfullscreen')

若想了解第三周演讲的书面总结，请查看 [笔记](https://github.com/eth-protocol-fellows/protocol-studies/files/14850973/Week.3.EPFsg.Consensus.Layer.Overview.pdf)。

要查阅讲座期间的讨论记录，请查看我们 [Discord 服务器](https://discord.gg/epfsg)中的 [讨论线程](https://discord.com/channels/1205546645496795137/1214219045205835776/1214219052969492541)。

## 预习资料

在开始第三周的内容之前，请先熟悉 [第二周](./eps/week2.md) 的资源。

此外，您可以通过以下资源进行学习和准备：
- [以太坊官网关于权益证明（PoS）的文档](https://ethereum.org/developers/docs/consensus-mechanisms/pos) 及其相关子话题
- [Beacon Chain 解释](https://ethos.dev/beacon-chain)
- [PoS 与太阳朋克未来，Dany Ryan 2022](https://www.youtube.com/watch?v=8N10a1EBhBc)，该讲座在合并前进行，对合并和 Beacon Chain 的开发与测试提供了深入的见解

## 大纲

- 区块链实现了数字稀缺性，但它需要安全性来确保其完整性
- 分布式网络处理拜占庭容错（BFT）
- 比特币首先通过工作量证明（PoW）解决了 BFT 问题
- 以太坊转向权益证明（PoS），从外部信号提供的 Sybil 防护转变为系统内的自我保护
- 通过 BFT 多数来确定链的状态
    - 协议可以观察到拜占庭错误，并且可以对权益进行“削减”
    - 分叉选择规则总结为 LMD-GHOST
    - 它通过 Casper 确保活跃性
- 提供更多的加密经济安全性

## 额外阅读和练习

- [Gasper 论文](https://arxiv.org/pdf/2003.03052.pdf)
- [Bitwise LMD GHOST：高效的 CBC Casper 分叉选择规则](https://medium.com/@aditya.asgaonkar/bitwise-lmd-ghost-an-efficient-cbc-casper-fork-choice-rule-6db924e57d1f)
- [Eth2book，注释版规范](https://eth2book.info/)
- [你应该了解的关于 PoS 的内容 by Domothy](https://domothy.com/proof-of-stake/)
- [Dankrad Feist 解释的 Slashing 场景](https://dankradfeist.de/ethereum/2022/03/24/run-the-majority-client-at-your-own-peril.html)
- [Justin Drake 讲解的 Beacon Chain 设计错误](https://www.youtube.com/watch?v=10Ym34y3Eoo)
- [Devcon 3 中的 Casper 与共识](https://archive.devcon.org/archive/watch/3/casper-and-consensus/?tab=YouTube)
- [一个时隙的解剖](https://www.youtube.com/watch?v=EswDO0kL_O0)

在学习了执行层（EL）和共识层（CL）之后，可以运行一对客户端。启动一个执行客户端和一个共识客户端，阅读它们的日志，了解它们的操作方式。
