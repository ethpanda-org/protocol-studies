# 以太坊协议路线图

以太坊的开发哲学强调协议的持续演进，同时在值得变革的情况下保持一定的风险规避。随着我们对以太坊的理解和经验不断增长，研究人员和开发者正在构思各种方案，以应对网络的挑战和局限性。在其发展过程中，[核心协议经历了许多变更](/wiki/protocol/history.md)。这些变更大多属于一些共同目标，我们可以称之为路线图。

尽管以太坊没有官方的路线图，也没有任何权威机构可以强制执行，但社区广泛的讨论仍然在一定程度上引导着协议的发展。通过达成共识并认同当前开发状态，社区、开发者和研究团队共同推动这一抽象路线图的前进。

## 无限花园（The Infinite Garden）

> *以太坊并不是一个零和游戏，也没有明确的终点，而是一场我们希望持续进行的游戏。为了实现这一目标，这座“无限花园”需要定期升级，包括安全性、可扩展性和可持续性等方面，直到它趋于稳定。那时，可能只会进行一些微调。*

## 核心研发（Core R&D）

以太坊核心协议的讨论、资源以及所有的研究和开发工作都是完全开放、免费且公开的。任何人都可以学习（正如你在这个 wiki 上所做的），甚至可以参与其中。没有特定的个人或组织能够单独推动核心协议的变更，整个以太坊社区都可以发声，引导讨论的方向。要了解更多关于塑造协议的核心研发内容，请阅读[相关 wiki 页面](/wiki/dev/core-development.md)。

## 路线图概览

虽然以太坊并没有一条固定的路线图，但我们可以通过跟踪当前的研发进展，描绘出正在发生的变化以及未来可能发生的变更。  
Vitalik 在 2023 年 12 月发布了一张路线图概览，涵盖了当前核心研发的多个领域：

![Vitalik 于 2023 年 12 月更新的以太坊路线图](../research/img/full_roadmap2024_1600x1596.webp)

在这张概览图中，不同的研究领域被归类到相关的“动机”中。其中的许多模块在本 wiki 上都有专门的页面，你可以深入了解更多细节。

### The Merge（合并）

这一升级涉及以太坊从工作量证明（PoW）切换到权益证明（PoS）。The Merge 于 2022 年 9 月 15 日 06:42:42 UTC 成功完成，使网络的年化能耗减少了 99.988% 以上。然而，该类别还追踪了合并后对共识机制的进一步改进，以及在实际运行中遇到的问题的优化方案。

# 以太坊协议路线图：升级进展

## 已实施（IMPLEMENTED）

| 升级                                  | 描述                                                                                                 | 影响                                                                                                         | 当前状态                                               |
|:------------------------------------ |:--------------------------------------------------------------------------------------------------:|:------------------------------------------------------------------------------------------------------------:|:---------------------------------------------------- |
| 启动信标链（Beacon Chain）            | 以太坊向权益证明（PoS）共识机制过渡的关键一步                                                        | 信标链作为独立网络启动，并连接到以太坊，为合并（The Merge）做准备，启动验证者机制                           | 已完成 </br> EIP-2982[^1]                             |
| 合并执行层与共识层                    | 以太坊的执行层与信标链（共识层）合并                                                                 | 工作量证明（PoW）机制终止，网络的共识机制转变为权益证明（PoS）。验证者负责交易验证与区块提议                 | 已完成                                                 |
| 启用提现                              | 以太坊过渡到 PoS 机制的最后一步                                                                     | 验证者可以添加提现凭证，信标链会清理所有不活跃的 ETH                                                         | 已完成 </br> EIP-4895[^2]                             |

## 待完成（TODO）

