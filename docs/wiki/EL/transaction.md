# 交易

**交易** 是由**外部账户**发出的加密签名指令，通过 [JSON-RPC](/wiki/EL/JSON-RPC.md) 提交给执行客户端，然后使用 [DevP2P](/wiki/EL/devp2p) 广播到整个网络。

交易包含以下字段：

- **nonce ($T_n$)**：一个整数值，等于发送方发送的交易的数量。随机数用于：

  - **防止重播攻击**：假设 Alice 在交易中向 Bob 发送了 1 个 ETH，Bob 可能会尝试将相同的交易重新广播到网络中，以从 Alice 的帐户中获取额外的资金。由于交易是用唯一的随机数签名的，因此如果 Bob 再次发送它，EVM 将简单地拒绝它。从而保护 Alice 的帐户免遭未经授权的重复交易的侵害。
  - **确定合约账户地址**：在`contract creation`模式下，nonce 与发送者地址一起用于确定合约账户地址。
  - **替换交易**：当交易由于 gas 价格低而陷入困境时，矿工通常允许替换具有相同随机数的交易。某些钱包可能会提供利用此行为取消交易的选项。本质上，发送了具有相同随机数、更高 gas 价格和 0 值的新交易，有效地掩盖了原始待处理交易。然而，重要的是要明白，替换待处理的交易的成功并不能得到保证，因为它依赖于矿工的行为和网络条件。

- **gasPrice ($T_p$)**：一个整数值，等于每单位 gas 支付的 wei 数。 **Wei** 是以太币的最小面额。 $1 \textnormal{ETH} = 10^{18} \textnormal{Wei}$。 gas 价格用于优先执行交易。 gas 价格越高，矿工就越有可能将交易作为区块的一部分。

- **gasLimit ($T_g$)**：一个整数值，等于执行该交易时使用的 gas 的最大数量。如果 GasLimit 耗尽，则交易的执行将停止。

- **to ($T_t$)**：此交易的接收者的 20 字节地址。 `to` 字段还确定交易的模式或用途：

| `to` 的值 | 交易模式 |描述 |
| ---------------- | ------------------ | --------------------------------------------------------- |
| _空_ |合同创建| 交易创建一个新的合约账户。 |
|外部账户 |价值转移| 交易将以太币转移到外部账户。 |
|合约账户 |合同执行| 交易调用现有的智能合约代码。 |

- **value ($T_v$)**：一个整数值，等于要传输给该交易接收者的 Wei 数量。 `Contract creation`模式下，value 成为新建合约账户的初始余额。

- **data ($T_d$) 或 init($T_i$)**：无限大小的字节数组，指定 EVM 的输入。在合约 `creation mode` 中，该值被视为 `init bytecode`，否则视为 `input data` 的字节数组。

- **签名 ($T_v, T_r, T_s$)**：发件人的 [ECDSA](/wiki/Cryptography/ecdsa.md) 签名。


## 合约创建

让我们将以下代码部署到新的合约帐户上：

```bash
[00] PUSH1 06 // Push 06
[02] PUSH1 07 // Push 07
[04] MUL      // Multiply
[05] PUSH1 0  // Push 00 (storage address)
[07] SSTORE   // Store result to storage slot 00
```

括号表示指令偏移量。对应字节码：

```bash
6006600702600055
```

现在，让我们准备交易的 `init` 值来部署此字节码。 Init 实际上由两个片段组成：

```
<init bytecode> <runtime bytecode>
```

`init` 仅在帐户创建时由 EVM 执行一次。初始化代码执行的返回值是**运行时字节码**，它作为合约帐户的一部分存储。每当合约账户收到交易时，就会执行运行时字节码。

让我们准备初始化代码，使其返回运行时代码：

```bash
// 1. Copy to memory
[00] PUSH1 08 // PUSH1 08 (length of our runtime code)
[02] PUSH1 0c // PUSH1 0c (offset of the runtime code in init)
[04] PUSH1 00 // PUSH1 00 (destination in memory)
[06] CODECOPY // Copy code running in current environment to memory
// 2. Return from memory
[07] PUSH1 08 // PUSH1 08 (length of return data)
[09] PUSH1 00 // PUSH1 00 (memory location to return from)
[0b] RETURN   // Return the runtime code and halt execution
// 3. Runtime code (8 bytes long)
[0c] PUSH1 06
[0e] PUSH1 07
[10] MUL
[11] PUSH1 0
[13] SSTORE
```

