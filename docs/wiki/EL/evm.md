# 以太坊虚拟机 (EVM)

以太坊虚拟机 (EVM) 是以太坊世界计算机的核心。它执行最终确定交易所需的关键计算，并将结果永久存储在区块链上。本文探讨了 EVM 在以太坊生态系统中的作用及其运作方式。

## 以太坊状态机

当 EVM 处理交易时，它改变了以太坊的整体状态。在这方面，以太坊可以被视为**状态机**。

在计算机科学中，**状态机**是用于对系统行为建模的抽象。它说明了系统如何由一组不同的状态表示以及输入如何驱动状态的变化。

一个熟悉的例子是自动售货机，这是一种在收到付款后分配产品的自动化系统。

我们可以对处于三种不同状态的自动售货机进行建模：空闲、等待用户选择和分发产品。硬币插入或产品选择等输入会触发这些状态之间的转换，如状态图所示：

![Vending machine](../../images/evm/vending-machine.gif)

让我们正式定义状态机的组件：

1. **状态 ($S$)**：状态表示系统在给定时间点可能处于的不同条件或配置。
   对于自动售货机来说，可能的状态有：

$$ S\in \set {Idle, Selection, Dispensing} $$

2. **输入 ($I$)**：输入是系统环境中的操作、信号或变化。输入触发 **状态转换函数**。
   对于自动售货机，可能的输入包括：

$$ I\in \set {InsertCoin, SelectDrink, CollectDrink} $$

3. **状态转换函数 ($\Upsilon$)**：状态转换函数定义系统如何根据输入及其当前状态从一种状态转换到另一种状态(或返回到相同状态)。从本质上讲，它决定了系统如何响应输入而演化。

$$\Upsilon (S,I) \Longrightarrow S'  $$

> 其中 S' = 下一个状态，S = 当前状态，I = 输入。

自动售货机的状态转换示例：

$$\Upsilon (Idle,InsertCoin) \Longrightarrow Selection $$
$$\Upsilon (Selection,SelectDrink) \Longrightarrow Dispensing $$
$$\Upsilon (Idle,SelectDrink) \Longrightarrow Idle $$

请注意，在最后一种情况下，当前状态会转换回自身。

### 以太坊作为状态机

以太坊作为一个整体，可以被视为一个 **基于交易的状态机**。它接收交易作为输入并转换到新状态。 以太坊的当前状态称为 **世界状态**。

