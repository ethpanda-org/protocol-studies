# Reth 的框架

## 概述

这张图片展示了 Reth 架构的大致组件流程：

<img src="images/el-architecture/reth-architecture-overview.png" width="1000"/>

- **引擎（Engine）**：与其他客户端类似，它是 Reth 的主要驱动程序。  
- **同步（Sync）**：Reth 具有两种同步模式：历史同步和实时同步。  
- **管道（Pipeline）**：管道以顺序方式执行历史同步，使我们能够优化同步过程的每个阶段。管道被划分为多个阶段，其中[阶段](https://paradigmxyz.github.io/reth/docs/reth_stages/trait.Stage.html)是一个特性（trait），它提供执行该阶段或回溯（撤销）它的功能。目前，管道包含 12 个可配置的阶段，前两个阶段单独运行。除非遇到问题，否则管道按照自上而下的顺序执行；如果出现问题，它会从发生问题的阶段向上回溯：  

  1. **HeaderStage（区块头阶段）**：区块头验证阶段。  
  2. **BodyStage（区块体阶段）**：通过 P2P 网络下载区块。  
  3. **SenderRecoveryStage（发送者恢复阶段）**：该计算成本较高，因为它需要从区块体中每笔交易的签名中提取发送者地址。  
  4. **ExecutionStage（执行阶段）**：最耗时且计算量最大的阶段，需要获取发送者、交易和区块头，并在 REVM 中执行它们。  
     - 该过程会生成收据和更改集（change sets）。  
     - 更改集是一种哈希映射数据结构，记录单个区块内账户之间的状态修改。  
     - 执行阶段在“纯状态”上运行，该状态仅包含地址和账户信息，以键值对的形式存储。  

  5. **MerkleStage（unwind，回溯阶段）**：在正常执行流程中被跳过，仅在回溯时使用。  
  6. **AccountHashingStage（账户哈希阶段）**：Merkle 阶段所需的步骤，我们对“纯状态”应用哈希函数，并将生成的哈希账户存储到专门用于存储账户的数据库中。  
  7. **StorageHashingStage（存储哈希阶段）**：与上一步类似，但针对存储数据进行哈希处理。  
  8. **MerkleStage（execute，执行阶段）**：使用前两个阶段生成的哈希计算状态根，并验证所得状态根是否与给定区块匹配。  
  9. **TransactionLookupStage（交易查找阶段）**：辅助阶段，使我们能够执行交易查找。  
  10. **IndexStorageHistoryStage（索引存储历史阶段）**：使我们能够检索过去的数据。执行阶段生成更改集，然后对执行区块前存在的数据进行索引，从而使我们能够根据任意区块号检索历史数据。  
  11. **IndexAccountHistoryStage（索引账户历史阶段）**：与上一步类似，但针对账户历史进行索引。  
  12. **FinishStage（完成阶段）**：通知引擎，它现在可以接收来自共识层的新分叉选择更新。  

- **区块链树（BlockchainTree）**：在同步过程中，当接近区块链的末端时，我们会过渡到区块链树。此时，同步发生在接近链顶端的位置，状态根验证和执行均在内存中进行。  
- **数据库（Database）**：当区块被确认为规范区块（canonicalized）后，它会被存入数据库。  
- **提供者（Provider）**：数据库的抽象层，提供各种实用函数，以帮助我们避免直接访问底层数据库的键值对。  
- **下载器（Downloader）**：通过点对点（P2P）网络检索区块和区块头。在管道的最初两个阶段使用此工具，并且在引擎需要填补链顶缺口时也会使用。  
- **P2P**：当我们接近链顶时，我们会通过 P2P 网络将读取的交易传输到交易池。  
- **交易池（Transaction Pool）**：包含 DDoS 保护机制。交易池中的交易按照用户偏好的燃气价格（Gas Price）从低到高排列。  
- **有效载荷构建器（Payload Builder）**：提取前 n 笔交易，以构建新的有效载荷（Payload）。  
- **修剪器（Pruner）**：允许我们运行完整节点。当区块链树将区块确认为规范区块后，我们需要再等待 64 个区块，使其最终确定（Finalized）。一旦最终确定，我们就可以确信该区块不会发生重组（Reorg）。因此，如果我们运行的是完整节点，就可以使用修剪器删除旧区块。  

## **存储（Storage）**  

Reth 主要使用 **mdbx** 数据库。此外，它提供了多个有价值的抽象层，以增强底层数据库的功能，包括数据转换、压缩、迭代、写入和查询操作。这些抽象层的设计允许 Reth 在最小修改现有存储抽象的情况下，更换底层数据库（mdbx）。  

### **编解码器（Codecs）**  

这个 [crate](https://github.com/paradigmxyz/reth/tree/main/crates/storage/codecs) 允许创建多种用途的编解码器。  
当前主要使用的编解码器是 [Compact trait](https://github.com/paradigmxyz/reth/blob/6d7cd53ad25f0b79c89fd60a4db2a0f2fe097efe/crates/storage/codecs/src/lib.rs#L43)，它可以压缩数据，例如通过去除无符号整数的前导零来减少存储占用。此外，它还能用于压缩访问列表（access-lists）、区块头等结构体。  

### **数据库抽象层（DB Abstractions）**  

- [**数据库 trait**](https://github.com/paradigmxyz/reth/blob/e158542d31bf576e8a6b6e61337b62f9839734cf/crates/storage/db/src/abstraction/database.rs#L12) 是存储层的核心抽象，提供对底层数据库事务的只读或读写访问能力。  

- [**游标（Cursor）**](https://github.com/paradigmxyz/reth/blob/e158542d31bf576e8a6b6e61337b62f9839734cf/crates/storage/db/src/abstraction/cursor.rs#L13) 允许我们遍历数据库中的值，并提供了一种快速检索交易或区块的方法。  
  - 在计算 Merkle 根时，它尤为重要，因为顺序访问数据的速度远快于随机读取。  
  - 当我们需要写入大量数据时，先对数据进行排序再写入的效率更高。  
  - 游标提供了便捷的函数，使我们能够优化存储方式，无论是写入排序数据还是未排序数据。  


## **数据表（Tables）**

| **表名**                   | **键（Key）**                         | **值（Value）**             | **描述（Description）**                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| -------------------------- | ----------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **CanonicalHeaders**           | BlockNumber                         | HeaderHash              | 存储按区块号索引的区块头哈希。                                                                                                                                                                                                                                                                                                                                                                      |
| **HeaderTerminalDifficulties** | BlockNumber                         | CompactU256             | 负责存储区块头中的总难度值。虽然主要用于工作量证明（PoW）系统，但目前未被使用。                                                                                                                                                                                                                                                                                                                    |
| **HeaderNumbers**              | BlockHash                           | BlockNumber             | 这是一个辅助表，存储区块头对应的区块号。                                                                                                                                                                                                                                                                                                                                                          |
| **Headers**                    | BlockNumber                         | Header                  | 存储区块头的具体内容。                                                                                                                                                                                                                                                                                                                                                                            |
| **BlockBodyIndices**           | BlockNumber                         | StoredBlockBodyIndices  | 存储区块的索引信息，包括交易索引及其数量。这使我们能够确定区块中包含哪些交易编号。                                                                                                                                                                                                                                                                                                               |
| **BlockOmmers**                | BlockNumber                         | StoredBlockOmmers       | 存储区块的叔块（ommer），即被包含的侧链区块（用于工作量证明）。                                                                                                                                                                                                                                                                                                                                 |
| **BlockWithdrawals**           | BlockNumber                         | StoredBlockWithdrawals  | 存储区块的提款信息。                                                                                                                                                                                                                                                                                                                                                                              |
| **Transactions**               | TxNumber                            | TransactionSignedNoHash | 以交易编号索引存储交易体信息，包括交易总数和已执行的交易数。此外，它还可以帮助快速检索单个交易。                                                                                                                                                                                                                                                           |
| **TransactionHashNumbers**     | TxHash                              | TxNumber                | 以交易哈希索引存储交易编号。                                                                                                                                                                                                                                                                                                                                                                      |
| **TransactionBlocks**          | TxNumber                            | BlockNumber             | 记录最高交易编号与区块号的映射关系，使我们可以根据交易编号获取对应的区块号。                                                                                                                                                                                                                                                                                                                      |
| **Receipts**                   | TxNumber                            | Receipt                 | 以交易编号索引存储交易收据。                                                                                                                                                                                                                                                                                                                                                                      |
| **Bytecodes**                  | B256                                | Bytecode                | 编译并存储所有智能合约的字节码。由于多个账户可能拥有相同的字节码，因此需要实现引用计数指针（reference counting pointer）。                                                                                                                                                                                                                                 |
| **PlainAccountState**          | Address                             | Account                 | 存储账户的当前状态，按账户地址索引（[Account 结构体](https://github.com/paradigmxyz/reth/blob/fb960fb3e45e11c24125ccb4bd93f2e2e21ce271/crates/primitives/src/account.rs#L15)）。**Plain State** 在执行阶段被更新。                                                                                                                                                                                 |
| **PlainStorageState**          | Address , SubKey = B256             | StorageEntry            | 存储存储键的当前值，子键是存储键的哈希值。关于子键：mdbx 允许我们使用 dup table（表内值重复），这可以加快某些值的访问速度。                                                                                                                                                                                                                             |
| **AccountsHistory**            | ShardedKey<Address>                 | BlockNumberList         | 存储每个账户键的变更集指针。每个账户都关联着一个修改记录，以区块列表的形式存储。例如，如果我们想要查询某个账户在区块 **1,000,000** 时的余额，我们需要确定下一个修改该账户的区块号。如果下次修改发生在 **1,000,001** 号区块，我们就需要从下列表中获取该账户的变更集。 |
| **StoragesHistory**            | StorageShardedKey                   | BlockNumberList         | 存储每个存储键的变更集指针，使我们能够索引变更集并找到历史修改记录。                                                                                                                                                                                                                                                                                                                              |
| **AccountChangeSets**          | BlockNumber, SubKey = Address       | AccountBeforeTx         | 在任何影响账户状态的交易执行之前（例如账户创建、自毁、被访问时为空，或者余额或 nonce 发生变化），存储该账户的先前状态。因此，我们可以获取每个区块和账户地址的历史状态。                                                                                                                                                                                      |
| **StorageChangeSets**          | BlockNumberAddress , SubKey = B256  | StorageEntry            | 记录存储键在特定交易修改前的状态。因此，我们可以基于 **区块号、账户地址和子键（存储键）** 获取存储的历史值。执行阶段会修改此表及上方的 **AccountChangeSets** 表。这些表用于 **Merkle Trie** 计算（需要增量更新的值），同时也被 **JSON-RPC API** 用于历史追溯。                                                                         |
| **HashedAccounts**             | B256                                | Account                 | 以 `keccak256(Address)` 索引存储账户的当前状态。该表用于 **Merkle 化**（计算状态根）。此表和下表都是 **Merkle Trie** 的数据来源，第一步需要对地址进行哈希并排序。                                                                                                                                                                                           |
| **HashedStorages**             | B256, SubKey = B256                 | StorageEntry            | 以 `keccak256(Address)` 及 `keccak256(key)` 作为子键存储当前存储值。同样用于 **Merkle 化**，因为哈希后的地址/键是排序好的。                                                                                                                                                                                                                                |
| **AccountsTrie**               | StoredNibbles                       | StoredBranchNode        | 存储当前状态的 **Merkle Patricia Tree**。                                                                                                                                                                                                                                                                                                                                                        |
| **StoragesTrie**               | B256 , SubKey = StoredNibblesSubKey | StorageTrieEntry        | 以 **HashedAddress => NibblesSubKey => Intermediate value** 形式存储。此表和上表提供 **Merkle Trie** 计算所需的节点信息。                                                                                                                                                                                                                                                                           |
| **TransactionSenders**         | TxNumber                            | Address                 | 存储每笔交易的发送方地址。它有助于加快执行阶段，并且可以在无需进行计算密集型的 **交易签名恢复**（Transaction Signer Recovery）操作的情况下，直接获取交易签名者。                                                                                                                                                                                             |
| **StageCheckpoints**           | StageId                             | StageCheckpoint         | 记录每个阶段的最高同步区块号及阶段特定的检查点。                                                                                                                                                                                                                                                                                                                                                  |
| **StageCheckpointProgresses**  | StageId                             | Vec<u8>                 | 存储用于跟踪阶段 **初始同步进度** 的任意数据。此表和上表使我们能够确定阶段的同步位置，并决定下一步操作。                                                                                                                                                                                                                                                    |
| **PruneCheckpoints**           | PruneSegment                        | PruneCheckpoint         | 记录 **已修剪的最大区块号** 及 **修剪模式**，用于跟踪数据修剪过程。修剪涉及删除变更集及相关索引，以移除历史数据，仅保留最近的数据（即 **tip 数据**）。                                                                                                                                                                                                      |
| **VersionHistory**             | u64                                 | ClientVersion           | 存储以 **Unix 时间戳（秒）** 为索引的 **客户端版本历史**，记录具有写权限的客户端访问数据库的历史。                                                                                                                                                                                                                                                                                               |

