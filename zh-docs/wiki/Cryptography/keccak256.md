# Keccak256

Keccak256 是以太坊区块链中广泛使用的加密哈希函数。

## 简要历史

Keccak256 最初是作为 [NIST 加密哈希算法竞赛](https://keccak.team/files/Keccak-submission-3.pdf) 的提交方案之一。该竞赛旨在寻找一种新的安全哈希算法，以取代 SHA-1。KECCAK 算法由 Guido Bertoni、Joan Daemen、Michaël Peeters 和 Gilles Van Assche 团队设计。

## Keccak 设计

Keccak 采用了独特的海绵结构（sponge construction），使其能够“吸收”任意长度的输入数据，并随后“挤出”所需长度的哈希值。

海绵函数是 Keccak 设计的核心，它分为两个不同的阶段：吸收（absorption）和挤出（squeezing）。

### 吸收阶段

- **输入处理**：在此阶段，输入数据被分割成多个块，并与海绵状态（bitrate 部分）进行按位异或（XOR）。
- **比特率（`r`）**：比特率是定义与输入数据直接交互的状态位数的参数。它决定了数据吸收过程的效率和吞吐量。
- **状态置换**：每次 XOR 操作后，都会对整个状态应用置换函数，以确保输入数据与状态充分混合。

### 挤出阶段

- **输出生成**：吸收阶段完成后，进入挤出阶段。在此阶段，哈希输出从状态的比特率部分生成。
- **可变输出长度**：挤出阶段可以生成任意长度的输出。

如果想更深入了解 Keccak 的内部工作机制，[Keccak 参考文档](https://keccak.team/files/CSF-0.1.pdf) 提供了关于其算法和安全特性的详细信息。

## EVM 实现

EVM（以太坊虚拟机）采用基于堆栈的架构来处理以太坊区块链的交易执行。EVM 指令（Opcode）是一组预定义的指令，EVM 解释并执行这些指令以完成交易和运行智能合约。EVM 指令包括算术运算、环境操作、控制流和堆栈操作等。目前，EVM 没有 keccak256 指令，但有一个 SHA3 指令。SHA3 指令用于加密来自堆栈的输入数据，并输出 Keccak256 哈希值。

[以太坊黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf) 概述了 Keccak256 在以太坊区块链中的其他实现：

### 区块创建与根数据结构中的应用

- **区块头字段**：区块头中的多个字段（如 `parentHash` 和 `stateRoot`）使用 Keccak 256 位哈希。这包括对父区块整个区块头的哈希、状态树（state trie）根节点的哈希，以及交易和收据树结构的根节点哈希。
- **默克尔帕特里夏树**：以太坊使用默克尔帕特里夏树（Merkle Patricia Tree）来编码其状态，其中每个节点都通过其内容的 Keccak 256 位哈希进行标识。这一结构是 `stateRoot` 字段在区块头中的基础。
- **存储内容编码**：Keccak 256 位哈希用于对账户的存储内容进行编码，将整数键的 Keccak 256 位哈希映射到 RLP 编码的整数值。

在所有这些应用场景中，Keccak256 在确保数据完整性、提高数据检索效率以及支持区块链底层安全机制方面都起着至关重要的作用。

## Keccak256 与 SHA3-256 的区别

[引用以太坊的 Nick Johnson](https://github.com/ethereum/go-ethereum/pull/2940#issuecomment-274809794)：
> SHA3-256 就是 Keccak256，区别仅在于数据填充方式的变化。以太坊选择 Keccak256，是因为在 SHA3 竞赛确定 Keccak256 获胜后、但在填充方式变更之前，以太坊协议已经定义完毕。

## 参考资料

- [NIST SHA-3 竞赛](https://keccak.team/files/Keccak-submission-3.pdf)
- [以太坊黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf)
- [EVM 指令集](https://www.evm.codes/?fork=shanghai)
