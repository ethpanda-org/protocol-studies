# 以太坊主网重大事件

> :warning: 这篇文章是一个[小作品](https://en.wikipedia.org/wiki/Wikipedia:Stub)，欢迎通过[贡献](./contributing.md)来扩展内容。
> 
> 事件指的是网络遭遇的中断、漏洞和攻击，这些事件对以太坊的稳定性和安全性提出了质疑。以下列出了一些影响网络的事件及其相关来源。

## 概述

以太坊网络在其发展过程中经历了各种挑战和事件。这些事件促使社区进行深入分析，并实施预防措施，从而提高网络的韧性和安全性。本页面记录了影响以太坊的重大事件。

若需更全面的以太坊事件列表及详细分析，可参考 [EthStaker 事件页面](https://ethstaker.org/incidents)。

## 近期事件

- [事件复盘：Blob 传播问题（2024-03-27）](https://gist.github.com/benhenryhunter/687299bcfe064674537dc9348d771e83)  
  2024 年 3 月，在 Dencun 升级后，某些区块构建者附加的 blob 在 P2P 网络中的传播速度过慢，导致部分客户端实现错过了一些区块插槽（slots）。

- [事件复盘报告：以太坊主网 DOS 攻击（2024-02-07）](https://blog.ethereum.org/2024/03/21/sepolia-incident)  
  发现自合并（The Merge）以来，直至 Dencun 硬分叉，以太坊主网存在潜在的拒绝服务（DoS）攻击风险。攻击者可创建超出 5MB 限制的区块，并在其中加入多个不超过 128KB 的交易，同时确保这些交易的总消耗 gas 低于 3000 万。这样大多数节点将拒绝这些区块，导致少数节点接受区块，从而引发区块分叉及提议者奖励损失。

- [事件复盘报告：以太坊主网终局性问题（2023-11-05）](https://medium.com/offchainlabs/post-mortem-report-ethereum-mainnet-finality-05-11-2023-95e271dfd8b2)  
  以太坊主网出现中断，导致区块未能正常出块，交易终局性（Finality）严重延迟，持续两天，并触发了非活跃性惩罚（Inactivity Penalties）。最终，网络在未进行人为干预的情况下完全恢复。

## 历史事件

- [事件复盘报告：少数派链分裂（2021-08-27）](https://github.com/ethereum/go-ethereum/blob/master/docs/postmortems/2021-08-22-split-postmortem.md)  
  该事件发生在 Geth 客户端尝试将数据写入内存时。由于 `datacopy` 操作的错误，数据未能存储到新位置，而是意外覆盖了原始数据，导致数据损坏并引发链分裂。

- [The DAO 攻击（2016）](https://www.coindesk.com/learn/understanding-the-dao-attack)  
  以太坊历史上最严重的事件之一。The DAO 智能合约存在漏洞，黑客利用该漏洞成功盗取约 360 万枚 ETH。此事件最终导致以太坊进行硬分叉，形成了 Ethereum Classic（ETC）和当前的 Ethereum（ETH）两条链。

- [上海 DOS 攻击（2016）](https://ethos.dev/shanghai-attacks)  
  该事件发生在 DevCon2 期间，攻击者利用以太坊虚拟机（EVM）部分操作码（特别是 `EXTCODESIZE`）的定价漏洞，导致区块处理速度大幅下降，并引发严重的网络拥堵。此次攻击促成了后续的 Tangerine Whistle 和 Spurious Dragon 硬分叉，这些升级调整了部分操作码的 gas 费用，以防止类似攻击再次发生。
