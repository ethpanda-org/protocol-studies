# MEV-boost：流行的 PBS 实现

[MEV-boost](https://github.com/flashbots/mev-boost) 是 Proposer-Builder Separation (PBS) 的广泛认可的协议外开源实现。它允许验证者将 区块构建外包给专门的构建者，这可能会增加验证者奖励并提高网络效率。

以下是 mev-boost 的工作原理：

<figure style="text-align: center;">
  <img src="images/pbs/block-building.png" alt="Block-building">
  <figcaption style="text-align: center;">当前区块大楼。来源：<a href="https://barnabe.substack.com/p/pbs">Barnabé's article</a></figcaption>
</figure>

一方面，mev-boost 实现了 Ethereum 节点使用的 [构建者 API](https://github.com/ethereum/builder-specs)，以外包区块的生产。另一方面，它连接到中继的网络并处理构建者和 提议者之间的通信。

1. **区块大楼：**
   专业构建者竞相为提议者打造最赚钱的区块。他们通过优化交易排序和包含来实现这一点，同时考虑 gas 费用、交易优先级和潜在 [MEV (最大可提取价值)](/wiki/research/PBS/mev.md) 等因素。
   构建者将构建的区块提交给中继。
2. **中继网络：**
   MEV-boost 运营着一个中继网络，充当构建者和 提议者之间的中介。
   中继从 构建者接收区块并执行各种功能，例如区块验证、过滤和传播。
   中继确保只有有效且高质量的区块才会发送到提议者。
3. **提议者选择：**
   验证者运行连接到其 Beacon node 的 mev-boost 软件。当选择验证者来提议区块时，他们会从中继接收区块，并根据预定义的标准选择最好的一个，通常是提供最高奖励的区块。
   然后，验证者将选定的区块提议给网络进行验证并包含在区块链中。

## PBS 区块创建

通过 PBS 创建区块的过程如下：

### 区块施工

- 构建者持续监视交易池 (内存池) 中是否有新的交易。他们根据潜在的 MEV 机会评估这些交易。他们选择最符合其 MEV 优化标准的交易。此外，区块构建者可以从私人订单流或 MEV 搜索者中获取交易捆绑包，就像矿工在原始 Flashbots 拍卖中的 PoW Ethereum 中所做的那样。在后一种情况下，构建者接受搜索者的密封价格出价，并将其捆绑包包含在区块中。
- 一旦选择了交易，构建者将它们组装成区块，确保区块遵守 Ethereum 协议的规则，例如：例如，txs 有效，gas 限制未超过。

### 区块拍卖

标准做法是使用中继，而不是将构建者直接以指定价格将组装好的区块提供给验证者。 中继在将标头传递到提议者 (验证者) 之前验证交易包。 提议者接受并使用其密钥对标头进行签名，并将 SignedHeader 返回给中继。现在，中继将完整的区块释放到提议者。此外，实现还可以引入托管，负责通过存储构建者发送的区块和 验证者发送的承诺来提供数据可用性。 

### mev-boost 的好处：

- **增加验证者奖励：** 通过将区块构建外包给专门的构建者，验证者可以通过优化交易排序和 MEV 提取获得更高的奖励。
- **减少中心化：** MEV-boost 能够打造更具竞争力的区块构建格局，降低大型矿池的规模经济，并使家庭质押者获得同样的奖励。

### 挑战和考虑因素：

虽然 mev-boost 提供了一定的好处，但它也引起了一些担忧：

- **安全性：** 引入新的参与者和依赖项可能会产生新的攻击向量和漏洞。由于 mev-boost 问题，在主网上发生了多起错过区块的 [事件](https://collective.flashbots.net/t/post-mortem-april-3rd-2023-mev-boost-relay-incident-and-related-timing-issue/1540)。 
- **抗审查性：** 如果只有少数强大的构建者或 中继主导生态系统，则可能会导致中心化和审查问题。
- **协调：** 构建者、中继和 提议者之间的有效沟通和协调对于 mev-boost 的顺利运行至关重要。

值得注意的是，mev-boost 只是 PBS 的一种实现。其他具有不同设计和功能的实现也正在开发和探索中，例如 [mev-rs](https://github.com/ralexstokes/mev-rs) 正在开发中。

总体而言，mev-boost 代表着在 Ethereum 中实现 PBS 的潜在优势迈出了重要一步。然而，持续的研究和开发对于应对挑战并确保安全、分散和高效的实施至关重要。实现更稳定的 PBS 模型的一种途径是 [将其纳入协议](/wiki/research/PBS/ePBS.md)，将类似 mev-boost 的功能直接添加到 Ethereum 客户端中。  

## 资源

- [关于 mev-boost 的 Flashbots 文档](https://boost.flashbots.net/)
- [审查实体概述](https://censorship.pics/) 
- https://www.mevwatch.info/
- [MEV-Boost：合并就绪的 Flashbots 架构，2021 年](https://ethresear.ch/t/mev-boost-merge-ready-flashbots-architecture/11177)
