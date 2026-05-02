# Engine API

> [!WARNING]
> 本页的 Glamsterdam 部分涵盖了活跃的研究领域(EIP-7732 ePBS 和 EIP-7928 BALs)。这些部分在阅读时可能已经过时，并且随着设计空间的发展，未来可能会更新。

**必备阅读：** [EL 架构](/wiki/EL/el-architecture.md)

引擎 API 是共识层 (CL) 和执行层 (EL) 之间经过身份验证的通信接口，在 The Merge 中引入。 CL 通过此接口驱动 EL 的区块构建、验证和分叉选择。它告诉 EL 哪条链是规范的，要求它构建新的区块，并发送收到的区块进行验证。

## 架构

### 网络隔离

引擎 API 在**专用端口(默认：8551)**上提供服务，与面向公众的 JSON-RPC API (默认：8545)严格分开。这种隔离是安全要求。共享端口将允许公共流量或故意的 DoS 洪流使共识对话陷入饥饿，导致提案丢失和证明。

### 通信协议

|协议|认证|笔记|
|----------|---------------|-------|
| HTTP |每个请求 JWT |标准；无状态|
| WebSocket | JWT 仅在初次握手时 |持久连接；没有每帧验证 |
| IPC |无 |仅限同一台机器；文件系统权限提供隔离 |

### 辅助 eth_ 方法

该规范要求 EL 在经过身份验证的引擎 API 端口上公开以下所有 9 个 `eth_` 方法，使 CL 无需单独连接即可查询链状态：

`eth_blockNumber`、`eth_call`、`eth_chainId`、`eth_getCode`、`eth_getBlockByHash`、`eth_getBlockByNumber`、`eth_getLogs`、`eth_sendRawTransaction`、`eth_syncing`

`eth_getLogs` 对于 CL 存款合约(Electra 之前)的监控至关重要。 `eth_call` 使 CL 能够验证 EIP-7002 提款凭证，而无需广播交易。

## 认证

CL 和 EL 共享一个 256 位(32 字节)十六进制编码的 JWT 密钥，通过指向 `jwt.hex` 文件的 `--jwt-secret` CLI 标志进行配置。如果省略，EL 会自动为该会话生成一个随机密钥，并将其写入其数据目录中的 `jwt.hex`。

**算法**：EL 必须强制执行 **HS256** (HMAC-SHA256)。必须立即拒绝任何指定 `alg: none` 的 JWT 以防止绕过身份验证。

**重播保护**：每个 JWT 必须包含 `iat`(发布于)声明。 EL 必须拒绝令牌的 `iat` 与 EL 的本地时钟偏差超过**±60 秒**的任何请求。这可以防止重放捕获的代币以引发链重组。

可选声明(`id`、`clv`)可能包含在遥测中，但未针对访问控制进行验证。

## 能力谈判

### engine_exchangeCapabilities

`engine_exchangeCapabilities` 没有版本后缀。这是唯一一个没有的 Engine API 方法。它让客户端发现彼此支持的方法版本。

- EL **必须**支持此方法； CL 可以选择性地调用它
- 各方发送其支持的方法名称数组以及版本后缀(例如 `["engine_newPayloadV3", "engine_newPayloadV4"]`)
- EL 用它自己的列表进行响应。 `engine_exchangeCapabilities` 本身**不得**出现在响应中。
- 如果从未调用此方法，则 EL 不得记录错误(向后兼容)

### engine_getClientVersionV1

一种可选方法，允许 CL 和 EL 识别彼此的软件。每一方返回一个 `ClientVersionV1` 结构，其中包含两个字母的客户端代码、人类可读的名称、版本字符串和 4 字节提交哈希。紧凑的标识符旨在适合 32 字节信标区块涂鸦字段，用于网络多样性跟踪。

|代码| EL 客户端 |代码| CL 客户端 |
|------|-----------|------|-----------|
| `BU` |Besu | `GR` |Grandine|
| `EG` |Erigon | `LH` |Lighthouse|
| `EJ` |以太坊JS | `LS` |Lodestar|
| `EX` |Ethrex | `NB` |Nimbus |
| `GE` |Geth | `PM` |Prysm |
| `NM` |Nethermind | `TK` |Teku |
| `RH` |Reth | | |
| `TE` | Trin 执行 | | |

如果从未调用此方法，则 EL 不得记录错误。

## 核心方法

### engine_forkchoiceUpdatedV3

更新 EL 的规范链视图并可选择启动区块构建。

