# 学习小组第二讲 | Consensus Layer

EPFsg 的第二天深入探讨 Ethereum 的 Consensus Layer。

观看 Alex Stokes 在 [StreamEth](https://streameth.org/watch?event=&session=65e9f54579935301489a01eb) 或 [Youtube](https://www.youtube.com/watch?v=FqKjWYt6yWk) 上关于 CL 概述的演示。演示幻灯片 [可在此处获取](https://github.com/eth-protocol-fellows/protocol-studies/tree/main/docs/eps/presentations/week3_presentation.pdf)。

[录音](https://streameth.org/embed/?playbackId=66a30awapcuiok0z&vod=true&streamId=&playerName=Consensus+Layer+Overview+%7C+Alex+Stokes+%7C+Week+3 ':include :type=iframe width=100% height=520 frameborder="0" allow="fullscreen" allowfullscreen')

有关第 3 周演示的书面摘要，请查看 [注释](https://github.com/eth-protocol-fellows/protocol-studies/files/14850973/Week.3.EPFsg.Consensus.Layer.Overview.pdf)

有关演讲期间讨论的存档，请检查我们的 Discord 服务器中的 [此线程](https://discord.com/channels/1205546645496795137/1214219045205835776/1214219052969492541)。

## 预读

在开始第 3 周的内容之前，请先熟悉 [第 2 周](/eps/week2.md) 中的资源。

此外，您可以通过学习以下资源来阅读并做好准备：

- [Ethereum.org 关于 Proof-of-Stake 的文档](https://ethereum.org/developers/docs/consensus-mechanisms/pos) 及其子主题
- [Beacon Chain 解释](https://ethos.dev/beacon-chain)
- [PoS 和太阳能朋克未来，Danny Ryan 2022](https://www.youtube.com/watch?v=8N10a1EBhBc)，合并前的演讲，对合并和 Beacon Chain 开发和测试的深刻见解

## 概要

- 区块链能够创造数字稀缺性，但它需要确保其完整性的安全性
- 分布式网络处理 Byzantine Fault Tolerance (BFT)
- Bitcoin 首先用 PoW 解决了 BFT
- Ethereum 转向 Proof-of-Stake，从 Sybil 保护的外源信号切换为系统中的内源信号
- 使用 BFT 多数来确定链的状态
  - 拜占庭故障可以通过协议观察，权益可以是 `slashed`
  - 分叉选择规则总结在 LMD-GHOST 中
  - 由于 Casper，它确保了活跃性
- 提供更多的加密经济安全性

## 额外阅读和练习

- [加斯帕纸](https://arxiv.org/pdf/2003.03052.pdf)
- [按位 LMD GHOST：高效的 CBC Casper 分叉选择规则](https://medium.com/@aditya.asgaonkar/bitwise-lmd-ghost-an-efficient-cbc-casper-fork-choice-rule-6db924e57d1f)
- [Eth2book，带注释的规范](https://eth2book.info/)
- [关于 PoS 你应该知道的事，作者 Domothy](https://web.archive.org/web/20240803111840/https://domothy.com/proof-of-stake/)
- [Dankrad Feist 的削减场景解释](https://dankradfeist.de/ethereum/2022/03/24/run-the-majority-client-at-your-own-peril.html)
- [Justin Drake 的 Beacon Chain 设计错误](https://www.youtube.com/watch?v=10Ym34y3Eoo)
- [Casper 与 Devcon 3 共识](https://www.youtube.com/watch?v=2r2k6awEJr8)
- [时隙剖析](https://www.youtube.com/watch?v=EswDO0kL_O0)

了解 EL 和 CL 后，运行客户端对。旋转一对单执行和共识客户端，阅读它们的日志以了解它们的操作方式。
