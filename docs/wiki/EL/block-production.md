# 区块构建、处理并应用交易来声明：

## 简介

区块构建是 Ethereum 区块链功能的一项关键任务，涉及确定验证者在提出区块之前如何获取区块的各种过程。 Ethereum 网络由运行互联共识的节点(CL)和执行客户端(EL)组成。两者对于网络参与和在每个时隙生成区块都是必不可少的。虽然执行客户端 (EL) 有许多重要的功能，您可以在“el-architecture”中研究更多关于它的信息，但这里的一个关键焦点是它在构建区块供 CL 使用时的作用。

当在时隙期间选择验证者来提议区块时，它会查找由 CL 生成的区块。重要的是，验证者并不限于仅从其自己的 EL 广播区块。还可以广播外部构建者产生的区块；详情请参阅[PBS](https://ethereum.org/en/roadmap/pbs/)。本文专门探讨了区块如何由 EL 生成，以及有助于其成功生成和交易执行的元素。

## 载荷构建例程

当 Consensus Layer 通过引擎 API 的分叉选择更新端点指示 Execution Layer 客户端执行此操作时，将创建区块，然后该端点通过载荷构建例程。

注意：构建的载荷的费用接收者可能与载荷属性的建议费用接收者有所不同：

<img src="images/el-architecture/payload-building-routine.png" width="1000"/>

节点使用 gossip 协议通过点对点网络广播交易。这些交易根据特定标准进行验证(例如，检查随机数正确性、足够的余额和正确的签名)，并存储在内存池中，等待包含在区块中。

每个时隙都有一个指定的区块提议者，由 Consensus Layer 通过伪随机过程选择。当验证者被选为时隙的 区块提议者时，其共识客户端通过执行引擎的分叉选择更新方法启动区块构建，该方法为构建分叉选择提供了必要的上下文区块。

我们可以简化和模拟构建区块的过程，尽管这种方法特定于 geth 中使用的 Go 类型。然而，这些概念通常可以应用于不同的客户端。

```go
func build(env environment, pool txpool.Pool, state state.StateDB) (types.Block, state.StateDB, error) { //1
    var (
        gasUsed = 0
        txs []types.Transactions
    ) //2

  for ; gasUsed < 30_000_000 || !pool.Empty(); { //3
      transaction := pool.Pop() //4
      res, gas, err := vm.Run(env, transaction, state) //5
      if err != nil { // 6
          // transaction invalid
          continue
      }
      gasUsed += gas // 7
      transactions = append(transactions, transaction)
  }
  return core.Finalize(env, transactions, state) //8
}
```

1. 我们获取环境，其中包含所有必要的信息(类似于之前的标头)，包括时间戳、区块编号、前面的区块、基本费用以及需要在区块中发生的所有提款。本质上，来自作为中央决策实体的 Consensus Layer 的信息决定了区块应该构建的上下文。接下来，我们获取交易池，它是交易的集合。为简单起见，我们假设这些交易根据其值按升序排列。考虑到在网络中观察到的交易，这种安排有助于我们为执行客户端构建最有利可图的区块。此外，我们还考虑一个状态 DB，代表执行所有这些交易的状态。
   - 我们返回一个区块，一个状态 DB，它已经累积了区块中的所有交易，并且可能是一个错误。
2. 在构建内部，我们跟踪使用的 gas，因为我们只能使用有限数量的 gas。并且，还将所有要进入区块的 交易存储起来。
3. 我们继续添加交易，直到池为空或消耗的 gas 数量大于 gas 限制，为了简单起见，在本示例中该限制固定为 3000 万(大约是主网当前的 gas 限制)。
4. 为了获得交易，我们必须查询交易池，假设该池维护交易的有序列表，确保我们始终收到下一个最有价值的交易。
5. 交易在 EVM 中执行，假设运行需要一个区块和环境都满足的接口。我们提供环境交易和状态作为输入。这将在环境定义的上下文中执行交易，并向我们提供更新的状态，其中包括累积的交易。
6. 如果交易执行不成功(表明运行期间发生错误)，我们只需继续执行即可，无需中断。这表明交易无效，并且由于区块中仍然存在未使用的 gas，因此我们不希望立即生成错误。这是因为区块中尚未发生错误。但是，交易很可能是无效的，因为它在执行过程中做了一些不好的事情，或者因为交易池稍微过时了。在这种情况下，我们允许自己继续并尝试将池中的下一个交易放入此区块中。
7. 一旦我们验证运行交易没有错误，我们将交易添加到交易列表中，并将运行返回的 gas 添加到使用的 gas 中。例如，如果第一个交易是一个简单的传输，花费 21,000 gas，我们使用的 gas 将从 0 到 21,000，我们将继续执行此过程步骤 3-7，直到满足步骤 3 的条件。
8. 我们通过获取交易和相关区块信息集来生成完全组装的区块来完成转换。这样做的目的是为了最后进行一定的计算。由于标头包含交易根、收据根和提款根，因此必须通过 Merkleization 列表来计算这些值并将其添加到区块的标头中。
   - 我们返回区块，声明 DB 和我们的错误。

## 代码演练

### Geth

以下示例使用 Geth 代码库来解释执行客户端如何构建区块。

 1. 首先，当选择验证者作为区块构建者时，它会通过 EL 上的 Engine API 调用 `engine_forkchoiceUpdatedV2` 函数。此处，EL 启动区块构建过程。  
    - https://github.com/ethereum/go-ethereum/blob/0a2f33946b95989e8ce36e72a88138adceab6a23/eth/catalyst/api.go#L398 
 2. 区块构建和交易执行的大部分核心逻辑驻留在 Geth 的 `miner` 模块中。 `buildPayload` 函数最初创建一个空的区块，因此节点不会错过时隙并且有一些建议。该函数实现还启动一个 go 例程进程，其工作是潜在地填充我们留空的区块，然后同时用填充的交易更新它。
    - https://github.com/ethereum/go-ethereum/blob/0a2f33946b95989e8ce36e72a88138adceab6a23/miner/payload_building.go#L180 - https://github.com/ethereum/go-ethereum/blob/0a2f33946b95989e8ce36e72a88138adceab6a23/miner/payload_building.go#L204
 3. 在 `buildPayload` 函数中，go 例程正在等待多个通信操作“情况”，在第一种情况下，它使用参数调用 `getSealingBlock`，该参数明确指定区块不应为空。查看 `fullParams` 变量，即 `noTxs:False`。
 4. 在 `getSealingBlock` 的定义中，请求在 `getWorkCh` 通道上发送。正在监听该通道，以便从中检索数据并生成工作。 
    - [https://github.com/ethereum/goethereum/blob/master/miner/worker.go#L1222](https://github.com/ethereum/go-ethereum/blob/0a2f33946b95989e8ce36e72a88138adceab6a23/miner/worker.go#L1222)
 5. 此 `getWorkCh` 通道正在同一文件中的 `mainLoop` 函数内部侦听。来自 `getWorkCh` 通道的数据随后被发送到 `w.generateWork`。
    - https://github.com/ethereum/go-ethereum/blob/master/miner/worker.go#L537
 6. `generateWork` 函数是交易在 区块中填充的地方。
    - https://github.com/ethereum/go-ethereum/blob/0a2f33946b95989e8ce36e72a88138adceab6a23/miner/worker.go#L1094
 7. `w.fillTransactions` 函数从内存池中检索所有待处理的交易并填充区块。这包括交易的所有类型，其中包括 blob。
    - https://github.com/ethereum/go-ethereum/blob/master/miner/worker.go#L1024
 8. 交易根据其费用填充排序，然后传递给 `commitTransactions` 函数。  
https://github.com/ethereum/go-ethereum/blob/master/miner/worker.go#L1072
 9. `commitTransactions` 函数检查每个交易是否有足够的 gas 剩余，然后提交该特定的交易。此外，按照 eip-4844 中的规定，每个区块允许一定数量的 blob。  
     - https://github.com/ethereum/go-ethereum/blob/master/miner/worker.go#L888 
     - https://github.com/ethereum/EIPs/blob/master/EIPS/eip-4844.md
 10. 如果您查看 `commitTransaction` 函数，它会返回调用 `w.applyTransaction` 函数。
    - https://github.com/ethereum/go-ethereum/blob/master/miner/worker.go#L760C18-L760C36 
 11. 然后 `applyTransaction` 函数继续调用核心包并调用 `core.ApplyTransaction`，后者根据本地 EL 状态执行所有交易。  
    - https://github.com/ethereum/go-ethereum/blob/master/miner/worker.go#L794.
 12. `ApplyTransaction` 函数针对本地 EL 状态运行交易并执行所有状态更改。它创建 EVM 上下文和环境来执行 EVM 中的交易。合约调用也都发生在这里。如果一切顺利，状态转换成功。     
     - https://github.com/ethereum/go-ethereum/blob/master/core/state_processor.go#L161
13. 交易也可能失败。如果交易失败，则不会应用于状态转换。它可能会由于链上原因而失败，例如 gas 用完、合约调用失败等
14. 从此以后，所有的交易都被一一执行。然后将交易捆绑到区块中。  
15. CL 然后通过 Engine API 请求 EL 来让这个交易填充一个载荷 `getPayload`。 EL 将这个载荷返回到 CL，后者将这个载荷放入 Beacon block 并传播它。
 
## 资源
1. [GETH 代码库](https://github.com/ethereum/go-ethereum)
2. [引擎 API：视觉指南](https://hackmd.io/@danielrachi/engine_api)