**参数：**
- `forkchoiceState`: `{headBlockHash, safeBlockHash, finalizedBlockHash}`
- `payloadAttributes`(可选)：`{timestamp, prevRandao, suggestedFeeRecipient, withdrawals, parentBeaconBlockRoot}`。如果提供，EL 开始构建载荷并返回 `payloadId`。

**返回：** `{payloadStatus, payloadId}`

`engine_forkchoiceUpdatedV3` 仅返回 `VALID`、`INVALID` 或 `SYNCING`。它永远不会返回 `ACCEPTED`。 分叉选择更新是重新组织或扩展规范链的权威命令，因此 EL 必须在执行更新之前完全解析头区块的状态。 `ACCEPTED` 是 `engine_newPayload` 独有的。

### 载荷状态值

|状态 |返回者 |意义|
|--------|-------------|---------|
| `VALID` |两者 | 区块和所有祖先完全下载并经过 EVM 验证 |
| `INVALID` |两者 |违反共识规则； `latestValidHash` 标识 fork 恢复的最高有效祖先 |
| `SYNCING` |两者 |本地缺少所需的祖先数据； EL 已开始从 p2p 获取 |
| `ACCEPTED` |仅新载荷 | 区块哈希有效，所有交易非零长度，载荷确实**不**扩展规范链(它位于侧分支上)，祖先在本地可用。 EVM 的执行被故意推迟，直到分叉选择可能转向此分支。 |

**ACCEPTED 与 SYNCING**：`SYNCING` 表示 EL 无法接受区块，因为其链历史记录丢失。 `ACCEPTED` 表示血统完好。 EL 选择不运行完整的 EVM 验证，因为这是当前的非规范侧分支。如果 LMD-GHOST 稍后转向此分支，则 EL 执行延迟的状态转换。

### engine_newPayloadV4

将执行载荷从接收到的信标区块传送到 EL 进行验证。

**参数：**
- `executionPayload`：完整的区块
- `expectedBlobVersionedHashes`：blob 版本哈希的有序列表
- `parentBeaconBlockRoot`：父信标区块根(EIP-4788)
- `executionRequests`：EL 生成的 CL (Electra+) 请求

**验证：**
- 执行所有交易并验证结果状态根
- **blob 哈希验证**：从载荷中的每个携带交易的 blob 中提取 `blob_versioned_hashes`，保留包含顺序，连接它们，并与 `expectedBlobVersionedHashes` 进行比较。任何不匹配或顺序差异都会返回 `INVALID`。

**返回：** `VALID`、`INVALID`、`SYNCING` 或 `ACCEPTED`

### engine_getPayloadV4

检索使用 `payloadAttributes` 调用 `forkchoiceUpdated` 后构建的载荷和 EL。

**参数：** `payloadId` 是 `engine_forkchoiceUpdatedV3` 返回的 8 字节 ID。

**返回：**
- `executionPayload`：组装后的区块
- `blockValue`：`feeRecipient`的优先费中的总wei(256位数量)
- `blobsBundle`：`{commitments, proofs, blobs}`。包含48字节KZG 承诺、48字节KZG证明和131,072字节blob数据，用于CL构建blob sidecar。
- `shouldOverrideBuilder`：来自 EL 的布尔**建议**，应使用本地构建的载荷而不是外部 MEV-Boost 出价。 CL 可能会或可能不会对此采取行动。它是 CL 决策的一个输入，而不是命令。 EL 使用实现定义的启发式方法进行设置(例如，高价值的交易已始终从构建者出价中排除)。如果 EL 未实现启发式，则它必须默认为 `false`。
- `executionRequests`：EL 生成的请求 (Electra+)

## 执行请求 (EIP-7685)

在 V4 (Prague/Electra) 中引入，执行请求允许 EVM 智能合约触发共识层状态更改。每个请求都是 `request_type ++ request_data`，其中 `request_type` 是 1 字节前缀。

**Electra 支持的类型：**

|类型 | EIP |描述 |
|------|-----|-------------|
| `0x00` | EIP-6110 | **存款请求**：EL 将存款事件直接推送到 CL，替代 CL 日志轮询。将验证者激活时间从约 12 小时缩短至约 13 分钟。每个载荷的最大值：`MAX_DEPOSIT_REQUESTS_PER_PAYLOAD = 8,192`。 |
| `0x01` | EIP-7002 | **提现请求**：智能合约持有`0x01`提现凭证可以触发验证者部分或全部退出EVM。通过 `0x00000961Ef480Eb55e80D19ad83579A64c007002` 处的预部署进行处理。目标：2/区块；最大值：16/区块。费用：`fake_exponential(1_wei, excess, 17)`，正常负载下接近1wei，持续需求下呈指数级昂贵。系统调用gas：30,000,000(不计入区块 gas记账)。 |
| `0x02` | EIP-7251 | **合并请求**：将多个 32 ETH 验证者合并为单个复合验证者(最多 2048 ETH 有效余额)。目标：1/区块；最大值：2/区块。待处理队列硬限制：`PENDING_CONSOLIDATIONS_LIMIT = 262,144` (2^18)。 |

