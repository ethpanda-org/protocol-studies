# 第 3 讲 | 学习小组执行层

第三天，我们将深入以太坊的执行层。 

在 [StreamEth](https://streameth.org/watch?event=&session=65dcdef0a6d370a1ab326de1) 或 [Youtube](https://www.youtube.com/watch?v=pniTkWo70OY) 上观看使用 Lightclient 深入了解 EL 内部结构的演示。 

演示文稿中创建的概述文档可[在此处获取](https://github.com/eth-protocol-fellows/protocol-studies/blob/main/docs/eps/presentations/week2_notes.md?plain=1)。

[录音](https://streameth.org/embed/?playbackId=70f6rq6un48dy74q&vod=true&streamId=&playerName=Execution+Layer+Overview+%7C+lightclient+%7C+Week+2 ':include :type=iframe width=100% height=520 frameborder="0" allow="fullscreen" allowfullscreen')

有关第 2 周演示的书面摘要，请查看[注释](https://ab9jvcjkej.feishu.cn/docx/BRDdd8kP9o00a2x6F4scRo0fnJh)

有关演讲期间讨论的存档，请检查我们的[Discord 服务器](https://discord.gg/epfsg) 中的[此线程](https://discord.com/channels/1205546645496795137/1210292746817110027/1210292751158222848)。 

## 预读

在开始第 2 周的内容之前，请先熟悉[第 1 周](/eps/week1.md) 中的资源。 

此外，您应该阅读以下文档来准备演示：
* [节点和 客户端](https://ethereum.org/developers/docs/nodes-and-clients)
* [以太坊：力学](https://cs251.stanford.edu/lectures/lecture7.pdf)(基于这些幻灯片的讲座也可以在 YouTube 上找到：[以太坊执行层概述 - Dan Boneh](https://www.youtube.com/watch?v=7sxBjSfmROc))

## 概要

###  执行层节点概述
* 区块验证
    * 用过于简单的术语来说，EL 处理状态转换
    * 每个交易都由客户端验证、执行，其结果累积到状态 Trie 中
    * 还有其他机制也必须每个区块进行更新，例如 EIP-1559 基本费用、EIP-4844 超额 blob gas、EIP-4844 信标根环缓冲区、信标链提款等。
    * 新的节点也必须能够在没有太多摩擦的情况下加入网络，因此 EL 提供高效的同步机制来引导其他人
* 区块构建
    * EL 还根据他们在网络上看到的交易构建区块
    * 这需要一个基于 p2p 的交易池系统

### 状态转换函数
* 标头验证
    * 验证默克尔根
    * 验证 gas 限制
    * 验证时间戳
* 区块验证
    * `state_processor.go` 中的演练 [`Process(..)`](https://github.com/ethereum/go-ethereum/blob/master/core/state_processor.go#L60)

### EVM 高级
* 堆垛机介绍
* 看看简单的程序
* 查看操作码的不同类别：堆栈/内存操纵器、env getter、以太坊系统操作等。

### p2p高级
* p2p主要服务于三件事
    * 历史数据
    * 待处理的交易
    * 状态
* 讨论快照同步
    * 第 1 阶段：下载快照图块
    * 第二阶段：愈合

### JSON-RPC
* 以太坊的“接口”
    * 我们的愿景是所有客户端都提供完全相同的 API，并且用户可以运行他们选择的任何客户端并与所有工具完美集成
    * 还没有完全实现，但我们已经相当接近了
* 回顾主要的 RPC 方法

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
- [引擎 API：视觉指南](https://hackmd.io/@danielrachi/engine_api) 作者：[Daniel Ramirez](https://hackmd.io/@danielrachi)
- [通过研究以太坊的源码来理解以太坊](https://gisli.hamstur.is/2020/08/understanding-ethereum-by-studying-the-source-code/)

Lightclient的vim和shell设置https://github.com/lightclient/dotfiles