# EVM 对象格式 (EOF)

EVM 对象格式 (EOF) 是 EVM 字节码的可扩展且版本化的容器格式，在部署时进行一次性验证。

目前，[EVM 字节码](/wiki/EL/evm.md#evm-bytecode) 是一个非结构化指令序列。 EOF引入了容器的概念，为字节码带来了结构，使得EVM更容易解析和分析。其主要目标是通过允许在部署时进行一次性代码验证以及严格分离代码与数据来实现对 EVM 的重大升级。

例如，EOF1 字节码以 2 字节魔术 `0xEF00` 和版本开头，后跟节头列表和 `0x00` 终止符。这种结构允许 EVM 实现在任何执行开始之前在部署时验证整个合约的正确性，包括堆栈深度和结构有效性。
```
// Example layout of an EOF container (bytecode):
Magic (0xEF00) | Version | (section_kind, section_size_or_sizes)+ | 0x00
[types section] [code section 0] [code section 1] … [data section]
```

---

## 目的和好处

EOF 旨在解决原始 EVM 字节码格式的几个限制：

- **更好的验证：** 在执行之前启用智能合约的彻底静态分析和验证。它拒绝截断的 PUSH 数据或未定义的操作码，使每个 EOF 合约在链上可证明有效。

- **提高执行效率：** 通过更好定义的代码段和删除运行时 JUMPDEST 扫描，促进 EVM 执行的优化。

- **增强安全性：** 通过执行更严格的代码结构规则并通过验证消除遗留怪癖(例如 SELFDESTRUCT 或 DELEGATECALL 问题)，减少攻击向量。

- **代码/数据分离：**通过将数据(例如编译器元数据)保存在单独的部分中，验证者和 L2 等工具可以轻松区分可执行代码和任意数据，从而提高安全性和 gas 效率。

---

## 相关EIP及实施

EOF 在多个 EIP 中指定，涵盖不同的功能、对各种操作码的更改以及引入新的。所有这些 EIP 均由单个元 EIP 跟踪：[EIP-7692](https://eips.ethereum.org/EIPS/eip-7692)。

### 核心容器和验证

- [**EIP-3540**](https://eips.ethereum.org/EIPS/eip-3540)：基本 EOF V1 规范。定义容器、标头和代码/数据分离的“有形好处”。

- [**EIP-3670**](https://eips.ethereum.org/EIPS/eip-3670)：在创建时强制进行代码验证，确保不会部署无效的操作码。

- [**EIP-5450**](https://eips.ethereum.org/EIPS/eip-5450)：引入堆栈验证以防止运行时堆栈错误。

### 控制流程和子程序

- [**EIP-4200**](https://eips.ethereum.org/EIPS/eip-4200)：静态相对跳转。使用有符号立即偏移量添加 `RJUMP`、`RJUMPI` 和 `RJUMPV`，从而不再需要动态 `JUMP` 和 `JUMPDEST`。

- [**EIP-4750**](https://eips.ethereum.org/EIPS/eip-4750): EOF 函数。支持多个代码段，并引入 `CALLF`/`RETF` 进行合约内函数调用。它还添加了一个类型部分，列出了每个函数的输入/输出。

- [**EIP-6206**](https://eips.ethereum.org/EIPS/eip-6206)：添加 `JUMPF` (操作码 `0xE5`) 用于尾调用跳转，并允许将函数标记为非返回(输出 `0x80`)以进行优化。

### 现代化指令

- [**EIP-7480**](https://eips.ethereum.org/EIPS/eip-7480)：数据部分访问。定义 `DATALOAD`、`DATALOADN`、`DATASIZE` 和 `DATACOPY` 以安全地访问数据。旧版 `CODECOPY` 在 EOF 中已弃用。

- [**EIP-663**](https://eips.ethereum.org/EIPS/eip-663)：无限堆栈访问。添加了 `SWAPN`、`DUPN` 和 `EXCHANGE`，允许编译器访问 1024 项堆栈上最多 256 个以上的项。

- [**EIP-7069**](https://eips.ethereum.org/EIPS/eip-7069)：修改了 CALL。将旧版 `CALL`/`DELEGATECALL` 替换为 `EXTCALL`、`EXTDELEGATECALL` 和 `EXTSTATICCALL`(删除 gas 参数并使用 63/64 规则)。

- [**EIP-5920**](https://eips.ethereum.org/EIPS/eip-5920)：引入qz​​ph0000082qz 操作码 (`0xFC`)，允许在不调用接收者代码的情况下进行ETH 传输，从而降低重入风险。

- [**EIP-7761**](https://eips.ethereum.org/EIPS/eip-7761) & [**EIP-7880**](https://eips.ethereum.org/EIPS/eip-7880)：添加 `EXTCODETYPE` 以检查帐户类型(EOA 与 EOF)和`EXTCODEADDRESS` 解决 EIP-7702 委托。

### 合约创建和元数据

- [**EIP-7620**](https://eips.ethereum.org/EIPS/eip-7620)：用 `EOFCREATE` 和 `RETURNCODE` 替换 `CREATE`/`CREATE2`。

- [**EIP-7873**](https://eips.ethereum.org/EIPS/eip-7873)：替换以前的EIP-7698。定义 `TXCREATE` 操作码和 InitcodeTransaction 以从 EOA 直接进行 EOF 部署。

- [**EIP-7834**](https://eips.ethereum.org/EIPS/eip-7834)：添加代码无法访问的单独元数据部分(类型为 `0x05`)，非常适合 CBOR 元数据或 IPFS 哈希，无需移动代码偏移量。

---

## 与旧版 EVM 字节码的差异

|特色|旧版 EVM | EOF |
|---|---|---|
|集装箱结构|非结构化字节码 |明确定义的部分(类型、代码、数据)|
|代码验证 |有限，运行时间 |全面的预执行(部署时)|
|跳跃力学|动态：`JUMP` / `JUMPI` |静态相对跳转：`RJUMP` / `RJUMPI` |
|堆栈验证 |仅运行时检查 |静态验证(无下溢/上溢)|
|打字 |无 |函数签名并输入 |
|数据处理|与代码混合 |单独的数据部分(`DATALOAD`)|
|内省| `CODECOPY`, `EXTCODESIZE` |已弃用；被更安全的替代品取代|

---

## 目前状态

EOF 于 2025 年 4 月 28 日在 [互操作测试#34 调用](https://ethereum-magicians.org/t/interop-testing-34-april-28-2025/23822/2) 期间正式从 Fusaka 硬分叉中删除。 Tim Beiko 在 [GitHub PR 更新 EIP-7607](https://github.com/ethereum/EIPs/pull/9703) 中确认了这一决定。

被删除的主要原因是：

- **复杂性：** 批评者，包括开发者 Pascal Caversaccio 在 [2025 年 3 月以太坊 Magicians 帖子](https://ethereum-magicians.org/t/evm-object-format-eof/5727) 中，认为 EOF 引入了太多同时发生的更改、新语义、十多个操作码添加和删除，并且可以通过更小、更有针对性的更新来实现其好处相反。
- **维护负担：** 运输 EOF 需要无限期地维护旧版 EVM 和 EOF 合同，这增加了客户端团队的长期复杂性。
- **流程失败：** Beiko 在删除 PR 中承认，ACD 流程未能在多个升级周期中充分解决社区反对意见，尽管核心开发人员一再同意发布它。
- **时间线风险：** 由于 PeerDAS 是 Fusaka 的首要任务，围绕 EOF 变体的持续规范争论被视为对整体升级计划的风险。

EOF 的冠军们有机会提出纳入后续 **Glamsterdam** 硬分叉的案例，但目前尚未计划纳入。该规范及其组成的 EIP 仍可作为历史参考。

---

## 资源

- [**EVM |帕维尔·比利卡 |讲座 17**](https://www.youtube.com/watch?v=gYnx_YQS8cM) — 详细技术深入探讨 EOF 内部结构和设计决策。

- [**以太坊魔术师：EVM 对象格式 (EOF)**](https://ethereum-magicians.org/t/evm-object-format-eof/5727) — 原始的 EIP 讨论线程，其中 EOF 是由核心开发人员提出并辩论的。

- [**以太坊注释：EOF**](https://notes.ethereum.org/t-1tLFnLSKCtLZpb-Rw0IA) — 有关 EOF 规范和开放设计问题的社区注释和工作文档。
