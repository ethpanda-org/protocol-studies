# 第 19 讲 | 学习小组 CL 网络、libp2p

学习小组的介绍部分现已结束，我们现在开始通过核心开发人员的现场会议进行更深入的探讨。 学习小组主阶段的第二周致力于网络。

本周的第二场现场会议重点关注基于 libp2p 的共识层网络。本次讲座由[DappLion](https://github.com/dapplion)主讲，他是一位经验丰富的共识层贡献者，SigP Lighthouse团队成员，曾是Lodestar 客户端的负责人，目前主要关注PeerDAS。

在 [Youtube](https://www.youtube.com/watch?v=kYJ7Rj0OGv4) 上观看讲座并遵循演讲中所示的 [libp2p 规范](https://github.com/libp2p/specs)

<iframe width="560" height="315" src="https://www.youtube.com/embed/kYJ7Rj0OGv4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预读

在开始第 19 天的内容之前，先熟悉一下前几周的资源，尤其是第 2 天的 CL 和第 16 天的共识机制。您应该对信标链、其网络特性以及以太坊共识层中的不同机制有一个大致的了解。

此外，您可以通过学习以下资源来做好准备。

- [libp2p 简介 - David Dias](https://www.youtube.com/watch?v=CRe_oDtfRLw)
- libp2p [文档](https://docs.libp2p.io/) 和 [规范](https://github.com/libp2p/specs)

## 概要

- CL 网络协议
- 库文件2p
- <name>Yamux</name>
- 噪音
- 八卦字幕

## 额外阅读和练习

- [揭秘 libp2p Gossipsub：可扩展且可扩展的 p2p Gossip 协议，作者：<name>Raúl Kripalani</name>](https://www.youtube.com/watch?v=BUc4xta7Mfk)
- [以太坊中的 libp2p 从区块扩散到 PeerDAS 和 FullDAS - <name>Csaba Kiraly</name>](https://www.youtube.com/watch?v=sI_Qr1vHUk4)
