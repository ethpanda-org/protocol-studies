# 网络

共识客户端使用 [libp2p][libp2p] 作为点对点协议，使用 [discv5][discv5] 进行节点发现，使用 [libp2p-noise][libp2p-noise] 进行加密，使用 [SSZ][ssz] 进行编码，并可选择使用 [Snappy][snappy] 进行压缩。

## ENR (Ethereum Node Records)

## discv5

## 规范

[Phase 0 -- Networking][consensus-networking] 页面规定了网络基础、协议及其设计选择的理由。


## libp2p - P2P 协议

[libp2p][libp2p] 被用作点对点协议。[libp2p and Ethereum][libp2p-and-eth] 是一篇很棒的文章，它深入探讨了 libp2p 的历史以及它在共识客户端中的应用。



## libp2p-noise - 加密

[Noise framework][noise-framework] 本身不是一个协议，而是一个设计密钥交换协议的框架。[specification][noise-specification] 是一个很好的起点。


有许多 [patterns][noise-patterns] 描述了密钥交换过程。共识客户端使用的模式是 [`XX`][noise-xx]（传输-传输），这意味着在密钥交换的初始阶段，发起方和响应方都会传输各自的公钥。


## SSZ - 编码

[Simple serialize (SSZ)][ssz] 替代了执行层中使用的 [RLP][rlp] 序列化，广泛应用于共识层的各个部分，除了点对点发现协议。SSZ 旨在具有确定性，并且能够高效地进行 Merkle 化。SSZ 可以被看作是由两个组件组成：一个序列化方案和一个与序列化数据结构高效配合的 Merkle 化方案。


## Snappy - 压缩

[Snappy][snappy] 是由谷歌工程师在 2011 年创建的压缩方案。其主要设计考虑因素优先考虑压缩/解压缩速度，同时仍保持合理的压缩比。


## Related R&D

- [EIP-7594][peerdas-eip] - 对等数据可用性采样（PeerDAS）

 
  一种允许信标节点执行数据可用性的网络协议。
  采样 (DAS) 以确保在仅下载数据的子集时，blob 数据已被提供。
  - [Consensus Specs][peerdas-specs]
  - [ETH Research][peerdas-ethresearch]

[consensus-networking]: https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/p2p-interface.md
[discv5]: https://github.com/ethereum/devp2p/blob/master/discv5/discv5.md
[libp2p-and-eth]: https://blog.libp2p.io/libp2p-and-ethereum/
[libp2p-noise]: https://github.com/libp2p/specs/tree/master/noise
[libp2p]: https://docs.libp2p.io/
[noise-framework]: https://noiseprotocol.org/
[noise-patterns]: https://noiseexplorer.com/patterns/
[noise-specification]: https://noiseprotocol.org/noise.html
[noise-xx]: https://noiseexplorer.com/patterns/XX/
[peerdas-eip]: https://github.com/ethereum/EIPs/pull/8105
[peerdas-ethresearch]: https://ethresear.ch/t/peerdas-a-simpler-das-approach-using-battle-tested-p2p-components/16541
[peerdas-specs]: https://github.com/ethereum/consensus-specs/pull/3574
[rlp]: https://ethereum.org/developers/docs/data-structures-and-encoding/rlp
[snappy]: https://en.wikipedia.org/wiki/Snappy_(compression)
[ssz]: https://ethereum.org/developers/docs/data-structures-and-encoding/ssz
[blog]: https://medium.com/coinmonks/dissecting-the-ethereum-networking-stack-node-discovery-4b3f7895f83f
