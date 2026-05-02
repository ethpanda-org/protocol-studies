# 第 16 讲 | 学习小组共识和加斯帕

学习小组的介绍部分现已结束，我们现在开始通过核心开发人员的现场会议进行更深入的探讨。 

在本周的第一场会议中，我们将与 [Ben Edgington](https://x.com/benjaminion_xyz) 深入探讨以太坊的共识机制。 Ben 是一位经验丰富的核心贡献者，曾参与 Teku 共识客户端、Optimism 的工作，并且是带注释的规范 Eth2 Book 的作者。 

在 Youtube 上观看 [Ben Edgington](https://x.com/benjaminion_xyz) 的演示录音。幻灯片[可在此处获取](https://docs.google.com/presentation/d/1mSn8JUfY88HvcCauLBkKRuy3f6YFlV9VcJptav0Ef24/edit#slide=id.p)。 

<iframe width="560" height="315" src="https://www.youtube.com/embed/cOivWPEBEMo" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预读

在开始第 16 天的内容之前，请先熟悉前几周的资源，尤其是第 2 天的 CL 和第 6 天的规范(至少是 CL 部分)。您应该对信标链和 以太坊共识层中的不同机制有大致的了解。 

此外，您可以通过学习以下资源来做好准备。

- Vitalik 的 [信标链分叉选择](https://github.com/ethereum/annotated-spec/blob/master/phase0/fork-choice.md) - 过时了，但仍然很优秀
- 《升级以太坊》书中的[共识章节](https://eth2book.info/latest/part2/consensus/)
- [分叉选择上的注释规范](https://eth2book.info/latest/part3/forkchoice/phase0/) 

## 概要

- 分叉选择简介
- LMD GHOST
- 卡斯帕 FFG
- 加斯帕
- 问答

## 额外阅读和练习

- Casper FFG 原始论文：[Casper theFriendly 最终确定性 Gadget](https://arxiv.org/abs/1710.09437)
- Gasper：[结合 GHOST 和 Casper(Gasper 论文)](https://arxiv.org/abs/2003.03052)
- [蓝眼睛谜题](https://xkcd.com/blue_eyes.html) - 认真思考！不要屈服并过早寻找解决方案。