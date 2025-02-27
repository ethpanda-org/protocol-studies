# 交易

A **交易** 是一个由**外部账户**发布的加密签名指令，使用 [JSON-RPC](/wiki/EL/JSON-RPC.md)广播到整个网络。

一个交易包含以下字段：

- **nonce ($T_n$)**: 等于发送方发送的交易数的一个整数值。Nonce 用于：
- **防止重放攻击**：假设 Alice 在一次交易中向 Bob 发送了1个 ETH ，Bob 可能会试图将相同的交易重新广播到网络中，以从 Alice 的账户中获取额外的资金。由于交易是用唯一的 nonce 签名的，如果 Bob 再次发送它，EVM 将简单地拒绝它。从而保护 Alice 的账户免受未经授权的重复交易。
- **确定合同账户地址**：在“合同创建”模式下，nonce 和发件人地址一起用于确定合同账户地址。
- **替换交易**：当交易由于天然气价格低而陷入僵局时，矿工通常允许替换交易具有相同的 nonce 。一些钱包可能会利用这种行为提供取消交易的选项。本质上，发送一个具有相同的 nonce、更高的 gas 价格和0值的新交易，有效地掩盖了原始待处理交易。然而，重要的是要理解，替换待处理交易的成功并不能保证，因为它依赖于矿工的行为和网络条件。

- **gasprice ($T_p$)**：一个整数值，等于每单位 gas 的价格。**Wei** 是以太币的最小面额。$1 \textnormal{ETH} = 10^{18} \textnormal{Wei}$。gas 价格用于确定交易执行的优先级。gas 价格越高，矿商将交易作为区块的一部分的可能性就越大。

- **gasLimit ($T_g$)**：一个整数值，等于在执行该交易时使用的最大 gas 量。如果 gas 限额耗尽，该交易的执行将停止。
- **to ($T_t$)**: 此交易接收者的 20 字节地址。‘ to ’ 字段也决定了交易的模式或目的：

|     `to`的值     |      交易模式     |                        描述                               |
| ---------------- | ------------------ |  -------------------------------------------------------- |
|        空        |      创建合约      |                 这个交易创建了一个新的合约账户             |
|     外部账户     |      价值转移      |             这个交易把 Ether 转移到了一个外部账户          |
|     合约账户     |      执行合约      |                 这个交易调用现有的智能合约代码             |

- **value ($T_v$)**: 一个整数值，等于要转移给该交易接收者的 Wei 的数量。在‘创建合约’模式下，value 成为新创建的合约账户的初始余额。
- **data ($T_d$) or init($T_i$)**: 指定 EVM 输入的无限大小字节数组。在合约的`创建模式`中，这个值被认为是 `init bytecode` ，否则是`输入数据`的字节数组。
- **Signature ($T_v, T_r, T_s$)**: [ECDSA](/wiki/Cryptography/ecdsa.md) 发送者的签名。


## 创建合约

让我们在一个新的合约账户中部署以下代码：

```bash
[00] PUSH1 06 // Push 06
[02] PUSH1 07 // Push 07
[04] MUL      // Multiply
[05] PUSH1 0  // Push 00 (存储地址)
[07] SSTORE   // 将结果储存到 storage slot 00
```

方括号表示指令偏移量 (instruction offset) 。对应的字节码:

```bash
6006600702600055
```

现在，让我们准备交易的 `init` 值来部署这个字节码。Init 实际上由两个片段组成：
```
<init bytecode> <runtime bytecode>
```

EVM 只在创建帐户时执行一次`init`。init code 执行的返回值是 **runtime bytecode** ，它作为合约账户的一部分存储。每当合约账户收到交易时，都会执行 runtime bytecode 。

让我们准备初始化代码 (init code) ，使其返回运行时代码 (runtime code) ：

```bash
// 1. 复制到内存
[00] PUSH1 08 // PUSH1 08 (我们 runtime code 的长度)
[02] PUSH1 0c // PUSH1 0c (runtime code 在 init 的 offset)
[04] PUSH1 00 // PUSH1 00 (内存中的目的地)
[06] CODECOPY // 将当前环境中运行的代码复制到内存中
// 2. 从内存中返回
[07] PUSH1 08 // PUSH1 08 (返回数据的长度)
[09] PUSH1 00 // PUSH1 00 (要返回的内存位置)
[0b] RETURN   // 返回 runtime code 和终止执行
// 3. Runtime code (8 字节长)
[0c] PUSH1 06
[0e] PUSH1 07
[10] MUL
[11] PUSH1 0
[13] SSTORE
```

代码做了两件简单的事情：首先，将 runtime code 复制到内存中，然后从内存中返回 runtime code 。
`init` bytecode:

```javascript
6008600c60003960086000f36006600702600055
```

接下来，准备交易负载/载荷(transaction payload)：

```javascript
[
  "0x", // nonce (0 nonce, 从第一次交易起)
  "0x77359400", // gasPrice (我们每单位 gas 支付 2000000000 wei)
  "0x13880", // gasLimit (80000 是用于部署的标准 gas)
  "0x", // to address (在合约创建模式中为空)
  "0x05", //value (我们将会给我们的新合约发送 5 wei)
  "0x6008600c60003960086000f36006600702600055", // init code
];
```

> 负载中的数值顺序很重要！

