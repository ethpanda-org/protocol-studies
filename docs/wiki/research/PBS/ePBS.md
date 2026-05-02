# 供奉 Proposer-Builder Separation (ePBS)

## 路线图跟踪器

| 升级 | URGE | 轨道 | 主题 | 交叉引用 |
|:-------:|:-----------:|:---------:|:---------------------------------:|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
| ePBS | The Scourge | MEV 轨迹 | 终局之战区块生产流程 | 与以下交集： [ET](https://ethresear.ch/t/execution-tickets/17944)、[PEPC](https://efdn.notion.site/PEPC-FAQ-0787ba2f77e14efba771ff2d903d67e4)、[IL](https://eips.ethereum.org/EIPS/eip-7547) |

## TLDR；

封装的 Proposer-Builder Separation (ePBS) 是将 Proposer-Builder Separation (PBS) 机制直接集成到 Ethereum 的协议中。这正式将核心协议中的区块提议者和 区块构建者分开，旨在提高效率、安全性和去中心化。

## PBS 是什么


Proposer-Builder Separation (PBS) 是一种设计哲学[^1]，它将提出区块 (提议者) 和构建区块 (构建者) 的角色分开。这种分离有助于解决区块生产中的挑战和低效率问题，特别是在最大可提取价值 (MEV) 方面。本文假设读者对 [MEV](/wiki/research/PBS/mev.md)、[PBS](/wiki/research/PBS/pbs.md) 和 [MEV-Boost](/wiki/research/PBS/mev-boost.md) 有一定的了解。


## ePBS 概述

ePBS 将 PBS 直接集成到 Ethereum 的协议中，这与 [MEV-Boost](/wiki/research/PBS/mev-boost.md) 等当前外部解决方案不同。此集成旨在通过消除对外部中继的需求来简化流程并增强安全性。

### 主要差异

- **提议者**：验证者提出新的区块，重点是选择区块而不构建它。
- **构建者**：组装区块以优化交易订单并通过透明拍卖将区块提供给提议者的实体或算法[^2][^3]。
- **中继**：在传统的 MEV-Boost 中，中继调解提议者和 构建者之间的通信。在 ePBS 下，协议本身促进了这种交互，减少或重新定义了对外部中继的需求。

### 从 mev-boost 过渡到 ePBS

过渡到 ePBS 是一个重大转变，旨在消除对第三方软件的依赖。下一节将深入探讨为什么需要 ePBS 以及这种协议内方法的好处。


## ePBS 案例

从 MEV-Boost 过渡到 ePBS 解决了与 Ethereum 核心价值观一致的几个关键问题[^4]：

### 主要原因

- **去中心化**：减少对中心化中继的依赖，更广泛地分配区块建设责任。
- **安全性**：确保区块构建遵循与网络其余部分相同的安全规则，与当前的链下中继不同。
- **效率和公平**：为区块空间创建一个更加透明和更具竞争力的市场，减少与 MEV 提取相关的低效率和不平等现象。

### 当前系统面临的挑战


- **中心化风险**：对少数中继的依赖可能会引入中心化和单点故障。
- **安全漏洞**：在 Ethereum 共识规则之外运行的外部中继会带来安全风险。
- **MEV 相关操作**：当前系统可能无法充分防止 MEV 操作。
- **运营可持续性**：中继的长期生存能力尚不确定，引发了对其持续有效性和财务可持续性的担忧。


### 经济和安全考虑

- **MEV 分布**：ePBS 旨在创建一个更加透明和公平的 MEV 市场，改变当前的动态。
- **市场效率**：通过将 PBS 集成到协议中，ePBS 培育了更具竞争力的市场，有可能减少 gas 价格拍卖和网络拥塞。
- **中心化风险**：当前对一些中继的依赖引入了中心化风险，ePBS 试图减轻这种风险。
- **操纵行为**：外部中继可能会导致操纵行为，ePBS 旨在更好地监管这种行为。

### MEV 烧录

MEV 燃烧涉及将 MEV 利润的一部分重新用于燃烧 Ether，减少供应并可能增加其价值。该机制旨在协调验证者、构建者和社区的利益。

## 对 ePBS 的反驳

ePBS 的批评者提出了一些担忧，而支持者则给出了强有力的回应。讨论还探讨了寻址 MEV[^3] 的替代方法。

### 主要反驳

- **复杂性和技术风险**：ePBS 向协议引入了新元素，可能会增加复杂性和技术风险。
- **网络性能**：担心 ePBS 影响网络性能，特别是延迟和区块传播时间。
- **验证者的灵活性降低**：担心 ePBS 可能会限制验证者在 区块提案中的选择。
- **过早优化**：表明 ePBS 可能会转移对更紧迫问题的注意力。
- **可绕过性**：验证者和 构建者可能会继续依赖外部解决方案，绕过 ePBS 机制[^8]。

### 支持者的回应

- **降低复杂性**：支持者认为 ePBS 的好处超过了复杂性和风险。
- **网络效率**：支持者认为 ePBS 的设计可以保持网络效率。
- **增强决策能力**：ePBS 提供更加透明和更具竞争力的构建者市场，促进权力下放。
- **战略重要性**：ePBS 解决了有关 MEV 提取的基本问题，并与 Ethereum 的长期目标保持一致。

关于 ePBS 的争论包含了关于 Ethereum 未来方向的更广泛讨论，即谨慎平衡创新。虽然反驳强调风险和替代方案，但 ePBS 倡导者强调其与 Ethereum 核心原则的一致性以及确保公平、安全和高效的区块链生态系统的必要性[^5]。

## 设计 ePBS

### ePBS 机制的理想特性

ePBS 机制应确保去中心化、安全、高效、透明、减少信任要求以及公平的 MEV 分配[^6]。

**最小可行 ePBS (MVePBS) 的核心属性**

- **去中心化和公平访问：**确保没有任何一个实体控制区块的提议和建设，保持对每个人的公平。
- **安全性和完整性：** 通过防止审查和双重支出等攻击来维护 Ethereum 的安全。
- **效率和可扩展性：** 在不增加大量开销的情况下提高网络的效率和可扩展性。
- **透明度和可预测性：** 使交易订购和区块生产对所有用户都清晰易懂。
- **减少信任要求：** 通过在协议中嵌入 PBS 最大限度地减少各方之间的信任需求。
- **MEV 分配的公平性：** 确保 MEV 机会公平分配并防止垄断控制。
- **最小信任假设：** 通过使用密码学和博弈论原理来减少对信任的需求。
- **经济可行性：** 确保参与 ePBS 对提议者、构建者和 验证者带来经济利益。
- **灵活性和兼容性：** 设计 ePBS 以适应未来的创新并与现有基础设施配合使用。
- **降低用户的复杂性：**尽管协议复杂性增加，但仍保持系统用户友好且简单。
- **激励协调：** 协调所有网络参与者的利益，以确保长期稳定和健康。

**ePBS 的最小免信任系统**

最小的无信任 ePBS 系统应包括诸如诚实构建者发布安全性、构建者揭示安全性、强制诚实重组、构建者扣留安全性、无条件付款、提议者安全性和抗审查性[^6] 等属性。

- **诚实的构建者出版物安全**：确保及时区块发布构建者被包括在内，防止审查并认可他们的工作。
- **构建者显示安全**：当区块内容和出价显示时，保护构建者免受提议者模棱两可的影响。
- **强制诚实重组**：强制执行重组规则，以阻止审查或利用 MEV 的恶意者。
- **构建者扣留安全**：保证对构建者在指导方针内战略性扣留区块不会受到不公平的处罚。
- **无条件付款**：确保构建者获得其工作报酬，无论区块是否包含在内，从而最大限度地减少信任。
- **提议者安全**：保护提议者免受损失补偿，鼓励区块提案参与。
- **诚实的构建者支付安全**：保证构建者的支付满足区块生产中的协议期望。
- **无需许可**：允许任何人成为构建者或 提议者，无需中心授权，促进竞争和去中心化。
- **抗审查性**：防止区块和 交易审查，维护 Ethereum 的去中心化完整性。
- **路线图兼容性**：使 ePBS 与 Ethereum 的开发路线图保持一致，以便与未来升级顺利集成。

此外，提议者在最小的无信任 ePBS 系统中以协议外方式出售区块应该没有任何优势。

**讨论最佳机制**

围绕最佳 ePBS 机制的讨论需要仔细考虑设计选择，通常涉及权衡[^9][^3]。

- **拍卖机制：** 决定是否使用第二价格拍卖或更复杂的拍卖 (例如频繁的批量拍卖) 会影响公平性、效率和对操纵的敏感性。
- **构建者选择和质押：** 选择如何选择构建者 (通过质押、声誉系统或随机选择) 会影响去中心化和安全性，而质押可能会集中富人的机会。
- **包含和抗审查性：** 确保系统抵抗审查并公平地包含交易可能涉及匿名提交或强制交易列表，以平衡垃圾邮件或恶意交易。
- **兼容性和适应性：** ePBS 必须与当前的 Ethereum 基础设施配合使用，并且能够灵活应对未来的更新，例如分片或新的第 2 层解决方案。

寻找最小可行的 ePBS 需要平衡这些方面，以促进去中心化、确保安全性和效率、保持透明度和公平性以及最大限度地减少信任要求。持续的社区讨论和研究对于完善这些概念并就最佳 ePBS 机制达成共识至关重要。

## ePBS 解决方案

### 两个区块 HeadLock (TBHL) 提案

Two- 区块 HeadLock (TBHL) 设计代表了 Ethereum 协议中 Proposer-Builder Separation (PBS) 的创新方法，旨在解决由 MEV](https://ethresear.ch/t/why-enshrine-proposer-builder-separation-a-viable-path-to-epbs/)。该设计是先前提案的细微差别迭代，集成了 Vitalik Buterin 的两个时隙设计的元素，并通过头锁机制对其进行了增强，以保护构建者免受提议者模棱两可的影响。在这里，我们根据提供的详细解释深入研究了 TBHL 的关键组件及其操作机制[^4]。

[TBHL 提案](/docs/wiki/research/PBS/TBHL.md) 有更多关于设计和流程的细节。

### 载荷-ePBS 的时效委员会 (PTC)

Payload Timeliness Committee (PTC) 提案是将 PBS (ePBS) 纳入 Ethereum 协议的设计。它代表了确定区块有效性的机制的演变，并包括验证者的子集，他们对区块的 载荷[^7][^12] 的及时性进行投票。

[PTC 提案](/docs/wiki/research/PBS/PTC.md) 有更多关于设计和流程的细节。

### ePBS PTC 规范概述

[当前 ePBS 规范](https://hackmd.io/@potuz/rJ9GCnT1C) 和 [GitHub 仓库](https://github.com/potuz/consensus-specs/tree/epbs_stripped_out/specs/_features/epbs) 被分为单独的组件，以构建在 Ethereum 组件的现有规范之上[^15][^16][^23]。 
- `Beacon-chain.md`：本文档指定了 ePBS 分叉的 Beacon Chain 规范[^18]。
- `Validator.md`：本文档指定了 ePBS 分叉的诚实验证者行为规范[^19]。
- `Builder.md`：本文档指定了 ePBS 分叉的诚实构建者规范[^20]。
- `Engine.md`：本文档指定由于 ePBS fork[^21] 导致的 Engine APi 更改。
- `fork-choice.md`：本文档指定由于 ePBS 分叉 [^22] 对分叉选择的更改。

[ePBS 设计规范](/docs/wiki/research/PBS/ePBS-Specs.md) 提供了有关实现规范和流程的更多详细信息。

### 协议强制提议者承诺 (PEPC)

协议强制提议者承诺 (PEPC) 是 PBS 的概念扩展和泛化，为提议者 (验证者) 引入了一种更灵活、更安全的方式来致力于区块的构建。与现有的 MEV-Boost 机制不同，该机制依赖于提议者和 构建者/中继之间的协议外协议，PEPC 旨在将这些承诺纳入 Ethereum 协议本身，为这些交互提供无需信任且无需许可的基础设施[^10][^11]。

[PEPC 提案](/docs/wiki/research/PBS/PEPC.md) 有更多关于设计和流程的细节。

## ePBS 中的开放问题

Mike Neuder 强调了有关将 PBS (ePBS) 写入 Ethereum 协议的几个有趣且具有挑战性的问题[^5]。

### 可绕过性意味着什么？

可绕过性是指验证者和 构建者可能继续使用外部中继或解决方案而不是所规定的协议。如果许多参与者选择退出，这会引起人们对 ePBS 有效性的担忧，以及创建一个在不限制验证者的自主权或 Ethereum 的去中心化性质的情况下无法绕过的系统的挑战。

### 祭祀的目的是什么？

内置化 PBS 的目的是在 Ethereum 协议中创建一个中立、无需信任的中继，以保护提议者-构建者关系。这项工作旨在降低 MEV-Boost 等外部解决方案的中心化风险，改进抗审查性，并更系统地解决 MEV 相关问题。即使具有可绕过性，内置化也可以提供可靠的后备，鼓励直接协议参与，并支持 MEV 重新分配机制 (例如 MEV-burn) 等长期目标。

### 不供奉的具体含义是什么？

不封装 PBS 会导致对区块构建和 MEV 分发到外部系统的重大控制，从而可能增加集中化和安全漏洞。这种选择可能需要优先支持中立的中继作为关键基础设施，以维护公平并防止 MEV 市场的垄断行为。

### ePBS 的真正需求是什么？

Ethereum 中对 ePBS 的需求是由解决当前限制以及为未来的可扩展性、安全性和去中心化需求做好准备的因素驱动的。这种需求源于区块生产格局的不断变化以及 MEV 机会的复杂性。量化这一需求具有挑战性，但反映了 Ethereum 社区解决当前问题和预测未来需求的更广泛需求。

### 我们在多大程度上可以依赖利他主义和社会阶层？

社交层由 Ethereum 的社区规范和价值观组成，在行为中发挥着至关重要的作用，而仅靠协议机制无法强制执行。虽然利他主义或长期的自身利益可能会激励一些 ETH 的大持有者支持所规定的解决方案，但仅仅依靠这些动机是不可靠的。 Ethereum 的诚信和权力下放应该得到强有力的、可执行的机制的支持，而不是自愿遵守理想。

### L1 ePBS 对于 L2 和 OFA 的未来有多重要？

随着 Ethereum 路线图的进展，L2 解决方案的活动增多以及 OFA 的开发，L1 MEV 的直接影响可能会减少。然而，ePBS 建立的原则和基础设施对于跨层的安全和去中心化 MEV 管理仍然至关重要，从而保持 ePBS 在多层生态系统中的相关性。

### 相对于其他协议升级，ePBS 应该具有什么优先级？

鉴于 Ethereum 的 MEV 动态的复杂情况和潜在变化，ePBS 相对于其他升级 (例如抗审查性、Single Slot Finality) 的优先级需要采取战略方法。社区可能会专注于能够立即带来好处和降低风险的升级，同时继续开发可在需要时快速部署的 ePBS 框架。

## 资源
- [Proposer-Builder Separation (PBS) 的注释](https://barnabe.substack.com/p/pbs)
- [Mike Neuder - 走向神圣的 Proposer-Builder Separation](https://www.youtube.com/watch?v=Ub8V7lILb_Q)
- [PBS 不完整指南 - 与 Mike Neuder 和 Chris Hager 合作](https://www.youtube.com/watch?v=mEbK9AX7X7o)
- [为何将 Proposer-Builder Separation 纳入协议？通往 ePBS 的可行路径](https://ethresear.ch/t/why-enshrine-proposer-builder-separation-a-viable-path-to-epbs/15710/1)
- [ePBS – 无限自助餐](https://notes.ethereum.org/@mikeneuder/infinite-buffet)
- [ePBS 设计约束](https://hackmd.io/ZNPG7xPFRnmMOf0j95Hl3w)
- [Payload Timeliness Committee (PTC) – ePBS 设计](https://ethresear.ch/t/payload-timeliness-committee-ptc-an-epbs-design/16054)
- [考虑 ePBS](https://notes.ethereum.org/@mikeneuder/consider-the-epbs)
- [ePBS 分组讨论室](https://www.youtube.com/watch?v=63juNVzd1P4)
- [解绑 PBS：转向协议强制提议者承诺 (PEPC)](https://ethresear.ch/t/unbundling-pbs-towards-protocol-enforced-proposer-commitments-pepc/13879/1)
- [PEPC FAQ](https://efdn.notion.site/PEPC-FAQ-0787ba2f77e14efba771ff2d903d67e4)
- [没有最大 EB 和 7002 的最小 ePBS](https://github.com/potuz/consensus-specs/pull/2)
- [EigenLayer 协议](https://docs.eigenlayer.xyz/eigenlayer/overview/whitepaper)
- [ePBS 规范说明](https://hackmd.io/@potuz/rJ9GCnT1C)
- [ePBS 设计规范 PR](https://github.com/potuz/consensus-specs/pull/2)
- [ePBS 设计规范 GitHub 仓库](https://github.com/potuz/consensus-specs/tree/epbs_stripped_out/specs/_features/epbs)
- [epbs - Beacon Chain 规范](https://github.com/potuz/consensus-specs/blob/epbs_stripped_out/specs/_features/epbs/beacon-chain.md)
- [epbs - 诚实的验证者规范](https://github.com/potuz/consensus-specs/blob/epbs_stripped_out/specs/_features/epbs/validator.md)
- [epbs - 诚实的构建者规范](https://github.com/potuz/consensus-specs/blob/epbs_stripped_out/specs/_features/epbs/builder.md)
- [epbs - Engine API 规范](https://github.com/potuz/consensus-specs/blob/epbs_stripped_out/specs/_features/epbs/engine.md)
- [epbs - 分叉选择规范](https://github.com/potuz/consensus-specs/blob/epbs_stripped_out/specs/_features/epbs/fork-choice.md)
- [EIP-7547 Inclusion Lists](https://eips.ethereum.org/EIPS/eip-7547)
- [更多关于提议者和 构建者的图片 (图)](https://mirror.xyz/barnabe.eth/QJ6W0mmyOwjec-2zuH6lZb0iEI2aYFB9gE-LHWIMzjQ)

## 参考文献
[^1]: https://barnabe.substack.com/p/pbs
[^2]: https://www.youtube.com/watch?v=Ub8V7lILb_Q
[^3]: https://www.youtube.com/watch?v=mEbK9AX7X7o
[^4]: https://ethresear.ch/t/why-enshrine-proposer-builder-separation-a-viable-path-to-epbs/15710/1
[^5]: https://notes.ethereum.org/@mikeneuder/infinite-buffet
[^6]: https://hackmd.io/ZNPG7xPFRnmMOf0j95Hl3w
[^7]: https://ethresear.ch/t/payload-timeliness-committee-ptc-an-epbs-design/16054
[^8]: https://notes.ethereum.org/@mikeneuder/consider-the-epbs
[^9]: https://www.youtube.com/watch?v=63juNVzd1P4
[^10]: https://ethresear.ch/t/unbundling-pbs-towards-protocol-enforced-proposer-commitments-pepc/13879/1
[^11]: https://efdn.notion.site/PEPC-FAQ-0787ba2f77e14efba771ff2d903d67e4
[^12]: https://hackmd.io/@potuz/rJ9GCnT1C
[^13]: https://github.com/potuz/consensus-specs/pull/2
[^14]: https://docs.eigenlayer.xyz/eigenlayer/overview/whitepaper
[^15]: https://hackmd.io/@potuz/rJ9GCnT1C
[^16]: https://github.com/potuz/consensus-specs/pull/2
[^17]: https://eips.ethereum.org/EIPS/eip-7547
[^18]: https://github.com/potuz/consensus-specs/blob/epbs_stripped_out/specs/_features/epbs/beacon-chain.md
[^19]: https://github.com/potuz/consensus-specs/blob/epbs_stripped_out/specs/_features/epbs/validator.md
[^20]: https://github.com/potuz/consensus-specs/blob/epbs_stripped_out/specs/_features/epbs/builder.md
[^21]: https://github.com/potuz/consensus-specs/blob/epbs_stripped_out/specs/_features/epbs/engine.md
[^22]: https://github.com/potuz/consensus-specs/blob/epbs_stripped_out/specs/_features/epbs/fork-choice.md 
[^23]: https://github.com/potuz/consensus-specs/tree/epbs_stripped_out/specs/_features/epbs
