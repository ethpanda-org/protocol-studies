# 单槽最终性（SSF）

## 路线图跟踪器

| 升级 |   驱动力    | 轨道 |          主题           |                                               相关参考文献                                               |
| :--: | :---------: | :--: | :--------------------: | :--------------------------------------------------------------------------------------------------: |
|  SSF  |  the Merge |   -  | PoS 升级、最终性与安全性 | 与以下交集：[MAX_EB](/docs/wiki/research/cl-upgrades.md), [ePBS](/docs/wiki/research/PBS/ePBS.md) |

[单槽最终性](docs/eps/week10-research.md) 是对 Beacon Chain 共识机制的改进研究概念，旨在解决与最终化区块所需时间相关的低效问题。该提案大幅提升区块验证效率，并显著减少最终性所需时间。
与其等待 2 个周期，区块可以在同一槽中提出并最终化。该主题也在 [EPS 第10周](https://github.com/eth-protocol-fellows/protocol-studies/blob/ssf/docs/eps/week10-research.md)中进行了讨论。

## 背景与动机
以太坊的共识层实现了 Gasper 协议，其中包括 Casper 友好的最终性保障机制（FFG）。Casper FFG 确保网络持续产生区块，并为每个周期积累验证者的关注度。最终性是 PoS 经济安全的终极体现，其变化需要 2/3 的验证者集群遭到惩罚。
Beacon Chain 每 2 个周期或每 64 个槽达成最终性。由于每个槽为 12 秒长，最终化大约需要 12.8 分钟，本文写作时的时长。
目前，最终性所需的时间对于大多数用户来说过长，对于那些不希望等待这么久以确保交易已永久生效的应用和交易所来说也非常不方便。  
区块提议和最终化之间的延迟还为攻击者提供了短暂的重组机会，攻击者可以利用这些机会审查某些区块或提取 MEV。

## SSF 的好处
* 对应用更方便 - 交易最终化时间提高了一个数量级，即 12 秒而非 12 分钟，这意味着为所有以太坊用户提供更好的用户体验
* 更难被攻击 - 多区块 MEV 重组可以被消除，因为链只需要 1 个区块来最终化，而不是 64 个区块
* 未来的共识机制（在 SSF 场景中）相比当前的 LMD-GHOST 和 Casper-FFG 组合具有更低的复杂性，从而减少了可能的攻击（平衡攻击、隐藏和节省攻击）

## SSF 中的分叉选择规则
当前的共识机制依赖于 Casper-FFG（决定是否 2/3 的验证者已经对某条链进行了证明）和分叉选择规则（LMD-GHOST 是一个决定当存在多个选项时哪条链正确的算法）的耦合[^1]。  
分叉选择算法仅考虑自上次最终化以来的区块。在 SSF 中，由于最终性发生在区块提出的同一槽中，因此分叉选择规则将没有任何区块可供考虑。这意味着在 SSF 中，**要么**分叉选择算法 **要么**最终性保障机制将在任何时刻处于活动状态。  
最终性保障机制将在 2/3 验证者在线并诚实证明的情况下最终化区块。如果某个区块无法超过 2/3 的门槛，分叉选择规则将启动以确定应该跟随哪条链。这也为维护不活跃泄漏机制提供了机会，该机制可以恢复 1/3 以上的验证者下线的链。如果某个区块无法超过 2/3 的门槛，分叉选择规则将启动以确定应该跟随哪条链。

分叉选择与共识之间的一些交互问题仍然存在，这些问题仍然需要解决。  
对现有分叉选择（例如视图合并）的短期改进也可能会影响 SSF 分叉选择的工作[^2]。

## 我们需要解决的关键问题，才能实现单槽最终性
Vitalik 提出的三大开放问题[^4]：

* 精确的共识算法是什么？
* 聚合策略是什么（针对签名）？
* 验证者经济学设计是什么？

## MAX_EB 和 Zipfian ETH 分布

## 参考文献

[^1]: 结合 GHOST 和 Casper [https://arxiv.org/pdf/2003.03052.pdf](https://arxiv.org/pdf/2003.03052.pdf), [[archived]](https://arxiv.org/pdf/2003.03052.pdf)

[^2]: SSF 页面在 Ethereum.org 上 [https://ethereum.org/en/roadmap/single-slot-finality/#role-of-the-fork-choice-rule](https://ethereum.org/en/roadmap/single-slot-finality/#role-of-the-fork-choice-rule), [[archived]](https://web.archive.org/web/20240309234119/https://ethereum.org/en/roadmap/single-slot-finality/#role-of-the-fork-choice-rule)

[^3]: EIP-7251: 增加 MAX_EFFECTIVE_BALANCE [https://eips.ethereum.org/EIPS/eip-7251](https://eips.ethereum.org/EIPS/eip-7251), [[archived]](https://web.archive.org/web/20240324072459/https://eips.ethereum.org/EIPS/eip-7251)

[^4]: Vitalik 的 SSF 笔记 [https://notes.ethereum.org/@vbuterin/single_slot_finality](https://notes.ethereum.org/@vbuterin/single_slot_finality), [[archived]](https://web.archive.org/web/20240330010706/https://notes.ethereum.org/@vbuterin/single_slot_finality)

[^5]: 在 SSF 后坚持每槽 8192 个签名 [https://ethresear.ch/t/sticking-to-8192-signatures-per-slot-post-ssf-how-and-why/17989](https://ethresear.ch/t/sticking-to-8192-signatures-per-slot-post-ssf-how-and-why/17989). [[archived]](https://web.archive.org/web/20240105131126/https://ethresear.ch/t/sticking-to-8192-signatures-per-slot-post-ssf-how-and-why/17989)

[^6]: 简单的单槽最终性协议 [https://ethresear.ch/t/a-simple-single-slot-finality-protocol/14920](https://ethresear.ch/t/a-simple-single-slot-finality-protocol/14920), [[archived]](https://web.archive.org/web/20231214080806/https://ethresear.ch/t/a-simple-single-slot-finality-pr
