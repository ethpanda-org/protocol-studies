# 学习小组 第 14 课 | Purge 和 Portal Network

本次讲座聚焦于 **历史数据删除（History Expiry）**，并深入探讨 **Portal Network**——一种为轻客户端提供替代性访问当前及历史数据的覆盖网络（Overlay Network）。

观看由 [Piper Merriam](https://twitter.com/parithosh_j) 主讲的演讲，可在 [StreamEth](https://streameth.org/65cf97e702e803dbd57d823f/epf_study_group) 或 [YouTube](https://www.youtube.com/@ethprotocolfellows/streams) 上观看。

<iframe width="560" height="315" src="https://www.youtube.com/embed/GxNrGyQB-3Q?si=gRPhA35dNYPEGDeZ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预阅读

在开始本次研究内容前，请熟悉前几周的资源，尤其是第 5 天的 **以太坊路线图（Roadmap）** 讲解。您需要了解执行客户端（Execution Client）数据库结构，以及古老数据（Ancient Data）与当前数据的区别。

此外，您可以通过以下资源进行预习：
- [去中心化访问以太坊——以太坊的 Portal Network](https://www.youtube.com/watch?v=LZ_yWmm7ISg)
- [无状态性（Statelessness）与历史数据删除，Ethereum.org](https://ethereum.org/en/roadmap/statelessness/)
- [Portal Network 官网](https://www.ethportal.net/)
- [执行层数据结构（EL Data structure）](https://epf.wiki/#/wiki/EL/data-structures)

## 讲座大纲

- 以太坊执行客户端中的历史数据删除（History Expiry）
- Portal Network 介绍

## 额外阅读与练习

### **EIP-4444**
- [EIP-4444: 执行客户端的历史数据删除提案](https://eips.ethereum.org/EIPS/eip-4444)

### **SELFDESTRUCT 操作码移除**
（以下部分提案可能已过时）
- [EIP-6049](https://eips.ethereum.org/EIPS/eip-6049)
- [EIP-6780](https://eips.ethereum.org/EIPS/eip-6780)
- [EIP-2936](https://eips.ethereum.org/EIPS/eip-2936)
- [EIP-4758](https://eips.ethereum.org/EIPS/eip-4758)
- [EIP-4760](https://eips.ethereum.org/EIPS/eip-4760)
- [EIP-6046](https://eips.ethereum.org/EIPS/eip-6046)
- [EIP-6190](https://eips.ethereum.org/EIPS/eip-6190)
- [EOF 相关内容](https://hackmd.io/@shemnon/mega-eof-scoping)

### **日志（LOG）操作码重构**
- [EIP-7668](https://eips.ethereum.org/EIPS/eip-7668)
- [GitHub PR #8368](https://github.com/ethereum/EIPs/pull/8368)

### **地址空间扩展（Address Space Extension）**
- [GitHub PR #8385](https://github.com/ethereum/EIPs/pull/8385)
- [以太坊魔法师论坛：关于地址空间扩展的讨论](https://ethereum-magicians.org/t/thoughts-on-address-space-extension-ase/6779)
- [地址大小从 20 字节扩展到 32 字节的讨论](https://ethereum-magicians.org/t/increasing-address-size-from-20-to-32-bytes/5485)
- [以太坊研究笔记：地址空间扩展](https://notes.ethereum.org/@ipsilon/address-space-extension-exploration)

### **状态过期（State Expiry）**
- [Vitalik Buterin: Verkle 和状态过期提案](https://notes.ethereum.org/@vbuterin/verkle_and_state_expiry_proposal)
- [Statelessness 系列（第一部分）：状态过期和历史数据删除](https://medium.com/@chaisomsri96/statelessness-series-part1-state-expiry-history-expiry-2bbd5835b329)（**未完全审核**）
- [Vitalik Buterin: 状态过期 EIP](https://notes.ethereum.org/@vbuterin/state_expiry_eip)
- [状态过期路径提案](https://hackmd.io/@vbuterin/state_expiry_paths)

### **无状态性（Statelessness）路线图**
- [Ethereum.org：无状态性](https://ethereum.org/en/roadmap/statelessness/)

### **额外推荐**
- [The Portal Network, EthZurich 2023](https://www.youtube.com/watch?v=8MUii5W2sMc)
