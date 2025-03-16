# 交易（Transaction）

**交易**（Transaction）是由**外部账户**（External Account）发出的、经过加密签名的指令，并通过 [JSON-RPC](/wiki/EL/JSON-RPC.md) 广播至整个网络。

## 交易字段

交易包含以下字段：

- **随机数（Nonce，$T_n$）**：一个整数，等于发送方已经发送的交易数量。Nonce 的作用如下：

  - **防止重放攻击**：假设 Alice 发送 1 ETH 给 Bob，Bob 可能尝试再次广播相同的交易来从 Alice 的账户中获取额外资金。然而，由于交易的签名包含唯一的 Nonce，EVM 会直接拒绝重复的交易，从而防止 Bob 的恶意行为。
  - **确定合约账户地址**：在`创建合约`模式下，Nonce 与发送方地址共同决定合约账户的地址。
  - **替换交易**：当某笔交易因 Gas 价格过低而滞留时，矿工通常允许提交相同 Nonce 的替代交易。一些钱包提供取消交易的选项，即发送一个相同 Nonce、更高 Gas 价格且金额为 0 的交易来覆盖原交易。然而，替换交易是否成功取决于矿工的行为和网络状况。

- **Gas 价格（gasPrice，$T_p$）**：一个整数，表示每单位 Gas 需要支付的 Wei 数量。**Wei** 是以太的最小单位，$1 \text{ETH} = 10^{18} \text{Wei}$。Gas 价格用于确定交易执行的优先级，Gas 价格越高，交易被矿工打包的可能性越大。

- **Gas 上限（gasLimit，$T_g$）**：一个整数，表示交易执行时最多可消耗的 Gas 量。如果 Gas 耗尽，交易执行将被终止。

- **接收方（to，$T_t$）**：一个 20 字节的地址，表示交易的接收方。`to` 字段决定交易的模式或目的：

| `to` 的值          | 交易模式         | 描述                                         |
| ------------------ | ---------------- | -------------------------------------------- |
| _为空_            | 创建合约         | 该交易用于创建新的智能合约。                 |
| 外部账户          | 价值转移         | 该交易用于向外部账户转账 ETH。               |
| 合约账户          | 执行合约         | 该交易用于调用已部署的智能合约。             |

- **转账金额（value，$T_v$）**：一个整数，表示交易向接收方转移的 Wei 数量。在 `创建合约` 模式下，该值成为新合约账户的初始余额。

- **数据（data，$T_d$）或初始化代码（init，$T_i$）**：一个不限制大小的字节数组，指定要传递给 EVM 的输入数据。在 `创建合约` 模式下，该字段表示 `初始化字节码`，否则表示 `输入数据`。

- **签名（$T_v, T_r, T_s$）**：[ECDSA](/wiki/Cryptography/ecdsa.md) 签名，用于验证交易发送者的身份。

## 合约创建（Contract Creation）

我们将以下代码部署到一个新的合约账户：

```assembly
[00] PUSH1 06 // 压入 06
[02] PUSH1 07 // 压入 07
[04] MUL      // 乘法运算
[05] PUSH1 0  // 压入 00（存储地址）
[07] SSTORE   // 存储结果到存储槽 00
```

对应的字节码如下：

```assembly
6006600702600055
```

合约 `init` 代码：

```assembly
// 1. 复制到内存
[00] PUSH1 08 // PUSH1 08（运行时代码长度）
[02] PUSH1 0c // PUSH1 0c（运行时代码偏移量）
[04] PUSH1 00 // PUSH1 00（内存目标地址）
[06] CODECOPY // 复制代码到内存
// 2. 从内存返回
[07] PUSH1 08 // PUSH1 08（返回数据长度）
[09] PUSH1 00 // PUSH1 00（内存起始位置）
[0b] RETURN   // 返回运行时代码并停止执行
// 3. 运行时代码（8 字节）
[0c] PUSH1 06
[0e] PUSH1 07
[10] MUL
[11] PUSH1 0
[13] SSTORE
```

生成 `init` 字节码：

```javascript
6008600c60003960086000f36006600702600055
```

### 交易签名与发布

使用 Foundry 工具 `anvil` 运行本地以太坊节点，并使用 `cast` 发送交易。

```bash
$ anvil
```

使用 `sign.js` 脚本签名交易：

```bash
$ node sign.js '[ "0x", "0x77359400", "0x13880", "0x", "0x05", "0x6008600c60003960086000f36006600702600055" ]' ac0974...
```

提交交易：

```bash
$ cast publish f864...
```

## 合约代码执行（Contract Execution）

智能合约的功能是计算 `6 × 7`，并将结果存储到存储槽 **0**。我们可以执行合约代码，方法是发送一笔 `to` 地址为合约地址的交易：

```javascript
[
  "0x1", // 随机数（增加 1）
  "0x77359400", // gasPrice
  "0x13880", // gasLimit
  "0x5fbdb2315678afecb367f032d93f642f64180aa3", // 合约地址
  "0x", // value（空）
  "0x" // data（空）
];
```

签名交易并发布：

```bash
$ cast publish f864...
```

查询存储槽 **0**：

```bash
$ cast storage 0x5fbdb2315678afecb367f032d93f642f64180aa3 0x
0x000000000000000000000000000000000000000000000000000000000000002a
```

结果为 **42**（0x2a）🎉。

---

## 资源

- 📜 Gavin Wood，《[以太坊黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf)》
- 📘 Andreas M. Antonopoulos, Gavin Wood，《[精通以太坊](https://github.com/ethereumbook/ethereumbook)》
- 📝 [以太坊 RLP 编码](https://ethereum.org/en/developers/docs/data-structures-and-encoding/rlp/)
- 📝 [以太坊交易](https://ethereum.org/en/developers/docs/transactions/)

