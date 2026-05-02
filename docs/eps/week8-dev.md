# 第 8 讲 | 学习小组共识客户端架构

第 8 讲开发课程介绍了 Ethereum Consensus Layer 客户端代码库，解释了其架构和功能。 

在 StreamEth 或 Youtube 上观看 [Paul Harris](https://twitter.com/rolfyone) 的演示录音。幻灯片 [可在此处获取](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/week8-dev.pdf)。 

<iframe width="560" height="315" src="https://www.youtube.com/embed/cZ33bfGXzOc?si=qnZ8xJF74oRlkHqF" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预读

在开始第 8 周的开发内容之前，请先熟悉前几周的资源，尤其是 Consensus Layer 上的第 2 天资源。 

Paul 深入研究了 Teku (Java 中的共识客户端实现)，并解释了如何实现 EIP 的示例。您至少应该具备语言语法的基本知识才能正确遵循。 

[Consensus-specs](https://github.com/ethereum/consensus-specs/) 是可执行的，并且对 Python 的了解可能会有所帮助，但在编写规范的级别上，它是一种相当容易推理的语言。

此外，您可以通过学习以下资源来做好准备：

- [合并后 Ethereum 客户端架构，作者：Adrian Sutton](https://www.youtube.com/watch?v=6d4pkhL37Ao)
- [Teku Architecture，2020](https://www.youtube.com/watch?v=1PHZHpVPLk4)
- [Teku 文档](https://docs.teku.consensys.io/)

## 概要

- Teku CL 客户端
- 简要介绍我们的 REST API、声明式框架
- 开发流程一览 EIP -> 规范 -> 代码
    - EIP-7251 (maxEB) 的示例

## 额外阅读和练习 
- [Teku github](https://github.com/Consensys/teku)
- [Teku 贡献者代码约定](https://wiki.hyperledger.org/display/BESU/Coding+Conventions) 
- [Teku 和合并，PEEPanEIP#83](https://www.youtube.com/watch?v=YTWaZ-NBpbM)
- [EIP 7251 - 最大 EB](https://github.com/ethereum/consensus-specs/tree/dev/specs/_features/eip7251)
- [Beacon API](https://github.com/ethereum/beacon-APIs)
- [Lighthouse Consensus 客户端架构](https://www.youtube.com/watch?v=pLHhTh_vGZ0) - Rust 中 CL 客户端架构的类似演讲客户端 Lighthouse
