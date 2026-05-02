# 最大可提取价值(之前为矿工可提取价值)

最大可提取价值(MEV)是指通过策略性排序(包括或排除区块中的交易)，可以从区块生产中提取超出标准区块奖励和 gas 费用的最大值。

在以太坊中，随着验证者提取越来越多的价值，特别是在去中心化金融(Decentralized Finance)应用中，MEV获得了越来越多的关注。通过在区块中订购交易，可以通过抢先交易、夹心交易或反向交易等策略促进套利机会。这也可能导致负面后果，例如大型矿池的不公平优势、审查制度或 DeFi 用户的滑点增加。

[提议者-构建者分离 (PBS)](/wiki/research/PBS/pbs.md) 可以改变 MEV 提取的动态，因为两个角色之间可能会重新分配 MEV，从而可能改变与每个角色相关的激励和奖励。由于区块构建者负责交易排序和包含，因此他们可能会制定新策略或促进竞争加剧，从而提高 MEV 在整个网络中的效率和更公平的分配。

## MEV的演变

最大可提取价值(MEV)起源于工作量证明时代，被称为“矿工可提取价值”。该术语反映了矿工影响区块中 交易顺序的能力，包括其包含、排除和排序。在以太坊通过 The Merge 过渡到权益证明之后，验证者已经接管了共识，使得挖矿在协议中变得过时。尽管发生了这些变化，价值提取的机制仍然存在，导致采用“最大可提取价值”一词来解决这些活动。

尽管自以太坊诞生以来，MEV 就已经成为可能，但随着 DeFi 和更易于使用的工具的兴起，它获得了极大的关注。早期，MEV 的机会主要是由公开内存池中出价更高的竞争对手抓住的，标志着一个被称为优先 gas 拍卖或 PGA 的时代。关于这个混乱时代的详细信息请参见[Flashboys 2.0](https://arxiv.org/abs/1904.05234)。在此期间，[Flashbots](https://www.flashbots.net/) 作为开放研发计划出现，旨在提高公众知识和对 MEV 工具的访问。 

在合并后的世界中，矿工的概念不再存在，但他们的构建者和 提议者角色由验证者促进，负责以相同的方式将区块添加到链中。预见到合并后的变化，Flashbots 与客户端团队和以太坊基金会开始开发 [mev-boost](/wiki/research/PBS/mev-boost.md)。 MEV-boost 是提议者-构建者分离的协议外实现。请参阅[有关 PBS 的部分](/wiki/research/PBS/pbs.md)。

## 资源

- [从 CryptoKitties 到 MEV-Boost 到 PBS 的交易供应链研究 - Barnabé Monnot](https://www.youtube.com/watch?v=jQjBNbEv9Mg)
- [MEV 日播放列表](https://www.youtube.com/playlist?list=PLRHMe0bxkuel3w3C7P_WVvp9ShLi3HKRI)
- [如何点亮黑暗森林-Flashbots](https://collective.flashbots.net/t/how-to-light-up-the-dark-forest-a-walkthrough-of-building-a-cryptopunk-mev-inspector/881)