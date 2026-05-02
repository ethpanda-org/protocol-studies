# 扩容

在计算机系统中，可扩展性是指系统在增加或扩大的工作负载下良好运行的能力。 区块链的主要目的是处理用户的交易并管理网络的账本状态。工作负载的增加可能是由于现有用户对交易的更高需求或用户量的增加造成的。

> **区块链系统的可扩展性可以定义为其处理不断增加的卷操作的能力，即在不增加对网络节点操作员的需求的情况下处理更多的交易**。

## 可扩展性限制

对于链上包含的每个区块，一定比例的验证者节点之间必须就其有效性达成总体一致，共识机制就是实现这一一致的方法论。 **区块延迟**是有效区块包含在链中所需的时间。
Ethereum 使用基于 Proof-of-Stake (PoS) 的共识协议，称为 [Gasper](https://eips.ethereum.org/assets/eip-2982/arxiv-2003.03052-Combining-GHOST-and-Casper.pdf)，其理想的区块延迟在规范中由常数 `SECONDS_PER_SLOT`(12 秒) 固定，但实际的区块延迟可能略有不同，因为区块可能会被特定的验证者遗漏，并且不会包含在特定的时隙中。

另一个基本的区块链设计参数是 **区块大小**，它是单个区块中可以存储的数据量的限制。 Ethereum 有一个名为 `gas_limit` 的配置参数，用于限制处理区块所需的计算量，这也有效地限制了区块的大小。 Ethereum 内发生的每个操作都需要一定数量的“gas”才能完成。 区块内所有操作的 gas 总量不能超过 Ethereum 的 `gas_limit`。这种方法可确保网络的处理能力不会过度扩展。 [EIP-1559](https://eips.ethereum.org/EIPS/eip-1559) 定义了 Ethereum gas 定价机制，允许动态调整区块大小以处理瞬时网络拥塞 (永远不会超过 30M gas 单位的限制) 并激励网络以目标 gas 由 `gas_target` (15M) 定义的数量。 gas 限制限制了 Ethereum 区块中可以包含的交易的数量 (及其计算复杂性)。

区块延迟和区块大小直接决定区块链的事务吞吐量，该吞吐量通过 **TPS** (交易每秒) 指标来衡量。 区块延迟可能受到多种因素的影响，例如节点网络的计算能力、共识机制的复杂性、网络流量拥塞和区块大小。因此，由几个彼此紧密连接的高性能节点组成并使用大区块大小的网络可以产生具有高 TPS 和极低区块延迟的特殊区块链。然而，这样的网络可能是高度中心化的，并且需要用户做出重要的信任假设。

去中心化的另一个障碍是区块链状态的大小。 **数据在需要时可访问的属性称为数据可用性**。必须保证状态数据的可用性，包括有足够的数据允许网络中的任何新的节点重建区块链的最新版本，而无需任何信任假设。必须保存所有数据来保证数据可用性可能会导致节点的存储要求很高，并且新数据的同步过程很长，这可能会阻碍去中心化。处理大量数据也会带来网络拥塞的问题，并且需要节点运营商提供高连接带宽。 Ethereum 通过使读取或写入状态的操作变得非常昂贵来阻止不加区别的状态增长。

请注意区块链的设计和调整 (共识机制、节点要求、状态结构和大小……) 及其去中心化能力之间的紧密关系。运行节点的硬件或网络带宽要求较高，导致运行节点的条件严酷且昂贵，因此难以运行，直接影响区块链拥有的节点数量。 **为了确保去中心化，必须让每个人都能负担得起且可行的方式运行节点，并激励用户这样做**。

作为博弈论机制的一部分，为了维持网络的可持续性，用户必须支付费用才能包含他们的交易。 Ethereum 中的费用价格由协议的基本费用、特定交易消耗的 gas 数量和优先级市场决定，支付较高优先级费用的人优先。由于 TPS 有限，在像 Ethereum 这样的高度去中心化的区块链中包含交易已成为一项有价值的服务。对有限且固定的 TPS 的交易需求的增加会提高网络可用性阈值的费用，并且需要牺牲去中心化和/或安全性才能扩展。

> **回顾可扩展性的定义，经典的区块链系统在不牺牲一定程度的去中心化的情况下无法扩展以供大规模采用。**

这个问题被广泛称为 **区块链 Trilemma**，它指出区块链网络必须牺牲安全性、去中心化或可扩展性，并且同时最大化这三者是困难的。 区块链技术的圣杯是创建一个安全且去中心化的交易网络，可以实现高 TPS 速率。

!["Blockchain trilemma - Bankless.com"](../img/scaling/blockchain-trilemma.png "Blockchain trilemma - Bankless.com")

## 区块链模块化

现代区块链设计提出了一种“分而治之”的方法，将系统划分为不同的特定功能组件，以独立地最大化三难困境的每个顶点。这种方法封装了系统各部分的复杂性，并通过为系统组件定义更简单的交互接口来降低系统复杂性。
区块链的功能组件：

- 共识：节点的 节点构建和交易排序之间的协议机制。
- 执行：交易定义状态演化的执行机制。
- 数据可用性：数据可用性确保数据在需要时可供查询的机制。
- 结算：保证最终确定性和 交易不变性的机制。

模块化区块链方法建议将区块链系统划分为不同的逻辑层。这使得设计的系统复杂性降低，组件更简单，可以更轻松地优化和重新设计以引入新的扩展解决方案。

## 扩容 Ethereum

Ethereum 的可扩展性工作的主要目标是在不影响去中心化或安全性的情况下增加 TPS 指标。

第 1 层扩展是指增加底层区块链协议本身的 TPS 的所有技术。一个简单的扩容解决方案是使用区块链和“更大的区块”，可以达到更高的 TPS 数字。然而，这种方法的问题在于，增加给定时间段内要验证的交易数量可能会导致只有有限数量的节点运营商 (那些拥有足够强大硬件的运营商) 可以参与网络。反过来，这可能会导致中心化加剧。因此，每个区块容量的增加必须伴随着对协议的各种修改，以保持去中心化。第一层扩展解决方案的示例包括分片、Proof-of-Stake 共识机制以及旨在优化区块处理效率的协议升级。

!["Layer 1 Scaling"](../img/scaling/layer-1-scaling.png "Layer 1 Scaling")

> **gas 限制的增加将有效扩大 Ethereum 可容纳的计算量和数据量，但也会提高对网络节点运营商的要求。另一个将直接影响区块大小的设计选择是 gas 操作定价。更便宜的操作将允许在相同的 gas 限制边界内包含更多数量和更复杂的交易。**

第 2 层扩展是构建在第 1 层区块链协议之上的一组解决方案，可以实现更高的 TPS，而不需要增加第 1 层资源消耗，同时仍然依赖其安全模型。这些解决方案通常在链外处理交易或使用比主区块链更快且更具可扩展性的替代共识机制。第 2 层扩展解决方案的示例包括状态通道、Plasma 链或 Rollup。

!["Layer 2 Scaling"](../img/scaling/layer-2-scaling.png "Layer 2 Scaling")

Ethereum 社区主要采用的解决可扩展性问题的解决方案是以多 Rollup 为中心的方法。在这里，Ethereum 充当结算的基本安全层，而数据可用性则充当大多数执行任务委托给称为 Rollup 的上层。 Rollup 将多个第 2 层交易捆绑到单个第 1 层交易中。这些交易存储在第 1 层，但它们的执行委托给链下机制。这可以在不牺牲安全性的情况下显着提高可扩展性。 Ethereum 的高水平可编程性使得通过 Ethereum 智能合约在平台之上创建可扩展的解决方案成为可能。如前所述，为了保持高水平的去中心化，Ethereum 通过 gas 限制，对区块中包含的交易可以使用的计算和存储资源进行限制。因此，存储在第 1 层的数据被最小化，仅存储原始交易以及执行这些交易所产生的第 2 层状态的哈希承诺。

> **Rollup 可以被视为压缩交易执行的一种方式，从而减轻第 1 层区块链的计算负担。这提供了增加 TPS 的可能性，同时仍然保持第 2 层交易的第 1 层安全级别，因为它们的数据和第 2 层状态转换承诺存储在第 1 层中。**

## Ethereum 核心朝着以 Rollup 为中心的路线图变化

请参阅 [下一节](/wiki/research/scaling/core-changes/core-changes.md)。

## Ethereum 第 2 层扩容

鉴于当前以 Rollup 为中心的路线图，多个第 2 层网络已经出现。根据其安全模型，它们可以分为两大类：optimistic Rollup 和 zk-Rollup。

这两种解决方案都有各自的优点、缺点和独特的技术堆栈。然而，它们共享一些核心组件：
  - 链上合约：这些合约控制各种 Rollup，并可能包括跟踪用户存款、监控状态更新等的智能合约。
  - 链下虚拟机 (VM)：通常是执行交易的修改后的 EVM 兼容链。

Ethereum 既充当数据可用性 (DA) 层又充当结算层，这意味着第 2 层继承了第 1 层的安全性。一旦 Rollup 交易提交到 Ethereum 的基础层，就无法回滚。  
从历史上看，Rollup 在 Ethereum 交易的 `calldata` 部分发布了它们的交易数据，但这导致了历史节点存储的过度增长。 EIP-4844 (proto-danksharding) 引入了一种新机制，允许 Rollup 在 blob (一个单独的、更高效的数据空间) 中提交数据，以及用于 blob 使用的单独 gas 模型，并对共识节点处理此数据的方式进行了细微更改。您可以在 [以下部分](https://epf.wiki/#/wiki/research/scaling/core-changes/eip-4844) 中了解有关 blob、danksharding 和 EIP-4844 的更多信息。

### 乐观 Rollup

乐观的 Rollup 的出现是一种提高交易吞吐量的方法，同时仍然依靠加密经济激励来继承 Ethereum 的安全性。
他们的验证模型基于一种称为乐观验证的技术——默认情况下，提交给第 1 层的每个交易都被假定为有效。
称为运营商或聚合器的实体在称为锚定的过程中向第 1 层提交一批 L2 交易。之后，有一个挑战期，在此期间任何人都可以通过提交 fraud proof 来质疑交易批次的有效性。
如果挑战成功，挑战者将获得奖励，而操作者将通过称为削减的过程受到惩罚。这种机制激励运营商只提交不会受到质疑的交易。

### ZK Rollup

ZK Rollup 是扩展解决方案，可使用先进的加密技术以数学确定性增强 Ethereum 的吞吐量。
Layer 2 上的交易被捆绑成批次，并生成 zero-knowledge proof 来验证其正确性。然后，该证明被提交到第 1 层，并在第 1 层进行验证。一旦经过验证，就有数学保证该批次中的所有交易都是有效的。
尽管由于使用 zero-knowledge proof，它们被称为 ZK Rollup，但主要好处实际上是简洁性——能够生成比所有交易的实际大小小得多的紧凑证明。
实践中使用的 zero-knowledge proof 系统主要有两种类型，zk-SNARKs (零知识简洁非交互式知识论证) 和 zk-STARKs (零知识可扩展透明知识论证)。  
zk-SNARK 目前在 ZK Rollup 领域得到了更广泛的采用，但 zk-STARK 由于其可扩展性和缺乏可信设置而获得动力。

## 资源：

- [Ethereum 共识机制](https://ethereum.org/developers/docs/consensus-mechanisms)，[已归档](https://web.archive.org/web/20240214225609/https://ethereum.org/developers/docs/consensus-mechanisms)
- [Ethereum Proof-of-Stake 共识规范](https://github.com/ethereum/consensus-specs/tree/dev?tab=readme-ov-file#ethereum-proof-of-stake-consensus-specifications)，[已存档](https://web.archive.org/web/20240208050731/https://github.com/ethereum/consensus-specs/tree/dev)
- [Ethereum Proof-of-Stake 共识规范](https://ethereum.github.io/consensus-specs/)，[已存档](https://web.archive.org/web/20240217155014/https://ethereum.github.io/consensus-specs/)
- [Vitalik 区块链可扩展性的限制](https://vitalik.eth.limo/general/2021/05/23/scaling.html)，[已存档](https://web.archive.org/web/20240205202358/https://vitalik.eth.limo/general/2021/05/23/scaling.html)
- [Ethereum 扩容](https://ethereum.org/en/developers/docs/scaling)，[已存档](https://web.archive.org/web/20240209083702/https://ethereum.org/en/developers/docs/scaling)
- [Rollup 不完整指南](https://vitalik.eth.limo/general/2021/01/05/rollup.html)，[已存档](https://web.archive.org/web/20240212014637/https://vitalik.eth.limo/general/2021/01/05/rollup.html)
- [Gemini Cryptopedia 区块链三难困境：快速、安全和可扩展的网络](https://www.gemini.com/cryptopedia/blockchain-trilemma-decentralization-scalability-definition)，[已存档](https://web.archive.org/web/20240209073156/https://www.gemini.com/cryptopedia/blockchain-trilemma-decentralization-scalability-definition)
- [结合 GHOST 和 Casper](https://eips.ethereum.org/assets/eip-2982/arxiv-2003.03052-Combining-GHOST-and-Casper.pdf)，[已存档](https://web.archive.org/web/20230907004049/https://eips.ethereum.org/assets/eip-2982/arxiv-2003.03052-Combining-GHOST-and-Casper.pdf)
- [Ethereum 区块](https://ethereum.org/developers/docs/blocks)，[已存档](https://web.archive.org/web/20240214052915/https://ethereum.org/developers/docs/blocks)
- [关于区块大小、gas 限制和可扩展性](https://ethresear.ch/t/on-block-sizes-gas-limits-and-scalability/18444)、[已存档](https://web.archive.org/web/20240220230246/https://ethresear.ch/t/on-block-sizes-gas-limits-and-scalability/18444)
- [L2beat：所有 L2 Rollup 的概述](https://l2beat.com/scaling/summary)
