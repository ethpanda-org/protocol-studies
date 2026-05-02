# 第 14 讲 | 学习小组清除和 Portal Network

本次演讲的重点是 History Expiry，并深入探讨 Portal Network，这是轻客户端的覆盖网络，支持对当前和历史数据的替代访问。 

观看 [Piper Merriam](https://twitter.com/parithosh_j) 在 [StreamEth](https://streameth.org/65cf97e702e803dbd57d823f/epf_study_group) 和 [Youtube](https://www.youtube.com/@ethprotocolfellows/streams) 上的演示。

<iframe width="560" height="315" src="https://www.youtube.com/embed/GxNrGyQB-3Q?si=gRPhA35dNYPEGDeZ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预读

在开始研究本研究内容之前，请先熟悉前几周的资源，尤其是路线图上的第 5 天演示。您应该了解执行客户端的数据库结构、古代数据和当前数据的区别。 

此外，您可以通过学习以下资源来做好准备：
- [利用 Ethereum 的 Portal Network 对 Ethereum 进行去中心化访问](https://www.youtube.com/watch?v=LZ_yWmm7ISg)
- [无状态和 History Expiry，Ethereum.org](https://ethereum.org/en/roadmap/statelessness/)
- [Portal Network web](https://www.ethportal.net/)
- [EL 数据结构](https://epf.wiki/#/wiki/EL/data-structures)

## 概要

- Ethereum 执行客户端中的历史记录到期
- Portal Network

## 额外阅读和练习

- EIP-4444
  - https://eips.ethereum.org/EIPS/eip-4444
- SELFDESTRUCT 删除 EIPS (许多都是陈旧的)
  - https://eips.ethereum.org/EIPS/eip-6049
  - https://eips.ethereum.org/EIPS/eip-6780
  - https://eips.ethereum.org/EIPS/eip-2936
  - https://eips.ethereum.org/EIPS/eip-4758
  - https://eips.ethereum.org/EIPS/eip-4760
  - https://eips.ethereum.org/EIPS/eip-6046
  - https://eips.ethereum.org/EIPS/eip-6190
  - EOF 事物：https://hackmd.io/@shemnon/mega-eof-scoping
- LOG 改革
  - https://eips.ethereum.org/EIPS/eip-7668
  - https://github.com/ethereum/EIPs/pull/8368
- 地址空间扩展
  - https://github.com/ethereum/EIPs/pull/8385
  - https://ethereum-magicians.org/t/thoughts-on-address-space-extension-ase/6779
  - https://ethereum-magicians.org/t/increasing-address-size-from-20-to-32-bytes/5485
  - https://notes.ethereum.org/@ipsilon/address-space-extension-exploration
- 状态到期
  - https://notes.ethereum.org/@vbuterin/verkle_and_state_expiry_proposal
  - https://medium.com/@chaisomsri96/statelessness-series-part1-state-expiry-history-expiry-2bbd5835b329 (没有完全审查这篇文章)
  - https://notes.ethereum.org/@vbuterin/state_expiry_eip
  - https://hackmd.io/@vbuterin/state_expiry_paths
- 一般无状态路线图事物
  - https://ethereum.org/en/roadmap/statelessness/
- [Portal Network，EthZurich 2023](https://www.youtube.com/watch?v=8MUii5W2sMc)