| 升级                                  | 描述                                                                                               | 预期影响                                                                                                                                                                                                                                                                                                        | 当前状态                                                                 |
|:------------------------------------ |:------------------------------------------------------------------------------------------------:|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|:----------------------------------------------------------------------- |
| 单 Slot 最终确定性（SSF）             | 允许区块在同一个 Slot 内被提议并最终确定                                                           | (i) 交易最终确定时间缩短一个数量级（12 分钟降至 12 秒），提升用户体验；(ii) 在[完整 Rollup 扩展](#the-surge)和实时 SNARK 证明实现后，单 Slot 最终确定性可加快 L2 的跨链桥效率；(iii) 更难攻击（消除多区块 MEV 重组攻击，减少共识机制的复杂性） | 研究中 </br>(i) VB 的 SSF 研究笔记[^3] </br>(ii) 8192 签名后 SSF[^4] </br>(iii) 简化 SSF 协议[^5] |
| 单一秘密领导者选举（SSLE）            | 允许选中的区块提议者在出块前保持匿名，以防止 DoS 攻击                                             | 只有被选中的验证者知道自己被选中为区块提议者                                                                                                                                                                                                                            | 研究中 </br> EIP-7441[^6]                                              |
| 启用更多验证者                        | 在保持最佳权衡的前提下，技术上如何高效协调不断增加的验证者，以实现 SSF                              | 增加冗余、更广泛的提议者池、更分散的验证者群体，整体提升网络弹性                                                                                                                                                                                                         | 研究中 </br> (i) EIP-7514[^7] </br>(ii) EIP-7251[^8] </br> (iii) 8192 签名[^5] |
| 量子安全签名                          | 主动研究并集成抗量子攻击的加密算法                                                                 | 量子安全、可聚合的签名机制将增强协议安全性，以抵御未来的量子计算攻击                                                                                                                                                                                                    | 研究中 </br> (i) 基于格密码[^9] </br>(ii) 基于 STARK[^10]               |

---

## The Surge（扩展升级）
通过 Rollup 和数据分片提升可扩展性。

### 已实施（IMPLEMENTED）

| 升级                | 领域 | 主题                | 描述                                                                                                     | 影响                 | 当前状态                     |
|:----------------- |:---:|:------------------:|:------------------------------------------------------------------------------------------------------:|:-------------------:|:-------------------------:|
| Proto-danksharding | -   | 基础 Rollup 扩展   | 不再永久存储 Rollup 数据，而是使用临时的“blob”存储，数据在不再需要时自动删除                           | 降低交易成本         | 已完成 </br> EIP-4844[^11] |

### 待完成（TODO）

| 升级                                         | 领域 | 主题                  | 描述                                                                 | 预期影响                                                                                                                                                                                                                                                                                                                                                                                                               | 当前状态                                                                                         |
|:------------------------------------------ |:---:|:--------------------:|:------------------------------------------------------------------:|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|:--------------------------------------------------------------------------------------------- |
| Danksharding                              | -   | 完整 Rollup 扩展     | Danksharding 是 Proto-Danksharding 的完全实现                        | 大量释放以太坊的存储空间，供 Rollup 存储压缩后的交易数据                                                                                                                                                                                                                                                                            | 研究中                                                                                         |
| 数据可用性抽样（DAS）                      | -   | 完整 Rollup 扩展     | 一种无需给单个节点施加过大压力的方式，确保 Rollup 数据可用               | (i) 确保 Rollup 运营方在 EIP-4844 之后仍然提供交易数据 (ii) 确保区块提议者提供完整数据，以支持轻客户端 (iii) 在提议者-构建者分离（PBS）模型下，仅区块构建者需处理完整区块，其他验证者可使用数据可用性抽样进行验证 | 研究中 </br> EIP-7594[^12]                                                                    |
| 解除 Rollup 训练轮                         | -   | 基础 & 完整 Rollup 扩展 | (i) 乐观 Rollup 故障证明 </br> (ii) ZK-EVMs </br> (iii) Rollup 互操作性 | (i) 让乐观 Rollup 具备实时证明系统，以降低 L2 的审查风险 (ii) ZK-EVM 提供更高的可扩展性和隐私，同时保持去中心化和安全性 (iii) L1 序列器或 L1 具备 Rollup 排序权的提议者，提高中立性、安全性，并增强 Rollup 的 L1 兼容性 | 研究中 </br> (i) Arbitrum BoLD[^13] </br> Optimism Cannon[^14] </br> (ii) ZK-EVMs[^15] [^16] [^17] </br> (iii) [ET](/wiki/research/PBS/ET.md) </br> [基于排序与预确认](/wiki/research/Preconfirmations/BasedSequencingPreconfs.md) |
| 量子安全且无需可信设置的承诺机制             | -   | -                    | 用无需可信设置、抗量子攻击的承诺机制替代 KZG 承诺                      | 量子安全承诺机制，提升未来的协议安全性                                                                                                                                                                                                                                                                    | 研究中                                                                                         |


### The Scourge（协议风险与去中心化升级）
与抗审查、去中心化、以及缓解 MEV 和流动质押/池化协议风险相关的升级。

#### 已实施（IMPLEMENTED）
| 升级       | 领域       | 主题                     | 描述                                                                 | 影响                                                               | 当前状态                                                     |
| :--------  | :-------:  | :-----------------------: | :------------------------------------------------------------------: | :-----------------------------------------------------------------: | :----------------------------------------------------------- |
| MEV-Boost  | MEV-Track  | 终极区块生产流程          | 跨协议 MEV 市场                                                      | 以太坊社区成功地通过跨协议市场对 MEV 进行商品化（部分实现）。目前大部分 MEV 进入了验证者手中。 | [已完成](/wiki/research/PBS/mev-boost.md)                      |

#### 待完成（TODO）
| 升级                       | 领域       | 主题                     | 描述                                                                                                                                                                                                                                                                                                      | 预期影响                                                                                                                                                                                                                                                                                                         | 当前状态                                                                 |
| :-------------------------- | :--------: | :-----------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------- |
| ePBS                        | MEV-Track  | 终极区块生产流程          | 在协议层面上将区块提议者和区块构建者分离，以应对抗审查和 MEV 风险缓解的需求                                                                                                                                                                                                                                 | (i) 在协议层面提供防止交易审查的机会 </br> (ii) 防止爱好者级验证者被更优化利润的机构玩家赶超 </br> (iii) 通过支持 Danksharding 升级来帮助以太坊扩展 | [研究中](/wiki/research/PBS/ePBS.md)[^18]                               |
| MEV-Burn                     | MEV-Track  | 终极区块生产流程          | 简单的内嵌 PBS 附加组件，用于平滑并重新分配 MEV 高峰                                                                                                                                                                                                                                                   | 提取的 ETH 将被销毁，从而使所有 ETH 持有者受益，而不仅仅是那些运行验证器的人。                                                                                           | [研究中](/wiki/research/PBS/ePBS.md#mev-burn)[^19]                    |
| ET                           | MEV-Track  | 终极区块生产流程          | 允许买家在一个无许可市场中购买提议执行负载的权利                                                                                                                                                                                                                                                       | 提议者与执行提议者分离：信标链提议者与执行提议者无关。执行提议者从无许可的执行票市场中选出，并有权将执行构建权转让给第三方。 </br>由于 ET 市场将成为协议组件，协议将对市场参与者及其支付意图进行审查。                                                       | [ET](/wiki/research/PBS/ET.md)、</br> APS-Burn[^20]                   |
| Inclusion Lists（IL）        | MEV-Track  | 终极区块生产流程          | 包容列表 - 让最去中心化的以太坊节点通过将自己的偏好输入区块构建过程中来抗审查                                                                                                                                                                                                                               | 防止区块构建者审查区块。通过提供强制包含交易的机制，让提议者保持一定的权威，避免当前的情形：如果没有强制交易包含机制，提议者只能选择不包含交易，或者他们在本地构建区块（对交易有最终决定权）并牺牲一些 MEV 收益。 | [研究中](/wiki/research/inclusion-lists.md)[^21] </br> 多重性组件 [^22] </br> COMIS [^23] |
| 分布式区块构建               | MEV-Track  | 终极区块生产流程          | 通过分布区块构建过程来实现去中心化                                                                                                                                                                                                                   | 去中心化构建者的不同部分： </br> (i) 选择交易的算法（区块构建交易排序） </br> (ii) 区块构建资源，尤其是在完全 Danksharding 下（分割大区块） </br> (iii) 增加额外的构建者服务（例如：预确认）                                                             | 研究中 </br> [预确认](/wiki/research/Preconfirmations/Preconfirmations.md)、</br> SUAVE[^24] |
| 应用层 MEV 最小化            | MEV-Track  | -                         | 应用层的努力，旨在最小化有害的 MEV                                                                                                                                                                                                                | 最小化技术目标： </br> (i) 前置交易 </br> (ii) 三明治攻击                                                                                                                                                                                                 | 示例[^25]                                                              |
| 预确认                       | MEV-Track  | -                         | 用户对交易执行的预确认，提供竞争性用户体验                                                                                                                                                                                                       | 区块构建者可以公开同意包含优先费用超过一定数量的交易，并发送用户一张确认收据，指明他们将在特定区块中包含该交易                                                                                                   | [研究中](/wiki/research/Preconfirmations/Preconfirmations.md)[^26]      |
| 增加最大有效余额（MAX_EFFECTIVE_BALANCE） | Staking Economics | 提高验证者上限           | 将以太坊验证者的最大余额从 32 ETH 提高，以减少大型质押者的开销                                                                                                                                                                               | 整合验证者，减少网络负载，简化大型质押者的操作                                                                                                 | [研究中](/wiki/research/eODS.md)[^27]，Pectra 升级已确认               |
| 更便宜的节点                   | Staking Economics | 改善节点运营商可用性     | 使用 Verkle 树和 SNARKs，使节点更便宜且更容易操作                                                                                                                                                                                                 | 降低 SSD 要求，加快同步时间，简化节点操作                                                                                                       | 研究/提案: [在 eps 节点研讨会中](/docs/eps/nodes_workshop.md)[^28]      |
| 限制验证者数量                 | Staking Economics | 探索总质押量上限         | 限制质押总量，以便更好地管理验证者之间的通信开销                                                                                                                                                                                                 | 防止过多的验证者参与，保持网络效率                                                                                                           | 研究/提案: [在研究中](/wiki/research/eODS.md)[^29]                      |
| 应对流动质押中心化             | Staking Economics | 探索解决流动质押中心化的方案 | 解决减少流动质押代币（LST）市场中心化的方案                                                                                                                                                                                                      | 防止大型 LST 提供者对网络的过度控制                                                                                                           | 研究/提案: [^30], [^31], [^32], [^33], [^34]                              |


                                              
### The Verge（验证优化）
与简化区块验证相关的升级

| 升级 | 领域 | 主题 | 描述 | 预期影响 | 当前状态 |
| :-- | :--: | :--: | :--: | :--: | :--: |

---

### The Purge（精简优化）
与降低运行节点的计算成本和简化协议相关的升级

---

### The Splurge（其他改进）
不属于上述类别的其他升级


## 资源

[^1] : [EIP-2982: Serenity Phase 0](https://eips.ethereum.org/EIPS/eip-2982), [[archived]](https://web.archive.org/web/20230928204358/https://eips.ethereum.org/EIPS/eip-2982)

[^2] : [EIP-4895: Beacon chain push withdrawals](https://eips.ethereum.org/EIPS/eip-4895), [[archived]](https://web.archive.org/web/20240415201815/https://eips.ethereum.org/EIPS/eip-4895)

[^3] : [VB's SSF notes](https://notes.ethereum.org/@vbuterin/single_slot_finality), [[archived]](https://web.archive.org/web/20240330010706/https://notes.ethereum.org/@vbuterin/single_slot_finality)

[^4] : [Sticking to 8192 signatures per slot post-SSF](https://ethresear.ch/t/sticking-to-8192-signatures-per-slot-post-ssf-how-and-why/17989). [[archived]](https://web.archive.org/web/20240105131126/https://ethresear.ch/t/sticking-to-8192-signatures-per-slot-post-ssf-how-and-why/17989)

[^5] : [A simple Single Slot Finality protocol](https://ethresear.ch/t/a-simple-single-slot-finality-protocol/14920), [[archived]](https://web.archive.org/web/20231214080806/https://ethresear.ch/t/a-simple-single-slot-finality-protocol/14920)

[^6] : [EIP-7441: Upgrade BPE to Whisk](https://eips.ethereum.org/EIPS/eip-7441), [[archived]](https://web.archive.org/web/20231001031437/https://eips.ethereum.org/EIPS/eip-7441)

[^7] : [EIP-7514: Add Max Epoch Churn Limit](https://eips.ethereum.org/EIPS/eip-7514), [[archived]](https://web.archive.org/web/20240309191714/https://eips.ethereum.org/EIPS/eip-7514)

[^8] : [EIP-7251:Increase the MAX_EFFECTIVE_BALANCE](https://eips.ethereum.org/EIPS/eip-7251), [[archived]](https://web.archive.org/web/20240324072459/https://eips.ethereum.org/EIPS/eip-7251)

[^9] : [Medium post on lattice encryption](https://medium.com/asecuritysite-when-bob-met-alice/so-what-is-lattice-encryption-326ac66e3175), [[archived]](https://web.archive.org/web/20230623222155/https://medium.com/asecuritysite-when-bob-met-alice/so-what-is-lattice-encryption-326ac66e3175)

[^10] : [VB's hackmd post on STARK signature aggregation](https://hackmd.io/@vbuterin/stark_aggregation), [[archived]](https://web.archive.org/web/20240313124147/https://hackmd.io/@vbuterin/stark_aggregation)

[^11] : [EIP-4844: Shard Blob Transactions](https://eips.ethereum.org/EIPS/eip-4844), [[archived]](https://web.archive.org/web/20240326205709/https://eips.ethereum.org/EIPS/eip-4844)

[^12] : [EIP-7594: PeerDAS](https://github.com/ethereum/EIPs/pull/8105) 

[^13] : [BoLd: dispute resolution protocol](https://github.com/OffchainLabs/bold/blob/e00b1c86124c3ca8c70a2cc50d9296e7a8e818ce/docs/research-specs/BOLDChallengeProtocol.pdf)

[^14] : [Fault proofs bring permissionless validation to the OP Sepolia testnet](https://blog.oplabs.co/open-source-and-feature-complete-fault-proofs-bring-permissionless-validation-to-the-op-sepolia-testnet/)

[^15] : [Parallel Zero-knowledge Virtual Machine](https://eprint.iacr.org/2024/387), [[archived]](https://web.archive.org/web/20240415180222/https://eprint.iacr.org/2024/387)

[^16] : [What is zkEVM](https://www.alchemy.com/overviews/zkevm), [[archived]](https://web.archive.org/web/20240129204732/https://www.alchemy.com/overviews/zkevm)

[^17] : [Types of ZK-EVMs](https://vitalik.eth.limo/general/2022/08/04/zkevm.html), [[archived]](https://web.archive.org/web/20240329112600/https://vitalik.eth.limo/general/2022/08/04/zkevm.html)

[^18] : [Barnabe - More pictures about proposers and builders](https://mirror.xyz/barnabe.eth/QJ6W0mmyOwjec-2zuH6lZb0iEI2aYFB9gE-LHWIMzjQ), [[archived]](https://web.archive.org/web/20240424010902/https://mirror.xyz/barnabe.eth/QJ6W0mmyOwjec-2zuH6lZb0iEI2aYFB9gE-LHWIMzjQ)

[^19] : [MEV burn—a simple design](https://ethresear.ch/t/mev-burn-a-simple-design/15590), [[archived]](https://ethresear.ch/t/mev-burn-a-simple-design/15590)

[^20] : [APS-Burn](https://mirror.xyz/barnabe.eth/QJ6W0mmyOwjec-2zuH6lZb0iEI2aYFB9gE-LHWIMzjQ#heading-aps-burn)

[^21] : [Inclusion lists](https://eips.ethereum.org/EIPS/eip-7547), [[archived]](https://web.archive.org/web/20240309191147/https://eips.ethereum.org/EIPS/eip-7547)

[^22] : [ROP-9: Multiplicity gadgets](https://efdn.notion.site/ROP-9-Multiplicity-gadgets-for-censorship-resistance-7def9d354f8a4ed5a0722f4eb04ca73b)

[^23] : [Committee-enforced inclusion sets (COMIS)](https://ethresear.ch/t/the-more-the-less-censored-introducing-committee-enforced-inclusion-sets-comis-on-ethereum/18835), [[archived]](https://web.archive.org/web/20240310000045/https://ethresear.ch/t/the-more-the-less-censored-introducing-committee-enforced-inclusion-sets-comis-on-ethereum/18835)

[^24] : [SUAVE](https://writings.flashbots.net/the-future-of-mev-is-suave), [[archived]](https://writings.flashbots.net/the-future-of-mev-is-suave)

[^25] : [Examples of app layer MEV minimization](https://herccc.substack.com/i/142947825/examples-of-the-defensive-side-of-mev)

[^26] : [Based preconfirmations](https://ethresear.ch/t/based-preconfirmations/17353), [[archived]](https://ethresear.ch/t/based-preconfirmations/17353)

[^27] : [EIP-7251: Increase the MAX_EFFECTIVE_BALANCE](https://eips.ethereum.org/EIPS/eip-7251)

[^28] : [Spin Up Your Own Ethereum Node - Ethereum.org](https://ethereum.org/en/developers/docs/nodes-and-clients/run-a-node/)

[^29] : [Paths to SSF](https://ethresear.ch/t/orbit-ssf-solo-staking-friendly-validator-set-management-for-ssf/19928)

[^30] : [Enshrining Liquid Staking/Decentralized Liquid Staking](https://notes.ethereum.org/@vbuterin/H1_5auGQd)

[^31] : [Enshrined LST from Arixon](https://ethresear.ch/t/enshrined-lst-allocating-stake-to-node-operators/11053)

[^32] : [Unbundling staking: towards rainbow staking](https://ethresear.ch/t/unbundling-staking-towards-rainbow-staking/11054)

[^33] : [Liquid Staking Maximalism design by Dankrad](https://ethresear.ch/t/liquid-staking-maximalism/11050)

[^34] : [Two-tiered staking from Mike Neuder](https://ethresear.ch/t/two-tiered-staking/11049)

[ethereum/EIPs github repository](https://github.com/ethereum/EIPs/tree/master#ethereum-improvement-proposals-eips)

[Roadmap on Ethereum.org](https://ethereum.org/en/roadmap/)

[ethroadmap.com](https://ethroadmap.com/)

