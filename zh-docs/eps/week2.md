# 学习小组 第2周 | 执行层

在第二周，我们将深入探讨以太坊的执行层。

观看由[Lightclient](https://github.com/lightclient)主讲的关于执行层内部机制的演讲，[在StreamEth](https://streameth.org/watch?event=&session=65dcdef0a6d370a1ab326de1)或[Youtube](https://www.youtube.com/watch?v=pniTkWo70OY)。

演讲中创建的概述文档[可以在此处查看](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/week2_notes.md?plain=1)。

[录音](https://streameth.org/embed/?playbackId=70f6rq6un48dy74q&vod=true&streamId=&playerName=Execution+Layer+Overview+%7C+lightclient+%7C+Week+2 ':include :type=iframe width=100% height=520 frameborder="0" allow="fullscreen" allowfullscreen')

查看第2周演讲的文字总结，请参见[笔记](https://ab9jvcjkej.feishu.cn/docx/BRDdd8kP9o00a2x6F4scRo0fnJh)。

对于讲座期间的讨论存档，请查看我们[Discord 服务器](https://discord.gg/epfsg)中的[this thread](https://discord.com/channels/1205546645496795137/1210292746817110027/1210292751158222848)。

## 预阅读

在开始第2周的内容之前，请先熟悉[第1周](/eps/week1.md)的资源。

此外，您还应阅读以下文档，为演讲做好准备：
* [节点和客户端](https://ethereum.org/developers/docs/nodes-and-clients)
* [以太坊：机制](https://cs251.stanford.edu/lectures/lecture7.pdf)（基于这些幻灯片的讲座也可以在YouTube上观看：[以太坊执行层概述 - Dan Boneh](https://www.youtube.com/watch?v=7sxBjSfmROc)）

## 大纲

### 执行层节点概述
* 块验证
    * 简单来说，EL处理状态转换
    * 每个交易都由客户端验证、执行，并将结果累积到状态树中
    * 还有一些机制也必须在每个区块中更新，如EIP-1559的基础费用、EIP-4844的额外Blob gas、EIP-4844的信标根环缓冲区、信标链提取等
    * 新节点也必须能够加入网络而不遇到太大障碍，因此EL提供了高效的同步机制来引导其他节点
* 块构建
    * EL还基于它们在网络中看到的交易构建区块
    * 这需要一个基于P2P的交易池系统

### 状态转换函数
* 头部验证
    * 验证Merkle根
    * 验证gas限制
    * 验证时间戳
* 块验证
    * 演示 [`Process(..)`](https://github.com/ethereum/go-ethereum/blob/master/core/state_processor.go#L60) 在 `state_processor.go` 中

### EVM 高级概述
* 栈机简介
* 查看简单程序
* 回顾不同类别的操作码：栈/内存操作、环境获取器、以太坊系统操作等

### P2P 高级概述
* P2P提供三项主要服务
    * 历史数据
    * 待处理交易
    * 状态
* 讨论快照同步
    * 第一阶段：下载快照瓦片
    * 第二阶段：恢复

### JSON-RPC
* 以太坊的“接口”
    * 目标是所有客户端提供完全相同的API，用户可以运行任何客户端并与所有工具完美集成
    * 虽然尚未完全实现，但我们已经相当接近
* 回顾主要RPC方法

## 额外阅读和练习

- https://www.evm.codes/
- https://ethervm.io/
- https://github.com/ethereum/go-ethereum
- https://github.com/ethereum/consensus-specs
- https://github.com/ethereum/execution-specs
- https://github.com/ethereum/devp2p
- https://github.com/ethereum/execution-apis
- https://blog.ethereum.org/2022/01/24/the-great-eth2-renaming
- https://blog.ethereum.org/2021/11/29/how-the-merge-impacts-app-layer
- [Engine API: A Visual Guide](https://hackmd.io/@danielrachi/engine_api) by [Daniel Ramirez](https://hackmd.io/@danielrachi)
- [通过研究源代码理解以太坊](https://gisli.hamstur.is/2020/08/understanding-ethereum-by-studying-the-source-code/)

Lightclient的vim和shell设置：https://github.com/lightclient/dotfiles
