# 更改以太坊核心以实现可扩展性

随着 Vitalik Buterin 在 2020 年发布的 [Rollup-Centric 路线图](https://ethereum-magicians.org/t/a-rollup-centric-ethereum-roadmap/4698)，以太坊已经走上了实现可扩展性的道路。 以太坊可扩展性的最终目标可以概括如下：

- 将执行层 (dApp) 完全过渡到第 2 层 (L2) Rollup。
- 优化以太坊，第 1 层 (L1)，主要用作结算层和数据可用性层。

在[合并](https://github.com/ethereum/consensus-specs/tree/dev/specs/bellatrix)之后，(参见[共识规范](https://github.com/ethereum/consensus-specs)、[注释规范](https://github.com/ethereum/annotated-spec/blob/master/merge/beacon-chain.md)和[执行规范](https://github.com/ethereum/execution-specs/blob/master/network-upgrades/mainnet-upgrades/paris.md))，以太坊链被分为两个不同的链： [共识层 (CL)](https://github.com/ethereum/consensus-specs) 和 [执行层 (EL)](https://github.com/ethereum/execution-specs)。 共识层负责以太坊的安全性、去中心化和抗审查特性，而执行层负责在共识层提出的每个新区块内执行交易。这些交易更新链的全局状态，该状态安全地存储在 CL 上。

虽然 EL 可用于直接 L1 dApp 交易，但如前所述，目标是让 dApp 交易完全迁移到 L2 Rollup。 EL 主要由 Rollup 用于更新 L2 状态，或者在 L2 由于某些原因[停止工作](https://docs.arbitrum.io/sequencer#unhappyuncommon-case-sequencer-isnt-doing-its-job) 时用作备份。

CL 将由 Rollup 用于数据可用性 (DA) 并存储有效性证明，特别是对于 ZK Rollup。为了实现可扩展性并降低 gas 成本，从长远来看，在 CL 区块上存储数据必须是负担得起的。为了实现这一雄心勃勃的路线图，CL 的开发可分为以下几个阶段：

- Proto-danksharding ([EIP-4844](https://eips.ethereum.org/EIPS/eip-4844))，引入 blob 的升级(于 2024 年 3 月 14 日上线)。
- 增加 [blob 计数和 gas 修改](https://ethresear.ch/t/on-increasing-the-block-gas-limit/18567)(计划于 2024 年 EOY)。
- 添加 [PeerDAS](https://ethresear.ch/t/peerdas-a-simpler-das-approach-using-battle-tested-p2p-components/16541)。
- 全面实施[Danksharding](https://ethresear.ch/t/from-4844-to-danksharding-a-path-to-scaling-ethereum-da/18046)。

在下一节中，我们将讨论 EIP-4844 将如何影响 EL 和 CL。