该代码做了 2 件简单的事情：首先，将运行时字节码复制到内存，然后从内存返回运行时字节码。

`init` 字节码：

```javascript
6008600c60003960086000f36006600702600055
```

接下来，准备交易载荷：

```javascript
[
  "0x", // nonce (zero nonce, since first transaction)
  "0x77359400", // gasPrice (we're paying 2000000000 wei per unit of gas)
  "0x13880", // gasLimit (80000 is standard gas for deployment)
  "0x", // to address (empty in contract creation mode)
  "0x05", //value (we'll be nice and send 5 wei to our new contract)
  "0x6008600c60003960086000f36006600702600055", // init code
];
```

> 载荷中的值的顺序很重要！

在此示例中，我们将使用 [Foundry](https://getfoundry.sh/) 在本地部署交易。 Foundry 是一个 Ethereum 开发工具包，提供以下 cli 工具：

- **Anvil**：本地 Ethereum 节点，专为开发而设计。
- **Cast**：用于执行 Ethereum RPC 调用的工具。

安装并启动 [anvil](https://book.getfoundry.sh/anvil/) 本地节点。

```
$ anvil


                             _   _
                            (_) | |
      __ _   _ __   __   __  _  | |
     / _` | | '_ \  \ \ / / | | | |
    | (_| | | | | |  \ V /  | | | |
     \__,_| |_| |_|   \_/   |_| |_|

    0.2.0 (5c3b075 2024-03-08T00:17:08.007462509Z)
    https://github.com/foundry-rs/foundry

Available Accounts
==================

(0) "0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266" (10000.000000000000000000 ETH)
.....

Private Keys
==================

(0) 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
.....
Listening on 127.0.0.1:8545
```

使用 anvil 的虚拟帐户之一签署交易：

```bash
$ node sign.js '[ "0x", "0x77359400", "0x13880", "0x", "0x05", "0x6008600c60003960086000f36006600702600055" ]' ac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

f864808477359400830138808005946008600c60003960086000f360066007026000551ca01446316c9bdcbe0cb87fac0b08a00e59552634c96d0d6e2bd522ea0db827c1d0a0170680b6c348610ef150c1b443152214203c7f66288ea6332579c0cdfa86cc3f
```

> 有关 `sign.js` 帮助程序脚本的来源，请参阅下面的**附录 A**。

最后，使用 [cast](https://book.getfoundry.sh/cast/) 提交交易：

```javascript
$ cast publish f864808477359400830138808005946008600c60003960086000f360066007026000551ca01446316c9bdcbe0cb87fac0b08a00e59552634c96d0d6e2bd522ea0db827c1d0a0170680b6c348610ef150c1b443152214203c7f66288ea6332579c0cdfa86cc3f

{
  "transactionHash": "0xdfaf2817f19963846490b330ae33eba7b42872e8c8bd111c8d7ea3846c84cd51",
  "transactionIndex": "0x0",
  "blockHash": "0xfde1475a716583d847f858c5db3e54156983b39e3dbefaa5829416e6e60a788a",
  "blockNumber": "0x1",
  "from": "0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266",
  "to": null,
  "cumulativeGasUsed": "0xd67e",
  "gasUsed": "0xd67e",
  // Newly created contract address 👇
  "contractAddress": "0x5fbdb2315678afecb367f032d93f642f64180aa3",
  "logs": [],
  "status": "0x1",
  "logsBloom": "0x0...",
  "effectiveGasPrice": "0x77359400"
}
```

查询本地 `anvil` 节点确认代码已部署：

```bash
$ cast code 0x5fbdb2315678afecb367f032d93f642f64180aa3
0x6006600702600055
```

初始余额可用：

```bash
$ cast balance 0x5fbdb2315678afecb367f032d93f642f64180aa3
5
```

---

模拟合约创建：

![Contract creation](../../images/evm/create-contract.gif)

## 合约代码执行

我们的简单合约将 6 和 7 相乘，然后将结果存储到存储 **时隙 0**。让我们用另一个交易来执行合约代码。

交易载荷类似，除了 `to` 地址指向智能合约、`value` 和 `data` 为空：

```javascript
[
  "0x1", // nonce (increased by 1)
  "0x77359400", // gasPrice (we're paying 2000000000 wei per unit of gas)
  "0x13880", // gasLimit (80000 is standard gas for deployment)
  "0x5fbdb2315678afecb367f032d93f642f64180aa3", // to address ( address of our smart contract)
  "0x", // value (empty; not sending any ether)
  "0x", // data (empty)
];
```

签署交易：

```bash

$ node sign.js '[ "0x1", "0x77359400", "0x13880", "0x5fbdb2315678afecb367f032d93f642f64180aa3", "0x", "0x"]' ac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

f86401847735940083013880945fbdb2315678afecb367f032d93f642f64180aa380801ba047ae110d52f7879f0ad214784168406f6cbb6e72e0cab59fa4df93da6494b578a02c72fcdea5b7838b520664186707d1465596e4ad4eaf8781a721530f8b8dd5f2
```

发布交易：

```bash
$ cast publish f86401847735940083013880945fbdb2315678afecb367f032d93f642f64180aa380801ba047ae110d52f7879f0ad214784168406f6cbb6e72e0cab59fa4df93da6494b578a02c72fcdea5b7838b520664186707d1465596e4ad4eaf8781a721530f8b8dd5f2

{
  "transactionHash": "0xc82a658b947c6083de71a0c587322e8335448e65e7310c04832e477558b2b0ef",
  "transactionIndex": "0x0",
  "blockHash": "0x40dc37d9933773598094ec0147bef5dfe72e9654025bfaa80c4cdbf634421384",
  "blockNumber": "0x2",
  "from": "0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266",
  "to": "0x5fbdb2315678afecb367f032d93f642f64180aa3",
  "cumulativeGasUsed": "0xa86a",
  "gasUsed": "0xa86a",
  "contractAddress": null,
  "logs": [],
  "status": "0x1",
  "logsBloom": "0x0...",
  "effectiveGasPrice": "0x77359400"
}
```

使用强制转换读取存储 **时隙 0**：

```
$ cast storage 0x5fbdb2315678afecb367f032d93f642f64180aa3 0x
0x000000000000000000000000000000000000000000000000000000000000002a
```

果然，结果确实是[42](<https://simple.wikipedia.org/wiki/42_(answer)>)(0x2a)🎉。

---

模拟合约执行：

![Contract execution](../../images/evm/contract-execution.gif)

## 收据
收据是 EVM 状态转换函数的输出工件。每个成功或不成功执行的交易都会产生相应的收据，如 wiki 的[数据结构](./data-structures.md#receipt-trie) 部分中所述。 在这里，我们将提供有关收据结构及其演变的更多详细信息。

`receipt` 的内容是由五个项目组成的元组：
- **交易类型**：传统交易之间的区别，稍后将详细讨论。
- **状态**：交易状态为 `0` 或 `1`，其中 `1` 表示成功的交易，`0` 表示失败的交易。
- **已使用的 gas**：区块中所有之前的交易消耗的总 gas + 当前交易已使用的 gas。
- **日志**：日志条目是记录器地址的元组、一系列可能为空的索引 32 字节日志主题以及原始事件数据的一些非索引字节。
- **Logs Bloom**：256 字节的 Bloom 过滤器，用于快速搜索区块中的相关日志，允许应用程序高效检查日志中是否包含地址或事件签名。

有关如何使用日志绽放来允许应用程序有效检查日志中是否包含地址或事件签名的一些附加信息可以在[此处](https://medium.com/coinmonks/ethereum-data-transaction-receipt-trie-and-logs-simplified-30e3ae8dc3cf#:~:text=the%20sections%20below.-,Logs%20Bloom,-Assume%20we%20want)找到。

`receipt` 致力于区块的**收据 Trie**。

## 输入交易和收据

EIP-2718 通过*打字信封*的概念为交易和收据引入了统一且可扩展的格式。此扩展简化了新交易和收据类型的引入，同时保持与旧版交易的完全向后兼容性。

在 EIP-2718 之前，添加新的交易类型需要繁琐的技术来在 RLP 编码的限制内区分它们，从而导致设计脆弱。 EIP-2718 通过定义专用的 ***交易 Type*** 前缀解决了这个问题。

EIP-2718 之后的交易遵循信封格式：`Typed Transaction = Transaction Type + Transaction Payload`

其中：
- ***交易类型***：指定交易类型的单字节标识符。
- ***交易载荷***：由相应交易类型规范定义的不透明字节数组。

请注意，旧版交易的格式为 `RLP([nonce, gasPrice, ..., s])`

### 收据编码

收据现在采用相同的信封图案：`Typed Receipt = Transaction Type + Receipt Payload`

其中：
- ***交易类型***：指定交易类型的单字节标识符。
- ***收据载荷***：根据关联的***交易类型***定义进行解释。

请注意，旧收据的格式为 `RLP([status, gasUsed, bloom, logs])`

交易和收据都可以被有效识别：
- 如果第一个字节为 `∈ [0x00, 0x7f]`，则它是**键入的** 交易或收据。
- 如果第一个字节为 `≥ 0xc0`，则它是 **legacy** 交易或收据，如 RLP 列表编码所示。

> 键入的收据的第一个字节**必须**与其关联的交易的 `TransactionType` 相同。

此规则确保客户端可以确定性地解码收据，而不需要额外的元数据。

总之，EIP-2718 使 Ethereum、交易和收据更具可扩展性，同时保留与旧版客户端的向后兼容性。

## 交易类型概述

### 旧版交易(类型 0x00)
在 [EIP-2718](https://eips.ethereum.org/EIPS/eip-2718) 引入交易类型的概念之前，旧版交易是唯一的交易格式。这样的交易在 Ethereum 中仍然兼容。即使使用 [EIP-1559](https://eips.ethereum.org/EIPS/eip-1559) 等更新，通过将 `max_fee_per_gas` 和 `max_priority_fee_per_gas` 设置为旧版交易，旧版交易也会转换为 EIP-1559 兼容性`gas_price`。

### 访问列表交易(类型 0x01)
访问列表交易与旧版交易类似，但它们还包括一个可选的 `access_list` 字段，列出运行交易所需的所有地址和存储槽。

*动机*

2016 年，攻击者在一次名为“上海 DoS 攻击”的事件中以网络为目标，最成功的策略是发送恶意交易访问大量地址和存储槽。该攻击迫使客户端在磁盘上搜索信息，导致 IO-heavy 交易需要很长时间才能处理。该攻击对于攻击者来说成本较低，但对于客户端来说成本较高。

因此，[EIP-2929](https://eips.ethereum.org/EIPS/eip-2929)被提议增加操作码“冷”访问存储的 gas 成本(第一次，在数据被复制到 RAM 之前)。这将使进一步的 DoS 攻击在经济上变得令人望而却步。

由于 gas 存储访问成本增加，EIP-2929 引入了潜在的合同破坏问题。因此，[EIP-2930](https://eips.ethereum.org/EIPS/eip-2930)引入了一个访问列表(`access_list`字段)，指定交易中要访问的所有帐户和存储槽。因此，客户端现在可以预加载数据，从而降低包含访问列表的交易的 gas 成本。

### 动态费用交易(类型 0x02) 
类型 2 交易引入了 [EIP-1559](https://eips.ethereum.org/EIPS/eip-1559) 中描述的新费用市场变化，并在伦敦分叉中引入。网络将尝试通过调整 gas 基本费用来维持每个区块的目标区块使用量为最大容量的 50%。如果当前 gas 使用量高于目标，则 gas 价格会上涨，从而降低需求。另一方面，如果区块没有使用足够的 gas，则 gas 基本费用会降低以进行补偿，从而刺激需求。然后基本费用将被销毁，从而减少 Ethereum 的总供应量。

基本费用增加或减少的一个重要方面是，这种变化只能以父级 gas 限制的 1/1024 的最大增量发生。这确保了 gas 价格能够对需求变化快速做出反应，同时也防止剧烈波动。

此外，交易可以包括额外的优先权费用，以激励特定的交易包含在区块中。该优先权费用不会被烧毁，而是支付给验证者。

*动机*

为什么首先要改变 gas 费用市场？
前期 EIP-1559，gas 价格随网络需求剧烈波动。常见的情况是发送具有特定 gas 价格的交易，并且由于 gas 价格意外飙升，导致交易陷入内存池中。这导致了糟糕的用户体验，通过新增的基本费用逐渐增加来解决。

为什么基本费用会被烧毁？
除了通过提供减少供应和平衡通货膨胀的机制为 Eth 持有者提供经济利益外，基本费用燃烧还降低了费用操纵的风险，防止验证者用“免费”交易淹没区块并保持 gas 价格人为抬高。

### blob- 携带交易(类型 0x03) 
</br>
<img src="images/el-transactions/blob.png" alt="blob" />

[EIP-4844](https://eips.ethereum.org/EIPS/eip-4844) 引入了短暂的 blob 数据存储，网络在一段时间内保持可用(当前为 4096 epoch，即大约 18 天)。

blob 不存储在区块链上，也不存储在标头或正文中。相反，blob 数据与区块一起发送 - 就像 sidecar - 与交易数据一起发送，并由节点提供。标头将仅包含每个 blob 数据的 KZG 承诺。

与 EIP-1559 交易类似(尽管数学不同) blob 交易有自己的 gas 价格市场，该市场根据需求进行调整，以达到总容量 50% 的目标使用率。

*动机*

在 Ethereum 中，可扩展性的长期愿景包括第 1 层(Execution Layer 和 Consensus Layer)和第 2 层链协同工作，第 1 层为第 2 层提供安全保证。因此，第 2 层是 Ethereum 中的一等公民。 

我们关心 blob 数据可用性因为维护临时可用的数据对于服务第 2 层是必要的。例如，乐观的 Rollup 像 Optimism 一样乐观地将交易批次中的承诺推送到链上。 交易可能被恶意省略(例如 Bob 有 10 个 eth，但提交的交易说他有 0 个 eth)。如果是这种情况，资金被盗的用户可以发布欺诈证明来证明不良行为并纠正这种情况。创建此欺诈证明需要访问交易数据，因此 Ethereum 使该数据在短时间内可用，如 blob，为用户提供合理的反应时间。因此，数据可用性对于确保第 2 层的合规状态转换是必要的。 

### EOA 的设置代码交易(类型 0x04)
类型 4 交易旨在将智能合约代码附加到外部拥有的帐户。让我们回想一下，EOA 有一个空的 `code_hash` 字段，因此它们本身通常没有程序。 [EIP-7702](https://eips.ethereum.org/EIPS/eip-7702) 通过将值设置为 `(0xef0100 || delegated_contract_address)` 的值来更改这一点，其中 `||` 表示串联。一旦设置了代码，EOA 就可以将其代码执行委托给保存的地址。需要指出的是，EOA 本身并不保存代码，而只是指向代码的指针。可以使用另一个类型 4 交易删除或更改该指针。因为我们可以区分 EOA 和合约，[EIP-3607](https://eips.ethereum.org/EIPS/eip-3607)，防止攻击者产生与现有合约冲突的地址并窃取资金，仍然可以得到尊重。

选择委托合约地址之前的值 `0xef0100` 来提供不会发生冲突的唯一标识符。
实际上，`0xef` 是根据 [EIP-3541](https://eips.ethereum.org/EIPS/eip-3541) 的保留字节。后面添加的`0100`是一个标识符，表示 EIP-7702 委托地址。因此，仍然可以通过查看代码哈希来区分智能合约和 EOA，因为 EOA 要么什么也没有，要么是以 `0xef0100` 开头的值。

*动机*

没有 EIP-7702 的纯 EOA 帐户遇到了几个 UX 问题。例如，通过智能合约发送 ERC-20 令牌将通过需要两个单独的交易的 `approve/transferFrom` 模式来完成。 [ERC-4337](https://eips.ethereum.org/EIPS/eip-4337) 中描述的 Account Abstraction (智能合约钱包) 旨在解决此问题，此外还提供附加功能，例如批量交易、gas 赞助等...

有了 EIP-7702，整个 Ethereum 生态系统现在可以通过允许 EOA 选择加入来从 Account Abstraction 中受益。

## 附录 A：交易签名者

`signer.js`：用于签名交易的简单 [节点.js](https://nodejs.org/) 脚本。解释见评论：

```javascript
/**
 * Utility script to sign a transaction payload array.
 * Usage: node sign.js '[payload]' [private key]
 */

const { rlp, keccak256, ecsign } = require("ethereumjs-util");

// Parse command-line arguments
const payload = JSON.parse(process.argv[2]);
const privateKey = Buffer.from(process.argv[3].replace("0x", ""), "hex");

//validate privatekey length
if (privateKey.length != 32) {
  console.error("Private key must be 64 characters long!");
  process.exit(1);
}

// STEP 1: Encode payload to RLP
// Learn more: https://ethereum.org/en/developers/docs/data-structures-and-encoding/rlp/
const unsignedRLP = rlp.encode(payload);

// STEP 2: Hash the RLP encoded payload
// Learn more: https://ethereum.org/en/glossary/#keccak-256
const messageHash = keccak256(unsignedRLP);

// STEP 3: Sign the message
// Learn more: https://epf.wiki/#/wiki/Cryptography/ecdsa
const { v, r, s } = ecsign(messageHash, privateKey);

// STEP 4: Append signature to payload
payload.push(
  "0x".concat(v.toString(16)),
  "0x".concat(r.toString("hex")),
  "0x".concat(s.toString("hex"))
);

// STEP 5: Output RLP encoded signed transaction
console.log(rlp.encode(payload).toString("hex"));
```

## 资源
- 📝 Gavin Wood，[“Ethereum 黄皮书。”](https://ethereum.github.io/yellowpaper/paper.pdf)
- 📘 Andreas M. Antonopoulos、Gavin Wood，[“掌握 Ethereum。”](https://github.com/ethereumbook/ethereumbook)
- 📝 Ethereum.org，[“RLP 编码。”](https://ethereum.org/en/developers/docs/data-structures-and-encoding/rlp/)
- 📝 Ethereum.org，[“交易。”](https://ethereum.org/en/developers/docs/transactions/)
- 📝 随机笔记，[“以艰难的方式签署交易。”](https://lsongnotes.wordpress.com/2018/01/14/signing-an-ethereum-transaction-the-hard-way/) • [已存档](https://web.archive.org/web/20240229045603/https://lsongnotes.wordpress.com/2018/01/14/signing-an-ethereum-transaction-the-hard-way/)
- 🎥 Lefteris Karapetsas，[“在 EVM 中理解交易 - 兼容区块链。”](https://archive.devcon.org/archive/watch/6/understanding-transactions-in-evm-compatible-blockchains-powered-by-opensource/?tab=YouTube)
- 🎥 奥斯汀·格里菲斯，[“交易 - ETH.BUILD。”](https://www.youtube.com/watch?v=er-0ihqFQB0)
- 🧮 Paradigm，[“Foundry：Ethereum 开发工具包。”](https://github.com/foundry-rs/foundry)
- [有线协议收据](https://github.com/ethereum/devp2p/blob/master/caps/eth.md) • [已存档](https://web.archive.org/web/20250328095848/https://github.com/ethereum/devp2p/blob/master/caps/eth.md)
- [EiP-2718](https://eips.ethereum.org/EIPS/eip-2718) • [已存档](https://web.archive.org/web/20250328095848/https://eips.ethereum.org/EIPS/eip-2718)
- [收据内容](https://medium.com/coinmonks/ethereum-data-transaction-receipt-trie-and-logs-simplified-30e3ae8dc3cf) • [已存档](https://web.archive.org/web/20250000000000/https://medium.com/coinmonks/ethereum-data-transaction-receipt-trie-and-logs-simplified-30e3ae8dc3cf)
