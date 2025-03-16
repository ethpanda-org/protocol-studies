# 学习小组 第 15 讲 | 以太坊的分叉选择（Fork-Choice）

本次研究讲座是 **原始学习小组的最后一场讲座**，涵盖了 **以太坊分叉选择（Fork-Choice）** 相关的多个主题，包括其演进过程及在未来升级中的作用。

📺 **观看演讲**
主讲人：[Francesco](https://twitter.com/fradamt)  
- [StreamEth](https://streameth.org/65cf97e702e803dbd57d823f/epf_study_group)  
- [YouTube](https://www.youtube.com/watch?v=x-_2gAVFlw8)  
- [演示文稿](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/week10-research.pdf)  

<iframe width="560" height="315" src="https://www.youtube.com/embed/x-_2gAVFlw8?si=xqMDpqrBabgiDYPb" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预阅读

在开始 **第 15 天** 的学习之前，请先熟悉前几周的内容，特别是：
- **第 2 天**：共识层（CL）
- **第 5 天**：以太坊路线图

你需要具备 **信标链（Beacon Chain）** 及 **当前共识研究** 的基本理解。

📚 **推荐阅读**
- [PoS 演进：以太坊权益证明的历史与未来](https://github.com/ethereum/pos-evolution/blob/master/pos-evolution.md)
- Gasper 论文：[结合 GHOST 与 Casper 的 Gasper 协议](https://arxiv.org/abs/2003.03052)
- **Gasper 的问题**
  - [PoS 以太坊的三种攻击](https://eprint.iacr.org/2021/1413)
  - [View-merge：替代提议者加速机制](https://ethresear.ch/t/view-merge-as-a-replacement-for-proposer-boost/13739)
- **改进 Gasper 的单时隙最终性（Single Slot Finality, SSF）**
  - [通往单时隙最终性的路径](https://notes.ethereum.org/@vbuterin/single_slot_finality)
  - [一种简单的单时隙最终性协议](https://ethresear.ch/t/a-simple-single-slot-finality-protocol/14920)

## 讲座大纲

- **Gasper 协议回顾**
- **Gasper 的问题与改进方案**
- **单时隙最终性（SSF）**
- **分叉选择如何影响以太坊的其他升级**
  - 以 **PeerDAS** 和 **ePBS** 为案例研究

## 额外阅读与练习

📖 **推荐阅读**
- [单时隙最终性研究笔记（Lincoln Murr）](https://publish.obsidian.md/single-slot-finality/Welcome+to+My+Research!)
- [提升 MAX_EFFECTIVE_BALANCE：一个温和的提案](https://ethresear.ch/t/increase-the-max-effective-balance-a-modest-proposal/15801)
- [SSF 之后的 LMD-GHOST 重组弹性与安全性](https://ethresear.ch/t/reorg-resilience-and-security-in-post-ssf-lmd-ghost/14164/3)
