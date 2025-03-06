# 第 9 周 | 本地测试与原型开发

本次开发讲座聚焦于本地测试与分叉原型开发，讨论当前状态及相关研究想法。

观看 [Parithosh](https://twitter.com/parithosh_j) 在 StreamETH 频道或 YouTube 上的演讲。幻灯片[点此查看](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/week9-dev.pdf)。

<iframe width="560" height="315" src="https://www.youtube.com/embed/Enf8006zKLI?si=hJe4xFqiY81C0DwQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预备阅读

在开始第 9 周的开发内容前，请先熟悉前几周的资源，特别是第 4 周的测试讲座及节点工作坊。理解协议的当前架构及其基本工具链至关重要。

此外，你可以通过以下资源进行准备：

- [测试的最佳实践：合并测试回顾（Pari）](https://archive.devcon.org/archive/watch/6/quest-for-the-best-tests-a-retrospective-on-testingthemerge/?tab=YouTube)
- [Dencun 测试](https://www.youtube.com/watch?v=88tZticGbTo)

## 运行环境准备

- [安装 Kurtosis 和 Docker](https://docs.kurtosis.com/quickstart/)

## 讲座概要

- 我们测试什么？为什么需要测试？
- 本地测试的重要性
- 如何为工具或协议更改创建原型？
- 什么是 Devnets？如何启动它们？
- Shadow forks（影子分叉）
- 开发者常用的 DevOps 工具：
  - Kurtosis
  - Template-devnets
  - Assertoor
  - Forky
  - Tracoor
  - Dora
  - Goomy-blob
  - Xatu
- 运行你自己的本地 Devnet 和 Shadowfork！

## 额外阅读与练习

- [Attacknet：以太坊的混沌工程](https://ethpandaops.io/posts/attacknet-introduction/)
- [Verkle 开发网络](https://github.com/ethpandaops/verkle-devnets)
- [Kurtosis](https://github.com/kurtosis-tech/kurtosis)
- 关注 Pari 在讲座中提出的练习：
  - 修改客户端代码，添加自定义日志消息，并使用 Kurtosis 运行
  - 部署相关工具，并连接到你自己的以太坊节点（任意网络）