例如，我们会使用 [Foundry](https://getfoundry.sh/) 来本地部署交易。 Foundry 是一个提供以下 cli 工具的以太坊开发工具包：
- **Anvil** : 本地以太坊节点，专为开发而设计。
- **Cast**: 执行以太坊 RPC 调用的工具。
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

可用的账户
==================

(0) "0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266" (10000.000000000000000000 ETH)
.....

私钥
==================

(0) 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
.....
Listening on 127.0.0.1:8545
```

使用 anvil 的一个虚拟账户签署交易：

```bash
$ node sign.js '[ "0x", "0x77359400", "0x13880", "0x", "0x05", "0x6008600c60003960086000f36006600702600055" ]' ac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

f864808477359400830138808005946008600c60003960086000f360066007026000551ca01446316c9bdcbe0cb87fac0b08a00e59552634c96d0d6e2bd522ea0db827c1d0a0170680b6c348610ef150c1b443152214203c7f66288ea6332579c0cdfa86cc3f
```

>有关`sign.js`辅助脚本的源代码，请参阅下面的**附录 A**。
最终，使用 [cast](https://book.getfoundry.sh/cast/) 提交交易：

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

查询本地的 `anvil` 节点确认代码已部署：
```bash
$ cast code 0x5fbdb2315678afecb367f032d93f642f64180aa3
0x6006600702600055
```

可用初始余额：

```bash
$ cast balance 0x5fbdb2315678afecb367f032d93f642f64180aa3
5
```

---

模拟创建合约：
![Contract creation](../../images/evm/create-contract.gif)

## 执行合约代码

我们的简单合约将 6 和 7 相乘，然后将结果存储到存储 **slot 0** 中。让我们用另一个交易执行合约代码。

交易的有效负载类似，除了 `to` 指向智能合约的地址点，`value` 和 `data` 为空：
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

给交易签名：

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

使用 cast 读取存储 **slot 0**：

```
$ cast storage 0x5fbdb2315678afecb367f032d93f642f64180aa3 0x
0x000000000000000000000000000000000000000000000000000000000000002a
```

果然，结果是 [42](<https://simple.wikipedia.org/wiki/42_(answer)>) (0x2a) 🎉.

---

模拟合约执行：

![Contract execution](../../images/evm/contract-execution.gif)

## 目录 A: 交易签署

`signer.js`: 一个 [node.js](https://nodejs.org/) 签署合约的简单脚本. 详情见注释：
```javascript
/**
 * Utility script to sign a transaction payload array.
 * Usage: node sign.js '[payload]' [private key]
 */

const { rlp, keccak256, ecsign } = require("ethereumjs-util");

// 解析命令行参数
const payload = JSON.parse(process.argv[2]);
const privateKey = Buffer.from(process.argv[3].replace("0x", ""), "hex");

//验证私钥长度
if (privateKey.length != 32) {
  console.error("Private key must be 64 characters long!");
  process.exit(1);
}

// 步骤1：将有效载荷编码为 RLP
// 了解更多: https://ethereum.org/en/developers/docs/data-structures-and-encoding/rlp/
const unsignedRLP = rlp.encode(payload);

// 步骤2：对 RLP 编码的有效载荷进行散列
// 了解更多: https://ethereum.org/en/glossary/#keccak-256
const messageHash = keccak256(unsignedRLP);

// 步骤3：签署信息
// 了解更多: https://epf.wiki/#/wiki/Cryptography/ecdsa
const { v, r, s } = ecsign(messageHash, privateKey);

// 步骤4：将签名添加到有效载荷
payload.push(
  "0x".concat(v.toString(16)),
  "0x".concat(r.toString("hex")),
  "0x".concat(s.toString("hex"))
);

// 步骤5：输出 RLP 编码的签名交易
console.log(rlp.encode(payload).toString("hex"));
```

## 资源
- 📝 Gavin Wood, ["Ethereum Yellow Paper."](https://ethereum.github.io/yellowpaper/paper.pdf)
- 📘 Andreas M. Antonopoulos, Gavin Wood, ["Mastering Ethereum."](https://github.com/ethereumbook/ethereumbook)
- 📝 Ethereum.org, ["RLP Encoding."](https://ethereum.org/en/developers/docs/data-structures-and-encoding/rlp/)
- 📝 Ethereum.org, ["Transactions."](https://ethereum.org/en/developers/docs/transactions/)
- 📝 Random Notes, ["Signing transactions the hard way."](https://lsongnotes.wordpress.com/2018/01/14/signing-an-ethereum-transaction-the-hard-way/) • [archived](https://web.archive.org/web/20240229045603/https://lsongnotes.wordpress.com/2018/01/14/signing-an-ethereum-transaction-the-hard-way/)
- 🎥 Lefteris Karapetsas, ["Understanding Transactions in EVM-Compatible Blockchains."](https://archive.devcon.org/archive/watch/6/understanding-transactions-in-evm-compatible-blockchains-powered-by-opensource/?tab=YouTube)
- 🎥 Austin Griffith, ["Transactions - ETH.BUILD."](https://www.youtube.com/watch?v=er-0ihqFQB0)
- 🧮 Paradigm, ["Foundry: Ethereum development toolkit."](https://github.com/foundry-rs/foundry)
