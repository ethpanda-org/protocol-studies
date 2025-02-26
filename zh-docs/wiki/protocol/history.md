# 协议历史与演进
> :warning: This article is a [stub](https://en.wikipedia.org/wiki/Wikipedia:Stub), help the wiki by [contributing](/contributing.md) and expanding it.
> :警告: 本文是一个存根，帮助维基贡献并扩展它。
This page highlights important technical changes in the history of Ethereum protocol. 
本页面重点介绍了以太坊协议历史上的重要技术变更。
Useful links: [Overview from Ethereum.org](https://ethereum.org/en/history) and [Meta EIPs from Ethereum.org](https://eips.ethereum.org/meta)
有用的链接：以太坊.org 的概述 (https://ethereum.org/en/history) 和 (https://eips.ethereum.org/meta)
## Frontier
前沿
The Frontier release marked the launch of the Ethereum Protocol. The release was essentially a beta release that allowed developers to learn, experiment, and begin building Ethereum decentralized apps and tools. It was launched on July 30, 2015, at 3:26:13 AM UTC, which is the timestamp of the [Ethereum genesis block](https://etherscan.io/block/0). Frontier launched with a gas limit of 5000. This gas limit was hard coded into the protocol to ensure that miners and users could get up and running by installing clients during the initial launch of the protocol. The gas limit would later be increased to 3 million with the Frontier thawing fork (exactly 3,141,592). The canary contracts were contracts that gave a binary signal of either 0 or 1. These canary contracts were an initial launch mechanism used only during the Frontier release of Ethereum. If clients detected that multiple contracts had switched to a signal of 1, they would stop mining and urge the user to update their client. This prevented prolonged outages by ensuring that miners did not prevent a chain upgrade.
前沿版本标志着以太坊协议的正式发布。该版本本质上是一个测试版，允许开发者学习、实验并开始构建以太坊去中心化应用和工具。它于 2015 年 7 月 30 日 UTC 时间凌晨 3:26:13 发布，这也是以太坊创世区块 的时间戳。前沿版本发布时的 Gas 上限为 5000。这一 Gas 上限被硬编码到协议中，以确保矿工和用户能够在协议初始发布时通过安装客户端启动并运行。随后，通过前沿解冻分叉，Gas 上限被提高到 300 万（确切值为 3,141,592）。金丝雀合约是一种提供二进制信号（0 或 1）的合约。这些金丝雀合约是仅在以太坊前沿版本发布期间使用的初始启动机制。如果客户端检测到多个合约的信号变为 1，它们将停止挖矿并敦促用户更新客户端。这通过确保矿工不会阻止链升级，避免了长时间的停机。
Learn more about Frontier in the following resources:
了解更多关于前沿的信息，请参阅以下资源：
- [Frontier is coming, what to expect and how to prepare](https://blog.ethereum.org/2015/07/22/frontier-is-coming-what-to-expect-and-how-to-prepare)
- [The thawing frontier](https://blog.ethereum.org/2015/08/04/the-thawing-frontier)
- [ethereum.org web archive](https://web.archive.org/web/20150802035735/https://www.ethereum.org/)
- [ethereum-protocol-update-1](https://blog.ethereum.org/2015/08/04/ethereum-protocol-update-1)
Frontier is coming, what to expect and how to prepare
前沿即将到来，期待什么以及如何准备 (https://blog.ethereum.org/2015/07/22/frontier-is-coming-what-to-expect-and-how-to-prepare)
解冻前沿 (https://blog.ethereum.org/2015/08/04/the-thawing-frontier)
ethereum.org 
网页存档 (https://web.archive.org/web/20150802035735/https://www.ethereum.org/)
以太坊协议更新1 (https://blog.ethereum.org/2015/08/04/ethereum-protocol-update-1)
## Homestead
家园
Homestead was the second major release of the Ethereum protocol, officially released on March 14, 2016, marking Ethereum’s transition from a beta phase to a more mature and stable platform.
Here are some of the notable features and changes introduced during the Homestead phase:
家园是以太坊协议的第二个主要版本，于 2016 年 3 月 14 日正式发布，标志着以太坊从测试阶段过渡到一个更加成熟和稳定的平台。以下是家园阶段引入的一些显著特性和变更：
- [EIP-2](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2.md): EIP-2 contained numerous fixes, such as increasing the gas cost for contract creation via transactions, ensuring that contract creation either succeeded or failed (preventing empty contracts from being created), and modifications to the difficulty adjustment algorithm. 
EIP-2：EIP-2 包含了多项修复，例如通过交易创建合约的 Gas 成本增加，确保合约创建要么成功要么失败（防止创建空合约），以及对难度调整算法的修改。
  1. **Increased gas cost for contract creation:**
  增加合约创建的 Gas 成本：
  The gas cost for creating contracts via a transaction was increased from 21,000 to 53,000.
  This change was designed to reduce the excessive incentive to create contracts through transactions rather than through the `CREATE` opcode within contracts, which remained unaffected.
通过交易创建合约的 Gas 成本从 21,000 增加到 53,000。这一变更旨在减少通过交易创建合约的过度激励，而通过合约内的 CREATE 操作码创建合约的成本保持不变。
  2. **Invalidation of high s-value signatures:**
  高 s 值签名的无效化：
  Transaction signatures with an s-value greater than `secp256k1n/2` are now considered invalid.
  This measure addressed a transaction malleability issue, preventing the alteration of transaction hashes by flipping the s-value (`s` -> `secp256k1n - s`).
  This change improved the reliability and integrity of transaction tracking.
现在，s 值大于 secp256k1n/2 的交易签名被视为无效。这一措施解决了交易延展性问题，防止通过翻转 s 值（s -> secp256k1n - s）来改变交易哈希。这一变更提高了交易跟踪的可靠性和完整性。
  3. **Contract creation out-of-gas handling:**
  合约创建的 Gas 不足处理：
  If a contract creation did not have enough gas to pay for the final gas fee to add the contract code to the state, the contract creation will fail (i.e., go out-of-gas) rather than leaving an empty contract.
如果合约创建没有足够的 Gas 来支付将合约代码添加到状态的最终 Gas 费用，合约创建将失败（即 Gas 耗尽），而不是留下一个空合约。
  5. **Change the difficulty adjustment algorithm:**
修改难度调整算法：
  The difficulty adjustment algorithm was modified to address issues observed in the Frontier phase.
  The new formula aimed to maintain the targeted block time and prevent excessive deviation by adjusting the difficulty based on the timestamp difference between blocks.
难度调整算法被修改以解决在前沿阶段观察到的问题。新公式旨在通过根据区块之间的时间戳差异调整难度，保持目标区块时间并防止过度偏差。
- [EIP-7](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7.md): EIP-7 introduced the `DELEGATECALL` opcode.
- [EIP-7](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7.md):EIP-7 引入了 DELEGATECALL 操作码。
  A new opcode, `DELEGATECALL`, was added at `0xf4`.
  It functions similarly to `CALLCODE`, but propagates the sender and value from the parent scope to the child scope.
  Propagating the sender and value makes it easier for contracts to store another address as a mutable source of code and "pass through" calls to it.
  Unlike the `CALL` opcode, there is no additional stipend of gas added, which makes gas management more predictable.
  一个新的操作码 DELEGATECALL 被添加到 0xf4。它的功能类似于 CALLCODE，但将发送者和值从父作用域传播到子作用域。传播发送者和值使得合约更容易将另一个地址存储为可变的代码源，并「传递」调用给它。与 CALL 操作码不同，没有额外的 Gas 津贴，这使得 Gas 管理更加可预测。
- [EIP-8](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-8.md): EIP-8 ensures that clients on Ethereum support future network upgrades by introducing devp2p forward compatibility requirements. 
- [EIP-8](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-8.md): EIP-8：EIP-8 通过引入 devp2p 前向兼容性要求，确保以太坊客户端支持未来的网络升级。
  The **devp2p Wire Protocol**, **RLPx Discovery Protocol**, and **RLPx TCP Transport Protocol** specify that implementations should be liberal in accepting packets by ignoring version numbers and additional list elements in hello and ping packets, discarding unknown packet types silently, and accepting new encodings for encrypted key establishment handshake packets.
  This ensures all client software can cope with future protocol upgrades and will accept handshakes, allowing liberal acceptance of data from others (see [Postel's Law](https://en.wikipedia.org/wiki/Robustness_principle)).
devp2p 有线协议、RLPx 发现协议 和 RLPx TCP 传输协议 规定，实现应宽松地接受数据包，忽略版本号和 hello 及 ping 数据包中的额外列表元素，静默丢弃未知的数据包类型，并接受加密密钥建立握手数据包的新编码。这确保了所有客户端软件能够应对未来的协议升级，并接受握手，允许宽松地接受来自他人的数据（参见 Postel 定律）。

Learn more about Homestead in the following resources:
了解更多关于家园的信息，请参阅以下资源：
- [Ethereum Homestead Documentation](https://readthedocs.org/projects/ethereum-homestead/downloads/pdf/latest/)
- [The Robustness Principle Reconsidered](https://queue.acm.org/detail.cfm?id=1999945)
- [Homestead blog release post](https://blog.ethereum.org/2016/02/29/homestead-release)
- [The Homestead release - github](https://github.com/ethereum/homestead-guide/blob/master/source/introduction/the-homestead-release.rst)

## The Merge

On September 15, 2022, Ethereum activated [EIP-3675](https://eips.ethereum.org/EIPS/eip-3675) and upgraded the consensus mechanism to proof-of-stake through an event known as The Merge. The Merge has resulted in the deprecation of the proof-of-work consensus, which was previously implemented in the same logic layer as execution. Instead, it has been replaced by a more complex and sophisticated proof-of-stake consensus that eliminates the need for energy-intensive mining. New proof-of-stake consensus has been implemented in its own layer with a separate p2p network and logic, also know as Beacon Chain. The Beacon Chain has been running and achieving consensus since December 1st, 2020. After a prolonged period of consistent performance without any failures, it was deemed ready to become Ethereum's consensus provider. The Merge gets its name from the union of the two networks.
在 2022 年 9 月 15 日，以太坊激活了 EIP-3675，并通过一项被称为 The Merge 的事件升级了共识机制，转向了权益证明（proof-of-stake）。The Merge 使得之前采用的工作量证明（proof-of-work）共识机制被废弃，工作量证明机制以前是与执行层在同一逻辑层中实现的。取而代之的是一种更复杂、更精密的权益证明共识机制，它不再需要高能耗的挖矿操作。新的权益证明共识机制被实现于其独立的层，拥有单独的点对点网络和逻辑系统，也被称为 Beacon Chain。Beacon Chain 自 2020 年 12 月 1 日起就已经运行并达成共识，在长时间稳定运行且没有任何故障后，它被认为已经准备好成为以太坊的共识提供者。The Merge 这个名称来源于两种网络的合并。
Learn more about The Merge in following resources and reading on Consensus layer. 
通过以下资源和阅读共识层相关内容，了解更多关于 The Merge 的信息。
 - [EIP-3675: Upgrade consensus to Proof-of-Stake](https://eips.ethereum.org/EIPS/eip-3675), [archived](https://web.archive.org/web/20240213102133/https://eips.ethereum.org/EIPS/eip-3675)
- [Gasper](https://ethereum.org/developers/docs/consensus-mechanisms/pos/gasper), [archived](https://web.archive.org/web/20240214225630/https://ethereum.org/developers/docs/consensus-mechanisms/pos/gasper)
- [Mega Merge Resources List](https://notes.ethereum.org/@MarioHavel/merge-resources), [archived](https://web.archive.org/web/20240302082121/https://notes.ethereum.org/@MarioHavel/merge-resources)
