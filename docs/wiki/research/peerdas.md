# Ethereum PeerDAS 简介

> :warning: 本文是一个 [存根](https://en.wikipedia.org/wiki/Wikipedia:Stub)，通过 [贡献](/contributing.md) 和扩展它来帮助维基。

> :warning: 本文档涵盖了一个活跃的研究领域。它在阅读时可能已经过时，并且随着设计空间的发展，未来可能会更新。

**PeerDAS** (对等节点 Data Availability Sampling) 是 [EIP-7594](https://eips.ethereum.org/EIPS/eip-7594) 中引入的网络协议。它旨在优化 Ethereum 网络上的数据分发和验证。 PeerDAS 确保 blob (主要包含来自第 2 层 (L2) 解决方案 (如 Rollup) 的数据) 保持可靠的可访问性，而不会压垮节点。

## Ethereum 路线图中的 PeerDAS

扩容对于 Ethereum 至关重要。网络必须提高其处理更多交易和存储数据的能力，使交易更便宜且更容易被用户访问。随着区块链应用程序需求的增长，Ethereum 需要在不影响去中心化或安全性的情况下支持更高的吞吐量。

PeerDAS 是 [Danksharding](https://ethereum.org/en/roadmap/danksharding/) 的重要组成部分，在 Ethereum 以 Rollup 为中心的路线图中发挥着关键作用。它是 [Surge](https://vitalik.eth.limo/general/2024/10/17/futures2.html) 路线图类别的一部分，该类别的重点是扩展交易吞吐量并降低成本。

第 2 层 (L2) 解决方案 (例如 Rollup) 依赖 Ethereum 来实现数据可用性和安全性。最初，Rollup 将其交易数据作为 calldata 直接发布到 Ethereum Execution Layer，这种方法既昂贵又低效。 [EIP-4844 (Proto-Danksharding)](https://eips.ethereum.org/EIPS/eip-4844) 引入了 **blob** - 一种新的数据结构，允许 Rollup 以显着降低的成本发布数据。虽然 EIP-4844 是一个重要的改进，但它只是迈向 Danksharding 的垫脚石。在 Danksharding 中，blob 将被转换为**数据列**并使用 PeerDAS 分布在网络上。这一转变将进一步降低成本并提高可扩展性。

Ethereum 研究人员概述了 PeerDAS 的渐进式多阶段路线图，该路线图在增加的吞吐量与网络稳健性之间取得了平衡：

- **阶段 0 (EIP-4844)：** 引入用于 blob 分发的子网，无需 Data Availability Sampling (DAS)，需要节点下载所有数据。
- **第 1 阶段：**通过水平扩展 blob 并引入列子网来实现 1D PeerDAS，启用对等节点采样以提高效率。
- **第 2 阶段：** 通过添加垂直 blob 扩展和轻量级、基于单元的对等节点采样，使用 2D PeerDAS 完成 Danksharding，支持稳健的分布式重建并最大化可扩展性。

## 了解 PeerDAS

### 数据分区和托管分配

PeerDAS 将数据 blob 划分为称为“列”的较小单元，作为数据采样的原子组件。网络将节点分配给负责特定列组的托管组。确定性函数使用可公开验证的输入 (例如节点 ID) 来控制此分配，以确保透明且可重复的分配。 节点必须维持最低托管阈值以保证基线数据可用性。那些存储额外列的成为**super- 节点**，保存所有列并增加系统冗余以增强容错能力。

### 数据编码与分发

数据 blob 使用 Reed-Solomon 纠删码进行编码。此过程将数据分为多个列并添加奇偶校验列，即使某些列丢失，也能够重建原始数据集。编码后，每列都会使用八卦协议在网络上传播。 提议者最初将列分配给节点的子集，然后中继将数据分配给它们的对等节点。 节点订阅与其托管组一致的子网，优化数据流并最大限度地减少网络拥塞。如果节点没有通过八卦接收列，它可以使用请求/响应协议与其他节点检索丢失的数据。

### Data Availability Sampling (DAS)

PeerDAS 使用 Data Availability Sampling 来验证数据，无需完整下载。 节点请求来自对等节点的列的随机样本。通过使用概率方法，节点在获得足够数量的样本时推断出完整数据集的可用性。如果检测到缺失列，节点可以发起直接请求来恢复数据。

### 使用 KZG 承诺进行密码学验证

为了确保数据的完整性和真实性，PeerDAS 使用 KZG (Kate-Zaverucha-Goldberg) 承诺。这些加密承诺使 节点能够验证采样列是否与原始数据集匹配，而无需下载完整数据。这种有效的验证过程可以防止篡改和数据损坏。

### 数据重建与冗余管理

节点不断对他们保管的色谱柱进行采样。当节点累积超过数据 blob 总列的 50% 时，它可以使用 Reed-Solomon 解码完全重建原始数据集。重建后，节点会将恢复的列重新分配回网络中。这种重新分配增强了整体数据可用性以及针对子网故障或临时数据不可用的恢复能力。

### 验证者协议和分叉选择规则

验证者遵循修改后的分叉选择规则，在共识过程中强制执行数据可用性。对于新提出的区块，验证者根据通过 gossip 子网接收的列来评估数据可用性。对于较旧的区块，他们依靠对等节点采样结果来验证正在进行的数据可用性。这种双重方法减轻了与临时扣留数据相关的风险，确保验证者仅投票给具有可验证数据的区块，从而维护区块链的完整性和安全性。

总体而言，PeerDAS 集成了确定性托管分配、概率性 Data Availability Sampling、鲁棒性纠删码和加密性承诺，为去中心化网络中的数据可用性提供可扩展、容错且安全的框架。其设计确保即使在不利条件下数据也保持可验证和可恢复，使其成为分布式数据管理的弹性架构。

## 参考文献

- [EIP-7594：PeerDAS](https://eips.ethereum.org/EIPS/eip-7594)
- [Fulu 规范包括 PeerDAS](https://github.com/ethereum/consensus-specs/tree/dev/specs/fulu)
- [使用 PeerDAS 扩展 Ethereum L1 - dapplion，演示](https://www.youtube.com/watch?v=_PW6jFTWLPc)
- [PeerDAS 在 Pectra 及其他领域 - Francesco D'Amato，演示](https://www.youtube.com/watch?v=WOdpO1tH_Us)
- [PeerDAS 书籍，作者 Manu Nalepa](https://hackmd.io/@manunalepa/peerDAS/https%3A%2F%2Fhackmd.io%2F%40manunalepa%2FB1idHCOfke)
- [Ethereum 协议的可能未来，第 2 部分：Vitalik Buterin 的激增](https://vitalik.eth.limo/general/2024/10/17/futures2.html)
- [从 4844 到 Danksharding：扩展 Ethereum DA (ethresear.ch)](https://ethresear.ch/t/from-4844-to-danksharding-a-path-to-scaling-ethereum-da/18046)
- [PeerDAS – 使用经过实战测试的 p2p 组件的更简单的 DAS 方法 (ethresear.ch)](https://ethresear.ch/t/peerdas-a-simpler-das-approach-using-battle-tested-p2p-components/16541)
- [DevCon Sea：使用 DAS 扩展 Ethereum，作者：Francesco](https://www.youtube.com/watch?v=toR2UKzE_zA)
- [DevCon Sea：从 PeerDAS 到 FullDAS](https://www.youtube.com/watch?v=Y8VKmyJMAUk&t=9s)
- [EthPrague：Dapplion 的 PeerDAS](https://www.youtube.com/watch?v=fCIPNxGXmmE&t=43s)
- [Francesco 在 Pectra 及其他领域的 PeerDAS](https://www.youtube.com/watch?v=WOdpO1tH_Us&t=334s)
