# 凯卡克 256

Keccak256 是一个加密哈希函数，主要用于 Ethereum 区块链。 

## 简史

Keccak256 最初是向 [NIST 加密哈希算法竞赛](https://keccak.team/files/Keccak-submission-3.pdf) 提交的。本次比赛旨在寻找一种新的安全哈希算法来替代 SHA-1。 KECCAK 算法是由 Guido Bertoni、Joan Daemen、Michael Peeters 和 Gilles Van Assche 组成的团队设计的。 

## 凯卡克设计

Keccak 因其海绵结构而脱颖而出，这种独特的功能使其能够“吸收”任何长度的输入数据，然后“挤压”出所需长度的哈希。

海绵功能是 Keccak 设计的核心，它分为两个不同的阶段：吸收和挤压。 

### 吸收阶段

- **输入处理**：在此阶段，输入数据被分为区块并与海绵的状态 (称为比特率) 进行异或。
- **比特率 (`r`)**：比特率是定义与输入直接交互的状态中的位数的参数。它决定了数据吸收过程的效率和吞吐量。
- **状态置换**：在每个 XOR 操作之后，置换函数将应用于整个状态，确保输入和状态的彻底混合。

### 挤压阶段

- **输出生成**：吸收完成后，挤压阶段开始。这里，输出哈希是根据状态的比特率部分生成的。
- **任意输出长度**：挤压阶段可以产生任何所需长度的输出。

为了更深入地了解 Keccak 的内部工作原理，[Keccak 参考](https://keccak.team/files/CSF-0.1.pdf) 提供了对其算法和安全功能的详细见解。

## EVM 实施

EVM (Ethereum Virtual Machine) 使用基于堆栈的架构来处理 Ethereum 区块链的 交易的执行。 EVM 操作码是 EVM 解释并随后执行以实现交易并运行智能合约的预定义指令。有算术、环境、控制流和堆栈操作操作码。现在没有 keccak256 操作码，但是有一个 SHA3 操作码。 SHA3 操作码用于加密来自堆栈的输入数据并输出 Keccak256 哈希。

[Ethereum 黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf) 概述了 Ethereum 区块链中 Keccak256 的其他实现：

### 区块创建和根数据结构中的用法

- **区块标头字段**：区块标头中的各个字段 (例如 `parentHash` 和 `stateRoot`) 使用 Keccak 256 位哈希。这包括对父区块的整个标头、状态 Trie 的根节点以及交易和收据的 Trie 结构的根节点进行哈希处理。
- **Merkle Patricia Tree**：Ethereum 采用 Merkle Patricia Tree 对其状态进行编码，其中树中的每个节点通过其内容的 Keccak 256 位哈希进行标识。该结构支撑区块标头中的 stateRoot 字段。
- **存储内容编码**：哈希用于对账户的存储内容进行编码，将整数键的 Keccak 256 位哈希映射到 RLP 编码的整数值。

在所有这些情况下，Keccak256 的角色对于确保数据完整性、促进高效数据检索以及支持区块链的底层安全机制至关重要。

## Keccak256 vs SHA3-256

[引用来自 Ethereum 的 Nick Johnson](https://github.com/ethereum/go-ethereum/pull/2940#issuecomment-274809794)：
> SHA3-256 是 Keccak256，但数据填充方式发生了变化。使用 Keccak256 是因为 Ethereum 的协议是在 Keccak256 显然是 SHA3 竞赛的获胜者之后、但在进行填充更改之前定义的。

## 参考文献

- [NIST SHA-3 比赛](https://keccak.team/files/Keccak-submission-3.pdf)
- [Ethereum 黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf)
- [EVM 操作码](https://www.evm.codes/?fork=shanghai)
