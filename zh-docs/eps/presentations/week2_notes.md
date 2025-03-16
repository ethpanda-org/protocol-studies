# 第 2 周讲座

## 概览

### 区块验证

```go
func stf(parent types.Block, block types.Block, state state.StateDB) (state.StateDB, error) {
    if err := core.VerifyHeaders(parent, block); err != nil {
            // 头部验证错误
            return nil, err
    }
    for , tx := range block.Transactions() {
        res, err := vm.Run(block.Header(), tx, state)
        if err != nil {
                // 交易无效，区块无效
                return nil, err
        }
        state = res
    }
    return state, nil
}

func newPayload(execPayload engine.ExecutionPayload) bool {
    if , err := stf(..); err != nil {
        return false
    }
    return true
}
```

[代码来源](https://github.com/ethereum/go-ethereum/blob/63aaac81007ad46b208570c17cae78b7f60931d4/consensus/beacon/consensus.go#L229C23-L229C35)

### 区块构建

```go
func build(env Environment, pool txpool.Pool, state state.StateDB) (types.Block, state.StateDB, error) {
    var (
        gasUsed = 0
        txs []types.Transactions
    )
    for ; gasUsed < env.GasLimit || !pool.Empty(); {
        tx := pool.Pop()
        res, gas, err := vm.Run(env, tx, state)
        if err != nil {
            // 交易无效
            continue
        }
        gasUsed += gas
        txs = append(txs, tx)
        state = res
    }
    return core.Finalize(env, txs, state)
}
```

### 状态转换函数

* 代码解析：go-ethereum

### EVM（以太坊虚拟机）

* 算术操作
* 位运算
* 运行环境
* 控制流
* 栈操作
    * push, pop, swap
* 系统调用
    * call, create, return, sstore
* 内存操作
    * mload, mstore, mstore8

### P2P 网络

* 执行层运行在 devp2p 之上
* devp2p 的子协议包括 eth/68、eth/69、snap、whisper、les、wit
* eth/1 -> eth/2 -> eth/6.1 -> eth/6.2

#### 主要职责

* 历史数据
    * GetBlockHeader
    * GetBlockBodies
    * GetReceipts
* 未确认交易
    * Transactions
    * NewPooledTransactionHashes
    * GetPooledTransactions
* 状态数据
    * snap

```
    R1      ->
   / \
  x   x
 / \ / \
a  b c  d

    R200      ->
   / \
  1   2
 / \ / \
r  a c  d

^^^ 连续状态检索

R200

a b c d
```

#### “修复阶段”

* 获取 R200 -> (1, 2)
* 获取 1 -> (r, a)

```
r a c d
```

* 获取 2 -> (c, d)

```
“修复完成”
```

#### 启动 Snap 同步

* 以弱主观性检查点启动 -> 区块哈希
* 获取与哈希关联的区块
* 针对该区块的状态启动 Snap 同步

