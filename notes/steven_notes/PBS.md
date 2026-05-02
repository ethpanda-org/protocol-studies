# 提议者-构建者分离(PBS)

## 简介
简要概述PBS，强调其对于以太坊的可扩展性和去中心化的重要性。


## 为什么是 PBS？
### Danksharding 基金会
 PBS 在 Danksharding 提案中发挥着至关重要的作用，该提案旨在将以太坊的吞吐量扩展到每秒超过 100,000 交易。要在区块构建中实现如此高的效率水平，需要更复杂的硬件，由于成本增加，可能会导致单独的质押者被边缘化。 PBS 通过启用专门的集中式区块构建流程来解决此问题，该流程可以处理高交易卷，同时允许区块提议者使用更少的硬件进行操作。这种分工不仅优化了交易的处理，还降低了验证者的进入门槛，支持更加去中心化和包容性的网络。

 Vitalik 提到了“**中心化区块构建者和高度去中心化区块提议者**”的想法：[Endgame](https://vitalik.eth.limo/general/2021/12/06/endgame.html)。


### 加强反审查措施
- 通过分离区块构建者和 提议者的职责，PBS 使构建者审查交易的难度显着增加，从而促进更自由、更开放的交易纳入过程。
- 它还可以防止来自可能试图影响交易选择的强大组织的外部压力，因为区块提议者不了解他们广播的区块中的交易内容。这种无知确保了更高程度的公正性和对审查制度的抵抗力

### MEV的经济调整
 PBS 旨在通过消除机构运营商相对于个人利益相关者所拥有的不成比例的优势，使最大可提取价值(MEV)的好处民主化。通过更新与 MEV 相关的经济激励措施，PBS 寻求公平的竞争环境，确保网络参与的奖励在所有贡献者之间更公平地分配，无论其规模如何。

## PBS 实现方法
PBS 有两种主要实现：[MEV-Boost](https://github.com/flashbots/mev-boost/) 和 Enshrined PBS (ePBS)。

MEV-Boost 和 ePBS 之间的主要区别在于它们在以太坊生态系统中的方法和集成水平。 MEV-Boost 作为验证者可以选择加入的外部服务运行，在验证者和 区块构建者竞争市场之间架起一座桥梁。这是一个实用的解决方案，无需更改以太坊的核心协议即可实现。

另一方面，ePBS 代表了一种更加集成的方法，旨在将提议者-构建者分离直接合并到以太坊的协议中。这需要在以太坊协议级别达成共识和实施，从而提供一个更统一且可能更安全的框架来管理区块构建和提案的角色。

### MEV-Boost
下面的图表说明了 MEV-Boost 如何与来自 [MEV-boost github repo](https://github.com/flashbots/mev-boost/) 的以太坊一起作为 sidecar 软件工作，可以在其中找到更详细的信息。

![mev-boost architecture](https://raw.githubusercontent.com/flashbots/mev-boost/54567443e718b09f8034d677723476b679782fb7/docs/mev-boost-integration-overview.png)

### ePBS
ePBS 有[许多提议的实现](https://github.com/michaelneuder/mev-bibliography#specific-proposals)，但是在以太坊社区内还没有 ePBS 的最终结论或普遍接受的设计。制定此类基础性变更的过程需要仔细考虑众多因素，以确保在不引入新漏洞或低效率的情况下实现提议者-构建者分离的好处

[为什么要实行提议者-构建者分离](https://ethresear.ch/t/why-enshrine-proposer-builder-separation-a-viable-path-to-epbs/15710) 提出了支持和反对 ePBS 的论据、ePBS 机制的布局设计目标，并说明了几种 ePBS 设计，例如两-区块 HeadLock 和乐观中继。


## 进展
以太坊提议者-构建者分离 (ePBS) 的演变见证了两种不同方法的发展，以实现其完全实现：
1. 自上而下的方法：此标准程序从社区讨论开始，形成共识和规范开发，然后是客户端团队实施。这是一种结构化方法，可确保 ePBS 的协调进展。

2. 通过 MEV-Boost 的自下而上方法：鉴于 MEV-Boost 在大约 90% 的以太坊区块(源)中采用，此方法建议通过发展 MEV-Boost 的中继基础设施来增量 ePBS 集成。它允许逐步转向 ePBS，而无需立即对共识软件进行广泛的更改。

## 进一步阅读
[延伸阅读](https://ethereum.org/en/roadmap/pbs/#further-reading)