**排序和验证规则**：`executionRequests` 数组必须是：
- 按 `request_type` **严格升序**排序
- 每个 `request_type` 字节**最多出现一次**(无重复)
- 没有包含空 `request_data` 的元素(必须排除长度 <= 1 字节的元素)

任何违规都会返回 `-32602: Invalid params`。 EL 还计算 `requests_hash` (排序列表上的 SHA-256)并根据区块标头对其进行验证；不匹配返回 `INVALID`。对于没有请求的区块，哈希默认为 `sha256("") = 0xe3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855`。

## 版本历史

|版本 | EL 分叉 | CL 分叉|关键变化|
|---------|---------|---------|-------------|
| V1 |巴黎 |贝拉特里克斯 |初始合并后：`forkchoiceUpdated`、`newPayload`、`getPayload` |
| V2 |上海|卡佩拉|将 `withdrawals` 添加到载荷属性和执行载荷 |
| V3 |Cancun |Deneb |添加了`blobVersionedHashes`、`parentBeaconBlockRoot`； `executionPayload` 增加 `blobGasUsed`、`excessBlobGas`； `getPayload` 返回 `BlobsBundleV1` |
| V4 |布拉格 |伊莱克特拉 |添加 `executionRequests` (EIP-7685) |

## 时隙生命周期

引擎 API 在严格的 12 秒时隙心跳内运行。对于提议者节点：

|时间 |行动|
|------|--------|
| **t = 0s** | 时隙开始。 CL 使用 `payloadAttributes` 调用 `engine_forkchoiceUpdatedV3`。 EL 返回 `payloadId`。 |
| **t = 1-3s** | EL 构建载荷：选择内存池交易，执行它们，计算状态根。 CL 可选择查询外部 MEV 中继。 |
| **t = 3s** | CL 调用 `engine_getPayloadV4`，将载荷包装成 `BeaconBlock`，对其进行签名，然后广播。 |
| **t = 4s** | **证明截止日期。** 其他验证者必须已收到区块，称为 `engine_newPayloadV4`，并在此时收到 `VALID`。迟到的区块未经证实，也没有获得收入。 |
| **t = 4-12s** | CL 调用 `engine_forkchoiceUpdatedV3` (不带 `payloadAttributes`)以将新的区块设置为头。 |

非提议者节点跳过前三步，只执行`engine_newPayloadV4`，然后执行`engine_forkchoiceUpdatedV3`。

### 乐观同步

在重负载期间，CL 可以乐观地导入信标区块，而无需等待完整的 EVM 执行。如果 EL 稍后返回 `INVALID`，则 CL 会重新组织回 `latestValidHash`。最大乐观导入深度以 `SAFE_SLOTS_TO_IMPORT_OPTIMISTICALLY` 为界(默认值：**128 时隙**，约 25.6 分钟)，可通过 `--safe-slots-to-import-optimistically` 进行配置。这可以防止“分叉选择中毒”攻击，其中恶意对等节点在链尖端提供结构有效但计算无效的区块。

## 错误处理

### JSON-RPC 错误代码

|代码|名称 |触发|
|------|------|---------|
| `-32700` 至 `-32603` |标准 JSON-RPC |解析错误、无效请求、未找到方法、无效参数、内部错误 |
| `-32000` |服务器错误 |通用 EL 故障；响应**必须**包含带有诊断上下文的 `data.err` 字符串(例如 `"LevelDB read failure"`) |
| `-38001` |未知载荷 | `engine_getPayload` 使用没有活动构建过程或已超时的 `payloadId` 进行调用 |
| `-38002` |无效的 forkchoice 状态 | `forkchoiceState` 哈希逻辑上不一致(例如 `safeBlockHash` 不是 `headBlockHash` 的祖先)
| `-38003` |无效的载荷属性 | `payloadAttributes` 字段在结构上无效或缺少 fork 所需字段 |
| `-38004` |要求太大 |数组参数超出硬编码内存限制 |
| `-38005` |不支持的分叉 | 载荷时间戳与 EL 的活动分叉不一致(例如在 Deneb 激活之前提交的 Deneb 格式载荷) |

### 失效模式

**`SYNCING`**：CL 重试 `engine_forkchoiceUpdatedV3`，直到 EL 赶上。请勿证明或以此区块为基础。

