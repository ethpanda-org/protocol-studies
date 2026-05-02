# 第 17 讲 | 学习小组 EVM

学习小组的介绍部分现已结束，我们现在开始通过核心开发人员的现场会议进行更深入的探讨。

本周的第二场会议是由来自 Ipsilon 团队的 [<name>Paweł</name> Bylica](https://github.com/chfast) 的讲座深入探讨以太坊虚拟机 (EVM)。

在 [Youtube](https://www.youtube.com/watch?v=gYnx_YQS8cM) 上观看讲座录音。 [幻灯片](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/day17_evm.pdf)。

<iframe width="560" height="315" src="https://www.youtube.com/embed/gYnx_YQS8cM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## 预读

在开始第 17 天的内容之前，请先熟悉前几周的资源，尤其是 EL 上的第 3 天和 EL 客户端上的第 12 天。您应该大致了解什么是 EVM 以及执行层的不同部分。
对虚拟机、汇编器或解释器有一定的了解可能会很方便，但这不是必需的。

此外，您可以通过浏览以下与以太坊执行相关的概念来做好准备：

- [交易](https://ethereum.org/yo/developers/docs/transactions/)
- [帐号](https://ethereum.org/yo/developers/docs/accounts/)
- [以太坊虚拟机](https://ethereum.org/yo/developers/docs/evm/)
- [互动EVM 操作码](https://www.evm.codes)

## 概要

- 以太坊状态转换
- 进程虚拟机
- 以太坊虚拟机
- 内部通话
- gas计量
- EVM 对象格式简介 (EOF)
- EVM 中的控制流程

## 额外阅读和练习

- [EOF - Danno Ferrin 的历史和动机](https://www.youtube.com/watch?v=X2mlptWzphc)
- [EVM跳跃的历史与未来](https://www.youtube.com/watch?v=8Cp8IsmIJl4)
