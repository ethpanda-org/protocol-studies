# 密码学

> ：警告：本文是一个[存根](https://en.wikipedia.org/wiki/Wikipedia:Stub)，通过[贡献](/contributing.md) 和扩展它来帮助维基。

密码学研究人员精心打造开发人员使用的变革武器或工具。他们使用先进的代数来利用宇宙对现实设定的硬性限制，并设计遵守某些属性的密码模式。从某种意义上说，他们是现实黑客。他们破解现实，创建遵循基础数学客观属性的系统。 

## 以太坊中的密码学：核心原语和协议角色

以太坊的安全模型取决于精心选择的加密结构，每个加密结构都解决特定的约束——无论是在交易验证、共识还是可扩展性方面。以下是关键方案及其协议级功能的高级细分。其中一些方案在密码学小节中进行了更详细的讨论。

1. ### ECDSA 和交易身份验证**
**用法：** 每个以太坊交易必须使用 ECDSA 在 secp256k1 曲线上进行签名。

#### 为什么它很重要：

与账户抽象模型 (ERC-4337) 不同，EOA 的每个操作都需要签名。

v, r, s 签名格式有历史怪癖 - 旧版交易使用 v ∈ {27, 28}，而 [EIP-155](https://eips.ethereum.org/EIPS/eip-155) 定义 v ∈ {0, 1} 以防止重放攻击。

**注意事项：**

签名可塑性是 EIP-155 之前的一个问题(通过 chainID 包含修复)。

硬件钱包通常实现自定义签名逻辑(例如，Ledger 的随机数派生)。

2. ### Keccak-256：超越“只是一个哈希函数”

#### 协议角色：

区块哈希(包括 PoW 中的 mixHash)。

状态 Trie 键(Merkle-Patricia 树依赖 Keccak 来获取节点引用)。

#### 设计选择：

以太坊在 NIST 标准化 SHA-3 之前采用了 Keccak，导致了细微的差异(例如，填充规则)。

曾考虑过像 Blake2b 这样的替代方案，但由于兼容性原因而被拒绝。

3. ### PoS 中的 BLS 签名：权衡与优化
#### 为什么是BLS？

签名聚合允许将数千个验证者证明压缩为单个 96 字节证明。

BLS12-381 曲线平衡了性能和安全性(配对友好，但比 secp256k1 慢)。

实施的细微差别：

以太坊的 BLS 规范 (EIP-2335) 强制执行严格的子组检查以避免流氓密钥攻击。

<name>blst</name> 和 <name>herumi</name> 等工具针对不同环境进行优化(例如，x86 与 ARM)。

4. ### Verkle 树：状态证明的转变
**当前限制：** 用于存储的 Merkle 证明太大(每个证明约 1 KB)。

**维尔克尔解决方案：**

将哈希替换为多项式承诺 (KZG)，将见证大小减少到约 200 字节。

需要可信设置(以太坊的 KZG 仪式)。

**开放问题：**

移交后如何处理历史证据？

gas 证明验证费用会改变吗？

5. ### ZKP 实践：不仅仅是 Rollup
**zkEVM 挑战：**

证明 EVM 执行需要处理可变操作码成本(例如，SSTORE 与 ADD)。

众所周知，某些电路(例如 Keccak)很难优化。

**超越 Rollup：**

轻客户端可以使用 ZK 证明进行同步委员会验证(PBS 提案)。

隐私池(例如，基于 ZK 的匿名交易)正在研究中。

6. ### 后量子准备：不仅仅是理论上的
**直接风险：**

量子计算机可以伪造 ECDSA 签名，但打破哈希函数(Keccak)更困难。

**迁移路径：**

基于格的方案(例如 Dilithium)是主要候选方案，但密钥大小存在问题(每个密钥约 2 KB)。

混合方法(例如，BLS + SPHINCS+)可能会弥合过渡。


- [BLS12-381 密钥库](https://eips.ethereum.org/EIPS/eip-2335)
- [Vitalik 的 ZK-SNARK 解释器](https://vitalik.eth.limo/general/2021/01/26/snarks.html)
- [以太坊中的Secp256k1](https://ethereum.org/en/developers/docs/evm/)
- [Verkle树](https://verkle.info/)
- [不同类型的 ZK-EVM](https://vitalik.eth.limo/general/2022/08/04/zkevm.html)
- [ZK-SNARKS](https://eprint.iacr.org/2018/046)

https://summerofprotocols.com/wp-content/uploads/2023/12/53-BEIKO-001-2023-12-13.pdf