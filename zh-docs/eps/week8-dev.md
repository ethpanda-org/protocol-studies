# 学习小组 第8周 | 共识客户端架构

第8周的开发轨道深入探讨以太坊共识层客户端代码库，解释其架构和功能。

观看由 [Paul Harris](https://twitter.com/rolfyone) 主讲的演讲录音，可以在 StreamEth 或 YouTube 上观看。幻灯片 [可以在此处查看](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/week8-dev.pdf)。

<iframe width="560" height="315" src="https://www.youtube.com/embed/cZ33bfGXzOc?si=qnZ8xJF74oRlkHqF" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预阅读

在开始第8周的开发内容之前，请先熟悉前几周的资源，特别是第2周关于共识层的资源。

Paul 详细讲解了 Teku——一个用 Java 实现的共识客户端，并通过一个示例说明了 EIP 如何实现。为了更好地理解，建议你至少具备基础的语言语法知识。

[Consensus-specs](https://github.com/ethereum/consensus-specs/) 是可执行的，了解一些 Python 知识可能会有帮助，但由于规格的内容较为简洁，这门语言也容易理解。

此外，您可以通过阅读以下资源为讲座做好准备：

- [Post-Merge Ethereum Client Architecture by Adrian Sutton](https://www.youtube.com/watch?v=6d4pkhL37Ao)
- [Teku Architecture, 2020](https://www.youtube.com/watch?v=1PHZHpVPLk4)
- [Teku 文档](https://docs.teku.consensys.io/)

## 大纲

- Teku 共识层客户端
- 简要介绍我们的 REST API 和声明式框架
- 了解开发过程 EIP -> 规格 -> 代码
    - EIP-7251（maxEB）示例

## 额外阅读和练习

- [Teku 代码规范](https://wiki.hyperledger.org/display/BESU/Coding+Conventions) 
- [Teku 与 Merge，PEEPanEIP#83](https://www.youtube.com/watch?v=YTWaZ-NBpbM)
- [EIP 7251 - Max EB](https://github.com/ethereum/consensus-specs/tree/dev/specs/_features/eip7251)
- [Beacon-API](https://github.com/ethereum/beacon-APIs)
- [Lighthouse 共识客户端架构](https://www.youtube.com/watch?v=pLHhTh_vGZ0) - 讲解 Rust 客户端 Lighthouse 的共识客户端架构
