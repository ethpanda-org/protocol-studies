# 第 20 讲 | 学习小组验证者客户端

学习小组的介绍部分现已结束，我们现在开始通过核心开发人员的现场会议进行更深入的探讨。

本讲座重点介绍验证者客户端在 Consensus Layer 中的实现。该演讲由 Prysm 的开发人员 [James](https://github.com/james-prysm) 进行，他负责 Beacon Chain 的实现。

[幻灯片](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/day20_validator.pdf)。

在 [Youtube](https://www.youtube.com/watch?v=rgrNMbYrOmM) 上观看讲座并按照 Prysm 的 [幻灯片](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/day20_validator.pdf) 和代码示例进行操作。

<iframe width="560" height="315" src="https://www.youtube.com/embed/rgrNMbYrOmM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预读

在开始第 20 天的内容之前，请先熟悉前几周的资源，尤其是第 2 天的 CL、第 6 天的规范 (至少是 CL 部分) 和第 16 天的共识机制。您应该对 Beacon Chain、Ethereum Consensus Layer 和 验证者职责中的不同机制有总体了解。

此外，您可以通过学习以下资源来做好准备。

- [验证者激励措施注释规范](https://eth2book.info/capella/part2/incentives/)
- [Preston Van Loon 与 Prysm 的进化](https://www.youtube.com/watch?v=Lvlit-nIRfM)
- [密钥管理 API](https://ethereum.github.io/keymanager-APIs/)
- [远程签名者 API](https://github.com/ethereum/remote-signing-api)
- [验证者生命周期](https://docs.prylabs.network/docs/how-prysm-works/validator-lifecycle)

## 概要

- 验证者客户端
- 验证者服务初始化
- 密钥库管理
- 履行验证者职责

## 额外阅读和练习

- 在 Hoodi 或 Ephemery 上运行节点和 验证者客户端，按照日志查看其行为和功能
  - [在测试网上旋转您自己的 Ethereum 验证者 | Devcon 波哥大](https://www.youtube.com/watch?v=dWCck2IniNc)
- 通过 https://github.com/ethpandaops/ethereum-package 在本地开发网上测试您的验证者客户端
