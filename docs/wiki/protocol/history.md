# 协议历史和演变

> ：警告：本文是一个[存根](https://en.wikipedia.org/wiki/Wikipedia:Stub)，通过[贡献](/contributing.md) 和扩展它来帮助维基。

本页重点介绍以太坊协议历史上的重要技术变化。 

有用的链接：[以太坊.org 的概述](https://ethereum.org/en/history) 和 [以太坊.org 的元 EIP](https://eips.ethereum.org/meta)

## 前沿

Frontier 的发布标志着以太坊协议的启动。该版本本质上是一个测试版，允许开发人员学习、实验并开始构建以太坊去中心化应用程序和工具。它于2015年7月30日3:26:13 AM UTC上线，这是[以太坊创世区块](https://etherscan.io/block/0)的时间戳。 Frontier 启动时 gas 限制为 5000。此 gas 限制被硬编码到协议中，以确保矿工和用户可以在协议首次启动期间通过安装客户端来启动和运行。 gas 限制后来通过 Frontier 解冻分叉增加到 300 万(确切地说是 3,141,592)。金丝雀合约是给出 0 或 1 二进制信号的合约。这些金丝雀合约是仅在以太坊 Frontier 版本期间使用的初始启动机制。如果客户端检测到多个合约已经切换到信号1，他们就会停止挖矿并敦促用户更新他们的客户端。这通过确保矿工不会阻止链升级来防止长时间的中断。

通过以下资源了解有关 Frontier 的更多信息：

- [前沿来了，期待什么以及如何准备](https://blog.ethereum.org/2015/07/22/frontier-is-coming-what-to-expect-and-how-to-prepare)
- [解冻前沿](https://blog.ethereum.org/2015/08/04/the-thawing-frontier)
- [ethereum.org 网络存档](https://web.archive.org/web/20150802035735/https://www.ethereum.org/)
- [以太坊协议更新-1](https://blog.ethereum.org/2015/08/04/ethereum-protocol-update-1)

## 家园

Homestead是以太坊协议的第二个主要版本，于2016年3月14日正式发布，标志着以太坊从测试阶段过渡到更加成熟稳定的平台。
以下是家园阶段引入的一些值得注意的功能和变化：

- [EIP-2](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2.md)：EIP-2 包含许多修复，例如增加通过交易创建合约的 gas 成本、确保合约创建成功或失败(防止创建空合约)以及修改难度调整算法。 

  1. **创建合约的 gas 成本增加：**
  通过交易创建合约的 gas 成本从 21,000 增加到 53,000。
  此更改旨在减少通过交易而不是通过合约内的 `CREATE` 操作码创建合约的过度激励，后者不受影响。
  2. **高s值签名失效：**
  s 值大于 `secp256k1n/2` 的交易签名现在被视为无效。
  此措施解决了交易可延展性问题，防止通过翻转 s 值(`s` -> `secp256k1n - s`)来更改交易哈希。
  此更改提高了交易跟踪的可靠性和完整性。
  3. **gas 处理之外的合同创建：**
  如果合约创建没有足够的 gas 来支付最终的 gas 费用以将合约代码添加到状态，则合约创建将失败(即超出 gas)，而不是留下一个空合约。
  4. **更改难度调整算法：**
  修改了难度调整算法以解决前沿阶段观察到的问题。
  新公式旨在通过根据区块之间的时间戳差异调整难度来维持目标区块时间并防止过度偏差。

- [EIP-7](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7.md)：EIP-7介绍了`DELEGATECALL` 操作码。

  在 `0xf4` 处添加了新的操作码、`DELEGATECALL`。
  它的功能与 `CALLCODE` 类似，但将发送者和值从父作用域传播到子作用域。
  传播发送者和值使合约更容易将另一个地址存储为可变代码源并“传递”对其的调用。
  与`CALL` 操作码不同的是，gas没有额外添加津贴，这使得gas的管理更加可预测。

- [EIP-8](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-8.md)：EIP-8 通过引入 devp2p 前向兼容性要求，确保以太坊上的客户端支持未来的网络升级。 

  **devp2p 有线协议**、**RLPx 发现协议**和 **RLPx TCP 传输协议** 指定实现应该自由地接受数据包，方法是忽略 hello 和 ping 数据包中的版本号和附加列表元素，静默丢弃未知数据包类型，并接受加密密钥建立握手数据包的新编码。
  这确保了所有客户端软件都可以应对未来的协议升级并接受握手，从而允许自由接受来自其他人的数据(请参阅 [Postel 定律](https://en.wikipedia.org/wiki/Robustness_principle))。

通过以下资源了解有关 Homestead 的更多信息：

- [以太坊家园文档](https://readthedocs.org/projects/ethereum-homestead/downloads/pdf/latest/)
- [鲁棒性原则再思考](https://queue.acm.org/detail.cfm?id=1999945)
- [Homestead博客发布帖子](https://blog.ethereum.org/2016/02/29/homestead-release)
- [Homestead 版本 - github](https://github.com/ethereum/homestead-guide/blob/master/source/introduction/the-homestead-release.rst)

## 合并

2022 年 9 月 15 日，以太坊激活了 [EIP-3675](https://eips.ethereum.org/EIPS/eip-3675)，并通过名为 The Merge 的事件将共识机制升级为权益证明。合并导致了工作量证明共识的废弃，该共识之前是在与执行相同的逻辑层中实现的。相反，它已被更复杂和精密的权益证明共识所取代，从而消除了对能源密集型挖矿的需求。新的权益证明共识已在其自己的层中实现，具有单独的 p2p 网络和逻辑，也称为信标链。 信标链自2020年12月1日起一直运行并达成共识。经过长时间的稳定表现，未出现任何故障，它被认为已准备好成为以太坊的共识提供者。合并得名于两个网络的联合。

通过以下资源和阅读共识层了解有关合并的更多信息。 

 - [EIP-3675：将共识升级为权益证明](https://eips.ethereum.org/EIPS/eip-3675)，[已存档](https://web.archive.org/web/20240213102133/https://eips.ethereum.org/EIPS/eip-3675)
- [加斯帕](https://ethereum.org/developers/docs/consensus-mechanisms/pos/gasper)，[已存档](https://web.archive.org/web/20240214225630/https://ethereum.org/developers/docs/consensus-mechanisms/pos/gasper)
- [大型合并资源列表](https://notes.ethereum.org/@MarioHavel/merge-resources)，[已存档](https://web.archive.org/web/20240302082121/https://notes.ethereum.org/@MarioHavel/merge-resources)
