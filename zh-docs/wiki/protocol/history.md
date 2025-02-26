# 协议历史与演进
> :警告: 本文是一个存根[stub](https://en.wikipedia.org/wiki/Wikipedia:Stub)，通过 [contributing](/contributing.md) 帮助维基并扩展它。
本页面重点介绍了以太坊协议历史上的重要技术变更。
有用的链接：[Overview from Ethereum.org](https://ethereum.org/en/history) 和 [Meta EIPs from Ethereum.org](https://eips.ethereum.org/meta) 。
## 前沿 (Frontier)
前沿版本标志着以太坊协议的正式发布。该版本本质上是一个测试版，允许开发者学习、实验并开始构建以太坊去中心化应用和工具。它于 2015 年 7 月 30 日 UTC 时间凌晨 3:26:13 发布，这也是以太坊创世区块 [Ethereum genesis block](https://etherscan.io/block/0) 的时间戳。前沿版本发布时的 Gas 上限为 5000。这一 Gas 上限被硬编码到协议中，以确保矿工和用户能够在协议初始发布时通过安装客户端启动并运行。随后，通过前沿解冻分叉，Gas 上限被提高到 300 万（确切值为 3,141,592）。金丝雀合约是一种提供二进制信号（0 或 1）的合约。这些金丝雀合约是仅在以太坊前沿版本发布期间使用的初始启动机制。如果客户端检测到多个合约的信号变为 1，它们将停止挖矿并敦促用户更新客户端。这通过确保矿工不会阻止链升级，避免了长时间的停机。
了解更多关于前沿的信息，请参阅以下资源：
- [Frontier is coming, what to expect and how to prepare](https://blog.ethereum.org/2015/07/22/frontier-is-coming-what-to-expect-and-how-to-prepare)
- [The thawing frontier](https://blog.ethereum.org/2015/08/04/the-thawing-frontier)
- [ethereum.org web archive](https://web.archive.org/web/20150802035735/https://www.ethereum.org/)
- [ethereum-protocol-update-1](https://blog.ethereum.org/2015/08/04/ethereum-protocol-update-1)
前沿即将到来，期待什么以及如何准备 (https://blog.ethereum.org/2015/07/22/frontier-is-coming-what-to-expect-and-how-to-prepare)
解冻的前沿 (https://blog.ethereum.org/2015/08/04/the-thawing-frontier)
ethereum.org 
网页存档 (https://web.archive.org/web/20150802035735/https://www.ethereum.org/)
以太坊协议更新1 (https://blog.ethereum.org/2015/08/04/ethereum-protocol-update-1)
## 家园 (Homestead)
家园是以太坊协议的第二个主要版本，于 2016 年 3 月 14 日正式发布，标志着以太坊从测试阶段过渡到一个更加成熟和稳定的平台。以下是家园阶段引入的一些显著特性和变更：
- [EIP-2](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2.md)：EIP-2 包含了多项修复，例如通过交易创建合约的 Gas 成本增加，确保合约创建要么成功要么失败（防止创建空合约），以及对难度调整算法的修改。
  1. **增加合约创建的 Gas 成本：**
通过交易创建合约的 Gas 成本从 21,000 增加到 53,000。这一变更旨在减少通过交易创建合约的过度激励，而通过合约内的 CREATE 操作码创建合约的成本保持不变。
  2. **高 s 值签名的无效化：**
现在，s 值大于 secp256k1n/2 的交易签名被视为无效。这一措施解决了交易延展性问题，防止通过翻转 s 值（s -> secp256k1n - s）来改变交易哈希。这一变更提高了交易跟踪的可靠性和完整性。
  3. **合约创建的 Gas 不足处理：**
如果合约创建没有足够的 Gas 来支付将合约代码添加到状态的最终 Gas 费用，合约创建将失败（即 Gas 耗尽），而不是留下一个空合约。
  5. **修改难度调整算法：**
难度调整算法被修改以解决在前沿阶段观察到的问题。新公式旨在通过根据区块之间的时间戳差异调整难度，保持目标区块时间并防止过度偏差。
- [EIP-7](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7.md):EIP-7 引入了 DELEGATECALL 操作码。
  一个新的操作码 DELEGATECALL 被添加到 0xf4。它的功能类似于 CALLCODE，但将发送者和值从父作用域传播到子作用域。传播发送者和值使得合约更容易将另一个地址存储为可变的代码源，并「传递」调用给它。与 CALL 操作码不同，没有额外的 Gas 津贴，这使得 Gas 管理更加可预测。
- [EIP-8](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-8.md)：EIP-8 通过引入 devp2p 前向兼容性要求，确保以太坊客户端支持未来的网络升级。
   **devp2p 有线协议**、**RLPx 发现协议**和**RLPx TCP 传输协议** 规定，实现应宽松地接受数据包，忽略版本号和 hello 及 ping 数据包中的额外列表元素，静默丢弃未知的数据包类型，并接受加密密钥建立握手数据包的新编码。这确保了所有客户端软件能够应对未来的协议升级，并接受握手，允许宽松地接受来自他人的数据（参见 [Postel's Law](https://en.wikipedia.org/wiki/Robustness_principle) ）。
  
了解更多关于家园的信息，请参阅以下资源：
- [Ethereum Homestead Documentation](https://readthedocs.org/projects/ethereum-homestead/downloads/pdf/latest/)
- [The Robustness Principle Reconsidered](https://queue.acm.org/detail.cfm?id=1999945)
- [Homestead blog release post](https://blog.ethereum.org/2016/02/29/homestead-release)
- [The Homestead release - github](https://github.com/ethereum/homestead-guide/blob/master/source/introduction/the-homestead-release.rst)

## The Merge

在 2022 年 9 月 15 日，以太坊激活了 [EIP-3675](https://eips.ethereum.org/EIPS/eip-3675) ，并通过一项被称为 The Merge 的事件升级了共识机制，转向了权益证明（proof-of-stake）。The Merge 使得之前采用的工作量证明（proof-of-work）共识机制被废弃，工作量证明机制以前是与执行层在同一逻辑层中实现的。取而代之的是一种更复杂、更精密的权益证明共识机制，它不再需要高能耗的挖矿操作。新的权益证明共识机制被实现于其独立的层，拥有单独的点对点网络和逻辑系统，也被称为 Beacon Chain。Beacon Chain 自 2020 年 12 月 1 日起就已经运行并达成共识，在长时间稳定运行且没有任何故障后，它被认为已经准备好成为以太坊的共识提供者。The Merge 这个名称来源于两种网络的合并。
Learn more about The Merge in following resources and reading on Consensus layer. 
通过以下资源和阅读共识层相关内容，了解更多关于 The Merge 的信息。
 - [EIP-3675: Upgrade consensus to Proof-of-Stake](https://eips.ethereum.org/EIPS/eip-3675), [archived](https://web.archive.org/web/20240213102133/https://eips.ethereum.org/EIPS/eip-3675)
- [Gasper](https://ethereum.org/developers/docs/consensus-mechanisms/pos/gasper), [archived](https://web.archive.org/web/20240214225630/https://ethereum.org/developers/docs/consensus-mechanisms/pos/gasper)
- [Mega Merge Resources List](https://notes.ethereum.org/@MarioHavel/merge-resources), [archived](https://web.archive.org/web/20240302082121/https://notes.ethereum.org/@MarioHavel/merge-resources)
