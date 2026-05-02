# 第 7 讲 | 学习小组执行客户端架构

第 7 天的开发轨道深入了解以太坊执行层客户端代码库，解释其架构并强调新颖的方法。 

在 StreamEth 或 Youtube 上观看 [Dragan](https://twitter.com/rakitadragan) 的演示录音。幻灯片[可在此处获取](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/week7-dev.pdf)。 

<iframe width="560" height="315" src="https://www.youtube.com/embed/ibcsc5cv-vc?si=mTR7ReFUZo3vFtJD" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预读

在开始第 7 讲的开发内容之前，先熟悉一下前几天的资源，尤其是 EL 上的 Day 3。执行客户端简介通过 geth 代码库中的示例提供了有关执行客户端及其主要功能的重要知识。本次演讲将深入探讨 reth 客户端设计，该设计是用 Rust 编写的，并采用现代设计方法 EL 客户端进行开发。 

此外，您可以通过学习以下资源来阅读并做好准备：

- Reth 文档 https://paradigmxyz.github.io/reth/
- Georgios https://www.youtube.com/watch?v=zntRpCKHyDc 介绍 Reth
- Dragan https://www.youtube.com/watch?v=pxhq7YrySRM 的更深入见解

## 概要

- Reth客户端 
- 设计与建筑
- 代码库概述、示例 
- 特点和亮点 

## 额外阅读和练习 

- Erigon 是 geth 的一个分支，它开创了 reth 实现的设计方法。这是 geth 和 reth 之间的一种中间立场，它是有关新颖执行客户端设计的重要资源来源
- 作为练习，运行 reth 并设置不同的 `DEBUG` 选项来探索各种客户端组件如何在较低级别上运行