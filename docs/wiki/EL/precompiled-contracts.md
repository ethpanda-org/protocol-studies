# 预编译合约

预编译合约是一组特殊帐户，每个帐户都包含一个具有确定的 gas 成本的内置函数，通常与复杂的密码计算相关。目前，它们定义在从 `0x01` 到 `0x0a` 的地址范围内。

与合约账户不同，预编译是以太坊协议的一部分，由 [execution 客户端](/wiki/EL/el-clients.md) 实现。这提出了一个有趣的警告 - EVM 中的 `EXTCODESIZE` 为 0。尽管如此，它们在执行时的功能就像带有代码的合约帐户。

预编译包含在 [EIP-2929](https://eips.ethereum.org/EIPS/eip-2929) 定义的交易的初始 `"accessed_addresses"` 中，以节省 gas 成本。

## 预编译与操作码

预编译和操作码都有执行任意计算的相同目标。在它们之间进行选择时要考虑以下因素：

- **有限的操作码空间**：1 字节的操作码空间本质上受到限制(256 操作码、`0x00`-`0xFF`)。这就需要对基本业务进行明智的分配。
- **效率**：预编译合约在 EVM 之外本机执行，从而能够高效实现复杂的加密计算，从而为许多跨链交互提供动力。

引用 Vitalik 的[“以太坊协议的史前史”](https://vitalik.eth.limo/general/2017/09/14/prehistory.html)：

> 第二个是“预编译”的想法，解决了允许在 EVM 中使用复杂的密码计算而不必处理 EVM 开销的问题。我们还经历了许多关于“原生合约”的更雄心勃勃的想法，如果矿工对某些合约进行了优化实施，他们可以“投票”降低这些合约的 Gasprice，因此大多数矿工可以更快执行的合约自然会具有较低的 gas 价格；然而，所有这些想法都被拒绝了，因为我们无法想出一种加密经济上安全的方法来实现这样的事情。攻击者总是可以创建一个执行一些陷门加密操作的合约，将陷门分发给自己和他们的朋友，使他们能够更快地执行该合约，然后对 Gasprice 进行投票，并使用它来对网络进行 DoS。相反，我们选择了一种不太雄心勃勃的方法，即对于哈希和 签名方案等常见操作，在协议中简单指定较少数量的预编译。

## 预编译列表

|地址 |名称 |描述 |自从 |
| ------- | -------------------- | ------------------------------------------ | ------------------------------------------------------------- |
| 0x01 | 0x01 ECRECOVER | 椭圆曲线公钥恢复 |前沿|
| 0x02 | 0x02 SHA2-256 | SHA2 256 位哈希方案 |前沿|
| 0x03 | 0x03 RIPEMD-160 | RIPEMD 160 位哈希方案 |前沿|
| 0x04 | 0x04 IDENTITY |恒等函数|前沿|
| 0x05 | 0x05 MODEXP |任意精度模幂|拜占庭 ([EIP-198](https://eips.ethereum.org/EIPS/eip-198)) |
| 0x06 | 0x06 ECADD | 椭圆曲线加法 |拜占庭 ([EIP-196](https://eips.ethereum.org/EIPS/eip-196)) |
| 0x07 | 0x07 ECMUL | 椭圆曲线标量乘法 |拜占庭 ([EIP-196](https://eips.ethereum.org/EIPS/eip-196)) |
| 0x08 | 0x08 ECPAIRING | 椭圆曲线配对检查 |拜占庭 ([EIP-197](https://eips.ethereum.org/EIPS/eip-197)) |
| 0x09 | 0x09 BLAKE2 | BLAKE2 压缩函数|伊斯坦布尔 ([EIP-152](https://eips.ethereum.org/EIPS/eip-152)) |
| 0x0a | 0x0a | KZG POINT EVALUATION |验证 KZG 证明 |坎昆 ([EIP-4844](https://eips.ethereum.org/EIPS/eip-4844)) |

## 它是如何运作的

预编译的美妙之处在于其界面设计，它与外部智能合约调用相同，允许熟悉的交互 - 从开发人员的角度来看，预编译的使用与外部调用没有什么不同。

gas 预编译的成本直接与输入数据相关 - 固定输入转化为固定成本。为了确定这些成本，开发人员依靠参考实现和基准的组合。基准测试通常测量特定硬件上的执行时间，而有些基准测试，如 `MODEXP`，[直接根据每秒 gas 使用情况定义消耗](https://eips.ethereum.org/EIPS/eip-2565#1-modify-computational-complexity-formula-to-better-reflect-the-computational-complexity)。这种细致的方法旨在通过确保可预测的资源分配来防止拒绝服务攻击。

在底层，客户端实现利用优化的库来执行预编译。虽然这种方法提高了效率，但也带来了潜在的安全风险。如果在这些库中发现错误，则可能会破坏整个协议层。为了减轻这种风险，严格的[测试](/wiki/testing/overview.md)至关重要(例如[MODEXP测试规范](https://github.com/ethereum/execution-spec-tests/tree/main/tests/byzantium/eip198_modexp_precompile))。

为了防止安全漏洞，预编译旨在避免嵌套调用。

## 调用预编译

与合约账户一样，可以使用 `*CALL` 系列的操作码来调用预编译。以下汇编代码示例显示了使用 `SHA-256` 预编译到哈希字符串“Hello”的用法：

```js
// First place the parameters in memory
PUSH5 0x48656C6C6F // Hello in UTF-8
PUSH1 0
MSTORE

// Call SHA-256 precompile (0x02)
PUSH1 0x20 // retSize
PUSH1 0x20 // retOffset
PUSH1 5 // argsSize
PUSH1 0x1B // argsOffset
PUSH1 2 // address
PUSH4 0xFFFFFFFF // gas
STATICCALL

POP // Pop the result of the STATICCALL

// LOAD the result from memory to stack
PUSH1 0x20
MLOAD
```

产生哈希：

```
185f8db32271fe25f561a6fc938b2e264306ec304eda518007d1764826381969
```

> ▶️[快来EVM游乐场试试吧！](https://www.evm.codes/playground?fork=cancun&unit=Wei&codeType=Mnemonic&code='~FirsKplaceqparameters%20inYZ5948656C6C6FjHello%20in%20UTF-8w0vMSTOREvv~Call%20SHA-256%20precompilJ%7BV02%7DNSizeNW5QSizewV1BQW2jaddressZ49FFFFFFFFjgasvbPOPjPop_ofqb~LOAD_fromYGo%20stackXvMLOAD'~%2F%2F%20wZ1%20v%5CnqGhJj%20~bSTATICCALLvv_qresulKZvPUSHY%20memoryXwV20WOffsetwV0xQjargsNXjretKt%20Je%20G%20t9%20V%019GJKNQVWXYZ_bjqvw~_)

请参阅 [EVM](/wiki/EL/evm.md) 上的 wiki 以了解汇编代码的工作原理。

## 建议的预编译

[EIP](https://eips.ethereum.org/) 可以引入新的预编译作为硬分叉的一部分。由于测试表面和维护成本很高，添加新的预编译普遍存在阻力。为了解决这个问题，建议的方法是首先在第 2 层解决方案上进行预编译原型设计，然后仅在它们表现出稳定性和广泛采用后将其集成到主网中。

 目前建议进行以下预编译：

- [EIP-2537：BLS12-381曲线操作的预编译](https://eips.ethereum.org/EIPS/eip-2537)
- [RIP-7212：预编译 secp256r1 曲线支持](https://github.com/ethereum/RIPs/blob/master/RIPS/rip-7212.md)
- [EIP-7545：Verkle证明验证预编译](https://eips.ethereum.org/EIPS/eip-7545)
- [EIP-5988：添加Poseidon 哈希函数预编译](https://eips.ethereum.org/EIPS/eip-5988)

引入新的预编译需要仔细考虑其网络效应。使用错误计算的 gas 成本进行预编译可能会消耗比预期更多的资源，从而导致拒绝服务。此外，越来越多的预编译可能会导致 EVM 客户端内的代码膨胀，从而增加验证者的负担。

预编译的密码函数及其相应参数的选择需要进行彻底的分析，以平衡安全性和效率。这些参数通常是在预编译逻辑中预设的，因为根据用户输入对这些参数进行参数化可能会引起安全问题。而且，用广泛的参数来优化安全功能很难快速执行，而这是预编译的基本要求。

## 删除预编译

关于是否可能删除过时、未充分利用或阻碍客户端软件效率的预编译的讨论正在进行中。身份预编译(由 `MCOPY` 操作码取代)、`RIPEMD-160` 和 BLAKE 函数是废弃的主要候选函数。

然而，这些预编译可以迁移到高效的智能合约实现，而不是完全删除。这种方法将确保持续的功能，但 gas 成本也会相应增加。

## 实施

- [Besu - `org.hyperledger.besu.evm.precompile` 包](https://github.com/hyperledger/besu/tree/3d5f45c35ffce4b5173b2ce5972827f9634317d6/evm/src/main/java/org/hyperledger/besu/evm/precompile)
- [Geth-`core/vm/contracts.go`](https://github.com/ethereum/go-ethereum/blob/b2b0e1da8cac279bf0466885d1abdc5d93402f41/core/vm/contracts.go)
- [Nethermind - `Nethermind.EVM.Precompiles` 命名空间](https://github.com/NethermindEth/nethermind/tree/f3edf2503d2637a37f8b509924e10f88491ddd6e/src/Nethermind/Nethermind.Evm/Precompiles)
- [Reth - REVM 预编译包](https://github.com/bluealloy/revm/tree/1ca3d39f6a9e9778f8eb0fcb74fe529345a531b4/crates/precompile/src)

## 研究

提议的方法称为[“渐进式预编译”](https://ethereum-magicians.org/t/eip-proposal-create2-contract-factory-precompile-for-deployment-at-consistent-addresses-across-networks/6083/26)，旨在改进部署过程。这些预编译将驻留在确定性的 CREATE2 地址，允许用户合约与同一地址进行交互，无论预编译是在主网还是特定的 L2 上运行。当本机客户端预编译可用时，此方法可确保更平滑的过渡。

## 资源

- [附录E：以太坊黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf)
- [第 10 周：Danno Ferrin 的预编译概述](/eps/week10-dev.md)
- [EVM预编译目录](https://github.com/shemnon/precompiles/)
- [进行以太坊预编译实现。](https://github.com/ethereum/go-ethereum/blob/master/core/vm/contracts.go)
- [以太坊协议的史前史](https://vitalik.eth.limo/general/2017/09/14/prehistory.html)
- [Stack Exchange：什么是预编译合约以及它们与本机操作码有什么不同？](https://ethereum.stackexchange.com/questions/440/whats-a-precompiled-contract-and-how-are-they-different-from-native-opcodes)
- [Stack Exchange：为什么不将更常见的算法作为预编译完成？](https://ethereum.stackexchange.com/questions/155787/why-arent-more-common-algorithms-done-as-precompiles)
- [一次调用、一次预编译、一次编译走进酒吧](https://blog.theredguild.org/a-call-a-precompile-and-a-compiler-walk-into-a-bar/)
