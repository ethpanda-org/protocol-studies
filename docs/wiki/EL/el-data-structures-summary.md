# 以太坊执行层数据结构

# 区块标头

|名称 |描述| EIP / 叉 |
|------- | ------- | ------- |
| parent_hash |上一个区块的 哈希 | |
| ommers_hash | 哈希 ommers(又名叔叔)列表 | |
|受益人|获得区块奖励的矿工地址或验证者 | |
| state_root |所有txs执行后世界状态 Trie的根哈希 | |
| transactions_root |此区块的 交易 Trie 的根哈希 | |
| receipts_root |此区块的收据 Trie 的根哈希 | |
| logs_bloom |布隆过滤器总结了此区块 | 中收据的所有日志|
|难度| PoW：区块的难度。 PoS：未使用。 | |
|数量 | 区块编号(链中的高度)| |
| gas_limit |此区块中允许的 gas 最大数量 | |
| gas_used |此区块中所有交易使用的 gas 总数 | |
|时间戳| 区块被提议时的 Unix 时间戳 | |
| extra_data |用于附加数据的任意 32 字节字段(例如 miner ID)| |
| prev_randao | POW，被称为`mix_hash`，用于nonce验证。 PoS：验证者的随机种子 | [EIP-4399](https://eips.ethereum.org/EIPS/eip-4399) / 巴黎 |
|随机数 | PoW：挖矿难题的解决方案。 PoS：未使用 | |
| base_fee_per_gas |每 gas 的最低基本费用 | [EIP-1559](https://eips.ethereum.org/EIPS/eip-1559) / 伦敦 |
| withdrawals_root |提款列表根Trie | [EIP-4895](https://eips.ethereum.org/EIPS/eip-4895) / 上海 |
| base_fee_per_blob_gas |每 gas 的最低基本费用。 | [EIP-4844](https://eips.ethereum.org/EIPS/eip-4844) / Dencun |
| blob_gas_used | 区块中使用的总计 blob gas。 | [EIP-4844](https://eips.ethereum.org/EIPS/eip-4844) / Dencun |
| excess_blob_gas |未使用的 blob gas 的滚动计数器。 | [EIP-4844](https://eips.ethereum.org/EIPS/eip-4844) / Dencun |
| parent_beacon_block_root | SSZ 父信标区块的根。 | [EIP-4788](https://eips.ethereum.org/EIPS/eip-4788) / Dencun |
| request_root | EL生成的跨层请求的根哈希。 | [EIP-7685](https://eips.ethereum.org/EIPS/eip-7685) / 佩克特拉 | 

# 区块正文

|名称 |描述| EIP/叉 |
|------- | ------- | ------- |
| 交易[] | 交易的列表在区块中执行，按顺序。 交易 | 可以有不同类型|
|奥默斯[] |在 PoS 中：空。在 PoW 中：ommers(叔叔)列表 | |
|提款[] | 验证者的 ETH 提现列表。 | [EIP-4895](https://eips.ethereum.org/EIPS/eip-4895) / 上海 |
|请求[] |执行产生的跨层请求列表。 | [EIP-7685](https://eips.ethereum.org/EIPS/eip-7685) / 佩克特拉 |

# 状态Trie
Trie 植根于区块标头的 `state_root`。
每个叶子节点编码为 `keccak(address1) -> RLP(nonce, balance, storage_root, code_hash)`

|名称 |描述|
|------- | ------- |
|随机数 |发送(EOA)或创建(合约)的交易数量 |
|平衡| wei 账户的 Eth 余额 |
| storage_root |帐户存储根 Trie |
| code_hash | keccak256 哈希合约账户代码。空哈希或 EOA 的授权指示符 |

# 收据Trie
Trie 植根于区块标头的 `receipts_root`

收据包含以下字段：
|名称 |描述| EIP / 叉 |
|------- | ------- | ------- |
|类型 |收据类型字节前缀。 | [EIP-2718](https://eips.ethereum.org/EIPS/eip-2718) / 柏林 | 
|状态 |表示交易状态。 1 = 成功，0 = 失败。前拜占庭被称为 `state_root`。 | [EIP-658](https://eips.ethereum.org/EIPS/eip-658) / 拜占庭 |
| cumulative_gas_used | gas 已使用的总数量(包括区块中的此交易)| |
| logs_bloom | 256 字节的布隆过滤器总结了来自该交易的所有日志。 | |
|日志[] |执行期间发出的事件日志列表 | |

`logs[]` 字段中的每个项目包含以下项目：
|名称 |描述|
|------- | ------- |
|地址 |发出日志的合约地址 |
|主题[] |索引主题数组(包括事件签名)|
|数据| ABI 编码的非索引事件数据 | 

# 提款 Trie
Trie 植根于区块标头的 `withdrawals_root`

提款字段为：
|名称 |描述|
|------- | ------- |
|索引 |单调递增指数 |
| validator_index | 验证者提现索引 |
|地址 |提款接收地址 |
|金额 |桂量 |

# 交易 Trie
Trie 植根于区块标头的 `transactions_root`。 交易可以有多种类型。

## 旧版交易(类型 0x00)
|名称 |描述 | EIP/叉|
|------- | ------- | ------- |
|随机数 |在此之前发送者发送的交易数量。 | |
| gas_price |旧定价模型：gas 每单位价格。 | |
| gas_limit |该 tx 允许消耗的最大 gas。 | |
|至 |收件人地址，或创建合同时为空 | |
|价值|以 wei 为单位转移的以太币数量 |
|数据|用于合约交互的 Calldata 或用于创建的 init 代码 |
| chain_id |链ID | [EIP-155](https://eips.ethereum.org/EIPS/eip-155) / 伪龙 |
| v、r、s | 签名组件(用于恢复发件人地址)| | 

## 访问列表交易(类型 0x01)
字段与类型 0 相同，但添加了 `access_list`。
编码为 `RLP(chain_id, nonce, gas_price, gas_limit, to, value, data, access_list, v, r, s)`

|名称 |描述| EIP/叉 |
|------- | ------- | ------- |
| access_list |要访问的地址/存储密钥列表。 | [EIP-2930](https://eips.ethereum.org/EIPS/eip-2930) / 柏林 |

## 动态费用交易(类型 0x02) 
编码为 `RLP(chain_id, nonce, max_priority_fee_per_gas, max_fee_per_gas, gas_limit, to, value, data, access_list, v, r, s)`，通过用新字段替换 `gas_price` 来扩展类型 1 交易。

|名称 |描述| EIP/叉 |
|------- | ------- | ------- |
| max_priority_fee | 验证者的提示可以激励其他交易之上的包容性。 | [EIP-1559](https://eips.ethereum.org/EIPS/eip-1559) / 伦敦 |
| max_fee_per_gas |每单位 gas 愿意支付的最高费用。 | [EIP-1559](https://eips.ethereum.org/EIPS/eip-1559) / 伦敦 |

## blob-携带交易(类型0x03) 
编码为 `RLP(chain_id, nonce, max_priority_fee_per_gas, max_fee_per_gas, gas_limit, to, value, data, access_list, max_fee_per_blob_gas, blob_versioned_hashes, v, r, s)`

与类型 2 交易相同，但新的 blob 相关字段除外。

|名称 |描述| EIP/叉|
|------- | ------- | ------- |
| max_fee_per_blob_gas |每 blob gas 单位愿意支付的最高费用。 | [EIP-4844](https://eips.ethereum.org/EIPS/eip-4844) / Dencun |
| blob_versioned_hashes |每个 blob 的哈希列表。 | [EIP-4844](https://eips.ethereum.org/EIPS/eip-4844) / Dencun |

## EOA 的设置代码交易(类型 0x04)
使用带有以下字段的新 `authorization_list` 扩展类型 3 交易。

|名称 |描述| EIP/叉|
|------- | ------- | ------- |
| chain_id |链ID | [EIP-7702](https://eips.ethereum.org/EIPS/eip-7702) / 布拉格 |
|地址 |委托地址智能合约 | [EIP-7702](https://eips.ethereum.org/EIPS/eip-7702) / 布拉格 |
|随机数 |在此之前发送者发送的交易数量。 | [EIP-7702](https://eips.ethereum.org/EIPS/eip-7702) / 布拉格 |
| y_parity，r，s |加密签名元素 | [EIP-7702](https://eips.ethereum.org/EIPS/eip-7702) / 布拉格 |

