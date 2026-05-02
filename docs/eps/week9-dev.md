# 第 9 讲 | 学习小组本地测试和原型设计 

本次开发演讲致力于在本地测试和原型化分叉，它讨论了当前的状态和正在研究的想法。 

在 StreamETH 频道或 Youtube 上观看 [Parithosh](https://twitter.com/parithosh_j) 的演示。幻灯片[可在此处获取](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/week9-dev.pdf)。 

<iframe width="560" height="315" src="https://www.youtube.com/embed/Enf8006zKLI?si=hJe4xFqiY81C0DwQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预读

在开始第 9 天的内容之前，请先熟悉前几周的资源，尤其是第 4 天的测试演示和节点研讨会。了解协议的当前架构及其基本工具非常重要。 

此外，您可以通过学习以下资源来做好准备：
- [寻求最佳测试，Pari 的 #TestingTheMerge 回顾](https://archive.devcon.org/archive/watch/6/quest-for-the-best-tests-a-retrospective-on-testingthemerge/?tab=YouTube)
- [Dencun测试](https://www.youtube.com/watch?v=88tZticGbTo)

## 准备工作
- [在系统上安装 kurtosis 和 docker](https://docs.kurtosis.com/quickstart/)

## 概要

- 我们测试什么以及为什么？
- 本地测试的重要性
- 如何制作工具原型或进行更改？
- 什么是开发网？我们如何旋转它们？
- 影子叉子 
- 方便的开发工具
  - Kurtosis
  - 模板开发网
  - 断言者
  - 叉叉
  - 特拉库尔
  - 朵拉
  - 古米-blob
  - 哈图
- 运行你自己的本地开发网和shadowfork！

## 额外阅读和练习
- [攻击网络：以太坊上的混沌工程](https://ethpandaops.io/posts/attacknet-introduction/)
- [Verkle 开发网](https://github.com/ethpandaops/verkle-devnets)
- [Kurtosis](https://github.com/kurtosis-tech/kurtosis)
- 遵循 Pari 在演讲中提出的练习
   - 使用自定义日志消息修改客户端并使用 Kurtosis 运行它
   - 部署一些收费，在任何网络上连接到您自己的节点 