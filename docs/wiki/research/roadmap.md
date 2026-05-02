# Ethereum 协议路线图 

Ethereum 的开发理念对协议演进持开放态度，并且为了获得值得改变的利益而规避一定的风险。随着我们对 Ethereum 的了解和经验的增长，研究人员和开发人员正在构思如何应对网络的挑战和限制。核心协议存在多年以来已经发生了 [许多变化](/wiki/protocol/history.md)。大多数这些变化都是一些共同目标的一部分，我们可以称之为路线图。 

尽管没有官方路线图，也没有权威机构可以规定它，但社区仍进行了广泛的讨论，以某些方式指导协议的开发。通过就一些目标达成一致并就当前的开发状态达成共识，社区、开发和研究团队共同努力，在这个抽象路线图上取得进展。 

## 无限花园

> *Ethereum 是 NOT 一个有明确终点线的零和游戏，而是一个我们想要连续玩的游戏。为了实现这一目标，无限花园需要定期升级，包括安全性、可扩展性或可持续性等主题，直到达到僵化状态。在那之后，可能只会有一些修剪——到处都是。*

## 核心研发

核心协议的讨论、资源和所有研发都是完全开放、免费和公开的。任何人都可以了解它 (正如您可能在这个 wiki 中所做的那样)，而且任何人都可以参与。没有一组个人可以推动核心协议的更改，Ethereum 社区可以发出声音来帮助引导讨论。要了解有关协议核心研发的更多信息，请阅读 [关于它的维基页面](/wiki/dev/core-development.md)。

## 路线图概述 

虽然 Ethereum 的开发没有遵循单一的路线图，但我们可以跟踪当前的研发工作，以绘制正在发生和未来可能发生的变化。 
Vitalik 的图表 (2023 年 12 月) 是一个流行的概述，描绘了当前核心研发的许多领域：

![Ethereum roadmap updated by V.B. Dec2023](../research/img/full_roadmap2024_1600x1596.webp)

在此概述中，不同的领域与相关类别耦合，形成各种“冲动”。其中许多盒子在这个 wiki 上都有自己的页面，您可以在其中了解更多信息。  

### 合并

与从 Proof-of-Work 到 Proof-of-Stake 的转换相关的升级。此次合并于 2022 年 9 月 15 日星期四 06:42:42 UTC 成功实现，使网络年化电量消耗减少了 99.988% 以上。然而，这个类别还跟踪后续的升级，这些升级可以用来改进共识机制并平滑我们在合并后遇到的问题。 

**IMPLEMENTED**
| 升级 | 描述 | 效果 | 最先进的 |
|:------------------------------------ |:---------------------------------------------------------------------------------------------------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------------------------------------- |
| 启动 Beacon Chain | Ethereum 转向 Proof-of-Stake 共识机制的关键一步 | Beacon Chain 作为连接到 Ethereum 的独立网络启动，引导验证者为合并做准备。 | 已发货 </br> EIP-2982<span markdown='1'>[^1]</span> |
| 合并 Execution Layer 和 Consensus Layer | Ethereum 的 Execution Layer 与 Beacon Chain 合并 (Consensus Layer) | Proof-of-Work 活动停止，网络的共识机制转向 Proof-of-Stake。 验证者具有处理所有交易的有效性并提出区块 | 区块的角色和责任已发货 |
| 启用提款 | Ethereum 向 Proof-of-Stake 共识机制过渡的三部分过程的最后一个 | 验证者可以添加提现凭证，Beacon Chain 扫除所有不活动的 ETH | 已发货 </br>EIP-4895[^2] |

