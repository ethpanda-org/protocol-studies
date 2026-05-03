# 第 2 周讲座

## 概述

### 区块验证

去
func stf(父类型.区块，区块类型.区块，状态 state.StateDB)(状态.StateDB，错误){
    if err := core.VerifyHeaders(parent, 区块);错误！=零{
            // 检测到标头错误
            返回零，错误
    }
    对于 , tx := 范围区块.交易() {
        res, err := vm.Run(区块.Header(), tx, 状态)
        如果错误！= nil {
                // 交易无效，区块无效
                返回零，错误
        }
        状态=资源
    }
    返回状态，无
}

func newPayload(execPayload engine.ExecutionPayload) bool {
    如果，错误：= stf(..);错误！=零{
        返回错误
    }
    返回真
}

https://github.com/ethereum/go-ethereum/blob/63aaac81007ad46b208570c17cae78b7f60931d4/consensus/beacon/consensus.go#L229C23-L229C35

### 区块构建

去
func build(env 环境，池 txpool.Pool，状态 state.StateDB) (types. 区块，state.StateDB，错误) {
    变量(
        已用 gas = 0
        txs []types. 交易
    )
    为； GasUsed < env.GasLimit || !pool.Empty(); {
        tx := pool.Pop()
        res, gas, err := vm.Run(env, tx, state)
        如果错误！= nil {
            // 交易无效
            继续
        }
        已用 gas += gas
        txs = 追加(txs, tx)
        状态=资源
    }
    返回 core.Finalize(env, txs, state)
}

### 状态转换函数
* 演练 go-ethereum

### EVM

* 算术
* 按位
* 环境
* 控制流程
* 堆栈操作
    * 推入、弹出、交换
* 系统
    * 调用、创建、返回、存储
* 记忆
    * mload、mstore、mstore8

### 点对点

* Execution Layer 在 devp2p 上运行
* devp2p => 子功能 eth/68、eth/69、snap、whisper、les、wit
* eth/1 -> eth/2 -> eth/6.1 -> eth/6.2 

#### 责任

* 历史数据
    * 获取区块头
    * 获取块体
    * 获取收据
* 待处理交易
    * 交易
    * 新池交易哈希
    * 获取集合交易
* 状态
    快照

    R1-> 
   / \
  x x
 / \ / \ 
a b c d

    R200-> 
   / \
  1 2
 / \ / \ 
雷达

^^^ 连续状态检索

R200

a b c d


“愈合阶段”

* 得到 R200 -> (1, 2)
* 得到 1 -> (r, a)

雷达

* 得到 2 -> (c, d)

“治愈完成”


#### 开始快照

* 启动 Weak Subjectivity- 检查点 -> 区块哈希
* 获取与哈希关联的区块
* 针对区块的状态启动快照
