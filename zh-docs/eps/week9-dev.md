# 学习小组 第9周 | 本地测试与原型开发

本次开发讲座专注于在本地测试和原型开发分叉，讨论了当前的状态以及正在开展的想法。

观看由 [Parithosh](https://twitter.com/parithosh_j) 主讲的演讲，可以在 StreamETH 频道或 YouTube 上观看。幻灯片 [可以在此处查看](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/week9-dev.pdf)。

<iframe width="560" height="315" src="https://www.youtube.com/embed/Enf8006zKLI?si=hJe4xFqiY81C0DwQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预阅读

在开始第9天的内容之前，请先熟悉前几周的资源，尤其是第4天关于测试和节点工作坊的讲解。了解协议的当前架构及其基本工具是非常重要的。

此外，您可以通过阅读以下资源为讲座做好准备：
- [Quest for the Best Tests，Pari对#TestingTheMerge的回顾](https://archive.devcon.org/archive/watch/6/quest-for-the-best-tests-a-retrospective-on-testingthemerge/?tab=YouTube)
- [Dencun 测试](https://www.youtube.com/watch?v=88tZticGbTo)

## 准备工作
- [在您的系统上安装 Kurtosis 和 Docker](https://docs.kurtosis.com/quickstart/)

## 大纲

- 我们测试什么以及为什么要测试？
- 本地测试的重要性
- 如何原型化一个工具或变更？
- 什么是开发网络？如何启动它们？
- Shadow forks
- 常用的开发运维工具
  - Kurtosis
  - Template-devnets
  - Assertoor
  - Forky
  - Tracoor
  - Dora
  - Goomy-blob
  - Xatu
- 运行您自己的本地开发网络和 Shadowfork！

## 额外阅读和练习

- [Attacknet：以太坊上的混沌工程](https://ethpandaops.io/posts/attacknet-introduction/)
- [Verkle 开发网络](https://github.com/ethpandaops/verkle-devnets)
- [Kurtosis](https://github.com/kurtosis-tech/kurtosis)
- 按照 Pari 在讲座中提出的练习
   - 修改客户端并添加自定义日志信息，使用 Kurtosis 运行它
   - 部署一些工具，连接到您自己的节点，无论是在任何网络上