**`INVALID`**：CL 将区块和所有后代标记为无效，将分叉选择恢复为 `latestValidHash`。

**EL 无法访问**(端口关闭、JWT 不匹配、崩溃)：CL 无法提议或验证。 验证者在连接恢复之前错过所有职责。

## 未来的升级

### Fusaka(Fulu/Osaka，2025 年 12 月 3 日，epoch 411392)

Fusaka 升级涵盖 13 个 EIP，涵盖 DA 扩展、执行性能和协议清理。关键引擎 API 影响：

**数据可用性:**
- **EIP-7594 (PeerDAS)**：节点采样小列子集，而不是下载完整的 blob，从而实现安全的 blob 吞吐量增加。 `engine_getPayloadBodiesByHashV2` 和 `engine_getPayloadBodiesByRangeV2` 已更新以支持 PeerDAS 单元证明结构。
- **EIP-7918**：blob 基本费用下限与执行基本费用成比例，防止 blob 费用在低需求期间暴跌至 1 wei。
- **EIP-7892(BPO 分叉)**：仅调整 blob 参数的分叉允许 blob 计数在 Fusaka 后通过轻量级网络调整进行扩展，而无需触发完整的硬分叉。 BPO1 和 BPO2 在 Fusaka 之后不久激活，将 blob 目标分别提高到 10/15 和 14/21。

**执行性能：**
- **EIP-7935**：默认区块 gas 限制提高到 **60M** (在客户端之间协调，不是共识规则)。
- **EIP-7825**：交易 gas 限制上限为 **2^24 = 16,777,216 gas**。任何一个交易都不能占据整个区块。
- **EIP-7934**：RLP 执行区块大小上限为 **8 MiB** (`MAX_RLP_BLOCK_SIZE = 10 MiB - 2 MiB safety margin`)。 CL 八卦协议已经将区块丢弃超过 10 MiB； 2 MiB 余量为信标区块保留余量，使有效 EL 载荷限制为 8 MiB。
- **EIP-7883**：MODEXP 预编译重新定价。最低 gas 从 200 提高到 **500**，一般成本公式 **三倍**，大指数乘数从 8 提高到 **16**。
- **EIP-7823**：MODEXP 输入的每个输入(底数、指数、模数)的上限为 **8192 位(1024 字节)**。所有历史现实世界的使用都低于此限制。
- **EIP-7917**：确定性提议者前瞻。 提议者时间表提前已知一个完整的 epoch，从而改善 MEV 和 PBS 的协调。

### Glamsterdam(后 Fusaka)

**EIP-7732 (ePBS)** 是拟议的 Glamsterdam CL 头条新闻。它将 PBS 从协议外 MEV-Boost sidecar 移至共识层，消除了对可信中继的依赖。引擎 API 时隙生命周期发生重大变化：提议者在 t=3s 时仅接收签名的 **构建者承诺** 并在没有执行载荷的情况下广播它；然后，构建者在 t=4s 证明截止日期后向网络显示完整的 `ExecutionPayloadEnvelope`。这可以防止提议者窃取构建者的 MEV，同时保留证明能力。新引擎 API 方法与完整的 EVM 执行分开处理承诺验证。

**EIP-7928(区块级访问列表)** 是拟议的 Glamsterdam EL 头条新闻。每个区块将包含执行期间访问的所有地址和存储槽的显式映射，从而能够：
- EVM 执行前并行预取状态
- 跨 CPU 核心并行执行不冲突的交易
- 无状态和 ZK 客户端的无执行状态验证

引擎 API 将通过执行载荷来验证这一点，在内部计算访问列表，并根据载荷标头中的 `blockAccessList` 进行验证。不匹配将返回 `INVALID`。两个 EIP 都是提案草案。

## 资源

- [引擎 API 规范(执行 API)](https://github.com/ethereum/execution-apis/tree/main/src/engine)
- [EIP-3675：升级到权益证明](https://eips.ethereum.org/EIPS/eip-3675)
- [EIP-7685：通用 EL 请求](https://eips.ethereum.org/EIPS/eip-7685)
- [EIP-7002：执行层可触发提现](https://eips.ethereum.org/EIPS/eip-7002)
- [EIP-7607：硬分叉元 - Fusaka](https://eips.ethereum.org/EIPS/eip-7607)
- [EIP-7732：供奉提议者-构建者分离](https://eips.ethereum.org/EIPS/eip-7732)
- [EIP-7928：区块级别访问列表](https://eips.ethereum.org/EIPS/eip-7928)
- [Engine API视觉指南](https://hackmd.io/@danielrachi/engine_api)
