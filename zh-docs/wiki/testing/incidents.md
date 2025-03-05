# 主网重大事件

> :warning: 本文是一个[存根条目](https://en.wikipedia.org/wiki/Wikipedia:Stub)，请通过 [贡献](/contributing.md) 来帮助 wiki 扩展它。
> 这些事件包括网络遭遇的中断、漏洞和攻击，对其稳定性和安全性提出了质疑。以下是影响网络的一些事件及其报告。

- [事后报告：Blob 传播问题 (2024/3/27)](https://gist.github.com/benhenryhunter/687299bcfe064674537dc9348d771e83) 2024 年 3 月，Dencun 升级后，某些构建者生成的区块附带的 blobs 在对等网络中的传播速度过慢，导致某个客户端实现错过了一些时隙。

- [事后报告：以太坊主网 DOS 事件 (2024/2/7)](https://blog.ethereum.org/2024/03/21/sepolia-incident) 该事件揭示了一种从合并（Merge）到 Dencun 硬分叉期间一直存在的拒绝服务攻击可能性。攻击者可以创建一个超出规定限制 5 MB 的区块，并在其中添加多个不超过 128 KB 的交易，同时确保区块内交易的总 Gas 低于 3000 万。此操作会导致大多数节点拒绝该区块，导致少数节点接受，从而产生分叉区块，并使提议者错失奖励。

- [事后报告：以太坊主网最终确定性 (2023/5/11)](https://medium.com/offchainlabs/post-mortem-report-ethereum-mainnet-finality-05-11-2023-95e271dfd8b2) 主网出现了一些中断，导致区块未能正常生成，进而显著延迟了交易的最终确定性。该问题持续了两天，并触发了怠惰惩罚，最终网络在无需外部干预的情况下完全恢复。

- [事后报告：少数链分裂 (2021/08/27)](https://github.com/ethereum/go-ethereum/blob/master/docs/postmortems/2021-08-22-split-postmortem.md) 此事件发生在 Geth 客户端执行 `datacopy` 操作后，尝试将数据写回内存时。本应存储在新位置的数据意外覆盖了原始数据，导致数据损坏。
