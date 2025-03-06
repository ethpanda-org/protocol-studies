# 第 9 周 | Purge 与 Portal Network

本周的研究讲座聚焦于历史数据清理（History Expiry）以及 Portal Network —— 一个为轻客户端提供替代性访问当前及历史数据的覆盖网络。

观看 [Piper Merriam](https://twitter.com/parithosh_j) 的演讲，视频可在 [StreamEth](https://streameth.org/65cf97e702e803dbd57d823f/epf_study_group) 和 [YouTube](https://www.youtube.com/@ethprotocolfellows/streams) 上观看。

<iframe width="560" height="315" src="https://www.youtube.com/embed/GxNrGyQB-3Q?si=gRPhA35dNYPEGDeZ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预备阅读

在开始本周研究内容前，请先熟悉前几周的资源，特别是第 5 周的路线图讲座。你应当理解执行客户端的数据库结构，以及古代数据（Ancient Data）和当前数据（Current Data）之间的区别。

此外，你可以通过以下资源进行准备：
- [Statelessness 与历史数据清理，Ethereum.org](https://ethereum.org/en/roadmap/statelessness/)
- [Portal Network 官网](https://www.ethportal.net/)
- [执行层数据结构](https://epf.wiki/#/wiki/EL/data-structures)

## 讲座概要

- 以太坊执行客户端的历史数据清理
- Portal Network 介绍

## 额外阅读与练习

### EIP-4444：历史数据清理
- [EIP-4444：执行客户端的历史数据清理](https://eips.ethereum.org/EIPS/eip-4444)

### SELFDESTRUCT 操作码移除相关 EIP（部分已过时）
- [EIP-6049](https://eips.ethereum.org/EIPS/eip-6049)
- [EIP-6780](https://eips.ethereum.org/EIPS/eip-6780)
- [EIP-2936](https://eips.ethereum.org/EIPS/eip-2936)
- [EIP-4758](https://eips.ethereum.org/EIPS/eip-4758)
- [EIP-4760](https://eips.ethereum.org/EIPS/eip-4760)
- [EIP-6046](https://eips.ethereum.org/EIPS/eip-6046)
- [EIP-6190](https://eips.ethereum.org/EIPS/eip-6190)
- [EOF 相关讨论](https://hackmd.io/@shemnon/mega-eof-scoping)

### 日志（LOG）机制改进
- [EIP-7668](https://eips.ethereum.org/EIPS/eip-7668)
- [EIP-8368（PR）](https://github.com/ethereum/EIPs/pull/8368)

### 地址空间扩展（Address Space Extension）
- [EIP-8385（PR）](https://github.com/ethereum/EIPs/pull/8385)
- [Ethereum Magicians 讨论](https://ethereum-magicians.org/t/thoughts-on-address-space-extension-ase/6779)
- [增加地址长度从 20 到 32 字节](https://ethereum-magicians.org/t/increasing-address-size-from-20-to-32-bytes/5485)
- [地址空间扩展探索](https://notes.ethereum.org/@ipsilon/address-space-extension-exploration)

### 状态清理（State Expiry）
- [Verkle 与 State Expiry 提案](https://notes.ethereum.org/@vbuterin/verkle_and_state_expiry_proposal)
- [Statelessness 系列：State Expiry 与 History Expiry](https://medium.com/@chaisomsri96/statelessness-series-part1-state-expiry-history-expiry-2bbd5835b329) *(未完全审核)*
- [State Expiry EIP](https://notes.ethereum.org/@vbuterin/state_expiry_eip)
- [State Expiry 路径探索](https://hackmd.io/@vbuterin/state_expiry_paths)

### 无状态以太坊（Stateless Ethereum）相关资源
- [Ethereum.org：Statelessness 路线图](https://ethereum.org/en/roadmap/statelessness/)

### Portal Network 相关资料
- [Portal Network 介绍，EthZurich 2023](https://www.youtube.com/watch?v=8MUii5W2sMc)
