# 学习小组 第三讲 | 执行层（Execution Layer）

在第三天，我们将深入研究以太坊的执行层（Execution Layer）。

可以通过以下链接观看深入讲解执行层内部机制的演讲：
- [StreamEth](https://streameth.org/watch?event=&session=65dcdef0a6d370a1ab326de1)
- [YouTube](https://www.youtube.com/watch?v=pniTkWo70OY)

演讲中的概览文档 [可在此查看](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/week2_notes.md?plain=1)。

[录播视频](https://streameth.org/embed/?playbackId=70f6rq6un48dy74q&vod=true&streamId=&playerName=Execution+Layer+Overview+%7C+lightclient+%7C+Week+2 ':include :type=iframe width=100% height=520 frameborder="0" allow="fullscreen" allowfullscreen')

若想了解第二周演讲的书面总结，请查看 [笔记](https://ab9jvcjkej.feishu.cn/docx/BRDdd8kP9o00a2x6F4scRo0fnJh)。

要查阅讲座期间的讨论记录，请查看我们 [Discord 服务器](https://discord.gg/epfsg)中的 [讨论线程](https://discord.com/channels/1205546645496795137/1210292746817110027/1210292751158222848)。

## 预习资料

在开始第二周的内容之前，请先熟悉 [第一周](./eps/week1.md) 的资源。

此外，您应阅读以下文档以准备演讲内容：
* [节点与客户端](https://ethereum.org/developers/docs/nodes-and-clients)
* [以太坊：机制](https://cs251.stanford.edu/lectures/lecture7.pdf)（该讲座的视频也可以在 YouTube 上找到：[以太坊执行层概述 - Dan Boneh](https://www.youtube.com/watch?v=7sxBjSfmROc)）

## 大纲

### 执行层节点概述
* **区块验证**
    * 简单来说，执行层（EL）处理状态转换。
    * 每笔交易都由客户端验证、执行，并将其结果累计到状态树中。
    * 还需要更新每个区块中的其他机制，例如 EIP-1559 基础费用、EIP-4844 剩余 blob gas、EIP-4844 信标根环缓冲区、信标链提取等。
    * 新节点还需要能够无太多摩擦地加入网络，因此 EL 提供高效的同步机制以引导其他节点加入。
* **区块构建**
    * 执行层根据网络中看到的交易构建区块。
    * 这需要通过 p2p 网络的交易池系统。

### 状态转换函数
* **头部验证**
    * 验证 Merkle 根
    * 验证 gas 限制
    * 验证时间戳
* **区块验证**
    * 演示 [`Process(..)`](https://github.com/ethereum/go-ethereum/blob/master/core/state_processor.go#L60) 函数在 `state_processor.go` 文件中的应用。

### EVM 高层概述
* **堆栈机器介绍**
* 查看简单程序
* 复习不同类别的操作码：堆栈/内存操控、环境获取、以太坊系统操作等。

### p2p 高层概述
* p2p 网络提供三项主要服务：
    * 历史数据
    * 待处理交易
    * 状态
* 讨论 **快照同步**：
    * 第一阶段：下载快照块
    * 第二阶段：修复

### JSON-RPC
* **以太坊接口**
    * 目标是所有客户端提供完全相同的 API，用户可以选择任何客户端，并与所有工具完美集成。
    * 虽然未完全实现这一目标，但我们已经接近。
* 复习主要的 RPC 方法。

## 额外阅读和练习

- [EVM.codes](https://www.evm.codes/)
- [Ethervm.io](https://ethervm.io/)
- [Go-Ethereum GitHub](https://github.com/ethereum/go-ethereum)
- [以太坊共识规范 GitHub](https://github.com/ethereum/consensus-specs)
- [以太坊执行规范 GitHub](https://github.com/ethereum/execution-specs)
- [以太坊开发 P2P GitHub](https://github.com/ethereum/devp2p)
- [以太坊执行 API GitHub](https://github.com/ethereum/execution-apis)
- [以太坊 2.0 重命名的博客文章](https://blog.ethereum.org/2022/01/24/the-great-eth2-renaming)
- [以太坊合并如何影响应用层的博客文章](https://blog.ethereum.org/2021/11/29/how-the-merge-impacts-app-layer)
- [Engine API: 视觉指南](https://hackmd.io/@danielrachi/engine_api) by [Daniel Ramirez](https://hackmd.io/@danielrachi)
- [通过研究源代码理解以太坊](https://gisli.hamstur.is/2020/08/understanding-ethereum-by-studying-the-source-code/)

Lightclient 的 vim 和 shell 设置：[Lightclient dotfiles](https://github.com/lightclient/dotfiles)
