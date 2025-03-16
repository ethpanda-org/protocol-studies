# 学习小组 第 10 天 | 预编译合约（Precompiles）

本次开发讲座深入探讨 **EVM 预编译合约** 及其在执行客户端（Execution Clients）中的集成方式。

📺 **观看演讲**
由 [Danno Ferrin](https://twitter.com/shemnon) 主讲，演讲可在以下平台观看：
- [StreamEth](https://streameth.org/65cf97e702e803dbd57d823f/epf_study_group)
- [YouTube](https://www.youtube.com/watch?v=daiMhkt0XTw)

📑 **相关资料**
- [讲座演示文稿](https://hackmd.io/@shemnon/precompiles)
- [Discord 讨论线程：Week 10D - Precompiles](https://discord.com/channels/1205546645496795137/1231990093506678785)

<iframe width="560" height="315" src="https://www.youtube.com/embed/daiMhkt0XTw?si=6c4EJRi-g1G5udJH" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预阅读

在开始本次开发内容前，请熟悉前几周的资源，尤其是：
- **第 2 天**：执行层（EL）架构
- **第 7 天**：执行客户端（EL Clients）

你需要具备对 **执行客户端（Execution Clients）架构** 和 **EVM** 的基本理解。

本次讲座将以 **Besu**（一个基于 Java 的执行客户端）为例，推荐具备 Java 语法的基础知识。

📚 **推荐阅读**
- [深入探索以太坊的预编译合约](https://lucasmartincalderon.medium.com/exploring-precompiled-contracts-on-ethereum-a-deep-dive-4e9f9682e0aa)
- [EVM 预编译合约文档（evm.codes）](https://www.evm.codes/precompiled)

## 讲座大纲

- **EVM 预编译合约**
- **预编译合约的集成方式**
- **现存的预编译合约**
- **L1 和 L2 如何使用预编译合约**
- **创建预编译合约的挑战**

## 额外阅读与练习

📖 **推荐阅读**
- [EVM 预编译合约与系统合约目录](https://github.com/shemnon/precompiles/)
- [RollCall 会议：预编译合约](https://www.youtube.com/watch?v=tg01COfxi_M)
- [Hyperledger Besu 的自定义 RPC 和预编译合约](https://www.youtube.com/watch?v=djL5nczlYFw)
