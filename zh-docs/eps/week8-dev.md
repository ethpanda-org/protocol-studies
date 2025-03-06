# 学习小组 第8周 | 共识客户端架构

第8周的开发轨道将深入探讨以太坊共识层客户端的代码库，解释其架构和功能。

观看 [Paul Harris](https://twitter.com/rolfyone) 在 StreamEth 或 YouTube 上的演讲录制。幻灯片[点此查看](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/week8-dev.pdf)。

<iframe width="560" height="315" src="https://www.youtube.com/embed/cZ33bfGXzOc?si=qnZ8xJF74oRlkHqF" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预备阅读

在开始第 8 周的开发内容前，请先熟悉前几周的资源，特别是第 3 周关于共识层的内容。

Paul 将深入讲解 Teku（一个用 Java 编写的共识客户端），并示范 EIP 在其中的实现方式。建议至少具备 Java 语言的基本语法知识，以便更好地理解内容。

[Consensus-specs](https://github.com/ethereum/consensus-specs/) 具有可执行性，掌握一些 Python 基础可能会有所帮助，但在规范编写的层面上，Python 还是比较容易理解的。

此外，你可以通过以下资源进行准备：

- [Adrian Sutton：合并后以太坊客户端架构](https://www.youtube.com/watch?v=6d4pkhL37Ao)
- [Teku 架构（2020 年）](https://www.youtube.com/watch?v=1PHZHpVPLk4)
- [Teku 官方文档](https://docs.teku.consensys.io/)

## 讲座概要

- Teku 共识层客户端
- REST API 及声明式框架的简要介绍
- EIP 的开发流程：EIP -> 规范 -> 代码
  - 以 EIP-7251（MaxEB）为示例

## 额外阅读与练习

- [Teku 代码规范（贡献者指南）](https://wiki.hyperledger.org/display/BESU/Coding+Conventions)  
- [Teku 与 The Merge，PEEPanEIP#83](https://www.youtube.com/watch?v=YTWaZ-NBpbM)  
- [EIP 7251 - Max EB](https://github.com/ethereum/consensus-specs/tree/dev/specs/_features/eip7251)  
- [Beacon API](https://github.com/ethereum/beacon-APIs)
