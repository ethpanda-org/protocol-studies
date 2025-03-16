## Mev-boost：一种流行的 PBS 实现

[Mev-boost](https://github.com/flashbots/mev-boost) 是一个被广泛认可的、协议外的开源提议者-构建者分离（PBS）实现。它允许验证者将区块构建工作外包给专业构建者，从而提高验证者的收益，并提升网络效率。

### Mev-boost 工作机制

<figure style="text-align: center;">
  <img src="images/pbs/block-building.png" alt="区块构建示意图">
  <figcaption style="text-align: center;">当前区块构建过程。来源：<a href="https://barnabe.substack.com/p/pbs">Barnabé 的文章</a></figcaption>
</figure>

Mev-boost 主要实现了两个核心功能：

- 提供 [构建者 API](https://github.com/ethereum/builder-specs)，允许以太坊节点将区块生产外包。
- 连接到中继网络，处理构建者与提议者之间的通信。

#### 工作流程

1. **区块构建**：
   - 专业构建者竞争构建最具盈利能力的区块。他们通过优化交易排序和包含策略来最大化收益，考虑因素包括 Gas 费用、交易优先级以及潜在的 [最大可提取价值（MEV）](/wiki/research/PBS/mev.md)。
   - 构建者将构建的区块提交给中继。

2. **中继网络**：
   - Mev-boost 依赖一组中继充当构建者与提议者之间的中介。
   - 中继接收区块并执行验证、筛选和传播等功能，确保提议者只收到有效且高质量的区块。

3. **提议者选择**：
   - 验证者运行 mev-boost 软件，并连接到信标节点。
   - 当某个验证者被选为区块提议者时，它会从中继处接收多个区块，并选择最优区块（通常是奖励最高的区块）。
   - 选定的区块随后被提交到网络进行验证，并最终加入区块链。

## PBS 区块创建过程

### 1. 区块构建

- 构建者持续监控交易池（mempool），评估交易的 MEV 机会，并挑选最符合 MEV 优化标准的交易。
- 除了普通交易外，构建者还可以从私有订单流或 MEV 搜索者那里获取交易包。
- 一旦选择了交易，构建者会将其组装成符合以太坊协议规则的区块（如交易有效、Gas 限制未超标等）。

### 2. 区块拍卖

- 构建者不会直接向验证者提供区块，而是通过中继进行验证。
- 中继在传递区块头部（Header）给提议者之前，会对交易包进行验证。
- 提议者收到区块头部后，使用私钥对其签名，并返回签名后的 Header 给中继。
- 中继随后释放完整区块给提议者。
- 一些实现方案还可以引入托管机制（Escrow），以保证数据可用性，并存储构建者发送的区块和验证者的承诺。

## Mev-boost 的优势

- **提高验证者收益**：通过将区块构建外包给专业构建者，验证者可以通过优化的交易排序和 MEV 提取获得更高收益。
- **减少中心化**：Mev-boost 促进了更加竞争性的区块构建环境，降低了大型矿池的规模经济效应，使家庭验证者（home stakers）也能获得与大规模参与者类似的收益。

## Mev-boost 的挑战与考量

尽管 Mev-boost 具有诸多优势，但它也带来了一些问题：

- **安全性**：引入新角色和依赖关系可能会带来新的攻击向量和漏洞。例如，Mev-boost 曾因故障导致主网出现多个[区块丢失事件](https://collective.flashbots.net/t/post-mortem-april-3rd-2023-mev-boost-relay-incident-and-related-timing-issue/1540)。
- **抗审查性**：如果少数强势的构建者或中继在生态系统中占据主导地位，可能会引发中心化和审查问题。
- **协调性**：构建者、中继和提议者之间的高效通信和协调对于 Mev-boost 的顺利运行至关重要。

需要注意的是，Mev-boost 只是 PBS 的一种实现。目前还有其他不同设计的 PBS 方案正在开发和探索，例如 [mev-rs](https://github.com/ralexstokes/mev-rs) 也是一个正在进行的 PBS 研究项目。

总体而言，Mev-boost 在 PBS 发展道路上迈出了重要一步。然而，持续的研究和开发对于解决现存挑战并确保 PBS 方案的安全性、去中心化和高效性至关重要。一个可能的长期方案是[将 PBS 直接内置到协议中](/wiki/research/PBS/ePBS.md)，在以太坊客户端中原生支持类似 Mev-boost 的特性。

## 相关资源

- [Flashbots 关于 mev-boost 的文档](https://boost.flashbots.net/)
- [审查实体概览](https://censorship.pics/)
- [MEV 观察站](https://www.mevwatch.info/)
- [MEV-Boost：Merge 时代的 Flashbots 架构（2021）](https://ethresear.ch/t/mev-boost-merge-ready-flashbots-architecture/11177)