**TODO** 
| 升级 | 描述 | 预期效果 | 最先进的 |
| :----------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :---------------------------------------------------------------------------------------------------------------- |
| Single Slot Finality (SSF) | 区块可以在同一个时隙 | 中提出并最终确定。 (i) 对应用程序来说更方便 (交易完成时间提高了一个数量级，即 12 秒而不是 12 分钟，这意味着对所有人来说 UX 更好。在 [完整 Rollup 缩放](#the-surge) 下，实现了实时 SNARK 证明，Single Slot Finality 也意味着更快的 L2 桥接)，(ii) 更难攻击 (可以消除多个区块 MEV 重组，并降低共识机制的复杂性) | 研究中 </br>(i) VB 的 SSF 笔记[^3] </br>(ii) 8192 签名 post-SSF[^4] </br>(iii) 简单 SSF 协议[^5] |
| 单一秘密领袖选举 (SSLE) | 允许当选的区块提议者在 区块发布之前保持私有，以防止 DoS 攻击 | 只有被选中的验证者知道它已被选中来提议区块。 | 研究中 </br>EIP-7441[^6] |
| 启用更多验证者 | 高效协调不断增加的验证者数量以实现 SSF 并尽可能实现最佳权衡的技术挑战 | 更大的冗余、更广泛的提议者范围、更广泛的证明者数组以及整体增强的弹性 | 研究中 </br> (i) EIP-7514[^7] </br>(ii) EIP-7251[^8] </br> (iii) 8192 签名[^5] |
| 量子安全签名 | 抗量子密码算法的积极研究和集成 | 量子安全、聚合友好的签名将增强针对量子攻击的协议安全性 | 研究中 </br> (i) 基于格[^9] </br>(ii) STARK 基于 [^10] 系统 |
### The Surge
通过 Rollup 和数据分片进行与可扩展性相关的升级。 

**IMPLEMENTED**
| 升级 | 轨道 | 主题 | 描述 | 效果 | 最先进的 |
| :----------------- | :---: | :------------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------: | :-----------------------: | :------------------------- |
| Proto-Danksharding | - | 基本 Rollup 缩放 | 我们可以停止在 Ethereum 上永久存储 Rollup 数据，并将数据移动到临时“blob”存储中，一旦不再需要，该存储就会从 Ethereum 中删除 | 降低交易成本 | 已发货 </br>EIP-4844[^11] |

**TODO** 
| 升级 | 轨道 | 主题 | 描述 | 预期效果 | 最先进的 |
| :---------------------------------------------- | :---: | :-------------------------: | :----------------------------------------------------------------------------------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Danksharding | - | 完整 Rollup 缩放 | Danksharding 是从 Proto-Danksharding 开始的 Rollup 缩放的完整实现 | Ethereum 上的大量空间可供 Rollup 转储压缩的交易数据 | 正在研究 </br> |
| Data Availability Sampling (DAS) | - | 完整 Rollup 缩放 | Data Availability Sampling 是网络检查数据是否可用的一种方式，而不会给任何个人带来太大压力 (i) 确保 Rollup 运营商在 EIP-4844 后提供其交易数据 (ii) 确保区块生产者提供所有数据以保护轻客户端 (iii) 在 Proposer-Builder Separation 下，仅区块构建者需要处理整个区块，其他验证者将使用 Data Availability Sampling | 进行验证研究中 </br> EIP-7594[^12] |
| 拆卸 Rollup 辅助轮 | - | 基本和完整 Rollup 缩放 | (i) 乐观 Rollup 故障证明器 </br> (ii) ZK-EVM </br> (iii) Rollup 互操作性 | (i) 乐观的 Rollup 拥有实时证明系统将解决 L2 的审查风险 </br>(ii) 通过 zkEVM (支持 zero-knowledge proof 的 EVM 兼容虚拟机，在不牺牲链的安全性和去中心化方面的情况下，对 Ethereum 的可扩展性和隐私性进行大规模改进 (iii) L1 排序器或 Ethereum L1 提议者具有给定的 Rollup 排序权限将带来更好的可信中立性和安全性，并提供 Rollup L1 兼容性 | 研究中 </br> (i)Arbitrum BoLD[^13] </br> 乐观炮[^14] </br> (ii) ZK-EVMs [^15] [^16] [^17] </br> (iii) [ET](/wiki/research/PBS/ET.md), </br> [基于排序和 Preconfirmations](/wiki/research/Preconfirmations/BasedSequencingPreconfs.md) |
| 量子安全且可信且无需设置承诺 | - | - | 将 KZG 承诺替换为不需要可信设置且量子安全的承诺 | 量子安全承诺 | 正在研究 </br> |

### The Scourge
与抗审查性相关的升级、去中心化以及减轻 MEV 和流动质押/池化的协议风险。 

**IMPLEMENTED**
| 升级 | 轨道 | 主题 | 描述 | 效果 | 最先进的 |
| :-------- | :-------: | :-------------------------------: | :------------------------: | :-------------------------------------------------------------------------------------------------------------------------------------: | :----------------------------------------------- |
| MEV-Boost | MEV- 追踪 | Endgame 区块生产管线 | 额外协议 MEV 市场 | Ethereum 社区通过协议外市场成功地将 MEV 商品化 (部分)。 MEV 的大部分现在转到验证者。 | [已发货](/wiki/research/PBS/mev-boost.md) </br> |
| 增加 MAX_EFFECTIVE_BALANCE | 质押经济学 | 提高验证者上限 | 将 Ethereum 验证者的最大有效余额从 32 ETH 增加到 2048 ETH | 整合验证者，减少网络负载，并简化大型质押者的操作 | 已发货 </br> EIP-7251[^27] |

**TODO** 

| 升级 | 轨道 | 主题 | 描述 | 预期效果 | 最先进的 |
| :--------------------------------- | :-------: | :-------------------------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :--------------------------------------------------------------------------------------------------------- |
| ePBS | MEV- 追踪 | Endgame 区块生产管线 | 区块提议者和 区块构建者在协议级别分离，因为反审查和 MEV 风险缓解原因 | (i) 创造机会防止交易在协议层面进行审查验证者 (ii) 防止业余爱好者验证者被机构参与者击败，机构参与者可以更好地优化其区块的盈利能力构建 </br> (iii) 有助于扩展 Ethereum 通过启用 Danksharding 升级 | [研究中](/wiki/research/PBS/ePBS.md)[^18] </br> |
| MEV - 刻录 | MEV- 追踪 | 终局之战区块生产管线 | 一个简单的 PBS 附加组件，用于平滑和重新分配 MEV 峰值 | 提取的 ETH 将被烧毁，因此使所有 ETH 持有者受益，而不仅仅是那些运行验证者的人。 | [研究中](/wiki/research/PBS/ePBS.md#mev-burn)[^19] |
| ET | MEV- 追踪 | 终局之战区块生产管线 | 一个无需许可的市场，允许买家购买提出执行载荷的权利。 | 证明者 - 提议者分离：信标提议者与执行提议者无关。执行提议者是从无需许可的执行票市场中选择的，并且可以选择将执行构建权转让给第三方。 </br>由于 ET 市场将成为一个协议小工具，协议将对谁进入市场以及他们愿意支付多少钱进行内省 | [ET](/wiki/research/PBS/ET.md), </br>APS- 刻录[^20] |
| IL | MEV- 追踪 | Endgame 区块生产管线 | Inclusion Lists - 最去中心化的 Ethereum 群体通过将他们的偏好输入到链的构建中来对抗审查制度的一种方式 | 防止区块构建者审查区块。通过提供一种强制包含交易的机制，让提议者保留一些权限，避免了目前的情况，在没有任何强制交易包含机制的情况下，提议者面临的选择是要么对被包含的交易说不，要么构建区块本地 (对交易有最终决定权) 并牺牲一些 MEV 奖励 | [研究中](/wiki/research/inclusion-lists.md)[^21] </br> 多重小工具 [^22] </br> COMIS [^23] |
| 分布式区块构建 | MEV- 追踪 | 终局之战区块生产管线 | 通过分发来分散区块构建过程 | 分散构建者的不同部分： </br> (i) 选择交易的算法 (区块构建交易排序) </br> (ii) 区块构建的资源，特别是在完整的情况下 Danksharding (拆分大区块)</br> (iii) 添加额外的构建者服务 (例如 Preconfirmations) | 研究中 </br> [Preconfirmations](/wiki/research/Preconfirmations/Preconfirmations.md),</br> SUAVE[^24] |
| 应用层 MEV 最小化 | MEV- 追踪 | - | 应用层努力尽量减少有害 MEV | 最小化技术的目标是：</br>(i) 抢先交易和 </br>(ii) 三明治攻击 | 示例[^25] |
| Preconfirmations | MEV- 追踪 | - | 用户 Preconfirmations 在 交易上执行，在 Ethereum 交互中获得有竞争力的用户体验 | 区块构建者可以公开同意包含交易，并支付超过一定金额的优先费，并向用户发送收据，表明他们打算将交易包含在特定的区块 | [研究中](/wiki/research/Preconfirmations/Preconfirmations.md)[^26] |
| 更便宜节点 | 质押经济学 | 提高节点操作员可用性 | 使用 Verkle Tree 和 SNARK 使节点更便宜且更易于操作 | 更低的 SSD 要求、更快的同步时间、更轻松的节点操作 | 研究/提案：[eps 节点研讨会](/docs/eps/nodes_workshop.md)[^28] |
| 封盖验证者套装 | 质押经济学 | 探索总股权上限 | 限制权益总额以管理验证者 | 之间的通信开销防止验证者过度参与，保持网络效率 | 研究/提案：[研究中](/wiki/research/eODS.md)[^29] |
| 对抗 LST 集权化 | 质押经济学 | 探索流动质押中心化解决方案 | 减少 Liquid Stake Token (LST) 市场中心化的解决方案 | 防止大型 LST 提供商获得对网络的过多控制 | 研究/提案：[^30]、[^31]、[^32]、[^33]、[^34] |


                                              
### The Verge
与验证区块相关的升级更容易
light- 客户端安全和状态验证的简洁证明。

| 升级 | 轨道 | 主题 | 描述 | 预期效果 | 最先进的 |
|:--------------------------------:|:---------------:|:------------------------:|:------------------------------------------------------------------------:|:-----------------------------------------------------:|:--------------------------:|
| Data Availability Sampling (DAS) | 完整 Rollup | blob 数据验证 | 轻客户端的概率 blob 采样，无需完整下载。 | 以最小的开销保护 L2 DA 和轻客户端。 | 正在研究 / EIP-7594 ([eips.ethereum.org](https://eips.ethereum.org/EIPS/eip-7594)) |
| Verkle Tree 承诺 | 无国籍 | 可验证的 Trie 证明 | 将 Merkle 证明替换为向量承诺以获得 O(1) 大小的证明。 | 大幅缩小的样张；精简轻客户端。 | 草案 / EIP-7736 ([eips.ethereum.org](https://eips.ethereum.org/EIPS/eip-7736)) |

### The Purge  

通过修剪历史和非活动状态来解决协议和数据膨胀问题。  

Vitalik 的“可能的未来”第 5 部分：“清除”强调历史和状态到期，以平衡持久性与效率 ([Ethereum 协议的可能未来，第 5 部分：清除](https://vitalik.eth.limo/general/2024/10/26/futures5.html))。  

| 升级 | 主题 | 描述 | 预期效果 | 最先进的 |
|:---------------------------------|:-----------------------:|:---------------------------------------------------------------------------------------------------------------------------:|:-----------------------------------------------:|:--------------------------------------------------------------------:|
| 历史记录到期 (EIP-4444) | 修剪旧区块 | 客户端修剪 Execution Layer 区块和超过 1 年的收据，限制磁盘使用情况。 | 降低全节点的存储要求。 | 草稿 / EIP-4444[^2search0] |
| 状态到期 (EIP-7736) | 基于 Verkle 的到期 | 删除在规定时间内未访问的不活动 Verkle 叶子；需要时通过证明复活。 | 将活动状态大小缩小至 ~20–50 GB。 | 草稿 / EIP-7736[^3search0] |

---

### The Splurge  

包含其他功能，虽然不紧急，但极大地提高了可用性、安全性和长期弹性。  

| 升级 | 类别 | 描述 | 预期效果 | 最先进的 |
|:------------------------------------|:----------------------:|:-----------------------------------------------------------------------------------------------------------------------------:|:-------------------------------------------------:|:---------------------------------------------------------------------------------------:|
| Account Abstraction (EIP-4337) | UX / 钱包 | 引入了用于智能合约钱包交易的 UserOperation 内存池、捆绑器和出纳员，无需更改共识。 | 实现社交恢复、赞助交易、批量操作。 | 已发货 / EIP-4337[^5search0] |
| 量子安全签名 | 面向未来 | 研究基于哈希、基于格子、基于 STARK 的多签名方案，以替代验证者和用户签名的 BLS/ECDSA。 | 保护 PoS 和用户帐户免受量子攻击。 | 研究 / 基于哈希 PQS[^6search1]、NIST PQC 概述[^6search3] |
| 形式化验证工具 | 安全/审核 | 扩展工具链，使用 Coq、SMT、TLA+、Dafny、Isabelle/HOL 证明协议客户端和 智能合约的正确性。 | 更好地保证协议不变性和客户端安全性。 | 不断发展 / [ethereum.org][^7search0]，基准测试工具[^7search1] |

---

## 资源

[^1]: [EIP-2982：宁静阶段 0](https://eips.ethereum.org/EIPS/eip-2982)，[[已存档]](https://web.archive.org/web/20230928204358/https://eips.ethereum.org/EIPS/eip-2982)

[^2]: [EIP-4895：Beacon Chain 推送提款](https://eips.ethereum.org/EIPS/eip-4895)，[[已存档]](https://web.archive.org/web/20240415201815/https://eips.ethereum.org/EIPS/eip-4895)

[^3]: [VB 的 SSF 笔记](https://notes.ethereum.org/@vbuterin/single_slot_finality), [[已存档]](https://web.archive.org/web/20240330010706/https://notes.ethereum.org/@vbuterin/single_slot_finality)

[^4]: [坚持 8192 签名每个时隙 post-SSF](https://ethresear.ch/t/sticking-to-8192-signatures-per-slot-post-ssf-how-and-why/17989)。 [[已存档]](https://web.archive.org/web/20240105131126/https://ethresear.ch/t/sticking-to-8192-signatures-per-slot-post-ssf-how-and-why/17989)

[^5]: [一个简单的单一时隙最终确定性协议](https://ethresear.ch/t/a-simple-single-slot-finality-protocol/14920), [[已存档]](https://web.archive.org/web/20231214080806/https://ethresear.ch/t/a-simple-single-slot-finality-protocol/14920)

[^6]: [EIP-7441：将 BPE 升级到 Whisk](https://eips.ethereum.org/EIPS/eip-7441)，[[已存档]](https://web.archive.org/web/20231001031437/https://eips.ethereum.org/EIPS/eip-7441)

[^7]: [EIP-7514：添加最大 epoch 流失限制](https://eips.ethereum.org/EIPS/eip-7514)，[[已存档]](https://web.archive.org/web/20240309191714/https://eips.ethereum.org/EIPS/eip-7514)

[^8]: [EIP-7251:增加 MAX_EFFECTIVE_BALANCE](https://eips.ethereum.org/EIPS/eip-7251),[[已存档]](https://web.archive.org/web/20240324072459/https://eips.ethereum.org/EIPS/eip-7251)

[^9]: [关于点阵加密的中型帖子](https://medium.com/asecuritysite-when-bob-met-alice/so-what-is-lattice-encryption-326ac66e3175)，[[已存档]](https://web.archive.org/web/20230623222155/https://medium.com/asecuritysite-when-bob-met-alice/so-what-is-lattice-encryption-326ac66e3175)

[^10]: [VB 在 STARK 签名聚合上的 hackmd 帖子](https://hackmd.io/@vbuterin/stark_aggregation)，[[已存档]](https://web.archive.org/web/20240313124147/https://hackmd.io/@vbuterin/stark_aggregation)

[^11]: [EIP-4844：分片 blob 交易](https://eips.ethereum.org/EIPS/eip-4844)，[[已存档]](https://web.archive.org/web/20240326205709/https://eips.ethereum.org/EIPS/eip-4844)

[^12]: [EIP-7594：PeerDAS](https://github.com/ethereum/EIPs/pull/8105) 

[^13]: [BoLd：争议解决协议](https://github.com/OffchainLabs/bold/blob/e00b1c86124c3ca8c70a2cc50d9296e7a8e818ce/docs/research-specs/BOLDChallengeProtocol.pdf)

[^14]: [故障证明为 OP Sepolia 测试网带来无需许可的验证](https://blog.oplabs.co/open-source-and-feature-complete-fault-proofs-bring-permissionless-validation-to-the-op-sepolia-testnet/)

[^15]: [并行零知识虚拟机](https://eprint.iacr.org/2024/387)、[[已存档]](https://web.archive.org/web/20240415180222/https://eprint.iacr.org/2024/387)

[^16]: [什么是 zkEVM](https://www.alchemy.com/overviews/zkevm), [[已存档]](https://web.archive.org/web/20240129204732/https://www.alchemy.com/overviews/zkevm)

[^17]: [ZK-EVM 的类型](https://vitalik.eth.limo/general/2022/08/04/zkevm.html)、[[已存档]](https://web.archive.org/web/20240329112600/https://vitalik.eth.limo/general/2022/08/04/zkevm.html)

[^18]: [Barnabe - 有关提议者和 构建者的更多图片](https://mirror.xyz/barnabe.eth/QJ6W0mmyOwjec-2zuH6lZb0iEI2aYFB9gE-LHWIMzjQ)，[[已存档]](https://web.archive.org/web/20240424010902/https://mirror.xyz/barnabe.eth/QJ6W0mmyOwjec-2zuH6lZb0iEI2aYFB9gE-LHWIMzjQ)

[^19]: [MEV 烧伤——一个简单的设计](https://ethresear.ch/t/mev-burn-a-simple-design/15590)，[[已存档]](https://ethresear.ch/t/mev-burn-a-simple-design/15590)

[^20]: [APS- 烧录](https://mirror.xyz/barnabe.eth/QJ6W0mmyOwjec-2zuH6lZb0iEI2aYFB9gE-LHWIMzjQ#heading-aps-burn)

[^21]: [Inclusion Lists](https://eips.ethereum.org/EIPS/eip-7547), [[已存档]](https://web.archive.org/web/20240309191147/https://eips.ethereum.org/EIPS/eip-7547)

[^22]: [ROP-9：多重小工具](https://efdn.notion.site/ROP-9-Multiplicity-gadgets-for-censorship-resistance-7def9d354f8a4ed5a0722f4eb04ca73b)

[^23]: [委员会强制执行的包含集 (COMIS)](https://ethresear.ch/t/the-more-the-less-censored-introducing-committee-enforced-inclusion-sets-comis-on-ethereum/18835)、[[已存档]](https://web.archive.org/web/20240310000045/https://ethresear.ch/t/the-more-the-less-censored-introducing-committee-enforced-inclusion-sets-comis-on-ethereum/18835)

[^24]: [SUAVE](https://writings.flashbots.net/the-future-of-mev-is-suave), [[已存档]](https://writings.flashbots.net/the-future-of-mev-is-suave)

[^25]: [应用层 MEV 最小化示例](https://herccc.substack.com/i/142947825/examples-of-the-defensive-side-of-mev)

[^26]: [基于 Preconfirmations](https://ethresear.ch/t/based-preconfirmations/17353)，[[已存档]](https://ethresear.ch/t/based-preconfirmations/17353)

[^27]: [EIP-7251：增加 MAX_EFFECTIVE_BALANCE](https://eips.ethereum.org/EIPS/eip-7251)

[^28]: [旋转你自己的 Ethereum 节点 - Ethereum.org](https://ethereum.org/en/developers/docs/nodes-and-clients/run-a-node/)

[^29]: [SSF 的路径](https://ethresear.ch/t/orbit-ssf-solo-staking-friendly-validator-set-management-for-ssf/19928)

[^30]: [内置化流动性质押/去中心化流动性质押](https://notes.ethereum.org/@vbuterin/H1_5auGQd)

[^31]: [来自 Arixon 的供奉 LST](https://ethresear.ch/t/enshrined-lst-allocating-stake-to-node-operators/11053)

[^32]: [分拆质押：迈向彩虹质押](https://ethresear.ch/t/unbundling-staking-towards-rainbow-staking/11054)

[^33]: [Dankrad 设计的流动性质押极简主义](https://ethresear.ch/t/liquid-staking-maximalism/11050)

[^34]: [Mike Neuder 的两层质押](https://ethresear.ch/t/two-tiered-staking/11049)

[ethereum/EIPs github 仓库](https://github.com/ethereum/EIPs/tree/master#ethereum-improvement-proposals-eips)

[Ethereum.org 上的路线图](https://ethereum.org/en/roadmap/)

[ethroadmap.com](https://ethroadmap.com/)