考虑一个简单的以太坊应用程序 - 一个 [NFT](https://ethereum.org/en/nft/) 市场。

在当前的世界状态 **S3**(绿色)中，Alice 拥有一个 NFT。下面的动画显示了交易将所有权转移给您(**S3** ➡️ **S4**)。同样，将 NFT 卖回给 Alice 会将状态转换为 **S5**：

![Ethereum state machine](../../images/evm/ethereum-state-machine.gif)

请注意，当前的世界状态是动画的_作为脉动的绿色气泡_。

上图中，每个交易都被提交到一个新状态。然而，实际上，一组交易被捆绑成一个 **区块**，并且结果状态被添加到先前状态的链中。现在应该很清楚为什么这项技术被称为**区块链**。

考虑到状态转换函数的定义，我们得出以下结论：

> ℹ️注意
> **EVM 是以太坊状态机的状态转换函数。它确定以太坊如何根据输入 (交易) 和当前状态转换到新的(世界)状态。**

在以太坊中，世界状态本质上是20字节地址到账户状态的映射。

![Ethereum world state](../../images/evm/ethereum-world-state.jpg)

每个账户状态由各种组件组成，例如存储、代码、其他数据中的余额，并与特定地址相关联。

以太坊有两种账户：

- **外部帐户：** 帐户[由关联私钥控制](/wiki/Cryptography/ecdsa.md) 和空 EVM 代码。
- **合约账户：** 由关联的非空 EVM 代码控制的账户。 EVM 代码作为此类帐户的一部分，通俗地称为_智能合约。_

有关如何实现世界状态的详细信息，请参阅[以太坊数据结构](wiki/EL/data-structures.md)。

## 虚拟机范例

鉴于我们对状态机的掌握，下一个挑战是**实现**。

软件需要转换为目标处理器的机器语言(指令集架构，ISA)才能执行。这个 ISA 因硬件而异(例如，英特尔与苹果芯片)。现代软件还依赖于主机操作系统来进行内存管理和其他必需功能。

确保不同硬件和操作系统的分散生态系统中的功能是一个主要障碍。传统上，必须将软件编译为每个特定目标平台的本机二进制文件：

![Platform dependent execution](../../images/evm/platform-dependent-execution.jpg)

为了应对这一挑战，采用了由两部分组成的解决方案。

首先，实现的目标是**虚拟机**，一个抽象层。源代码被编译成 **字节码**，表示指令的字节序列。每个字节码映射到虚拟机执行的特定操作。

第二部分涉及特定于平台的虚拟机，它将字节码转换为本机代码以供执行。

这提供了两个主要好处：可移植性(字节码在不同平台上运行而无需重新编译)和抽象性(将硬件复杂性与软件分开)。因此，开发人员可以为单个虚拟机编写代码：

![Virtual machine paradigm](../../images/evm/virtual-machine-paradigm.jpg)

Java 的 [JVM](https://en.wikipedia.org/wiki/Java_virtual_machine) 和 Lua 的 LuaVM 是虚拟机的流行示例。他们创建了平台中立的字节码，使代码无需重新编译即可在各种系统上运行。

## EVM

虚拟机概念是一个抽象概念。 以太坊虚拟机 (EVM) 是此抽象的_特定_软件实现。 EVM 的解剖结构描述如下：

在计算机体系结构中，字指的是 CPU 可以一次处理的固定大小的数据单元。 EVM 的字大小为 **32 字节**。

![EVM anatomy](../../images/evm/evm-anatomy.jpg)

_为了清楚起见，上图简化了以太坊状态。实际状态包括附加元素，例如消息帧和瞬时存储。_

在上述剖析中，EVM 显示正在操纵帐户实例的存储、代码和余额。

在现实场景中，EVM 可能会执行涉及多个帐户(每个帐户具有独立的存储、代码和余额)的交易，从而在以太坊上实现复杂的交互。

为了更好地理解虚拟机，让我们扩展我们的定义：

> ℹ️注意
> EVM 是以太坊状态机的状态转换函数。它确定以太坊如何根据以下条件转换到新的(世界)状态
> 输入(交易)和当前状态。 **它是作为虚拟机实现的，因此可以在任何平台上运行，独立于
> 底层硬件。**

## EVM 字节码

EVM 字节码是将程序表示为 [**字节**(8 位)序列。](https://en.wikipedia.org/wiki/Byte) 字节码中的每个字节可以是：

- 称为 **操作码** 的指令，或
- 输入到称为 **操作数** 的操作码。

![Binary bytecode](../../images/evm/opcode-binary.jpg)

为简洁起见，EVM 字节码通常以 [**十六进制**](https://en.wikipedia.org/wiki/Hexadecimal) 表示法表示：

![Hexadecimal bytecode](../../images/evm/opcode-hex.jpg)

为了进一步增强理解，操作码具有人类可读的助记符。这个简化的字节码称为 **EVM 程序集**，是 EVM 代码的最低人类可读形式：

![EVM Assembly](../../images/evm/opcode-assembly.jpg)

从操作数中识别操作码很简单。目前，只有 `PUSH*` 操作码有操作数(这可能会随 [EOF](https://eips.ethereum.org/EIPS/eip-7569) 改变)。 `PUSHX` 定义操作数长度(PUSH 之后的 X 个字节)。

选择本次讨论中使用的操作码：

| 操作码 |名称 |描述 |
| ------ | -------------- | -------------------------------------------------- |
| 60| `PUSH1` |将 1 个字节压入堆栈 |
| 01 | `ADD` |添加堆栈顶部的 2 个值 |
| 02 | `MUL` |将堆栈顶部的 2 个值相乘 |
| 39 | 39 `CODECOPY` |将当前环境中运行的代码复制到内存 |
| 51 | 51 `MLOAD` |从内存中加载单词 |
| 52 | 52 `MSTORE` |将单词存储到内存中 |
| 53 | 53 `MSTORE8` |将字节存储到内存|
| 59 | 59 `MSIZE` |获取扩展内存的字节大小 |
| 54 | 54 `SLOAD` |从存储加载单词 |
| 55 | 55 `SSTORE` |将单词存储到存储 |
| 56 | 56 `JUMP` |改变程序计数器 |
| 5B| `JUMPDEST` |标记跳跃目的地 |
| f3 | `RETURN` |停止执行返回输出数据 |
| 35 | 35 `CALLDATALOAD` |将 32 个字节从 calldata 复制到堆栈 |
| 37 | 37 `CALLDATACOPY` |将输入数据从 calldata 复制到内存 |
| 80–8F | `DUP1–DUP16` |将第 N 个堆栈项目复制到顶部 |
| 90–9F | `SWAP1–SWAP16` |将顶部与第 N+1 个堆栈项交换 |

完整列表请参阅[黄皮书附录H](https://ethereum.github.io/yellowpaper/paper.pdf)。

> ℹ️注意
> [EIPs](https://eips.ethereum.org/)可以提出EVM修改。例如，[EIP-1153](https://eips.ethereum.org/EIPS/eip-1153)引入了`TSTORE`和`TSTORE` 操作码。

以太坊客户端例如 [geth](https://github.com/ethereum/go-ethereum) 实现 [EVM 规范](https://github.com/ethereum/execution-specs)。这确保所有节点都同意交易如何改变系统状态，从而在网络上创建统一的执行环境。

我们已经介绍了 EVM **什么**，让我们探索一下**它是如何工作的。

# EVM 数据位置

EVM 在执行期间有四个主要存储数据的地方：

- **堆栈**
- **内存**
- **存储**
- **通话数据**

让我们更深入地探讨每个数据存储。

## 堆栈

栈是一个简单的数据结构，有两个操作：**PUSH**和**POP**。 Push 将一个项目添加到堆栈顶部，而 pop 则删除最顶部的项目。堆栈按照后进先出 (LIFO) 原则运行 - 最后添加的元素最先删除。如果您尝试从空堆栈中弹出，则会发生**堆栈下溢错误**。

由于堆栈是大多数操作码操作的地方，因此它负责保存用于读取和写入 **内存** 和 **存储** 的值，我们稍后将详细介绍。

EVM 堆栈的主要用途是存储计算中的中间值并向操作码提供参数。

![EVM stack](../../images/evm/stack.gif)

> EVM 堆栈的最大大小为 1024 个项目，每个项目由 32 字节组成，并在每次合约执行后重置。仅可访问前 16 项。如果堆栈用完，合约执行将会失败。

这 16 个项目堆栈大小背后的原因是由于 **DUP** 和 **SWAP** 操作码。

- **`DUPn`**：将第 n 个堆栈项复制到顶部。
- **`SWAPn`**：将顶部与第 n+1 个堆栈项交换。

**DUP** 和 **SWAP** 的最大 n 为 `16`，分别对应于操作码 0x80–0x8f 和 0x90–0x9f。这些操作码在 EVM 中明确定义并形成一个固定集 - 使 16 项限制成为 EVM 级别约束。

在字节码执行期间，EVM 堆栈充当 _scratchpad_：操作码从顶部消耗数据并将结果推回(请参阅下面的 `ADD` 操作码)。考虑一个简单的加法程序：

![EVM stack addition](../../images/evm/stack-addition.gif)

提醒：所有值均为十六进制，即 `0x06 + 0x07 = 0x0d (decimal: 13)`。

让我们花点时间庆祝我们的第一个 EVM 汇编代码🎉。

### 程序计数器

回想一下，字节码是一个平面字节数组，每个操作码是 1 个字节。 EVM 需要一种方法来跟踪要在字节码数组中执行的下一个字节 (操作码)。这就是 EVM **程序计数器** 发挥作用的地方。它将跟踪下一个操作码的偏移量，这是要在堆栈上执行的下一条指令的字节数组中的位置。

在上面的示例中，汇编代码左侧的值表示字节码中每个操作码的字节偏移量(从 0 开始)：

| 字节码 |组装|指令长度(以字节为单位)|十六进制偏移量 |
| -------- | -------- | ------------------------------ | ------------- |
| 60 06 | 60 06 PUSH1 06 | 2 | 00 | 00
| 60 07 | PUSH1 07 | 2 | 02 |
| 01 | ADD | 1 | 04 |

请注意上表不包含偏移量 01。这是因为操作数 06 占用偏移量 01 的位置，同样的概念也适用于操作数 07 占用偏移量 03 的位置。

本质上，**程序计数器**确保 EVM 知道下一条要执行的指令的位置以及何时停止执行，如下例所示。

![EVM program counter](../../images/evm/program-counter.gif)

`JUMP` 操作码直接设置程序计数器，从而实现动态控制流，并通过允许灵活的程序执行路径来促进 EVM 的[图灵完整性](https://en.wikipedia.org/wiki/Turing_completeness)。

![EVM JUMP opcode](../../images/evm/jump-opcode.gif)

代码无限循环运行，反复加7。它引入了两个新的操作码：

- **JUMP**：将程序计数器设置为堆栈顶部值(在我们的例子中为 02)，确定下一条要执行的指令。
- **JUMPDEST**：标记跳转操作的目的地，确保到达预期目的地并防止不必要的控制流中断。

> [Solidity](https://soliditylang.org/) 等高级语言利用 if 条件、for 循环和内部函数调用等结构的跳转。

### gas

我们无辜的小程序可能看起来无害。然而，EVM 中的无限循环构成了重大威胁：它们可以**吞噬资源**，可能导致网络 [**DoS 攻击**。](https://en.wikipedia.org/wiki/Denial-of-service_attack)

EVM 的 **gas** 机制通过充当计算资源的货币来应对此类威胁。 gas 成本[旨在反映](https://web.archive.org/web/20170904200443/https://docs.google.com/spreadsheets/d/15wghZr-Z6sRSMdmRmhls9dVXTOpxKy8Y64oy9MvDZEQ/edit#gid=0) 硬件的限制，例如存储容量或处理能力。 交易在**以太币 (ETH)** 中支付 gas 来使用 EVM，如果它们在完成之前用完 gas(就像无限循环)，EVM 会停止它们以防止资源占用。

这可以保护网络免受资源密集型或恶意活动的堵塞。由于 gas 将计算限制为有限数量的步骤，因此 EVM 被认为是**准图灵完备**。

![EVM Gas](../../images/evm/evm-gas.gif)

为了简单起见，我们的示例假定每个操作码 1 个单位 gas，但实际成本根据复杂性而变化。然而，核心概念保持不变。

具体gas费用参见[黄皮书附录G](https://ethereum.github.io/yellowpaper/paper.pdf)。

## 内存

EVM 内存是 $2^{256}$ (或[实际上无限](https://www.talkcrypto.org/blog/2019/04/08/all-you-need-to-know-about-2256/))字节的字节数组。内存中的所有位置最初都明确定义为零。

![EVM Memory](../../images/evm/evm-memory.gif)

与向单个指令提供数据的堆栈不同，内存存储与整个程序相关的临时数据。由于堆栈有一个字时隙的硬限制，**内存**通过允许对任意大小的数据进行索引访问来补充堆栈。堆栈值可以根据需要存储到**内存**或从**内存**加载。

### 写入内存

`MSTORE` 从堆栈中获取两个值：地址**偏移**和 32 字节**值**。然后它将值写入内存中指定的偏移量处。

![MSTORE](../../images/evm/mstore.gif)

`MSIZE` 报告堆栈上当前使用的内存大小(以字节为单位)(在本例中为 32 字节或 20 十六进制)。

`MSTORE8` 采用与 `MSTORE` 相同的参数，但写入 1 个字节而不是 1 个字。

![MSTORE8](../../images/evm/mstore8.gif)

注意：将07写入内存时，现有值(06)保持不变。它被写入相邻字节偏移量。

活动内存的大小仍然是 1 个字。

### 内存扩展

在 EVM 中，内存以 1 个字“页”的倍数动态分配。 gas 按展开的页数收费。

![Memory expansion](../../images/evm/memory-expansion.gif)

在 1 字节偏移处写入一个字会溢出初始内存页，从而触发扩展到 2 个字(64 字节或 0x40)。

### 从记忆中读取

`MLOAD` 从内存中读取一个值并将其推入堆栈。

![MLOAD](../../images/evm/mload.gif)

EVM 没有与 `MSTORE8` 直接等效的用于读取的内容。您必须使用 `MLOAD` 读取整个字，然后使用 [掩码](<https://en.wikipedia.org/wiki/Mask_(computing)>) 提取所需的字节。

> EVM 内存显示为 32 字节的区块以说明内存扩展的工作原理。实际上，它是一个无缝的字节序列，没有任何固有的划分或区块。

## 呼叫数据

**calldata** 是通过消息调用指令或从交易传递到 EVM 的只读输入数据，并存储为可通过特定操作码访问的字节序列。

### 从通话数据中读取

可以使用以下任一方式访问当前环境的调用数据：

- `CALLDATALOAD` 操作码从所需的偏移量读取 32 个字节到堆栈上，[了解更多](https://veridelisi.medium.com/learn-evm-opcodes-v-a59dc7cbf9c9)。
- 或者，使用 `CALLDATACOPY` 将部分 calldata 复制到内存。

## 存储

存储被设计为**字寻址字数组**。与内存不同，存储与以太坊帐户关联，并且作为世界状态的一部分在交易上**持久**。它可以被认为是与智能合约关联的键值**数据库**，这就是它包含合约的“状态”变量的原因。存储大小固定为2^256 时隙，每个32字节。

![EVM Storage](../../images/evm/evm-storage.jpg)

存储只能通过其关联帐户的代码访问。外部帐户没有代码，因此无法访问自己的存储空间。

## 写入存储

`SSTORE` 从堆栈中获取两个值：存储 **时隙** 和 32 字节**值**。然后它将值写入帐户的存储中。注意：时隙可以被认为是存储的基本单位，因此在写入存储时，我们处理的是时隙而不是单个字节。写入存储的成本很高。当 Solidity 等高级语言的总大小小于或等于 32 字节时，它们会通过将多个变量打包到单个 32 字节时隙中来优化存储。

![EVM Storage write](../../images/evm/sstore.gif)

我们一直在运行合约账户字节码。现在我们才看到该帐户和世界状态，它与 EVM 内的代码匹配。

再次需要注意的是，存储不是 EVM 本身的一部分，而是当前正在执行的合约帐户的一部分。  更具体地说，合约帐户包含一个**存储根**，它指向该合约存储的单独 Merkle Patricia Trie。

上面的示例仅显示了帐户存储的一小部分。与内存一样，存储中的所有值都明确定义为零。此外，当合约执行 `SSTORE` 操作码将 存储槽的值从非零设置回零时，该操作就有资格获得 gas 退款(gas 不会立即返回，而是记入交易的退款中)计数器)​。  这笔退款是对账户从 Trie 状态释放宝贵存储空间的奖励，并在交易结束时应用，以抵消 gas 的部分成本。 


### 从存储中读取

`SLOAD` 从堆栈中获取存储 **时隙** 并将其值加载回其中。

![EVM Storage read](../../images/evm/sload.gif)

请注意，存储值在示例之间保持不变，这表明它在世界状态中保持不变。由于世界状态是在所有节点之间复制的，因此存储操作 gas 的成本很高。

> ℹ️注意
> 查看 [交易](/wiki/EL/transaction.md) 上的 wiki，了解 EVM 的实际效果。

## 总结

我们探讨了操作码是如何由 EVM 执行的核心指令，在堆栈上操作以执行计算。计算结果可以临时存储在内存中或持久存储在合约存储中。

开发人员很少直接编写 EVM 汇编代码，除非性能优化至关重要。相反，大多数开发人员使用 [Solidity](https://soliditylang.org/) 等高级语言，然后将其编译为字节码。

以太坊是一个不断发展的协议，虽然我们讨论的基本原理仍然在很大程度上具有相关性，但遵循 [以太坊改进提案 (EIP)](https://eips.ethereum.org/) 和 [网络升级](https://ethereum.org/history)，我们鼓励您随时了解以太坊生态系统的最新发展。

## EVM 升级

虽然以太坊协议在每次升级中都会经历许多变化，但 EVM 的变化却相当微妙。 EVM 的重大更改可能会破坏合同和语言，需要保留 EVM 的多个版本，这会带来大量复杂性开销。 EVM 本身仍然进行了某些升级，例如新的操作码或对现有升级的更改，但不会破坏其逻辑。一些示例是 EIP，例如 [1153](https://eips.ethereum.org/EIPS/eip-1153)、[4788](https://eips.ethereum.org/EIPS/eip-4788)、[5000](https://eips.ethereum.org/EIPS/eip-5000)、[5656](https://eips.ethereum.org/EIPS/eip-5656) 和 [6780](https://eips.ethereum.org/EIPS/eip-6780)。这些提议添加新的操作码，除了最后一个，它特别有趣，因为它在不破坏兼容性的情况下中和了 `SELFDESTRUCT` 操作码。 EVM 的另一个重要升级是 [EOF](https://notes.ethereum.org/@ipsilon/mega-eof-specification)，这将标志着一个重大变化。它创建了一种字节码的格式，EVM可以更容易地理解和处理，它包含各种EIP，并且已经讨论和完善了相当长的一段时间。

## 资源

### 状态机和计算理论

- 📝 Mark Shead，[“理解状态机。”](https://medium.com/free-code-camp/state-machines-basics-of-computer-science-d42855debc66) • [已存档](https://web.archive.org/web/20210309014946/https://medium.com/free-code-camp/state-machines-basics-of-computer-science-d42855debc66)
- 🎥 Harry Porter 教授，[“计算理论”](https://www.youtube.com/playlist?list=PLbtzT1TYeoMjNOGEiaRmm_vMIwUAidnQz)
- 📘 Michael Sipser，[“计算理论导论”](https://books.google.com/books/about/Introduction_to_the_Theory_of_Computatio.html?id=4J1ZMAEACAAJ)
- 🎥 Shimon Schocken 等人，[“从第一原理构建现代计算机：从 Nand 到俄罗斯方块。”](https://www.coursera.org/learn/build-a-computer)

### EVM

以下资源根据不同的 EVM 学习阶段分为不同的部分。

#### EVM 的基础知识

- 🎥 Whiteboard Crypto，[“EVM：动画非技术介绍。”](https://youtu.be/sTOcqS4msoU)
- 📝 Vasa，[深入了解 EVM：以太坊后台如何工作](https://medium.com/swlh/getting-deep-into-evm-how-ethereum-works-backstage-ab6ad9c0d0bf)
- 📝 Zaryab Afser，[以太坊虚拟机的基本知识](https://www.decipherclub.com/the-abcs-of-ethereum-virtual-machine/)
- 📝 Preethi，[EVM 推文主题](https://twitter.com/iam_preethi/status/1483459717670309895)
- 📝 Decipher Club，[EVM 根据您的专业水平提供的学习资源](https://www.decipherclub.com/evm-learning-resources/)

#### 了解 EVM 架构和核心组件

- 📝 Gavin Wood，[“以太坊黄皮书。”](https://ethereum.github.io/yellowpaper/paper.pdf)
- 📝 以太坊书，[第 13 章，以太坊书](https://cypherpunks-core.github.io/ethereumbook/13evm.html?ref=decipherclub.com)
- 📘 Andreas M. Antonopoulos 和 Gavin Wood，[“掌握以太坊。”](https://github.com/ethereumbook/ethereumbook)
- 🎥 乔丹·麦金尼，[“以太坊解释：EVM。”](https://www.youtube.com/watch?v=kCswGz9naZg)
- 📝 LeftAsExercise，[“智能合约和 以太坊虚拟机。”](https://leftasexercise.com/2021/08/08/q-smart-contracts-and-the-ethereum-virtual-machine/) • [已存档](https://web.archive.org/web/20230324200211/https://leftasexercise.com/2021/08/08/q-smart-contracts-and-the-ethereum-virtual-machine/)
- 📝 Femboy Capital，[“与 EVM 的约会。”](https://femboy.capital/evm-pt1) • [已存档](https://web.archive.org/web/20221001113802/https://femboy.capital/evm-pt1)
- 🎥 Alex，[EVM - 需要一些组装](https://www.youtube.com/watch?v=yxgU80jdwL0)

#### 深入了解 EVM

- 📝 谷武信，[EVM 图解](https://github.com/takenobu-hs/ethereum-evm-illustrated)
- 📝 Shafu，[“EVM 从头开始。”](https://evm-from-scratch.xyz/)
- 📝 NOXX，[“第 3 部分系列：EVM 深入研究 - 通往阴暗超级编码器的道路。”](https://noxx.substack.com/p/evm-deep-dives-the-path-to-shadowy) • [已存档](https://web.archive.org/web/20240106034644/https://noxx.substack.com/p/evm-deep-dives-the-path-to-shadowy)
- 📝 OpenZeppelin，[“6 个系列：解构 Solidity”。](https://blog.openzeppelin.com/deconstructing-a-solidity-contract-part-i-introduction-832efd2d7737) • [已存档](https://web.archive.org/web/20240121025651/https://blog.openzeppelin.com/deconstructing-a-solidity-contract-part-i-introduction-832efd2d7737)
- 📝 TrustLook，[“了解 EVM 字节码。”](https://blog.trustlook.com/understand-evm-bytecode-part-1/) • [已存档](https://web.archive.org/web/20230603080857/https://blog.trustlook.com/understand-evm-bytecode-part-1/)
- 📝 Degatchi，[“Solidity 存储管理低级指南”](https://degatchi.com/articles/low_level_guide_to_soliditys_storage_management) • [已存档](https://web.archive.org/web/20231202105650/https://degatchi.com/articles/low_level_guide_to_soliditys_storage_management/)
- 📝 Zaryab Afser，[“智能合约从 Solidity 到字节码的旅程”](https://www.decipherclub.com/ethereum-virtual-machine-article-series/)
- 🎥 以太坊工程组，[EVM：从 Solidity 到字节码、内存和存储](https://www.youtube.com/watch?v=RxL_1AfV7N4&t=2s)
- 📝 Trust Chain，[关于 Solidity 如何在幕后使用 EVM 的 7 部分系列。](https://trustchain.medium.com/reversing-and-debugging-evm-smart-contracts-392fdadef32d)
- [了解 EVM 操作码](https://veridelisi.medium.com/learn-evm-opcodes-v-a59dc7cbf9c9) • [已存档](https://web.archive.org/web/20240806231824/https://veridelisi.medium.com/learn-evm-opcodes-v-a59dc7cbf9c9)
- [有关 EVM 存储的更多信息](https://medium.com/coinmonks/solidity-storage-how-does-it-work-8354afde3eb) • [已存档](https://web.archive.org/web/20230808231549/https://medium.com/coinmonks/solidity-storage-how-does-it-work-8354afde3eb)
- [存储、内存和堆栈概述](https://ethereum.stackexchange.com/questions/23720/usage-of-memory-storage-and-stack-areas-in-evm) • [已存档](https://web.archive.org/web/20240529150647/https://ethereum.stackexchange.com/questions/23720/usage-of-memory-storage-and-stack-areas-in-evm)
- [通话数据](https://learnevm.com/chapters/fn/calldata) • [已存档](https://web.archive.org/web/20250306133755/https://learnevm.com/chapters/fn/calldata)

### 工具和 EVM 谜题

- 🧮 smlXL，[“evm.codes：操作码参考和交互式游乐场。”](https://www.evm.codes/)
- 🧮 smlXL，[“evm.storage：交互式存储资源管理器。”](https://www.evm.storage/)
- 🧮 Ethervm，[EVM 操作码的低级参考](https://ethervm.io/)
- 🎥 奥斯汀·格里菲斯，[“ETH.BUILD。”](https://www.youtube.com/watch?v=30pa790tIIA&list=PLJz1HruEnenCXH7KW7wBCEBnBLOVkiqIi)
- 💻 Franco Victorio，[“EVM 谜题。”](https://github.com/fvictorio/evm-puzzles)
- 💻 Dalton Sweeney，[“更多 EVM 谜题。”](https://github.com/daltyboy11/more-evm-puzzles)
- 💻 Zaryab Afser，[“破译 EVM 谜题。”](https://www.decipherclub.com/decipher-evm-puzzles-game/)

## 实施

- 💻 Solidity：Brock Elmore，[“求解：EVM 在 Solidity 中实现。”](https://github.com/brockelmore/solvm)
- 💻 去：[Geth](https://github.com/ethereum/go-ethereum)
- 💻 C++: [EVMONE](https://github.com/ethereum/evmone)
- 💻 Python：[py-evm](https://github.com/ethereum/py-evm)
- 💻 Rust：[revm](https://github.com/bluealloy/revm)
- 💻 Js/CSS：Riley，[“以太坊虚拟机。”](https://github.com/jtriley-eth/the-ethereum-virtual-machine)

### 基于 EVM 的编程语言

- 🗄 [坚固性](https://soliditylang.org/)
- 🗄 [怒](https://github.com/huff-language/)
- 🗄 [Vyper](https://docs.vyperlang.org/en/stable/)
- 🗄 [铁](https://fe-lang.org/)
