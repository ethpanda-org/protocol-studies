# 戈隆·丹笔记

## 目标

加入这个项目的目的是为了更深入地了解使应用层一切成为可能的基础设施，以及进一步的方向，同时扩展我的协议知识。此外，我非常有兴趣了解当前在协议级别进行的工作将如何在中短期内影响应用程序层。
阅读了之前的 EPF 程序中的材料，我意识到在完成 EPS 并进入 EPF 后，我们将不得不变得更加技术/实用。据我了解，这意味着确定我们可以添加自己的兴趣领域并设定明确的项目目标，从而开展实际项目，并具有明确的可交付成果期望(改进/应用研究/分析工具)。

## 感兴趣的领域

* 质押设计 - 我认为这个主题超越了正在进行和未来的协议开发，它将在中短期内对应用层产生明显的影响。也是长期的，但这最有可能受到[时间因素](../docs/wiki/research/roadmap.md#roadmap-overview)的影响。

## 潜在项目
我的目标是参与一个项目，该项目将提出最小可行的流动性抵押。 

Vitalik 在本文档 [[1]](#resources) 中设定了基本原理，这里有一个 Ethresearch 提案 [[2]](#resources)。

一个例子可以是：“编写 EIP 和相应的 POC 实现，以实现*不可侵犯的运营商与委托人分离*，或*增加无需信任的流动性质押可行性”*。

### 可能的拟议工作：
* 添加协议内验证者 **快速质押密钥**(Q 键)以及当前所需的**持久质押密钥**(P 键)
* 调整协议以考虑 P+Q 公共验证者密钥格式(可能的方向)：
    - 适应削减设计和/或
    - 调整[包含列表](../docs/wiki/research/inclusion-lists.md) 设计

### 挑战
* 该项目不应影响验证者的经济性，但应提出简单的协议调整，以实现最小化可行的流动性质押。
* 供奉有它的局限性[[3]](#resources)
* 项目期间要提出和回答的问题：
    - 项目提议的规范变更的实施真的会改善质押场景吗？ 
    - 大型质押池中的大量委托人可能会在网络中造成异常(区块生产延迟，错过证明)。一种测试工具，是第三队列 [[4]](#resources) 中先前 EPF 工作的一部分。
    - 拟议的变更将如何与其他正在进行的开发相互作用，例如增加 MAX_EFFECTIVE_BALANCE(EIP-7215)[[5]](#resources)、[light-客户端](../docs/wiki/research/light-clients.md)、SSF 等？它会产生总体积极影响并与当前/之前的协议改进很好地结合起来吗？

### 可能的预期效果
* 中短期： 
    - 改进协议和质押池的去中心化
    - 允许质押委托人以有意义的方式参与共识； 
* 长期： 
    - 为消除质押风险铺平道路 ETH
    - 为SSF铺平道路

我期待收到感兴趣的同学和愿意的导师的来信，以探索最佳的项目轨道🤝

## 资源
[[1] 协议和质押池的变化可以改善去中心化并减少共识开销](https://notes.ethereum.org/@vbuterin/staking_2023_10)

[[2]分拆质押](https://ethresear.ch/t/unbundling-staking-towards-rainbow-staking/18683)

[[3] 以太坊是否可以在协议中包含更多内容？](https://vitalik.eth.limo/general/2023/09/30/enshrinement.html#what-do-we-learn-from-all-this)

[[4]质押池证明的链上分析](https://github.com/eth-protocol-fellows/cohort-three/blob/master/projects/On-chain-analysis-of-staking-pools.md)

[[5]EIP-7215当前研究](../docs/wiki/research/roadmap.md#current-research)