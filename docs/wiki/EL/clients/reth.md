# 雷斯的建筑

## 概述

该图代表了 Reth 架构的粗略组件流程：

<img src="images/el-architecture/reth-architecture-overview.png" width="1000"/>

- **Engine**：与其他客户端类似，它是Reth的主要驱动程序。
- **同步**：Reth有同步历史和实时两种模式
- **Pipeline**: The pipeline performs historical sync in a sequential manner, enabling us to optimize each stage of the synchronization process. The pipeline is split into stages , where a [stage](https://paradigmxyz.github.io/reth/docs/reth_stages/trait.Stage.html) is a trait that provides us with a function to execute the stage or unwind(undo) it. Currently the pipeline has 12 stages that can be configured, with the first two running separately, the pipeline proceeds top to bottom except when there is a problem encountered then it proceeds to unwind from the issue stage upwards :
  1. **HeaderStage**: Header verification stage.
  2. **BodyStage**: Download 区块 over P2P.
  3. **SenderRecoveryStage**: The computation is costly as it retrieves the sender's address from the 签名 for each 交易 in the 区块's body.
  4. **ExecutionStage**: The most time-consuming & computationally heavy stage involves taking the sender, 交易, and header and executing them within the REVM. This process generates receipts and change sets.更改集是充当哈希映射的数据结构，描述单个区块内帐户之间发生的修改。 In addition, the execution stage operates on a plain state that contains only the addresses and account information in the form of key-value pairs.
  5. **MerkleStage**(unwind)：在执行流程中跳过，在展开时使用。
  6. **AccountHashingStage**: Required by the merkle stage,we take the plain state and apply a hashing function to it. Then, we save the resulting hashed account in a database specifically designed for storing accounts.
  7. **StorageHashingStage**: Similar to above but for storage.
  8. **MerkleStage**(execute): generates a 状态根 by using the 哈希 produced by the two preceding stages and then checks if the resulting 状态根 is accurate for the given 区块.
  9. **TransactionLookupStage**：辅助阶段，允许我们进行交易查找。
  10. **IndexStorageHistoryStage**: Enables us to retrieve past data, the execution phase generates the change set, which then indexes the data that existed prior to the execution of the 区块. Enables us to retrieve the historical data for any given 区块 number.
  11. **IndexAccountHistoryStage**: Similar to above.
  12. **FinishStage**:We notify that the engine is now capable of receiving new 分叉选择 updates from the 共识层's.
- **BlockchainTree**：当我们在同步过程中接近链的末端时，我们过渡到区块链树。当状态根验证和执行在内存中进行时，同步发生在接近尖端的地方。
- **数据库**：当区块被规范化时，它会被移动到数据库中
- **Provider**：对数据库的抽象，提供实用函数来帮助我们避免直接访问底层数据库的键和值。
- **Downloader**: Retrieves 区块 and headers using 点对点 (P2P) networks. This tool is utilized by the pipeline during its initial two stages and by the engine in the event that it need to bridge the gap at the tip.
- **P2P**: When we approach the tip, we transfer the 交易 we have read over P2P to the 交易 pool.
- **交易池**：包括 DDoS 缓解措施。 Consists of 交易 arranged in ascending order based on the gas price preferred by the users.
- **载荷构建者**: Extracts the initial n 交易 in order to construct a fresh 载荷.
- **Pruner**：允许我们拥有一个全节点。一旦区块被 区块链树规范化，我们必须等待额外的 64 个区块才能达到最终确定。一旦最终确定过程完成，我们可以确定区块不会进行重组。因此，如果我们正在操作全节点，我们可以选择使用修剪器消除旧的区块。

## 存储

Reth 主要利用 mdbx 数据库。此外，它还提供了几个有价值的抽象，通过启用数据转换、压缩、迭代、写入和查询功能来增强其底层数据库。这些抽象旨在允许 reth 可以选择更改其底层 DB、mdbx，同时对现有存储抽象进行最小程度的修改。

**编解码器**

这个[crate](https://github.com/paradigmxyz/reth/tree/main/crates/storage/codecs)可以为各种目的创建不同的编解码器。在这种情况下使用的主要编解码器是[Compact Trait](https://github.com/paradigmxyz/reth/blob/6d7cd53ad25f0b79c89fd60a4db2a0f2fe097efe/crates/storage/codecs/src/lib.rs#L43)，它可以压缩数据，例如通过压缩前导零来压缩无符号整数，以及访问列表、标头等结构。

**DB 抽象**

[数据库特征](https://github.com/paradigmxyz/reth/blob/e158542d31bf576e8a6b6e61337b62f9839734cf/crates/storage/db/src/abstraction/database.rs#L12) 是基本抽象，它提供对低级数据库中的交易的只读或读/写访问。

[cursor](https://github.com/paradigmxyz/reth/blob/e158542d31bf576e8a6b6e61337b62f9839734cf/crates/storage/db/src/abstraction/cursor.rs#L13) 支持对数据库中的值进行迭代，并提供一种快速方法来检索交易或 区块。它在计算 Merkle 根时特别有用，因为顺序值访问比随机查找快得多。另外，如果我们有大量数据要写入，排序写入速度会快很多。游标允许我们通过提供方便的函数来写入排序或未排序的数据来优化我们的方法。

**表格**

|表|关键|价值|描述 |
| -------------------------- | ----------------------------------- | ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|规范标题 |区块编号 |标题哈希 |存储由标头哈希 | 索引的区块编号
|标题终端困难 |区块编号 |紧凑U256 |负责存储从区块标头获得的总难度值。尽管它通常用于工作量证明系统，但目前尚未使用。                                                                                                                                                                                                                                                                                                        |
|标题编号 |区块哈希 |区块编号 |这是一个实用程序表，它存储与标头关联的区块编号。                                                                                                                                                                                                                                                                                                                                                                                                        |
|标题 |区块编号 |标题|存储标头主体。                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| BlockBodyIndi​​ces | 块体索引区块编号 |存储块体索引 |存储区块索引，其中包含交易的索引及其计数。这使我们能够确定区块中包含哪些交易编号。                                                                                                                                                                                                                                                                                                                 |
|布洛克奥默斯 |区块编号 |存储块Ommers |存储区块的叔叔/奥默斯，这是包含的侧面区块(用于工作量证明) |
|块提款 |区块编号 |存储区块提款 |存储区块提款。                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| 交易 |发送号码 |交易签名无哈希 |这里交易正文存储为普通交易编号索引。此信息包括交易的总数和已执行的交易的数量。此外，它使我们能够轻松检索单个交易。                                                                                                                                                                                                         |
|交易哈希编号 |交易哈希 |发送号码 |存储由交易哈希索引的交易编号。                                                                                                                                                                                                                                                                                                                                                                                                                    |
|交易区块 |发送号码 |区块编号 |存储最高交易编号到区块编号的映射。允许我们获取给定交易号码的区块号码。                                                                                                                                                                                                                                                                                                                                    |
|收据|发送号码 |收据|存储按交易编号索引的交易收据。                                                                                                                                                                                                                                                                                                                                                                                                                        |
|字节码 | B256 | B256 字节码 |编译并存储所有智能合约的字节码。将有多个具有相同字节码的帐户。因此，有必要实现一个引用计数指针。                                                                                                                                                                                                                                                                                           |
|普通帐户状态 |地址 |账户 |存储[帐户](https://github.com/paradigmxyz/reth/blob/fb960fb3e45e11c24125ccb4bd93f2e2e21ce271/crates/primitives/src/account.rs#L15)的当前状态，即普通状态，由帐户地址索引。普通状态在执行阶段更新。                                                                                                                                                                                                         |
|普通存储状态 |地址，子密钥 = B256 |存储入口 |存储存储键的当前值，子键为存储键的哈希。关于子键：mdbx 允许我们复制表(表内的重复值)，这可以更快地访问某些值。                                                                                                                                                                                                                                                        |
|账户历史 | ShardedKey<Address> |区块编号列表 |存储指向区块变更集的指针，其中包含每个帐户密钥的修改。每个帐户都与一条修改记录相关联，表示为区块列表。例如，如果我们要检索区块处的账户余额为100万，我们需要确定该账户被修改的下一个区块。如果下一次修改发生在区块编号 100 万和 1，我们需要从下表中获取该帐户的更改集。 |
|                            |
|存储历史 |存储 ShardedKey |区块编号列表 |存储指向区块编号变更集的指针以及每个存储键的更改。这允许我们对变更集进行索引并查找历史记录中发生的变更 |
|帐户更改集 |区块编号，子密钥 = 地址 |交易前账户 |帐户的状态在任何更改帐户的交易之前存储，例如当帐户创建、自毁、空时访问或修改其余额或随机数时。因此，对于每个区块号码。因此，我们拥有每个区块和帐户地址的先前值。                                                                                                                                                                  |
|存储更改集 |块编号地址，子密钥 = B256 |存储入口 |在特定交易更改存储之前保留存储的状态。因此，对于每个区块号、账户地址和子密钥作为存储密钥，我们可以获得之前的存储值。执行阶段会修改该表及其上方的表。这些表用于 merkle Trie 计算，该计算要求值是增量的。它们还用于 JSON-RPC API 执行的任何历史记录跟踪。                        |
|哈希帐户 | B256 | B256账户 |存储由 keccak256(Address) 索引的帐户的当前状态。该表是为Merkle 化准备和状态根的计算而准备的。此表和下表由 merkle Trie 使用，对于 merkle Trie 的第一次计算，我们需要排序的哈希地址 |
|哈希存储| B256，子密钥 = B256 |存储入口 |将由 keccak256(Address) 索引的当前存储值和子密钥存储为存储密钥 keccak256(key) 的哈希。就像上面对 Merkle 化有用的那样，因为散列地址/键已排序。                                                                                                                                                                                                                                                                           |
| AccountsTrie |存储小食 |存储分支节点 |存储当前状态的 Merkle Patricia 树。                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|存储特里 | B256，子密钥= StoredNibblesSubKey |存储特里条目 |来自 HashedAddress => NibblesSubKey => 中间值。此表和上表存储了merkle Trie 计算所需的节点 |
|交易发送者 |发送号码 |地址 |存储每个交易的 交易发件人。它需要加速执行阶段，并允许获取签名者，而无需执行计算成本高昂的交易签名者恢复 |
|阶段检查点 |阶段 ID |阶段检查站 |存储每个阶段的最高同步区块编号和特定于阶段的检查点。                                                                                                                                                                                                                                                                                                                                                                                               |
|阶段检查点进度 |阶段 ID | vec<u8> |存储任意数据以跟踪阶段首次同步进度。该表和上表使我们能够知道阶段停止的位置并确定下一步该做什么。                                                                                                                                                                                                                                                                                                          |
|修剪检查点|修剪段|修剪检查点|记录剪枝过程中各段的最大剪枝区块数和剪枝模式。这使我们能够确定数据修剪的程度，包括消除变更集及其相应的索引，以消除历史数据，只留下要检索的最新数据，即获取提示。                                                                                                                   |
|版本历史 | u64 | 64客户端版本 |存储客户端版本的历史记录，这些版本已使用按 unix 时间戳秒索引的写入权限访问过数据库。                                                                                                                                                                                                                                                                                                                                                    |
