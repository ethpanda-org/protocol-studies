# 值得注意的主网事件

> ：警告：本文是一个[存根](https://en.wikipedia.org/wiki/Wikipedia:Stub)，通过[贡献](/contributing.md) 和扩展它来帮助维基。
> 事件是网络面临的中断、漏洞和攻击，对其稳定性和安全性提出质疑。以下是影响网络的一些事件及其来源。

## 概述

以太坊网络在其历史上面临着各种挑战和事件。通过仔细分析和实施预防措施，这些事件有助于提高网络的弹性和安全性。本页记录了影响以太坊的值得注意的事件。

有关以太坊事件的完整列表及其详细分析，您可以参考[EthStaker事件页面](https://ethstaker.org/incidents)。

## 最近发生的事件

- [事后分析，Holesky 最终确定性问题 (24/02/2025)](https://github.com/ethereum/pm/blob/master/Pectra/holesky-postmortem.md)
  2025 年 2 月，Pectra 对 Holesky 测试网进行升级后，由于许多 EL 客户端的充值合约地址配置不正确，区块未能最终确定。这导致一些EL 客户端拒绝无效的区块，而另一些则接受它们，从而导致网络分裂。

- [事后分析，blob 传播问题 (27/03/2024)](https://gist.github.com/benhenryhunter/687299bcfe064674537dc9348d771e83)
  2024 年 3 月，Dencun 升级后，blob 从某些构建者附加到区块，通过 p2p 传播太慢，导致客户端实现错过了几个时隙。

- [验尸报告：以太坊主网 DOS 事件 (07/02/2024)](https://blog.ethereum.org/2024/03/21/sepolia-incident)
  人们发现，从 dencun 硬分叉发生合并时起，就有可能发生拒绝服务攻击。攻击者可以创建超过指定限制 5mb 的区块，并将多个交易添加到区块中，每个交易不超过 128kb，同时还确保区块中的交易的集体 gas 低于 30万。通过此操作，大多数节点将拒绝区块，这将导致少数节点接受创建分叉区块并错过提议者奖励。

- [验尸报告：以太坊主网最终确定性 (05/11/2023)](https://medium.com/offchainlabs/post-mortem-report-ethereum-mainnet-finality-05-11-2023-95e271dfd8b2)
  主网出现了一些中断，导致区块无法生成，导致交易到达最终确定性的时间明显延迟，这种情况持续了两天并导致不活动的后果，网络在无需干预的情况下完全恢复。

- [Reth 主网状态根不匹配 (01/09/2025](https://laced-king-de5.notion.site/Incident-Post-Mortem-Reth-Mainnet-State-Root-Mismatch-26732f2c348480dea8b8c2a8753696dc)
  Reth 处理 Trie 更新的错误导致 Reth 节点中的 Trie 表包含不正确的信息，从而导致节点在稍后的区块处计算出不正确的状态根。 

## 历史事件

- [事后分析报告：少数派分裂(2021-08-27)](https://github.com/ethereum/go-ethereum/blob/master/docs/postmortems/2021-08-22-split-postmortem.md)
  当 Geth 在 `datacopy` 操作后尝试将数据分配回内存时，就会发生这种情况。它没有将数据保存在新位置，而是意外覆盖了原始数据，导致其损坏。

- [DAO 攻击 (2016)](https://www.coindesk.com/learn/understanding-the-dao-attack)
  以太坊历史上最重大的事件之一，其中 DAO 智能合约中的漏洞被利用，导致约 360 万个 ETH 丢失。这一事件最终导致了以太坊区块链的硬分叉，创建了以太坊 Classic(ETC)和当前的以太坊(ETH)链。

- [上海DOS攻击(2016)](https://ethos.dev/shanghai-attacks)
  在上海 DevCon2 期间，网络面临一系列 DOS 攻击，攻击者利用价格低廉的 EVM 操作码(特别是 EXTCODESIZE)来减慢区块处理速度，从而导致网络拥塞。这导致了后续的硬分叉(Tangerine Whistle 和 Spurious Dragon)，调整了目标操作码的 gas 成本，以防止类似的攻击。

