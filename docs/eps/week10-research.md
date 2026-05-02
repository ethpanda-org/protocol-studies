# 第 15 讲 | 学习小组 Ethereum 分叉选择

最后一次关于原始学习小组的讨论涵盖了与 Ethereum 的分叉选择、其演变及其在未来升级中的作用相关的各种主题。

观看 [Francesco](https://twitter.com/fradamt) 在 [StreamEth](https://streameth.org/65cf97e702e803dbd57d823f/epf_study_group) 或 [Youtube](https://www.youtube.com/watch?v=x-_2gAVFlw8) 上的演示。演示文稿 [可在此处获取](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/week10-research.pdf)。

<iframe width="560" height="315" src="https://www.youtube.com/embed/x-_2gAVFlw8?si=xqMDpqrBabgiDYPb" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预读

在开始第 15 天的内容之前，请先熟悉前几周的资源，尤其是 CL 上的第 2 天和路线图上的第 5 天。您应该了解 Beacon Chain 和当前共识研究主题。

此外，您可以通过学习以下资源来做好准备。

- 多合一资源：[PoS 进化](https://github.com/ethereum/pos-evolution/blob/master/pos-evolution.md)
- Gasper：[结合 GHOST 和 Casper (Gasper 论文)](https://arxiv.org/abs/2003.03052)
- 加斯帕的问题：
  - [PoS Ethereum 的三种攻击](https://eprint.iacr.org/2021/1413)
  - [查看合并](https://ethresear.ch/t/view-merge-as-a-replacement-for-proposer-boost/13739)
- 使用 Single Slot Finality 改进 Gasper：
  - [通往单一时隙最终确定性的路径](https://notes.ethereum.org/@vbuterin/single_slot_finality)
  - [简单的 Single Slot Finality](https://ethresear.ch/t/a-simple-single-slot-finality-protocol/14920)

## 概要

- 加斯帕回顾
- Gasper 的问题和修复
- Single Slot Finality (SSF)
- 分叉选择如何影响其他 Ethereum 升级：PeerDAS 和 ePBS 案例研究

## 额外阅读和练习

- [关于 SSF 的注释，林肯·穆尔](https://publish.obsidian.md/single-slot-finality/Welcome+to+My+Research!)
- [增加 MAX_EFFECTIVE_BALANCE – 一个温和的建议](https://ethresear.ch/t/increase-the-max-effective-balance-a-modest-proposal/15801)
- [SSF LMD-GHOST 后的重组弹性和安全性](https://ethresear.ch/t/reorg-resilience-and-security-in-post-ssf-lmd-ghost/14164/3)
