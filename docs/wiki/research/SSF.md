# Single Slot Finality (SSF)

## 路线图跟踪器

| 升级 | URGE | 轨道 | 主题 | 交叉引用 |
| :-----: | :-------: | :---: | :----------------------: | :----------------------------------------------------------------------------------------------------------: |
| SSF |合并| - | PoS 升级，最终确定性和安全 |与: [MAX_EB](/docs/wiki/research/cl-upgrades.md), [ePBS](/docs/wiki/research/PBS/ePBS.md) | 交集

[Single 时隙最终确定性](/docs/eps/week10-research.md) 是一个改进 Beacon Chain 共识机制的研究概念，解决与最终确定区块所需时间相关的低效率问题。它建议显着提高区块验证效率并大幅缩短最终确定性的时间。
区块可以在同一个时隙中提出并最终确定，而不是等待 2 epoch。 [EPS 第 10 周](https://github.com/eth-protocol-fellows/protocol-studies/blob/ssf/docs/eps/week10-research.md) 中也涵盖了该主题。

## 背景和动机
Ethereum Consensus Layer 实现 Gasper 协议，其中包括 Casper 友好最终确定性 Gadget。 Casper FFG 确保网络不断生成区块并为每个 epoch 积累验证者注意力。 最终确定性是 PoS 经济安全的最终状态，其改变需要削减 2/3 的验证者集。 
Beacon Chain 每 2 个 epoch 或每 64 个时隙达到最终确定性。由于每个时隙长 12 秒，在撰写本文时，最终完成大约需要 12.8 分钟。
对于大多数用户来说，最终确定性的当前时间太长，并且对于可能不想等待那么长时间来确定其交易是永久的应用程序和交易所来说不方便。 
区块的提案和最终确定之间的延迟也为短期重组创造了机会，攻击者可以利用它来审查某些区块或提取 MEV。

## SSF 的好处
* 对应用程序来说更方便 - 交易最终确定时间提高了一个数量级，即 12 秒而不是 12 分钟，这意味着对所有 Ethereum 用户来说更好的 UX
* 攻击难度更大 - 可以消除多个区块 MEV 重组，因为链只需 1 个区块即可完成，而不是 64 个区块
* 与当前的 LMD-GHOST 和 Casper-FFG 组合相比，未来的共识机制 (在 SSF 场景中) 的复杂性将降低，这可能导致攻击 (平衡攻击、扣留和储蓄攻击)

## SSF 中的分叉选择规则
今天的共识机制依赖于 Casper-FFG (确定验证者的 2/3 是否已证明某个链的算法) 和分叉选择规则 (LMD-GHOST 是在有多个选项时决定哪条链是正确的算法) 之间的耦合[^1]。 
分叉选择算法仅考虑自上次最终确定的区块以来的区块。在 SSF 下，将不会有任何区块供 分叉选择规则考虑，因为最终确定性出现在与提议的区块相同的时隙中。这意味着在 SSF 下，**分叉选择算法**或** 最终确定性小工具将随时处于活动状态。 
最终确定性小工具将最终确定区块，其中验证者的 $2/3$ 在线并诚实地证明。如果区块无法超过 $2/3$ 阈值，则分叉选择规则将启动以确定要遵循哪个链。这也为维护不活动泄漏机制创造了机会，该机制可恢复 $>1/3$ 验证者离线的链。如果区块无法超过 $2/3$ 阈值，则分叉选择规则将启动以确定要遵循哪个链。

分叉选择和共识之间的一些交互问题确实存在于任何此类设计中，解决这些问题仍然很重要。对现有分叉选择的短期改进 (例如视图合并) 也可能会融入到 SSF 分叉选择的工作中。[^2]

## 实现 Single Slot Finality 需要解决哪些关键问题？
Vitalik[^4] 概述了三个开放性问题： 

* 确切的共识算法是什么？

* 聚合策略是什么 (对于签名)？

* 验证者经济学的设计是什么？

## MAX_EB 和 Zipfian ETH 分布

## 参考文献

[^1]: 结合 GHOST 和 Casper https://arxiv.org/pdf/2003.03052.pdf, [[已存档]](https://arxiv.org/pdf/2003.03052.pdf)

[^2]: SSF Ethereum.org 上的页面 https://ethereum.org/en/roadmap/single-slot-finality/#role-of-the-fork-choice-rule, [[已存档]](https://web.archive.org/web/20240309234119/https://ethereum.org/en/roadmap/single-slot-finality/#role-of-the-fork-choice-rule)

[^3]: EIP-7251：增加 MAX_EFFECTIVE_BALANCE https://eips.ethereum.org/EIPS/eip-7251, [[已存档]](https://web.archive.org/web/20240324072459/https://eips.ethereum.org/EIPS/eip-7251)

[^4]: VB 的 SSF 笔记 https://notes.ethereum.org/@vbuterin/single_slot_finality, [[已存档]](https://web.archive.org/web/20240330010706/https://notes.ethereum.org/@vbuterin/single_slot_finality)

[^5]: 坚持每个时隙后的 8192 签名 SSF https://ethresear.ch/t/sticking-to-8192-signatures-per-slot-post-ssf-how-and-why/17989. [[已存档]](https://web.archive.org/web/20240105131126/https://ethresear.ch/t/sticking-to-8192-signatures-per-slot-post-ssf-how-and-why/17989)

[^6]: 一个简单的单一时隙最终确定性协议 https://ethresear.ch/t/a-simple-single-slot-finality-protocol/14920, [[已存档]](https://web.archive.org/web/20231214080806/https://ethresear.ch/t/a-simple-single-slot-finality-protocol/14920)

[^7]: 关于 SSF 的注释林肯·穆尔 https://publish.obsidian.md/single-slot-finality/Welcome+to+My+Research!,
[[已存档]](https://web.archive.org/save/https://publish.obsidian.md/single-slot-finality/Welcome+to+My+Research!)
