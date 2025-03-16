# 固定提议者-构建者分离（ePBS）

## 路线图跟踪器

| 升级   |    迫切需求    |   跟踪   |               话题               |                                                                                          交叉引用                                                                                          |
|:-------:|:-----------:|:---------:|:---------------------------------:|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
|  ePBS   | The Scourge | MEV 路径 | 终极区块生产管道 | 交叉点：[ET](https://ethresear.ch/t/execution-tickets/17944)、[PEPC](https://efdn.notion.site/PEPC-FAQ-0787ba2f77e14efba771ff2d903d67e4)、[IL](https://eips.ethereum.org/EIPS/eip-7547) |

## 摘要

固定提议者-构建者分离（ePBS）旨在将提议者-构建者分离（PBS）机制直接整合到以太坊协议中。这一做法正式化了区块提议者和区块构建者在核心协议中的分离，旨在提高效率、安全性和去中心化。

## 什么是 PBS

提议者-构建者分离（PBS）是一种设计理念[^1]，它将区块提议（提议者）和区块构建（构建者）角色分开。这一分离有助于解决区块生产中的挑战和低效问题，尤其是在最大可提取价值（MEV）方面。本文假设读者已经了解 [MEV](/wiki/research/PBS/mev.md)、[PBS](/wiki/research/PBS/pbs.md) 和 [MEV-Boost](/wiki/research/PBS/mev-boost.md)。

## ePBS 概述

ePBS将 PBS 直接整合到以太坊协议中，而不像当前的外部解决方案 [MEV-Boost](/wiki/research/PBS/mev-boost.md)。这一整合旨在简化过程并通过消除对外部中继的需求来提高安全性。

### 主要区别

- **提议者**：提议新区块的验证者，主要负责选择区块，而非构建它。
- **构建者**：实体或算法负责组装区块，以优化交易顺序并通过透明拍卖向提议者提供区块[^2][^3]。
- **中继**：在传统的 MEV-Boost 中，中继在提议者和构建者之间进行通信调解。而在 ePBS 中，协议本身促进这种互动，从而减少或重新定义外部中继的需求。

### 从 mev-boost 到 ePBS 的过渡

从 MEV-Boost 过渡到 ePBS 代表着一次重大的转变，旨在消除对第三方软件的依赖。下一部分将深入探讨为什么需要 ePBS 以及这一协议内方法的好处。

## ePBS 的理由

从 MEV-Boost 过渡到 ePBS 解决了与以太坊核心价值观相关的几个关键问题[^4]：

### 主要原因

- **去中心化**：减少对集中化中继的依赖，更广泛地分配区块构建责任。
- **安全性**：确保区块构建遵循与网络其他部分相同的安全规则，而不像当前的离链中继。
- **效率和公平性**：创建一个更透明和竞争的区块空间市场，减少与 MEV 提取相关的低效和不公。

### 当前系统的挑战

- **中心化风险**：对少数中继的依赖可能带来中心化和单点故障。
- **安全漏洞**：外部中继在以太坊共识规则之外运行，存在安全风险。
- **MEV 相关操作**：当前系统可能无法充分保护免受 MEV 操控。
- **运营可持续性**：中继的长期可行性不确定，可能影响其持续有效性和财务可持续性。

### 经济与安全考量

- **MEV 分配**：ePBS 旨在创造一个更加透明和公平的 MEV 市场，改变当前的动态。
- **市场效率**：通过将 PBS 集成到协议中，ePBS 促进了一个更具竞争力的市场，可能减少煤气拍卖和网络拥堵。
- **中心化风险**：当前对少数中继的依赖带来了中心化风险，ePBS 旨在缓解这一风险。
- **操控性做法**：外部中继可能导致操控性做法，而 ePBS 旨在更好地进行调节。

### MEV 燃烧

MEV 燃烧涉及将部分 MEV 利润用于销毁以太坊，减少供应并可能提高其价值。该机制旨在使验证者、构建者和社区的利益保持一致。

## 对 ePBS 的反对意见

ePBS 的批评者提出了几个问题，而支持者则提出了有力的回应。讨论还探讨了应对 MEV 的替代方法[^3]。

### 主要反对意见

- **复杂性和技术风险**：ePBS 引入了新的协议元素，可能增加复杂性和技术风险。
- **网络性能**：担心 ePBS 影响网络性能，特别是延迟和区块传播时间。
- **减少验证者灵活性**：担心 ePBS 可能限制验证者在区块提议中的选择。
- **过早优化**：有人认为 ePBS 可能将注意力从更紧迫的问题上转移。
- **可绕过性**：验证者和构建者可能继续依赖外部解决方案，从而绕过 ePBS 机制[^8]。

### 支持者的回应

- **缓解复杂性**：支持者认为，ePBS 的好处超过了复杂性和风险。
- **网络效率**：支持者相信，ePBS 可以设计为保持网络效率。
- **增强决策**：ePBS 提供了更透明和竞争的构建者市场，促进去中心化。
- **战略重要性**：ePBS 解决了有关 MEV 提取的根本问题，并与以太坊的长期目标保持一致。

围绕 ePBS 的辩论反映了以太坊未来发展方向的广泛讨论，在创新与谨慎之间寻找平衡。虽然反对意见强调了风险和替代方案，ePBS 的支持者强调它与以太坊核心原则的契合，并强调它对确保公平、安全和高效的区块链生态系统的重要性[^5]。

## 设计 ePBS

### ePBS 机制的理想属性

ePBS 机制应确保去中心化、安全性、效率、透明性、减少信任要求和公平的 MEV 分配[^6]。

**最低可行 ePBS（MVePBS）的核心属性**

- **去中心化和公平访问**：确保没有单一实体控制区块提议和构建，保持公平性。
- **安全性和完整性**：通过防止审查和双重支付等攻击，保持以太坊的安全。
- **效率和可扩展性**：提高网络效率和可扩展性，而不增加显著开销。
- **透明性和可预测性**：使交易排序和区块生产对所有用户清晰可见。
- **减少信任要求**：通过将 PBS 嵌入协议，最大程度减少各方之间的信任需求。
- **MEV 分配的公平性**：确保 MEV 机会公平分配，防止垄断控制。
- **最小信任假设**：通过使用密码学和博弈理论原则，减少信任需求。
- **经济可行性**：确保参与 ePBS 对提议者、构建者和验证者是经济上有益的。
- **灵活性和兼容性**：设计 ePBS 以适应未来创新，并与现有基础设施兼容。
- **减少用户复杂性**：尽管协议复杂度有所增加，但保持系统对用户友好和简洁。
- **激励对齐**：确保所有网络参与者的利益对齐，以确保长期稳定和健康。

**最小信任系统**

一个最小信任的 ePBS 系统应包括以下属性：诚实构建者发布安全性、构建者揭示安全性、强制诚实重组、构建者 withholding 安全性、无条件支付、提议者安全性、诚实构建者支付安全性以及抗审查性[^6]。


# **诚实构建者的安全性**

- **诚实构建者发布安全性**：确保按时发布区块的构建者被包含，防止审查，并认可他们的工作。
- **构建者揭示安全性**：保护构建者在揭示区块内容和竞价时不受提议者双重提议（equivocation）攻击。
- **强制诚实重组**：强制执行重组规则，以阻止恶意重组，防止审查和剥削 MEV。
- **构建者隐匿安全性**：确保构建者在符合规范的情况下战略性地隐匿区块时不会受到不公平的惩罚。
- **无条件支付**：确保构建者能获得报酬，无论区块是否被包含，从而降低信任需求。
- **提议者安全性**：保护提议者免受报酬损失，鼓励他们参与区块提议。
- **诚实构建者支付安全性**：确保符合协议要求的构建者在区块生产中获得支付。
- **无许可性**：任何人都可以成为构建者或提议者，无需中心化授权，以促进竞争和去中心化。
- **抗审查性**：防止区块和交易审查，维护以太坊的去中心化完整性。
- **兼容以太坊路线图**：确保 ePBS 设计符合以太坊的未来升级，以便顺利集成。

此外，在一个最小化信任的 ePBS 系统中，不应存在提议者通过协议外交易出售区块的优势。

## **探讨最优机制**

关于最佳 ePBS 机制的讨论需要谨慎权衡设计选择，这些选择通常涉及取舍。

- **拍卖机制**：决定是采用二次价格拍卖，还是更复杂的机制（如频繁批量拍卖）会影响公平性、效率和抗操纵性。
- **构建者选择与质押**：构建者的选择方式（质押、信誉系统或随机选择）会影响去中心化和安全性，而质押可能使财富集中的个体占据更多机会。
- **交易包含与抗审查**：确保系统能够抵抗审查并公平包含交易，可能涉及匿名提交或强制交易列表，同时需要权衡垃圾交易或恶意交易的风险。
- **兼容性与适应性**：ePBS 必须与当前的以太坊基础设施兼容，并能适应未来升级（如分片或新的二层解决方案）。

在设计 ePBS 时，需要平衡这些因素，以促进去中心化，确保安全性和效率，维护透明性和公平性，并尽量减少信任需求。社区的持续讨论和研究对于优化 ePBS 机制至关重要。

## **ePBS 解决方案**

### **双区块 HeadLock（TBHL）提案**

双区块 HeadLock（TBHL）方案是对以太坊 PBS 的创新改进，旨在解决 MEV 相关的操作和策略问题。该设计是对之前提案的精细调整，融合了 Vitalik Buterin 的双时隙设计，并引入 headlock 机制，以保护构建者免受提议者的双重提议攻击。

[TBHL 方案](https://ethresear.ch/t/why-enshrine-proposer-builder-separation-a-viable-path-to-epbs/)提供了更详细的设计和流程。

### **ePBS 的有效载荷及时性委员会（PTC）**

有效载荷及时性委员会（PTC）是一种用于在以太坊协议中内置 PBS（ePBS）的设计方案。该机制通过一组验证者投票决定区块有效性，确保其及时性。

[PTC 方案](/docs/wiki/research/PBS/PTC.md)包含更详细的设计和流程。

### **ePBS 规范概述**

当前的 [ePBS 规范](https://hackmd.io/@potuz/rJ9GCnT1C) 和 [GitHub 仓库](https://github.com/potuz/consensus-specs/tree/epbs_stripped_out/specs/_features/epbs) 详细描述了 ePBS 相关的不同组件：

- `beacon-chain.md`：ePBS 分叉中的信标链规范。
- `validator.md`：ePBS 分叉中的诚实验证者行为规范。
- `builder.md`：ePBS 分叉中的诚实构建者规范。
- `engine.md`：ePBS 分叉中的 Engine API 变更。
- `fork-choice.md`：ePBS 分叉中的 fork-choice 变更。

[详细的 ePBS 设计规范](/docs/wiki/research/PBS/ePBS-Specs.md)提供了关于实现规范和流程的更多信息。

### **协议强制提议者承诺（PEPC）**

PEPC 方案是对 PBS 机制的扩展，旨在提供一个更加灵活、安全的方式，让提议者（验证者）承诺区块的构建方式。与现有的 MEV-Boost 依赖协议外的提议者-构建者协议不同，PEPC 试图将这些承诺内置到以太坊协议中，形成一个无信任、无许可的基础设施。

[PEPC 方案](/docs/wiki/research/PBS/PEPC.md)提供了更详细的设计和流程。

## **ePBS 的开放性问题**

Mike Neuder 提出了多个关于在以太坊协议中内置 PBS（ePBS）的重要问题：

- **可绕过性意味着什么？**
  ePBS 可能面临验证者和构建者继续使用协议外的解决方案（如 MEV-Boost），这可能会削弱 ePBS 的影响力。
- **内置 PBS 旨在实现什么？**
  目标是创建一个去信任的协议内中继，减少中心化风险，并改善抗审查能力。
- **如果不内置 PBS，会有哪些影响？**
  PBS 依赖外部系统可能导致区块构建和 MEV 分配的中心化。
- **ePBS 的真实需求有多大？**
  需求来自区块生产的演进和 MEV 机会的复杂性。
- **L1 的 ePBS 在 L2 和 OFA 时代仍然重要吗？**
  即使 L1 MEV 影响减小，ePBS 仍可能是管理 MEV 的关键基础设施。
- **ePBS 的优先级如何？**
  在以太坊升级（如抗审查性、单槽终结性）中，ePBS 需要与其他提案进行优先级权衡。

## **资源**

- [Proposer-Builder Separation (PBS) 备忘录](https://barnabe.substack.com/p/pbs)
- [Mike Neuder - 迈向内置 PBS](https://www.youtube.com/watch?v=Ub8V7lILb_Q)
- [PBS 不完全指南](https://www.youtube.com/watch?v=mEbK9AX7X7o)
- [为什么要内置 PBS？](https://ethresear.ch/t/why-enshrine-proposer-builder-separation-a-viable-path-to-epbs/15710/1)
- [ePBS - 无限自助餐](https://notes.ethereum.org/@mikeneuder/infinite-buffet)
- [ePBS 设计约束](https://hackmd.io/ZNPG7xPFRnmMOf0j95Hl3w)

（更多资源见原文链接）

## 参考资料
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
