# 第 23 讲 | 学习小组 EL 数据结构

本讲座深入探讨 Execution Layer 的数据结构、Merkle Patricia 树以及不同的客户端如何实现它。讲座由 Besu 团队的 [Gary](https://github.com/garyschulte) 和 [<name>Karim</name>](https://github.com/matkt) 主讲。

在 [Youtube](https://youtu.be/EY_pVZTXS1w) 上观看讲座。 [幻灯片可在此处获取](https://docs.google.com/presentation/d/1YJbrZpgxjTHy7QlgXFRG5OjSK-G5uPrExBPu3Hiefvk/edit?usp=sharing)。

<iframe width="560" height="315" src="https://www.youtube.com/embed/EY_pVZTXS1w" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预读

在开始第 23 天的内容之前，请先熟悉前几周的资源，尤其是第 3 天的 EL 和第 7 天的 EL 客户端架构。

此外，您可以通过学习以下资源来做好准备。

- [<name>Merkling</name> 在 Ethereum](https://blog.ethereum.org/2015/11/15/merkling-in-ethereum)
- [ethereum.org 上的 MPT](https://ethereum.org/en/developers/docs/data-structures-and-encoding/patricia-merkle-trie/)
- https://en.wikipedia.org/wiki/Trie#Patricia_trees

## 概要

1. Ethereum 状态概述
2. Merkle Patricia Trie (MPT)
   - Trie-within-a-Trie 账户存储的结构和概念
   - 状态根是什么以及它的用途是什么
   - 随着时间的推移，状态规模和性能面临的挑战
3. 状态解决方案的演变：从基于哈希到基于路径
   - 描述森林模型
   - 尺寸和性能限制
   - Bonsai
   - 基于路径 Trie
   - 平面数据库
   - 其他存储模型
   - half-path, nethermind
   - erigon/reth 扁平数据库模型
   - bonsai archive

## 额外阅读和练习

- [Bonsai Trees guide](https://consensys.io/blog/bonsai-tries-guide)
- [Nethermind 半路径](https://github.com/NethermindEth/nethermind/pull/6331)
- [Nethermind Paprika](https://github.com/NethermindEth/Paprika/blob/main/docs/design.md)
- [Erigon 架构](https://github.com/erigontech/erigon/blob/main/erigon-lib/kv/tables.go)
- [实现 MPT](https://medium.com/coinmonks/implementing-merkle-tree-and-patricia-trie-b8badd6d9591)
- [Ethereum 数据结构论文](https://www.researchgate.net/publication/353863430_Ethereum_Data_Structures)
