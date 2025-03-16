# 学习小组 第7周 | 执行客户端架构

第7周的开发轨道将深入探讨以太坊执行层客户端的代码库，解释其架构并突出介绍新的设计方法。

观看由 [Dragan](https://twitter.com/rakitadragan) 主讲的演讲录音，可以在 StreamEth 或 YouTube 上观看。幻灯片 [可以在此处查看](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/week7-dev.pdf)。

<iframe width="560" height="315" src="https://www.youtube.com/embed/ibcsc5cv-vc?si=mTR7ReFUZo3vFtJD" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预阅读

在开始第7周的开发内容之前，请先熟悉前几周的资源，特别是第2周。执行客户端简介提供了有关执行客户端及其主要特性的知识，并通过 geth 代码库中的示例加以说明。本次演讲将深入探讨使用现代设计方法开发的 reth 客户端设计，该客户端使用 Rust 编写。

此外，您还可以通过阅读以下资源为演讲做好准备：

- Reth 文档：[https://paradigmxyz.github.io/reth/](https://paradigmxyz.github.io/reth/)
- Georgios 对 Reth 的介绍：[https://www.youtube.com/watch?v=zntRpCKHyDc](https://www.youtube.com/watch?v=zntRpCKHyDc)
- Dragan 的深入介绍：[https://www.youtube.com/watch?v=pxhq7YrySRM](https://www.youtube.com/watch?v=pxhq7YrySRM)

## 大纲

- Reth 客户端
- 设计与架构
- 代码库概述，示例
- 特性与亮点

## 额外阅读和练习

- Erigon 是 geth 的一个分支，它开创了 reth 实现的设计方法。作为 geth 和 reth 之间的中间地带，它是了解新型执行客户端设计的绝佳资源。
- 作为练习，运行 reth 并设置不同的 `DEBUG` 选项，探索各个客户端组件在较低层级上的操作。
