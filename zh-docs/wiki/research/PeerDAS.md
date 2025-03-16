# 以太坊 PeerDAS 介绍

> ⚠️ 本文是一个[初稿](https://en.wikipedia.org/wiki/Wikipedia:Stub)，欢迎通过[贡献指南](/contributing.md)进行扩展和完善。

> ⚠️ 本文档涵盖了一个正在积极研究的领域。由于设计方案仍在演化，阅读时可能已存在过时信息，并可能在未来发生更新。

**PeerDAS**（Peer Data Availability Sampling，点对点数据可用性抽样）是一种网络协议，首次提出于 [EIP-7594](https://eips.ethereum.org/EIPS/eip-7594)。它旨在优化以太坊网络中的数据分发和验证，确保包含 L2（Layer 2）解决方案数据的 **blobs**（数据块）能够可靠存取，同时避免给节点带来过大负担。

---

## PeerDAS 在以太坊路线图中的角色

以太坊的扩展性至关重要。为了让交易成本更低、用户体验更好，网络需要提高处理交易和存储数据的能力。在区块链应用需求增长的背景下，以太坊必须在不牺牲**去中心化**和**安全性**的前提下，提高吞吐量。

PeerDAS 是[Danksharding](https://ethereum.org/en/roadmap/danksharding/) 的核心组成部分，在以太坊的**Rollup 优先（Rollup-Centric）**路线图中扮演关键角色。它属于 [The Surge](https://vitalik.eth.limo/general/2024/10/17/futures2.html) 路线图阶段，旨在提高交易吞吐量并降低成本。

L2 解决方案（如 Rollup）依赖以太坊提供数据可用性和安全性。最初，Rollup 直接将交易数据以 **calldata**（调用数据）的形式发布到以太坊执行层，但这种方式既昂贵又低效。[EIP-4844（Proto-Danksharding）](https://eips.ethereum.org/EIPS/eip-4844) 引入了 **blobs**（数据块），允许 Rollup 以更低成本发布数据。然而，EIP-4844 只是 Danksharding 迈出的第一步。在 Danksharding 完全实现后，blobs 将被转换为 **数据列（Data Columns）**，并通过 PeerDAS 在网络中分发，以进一步降低成本并提高可扩展性。

以太坊研究者提出了一种渐进式的 PeerDAS 路线图，以在提高吞吐量的同时保持网络稳健性：

- **阶段 0（EIP-4844）：** 引入用于 blob 分发的子网，但不包含数据可用性抽样（DAS），节点必须下载所有数据。
- **阶段 1：** 实现 **一维（1D）PeerDAS**，对 blobs 进行水平扩展，并引入**列子网（Column Subnets）**，使得节点可以进行点对点抽样，提高数据可用性验证的效率。
- **阶段 2：** 通过垂直扩展 blobs 并采用**轻量级单元抽样（Cell-Based Peer Sampling）**，最终实现 **二维（2D）PeerDAS**，支持更强的数据重构能力，并最大化可扩展性。

---

## PeerDAS 的工作原理

### 数据分片与托管分配（Custody Assignment）

PeerDAS 将数据 blobs 划分为**列（Columns）**，这些列是数据抽样的基本单元。网络会将这些列分配给**托管组（Custody Groups）**中的节点，以确保数据存储的去中心化。分配过程采用**确定性函数**（使用节点 ID 作为可验证的输入），确保数据分发过程的透明性和可复现性。

节点必须维持最低的存储阈值，以保证基础的数据可用性。而一些存储额外数据列的**超级节点（Super-Nodes）**，能够存储所有数据列，从而提高系统冗余度和容错性。

### 数据编码与分发

PeerDAS 采用 **Reed-Solomon 纠错码** 对数据进行编码。该方法将 blobs 分割成多个列，并添加**校验列（Parity Columns）**，即使部分列丢失，也能恢复原始数据。

编码完成后，每个数据列通过 **gossip 协议** 在网络中传播。提议者（Proposer）首先将列分发给部分节点，这些节点再向其对等节点（Peers）中继数据。节点会订阅与自身**托管组**相关的子网，从而优化数据流动，减少网络拥堵。如果节点未能通过 gossip 收到某个列，它可以向其他节点发起请求以获取缺失数据。

### 数据可用性抽样（DAS）

PeerDAS 采用**数据可用性抽样（DAS）**，使节点无需完整下载数据即可验证其可用性。节点从其他对等节点随机请求部分数据列，并基于**概率方法**推断完整数据是否可用。如果检测到缺失数据，节点可以直接请求缺失部分，以提高数据恢复能力。

### 通过 KZG 承诺进行加密验证

为确保数据的完整性和真实性，PeerDAS 采用 **KZG（Kate-Zaverucha-Goldberg）承诺** 进行加密验证。这种承诺机制允许节点在无需下载完整数据的情况下，验证抽样列是否匹配原始数据，从而防止数据篡改和损坏。

### 数据恢复与冗余管理

节点会不断对自己存储的数据列进行采样。当某个节点**收集到超过 50% 的数据列**时，它就可以使用 **Reed-Solomon 译码** 恢复完整数据。一旦恢复成功，节点会将重建的数据重新传播到网络，以增强整体数据可用性，并防止由于子网故障或数据暂时丢失导致的数据不可用风险。

### 验证者协议与分叉选择规则

验证者在共识过程中遵循修改后的**分叉选择规则（Fork-Choice Rules）**，以确保数据可用性：

- **对于新提议的区块：** 验证者会根据 gossip 子网收到的列来评估数据可用性。
- **对于旧区块：** 验证者依赖**点对点抽样**的结果来持续验证数据可用性。

这种双重机制可有效防止**数据暂时隐藏攻击（Temporary Data Withholding Attack）**，确保验证者仅投票支持具有可验证数据的区块，从而维护区块链的完整性和安全性。

---

## 结论

PeerDAS 结合**确定性数据分配**、**概率数据可用性抽样**、**强大的纠删编码**和**加密承诺**，为去中心化网络提供了**可扩展、容错和安全的数据可用性框架**。其设计确保数据即使在**恶意环境**下仍然**可验证和可恢复**，为去中心化数据管理提供了强大支撑。

---

## 参考资料

- [EIP-7594: PeerDAS](https://eips.ethereum.org/EIPS/eip-7594)
- [Fulu 规范（包含 PeerDAS）](https://github.com/ethereum/consensus-specs/tree/dev/specs/fulu)
- [Scaling Ethereum L1 with PeerDAS - dapplion 演讲](https://www.youtube.com/watch?v=_PW6jFTWLPc)
- [从 4844 到 Danksharding：以太坊数据可用性扩展路线（ethresear.ch）](https://ethresear.ch/t/from-4844-to-danksharding-a-path-to-scaling-ethereum-da/18046)
- [Vitalik Buterin：The Surge - 以太坊扩展的未来](https://vitalik.eth.limo/general/2024/10/17/futures2.html)
- [以太坊 DevCon 会议：从 PeerDAS 到 FullDAS](https://www.youtube.com/watch?v=Y8VKmyJMAUk&t=9s)

---
