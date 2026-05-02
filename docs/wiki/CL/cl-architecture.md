# 共识层 (CL) 架构

> 许多区块链共识协议都是_forkful_。分叉链使用分叉选择规则，有时会进行重组。
>
> 以太坊的共识协议结合了两个独立的共识协议。 _LMD GHOST_本质上提供了活力。 _Casper FFG_提供最终确定性。他们一起被称为_Gasper_。
>
> 在_live_协议中，总会有好事发生。在_安全_协议中，不会发生任何不好的事情。任何实用的协议都不可能永远安全且永远有效。

## 分叉选择机制

如 [BFT](/wiki/CL/overview.md?id=byzantine-fault-tolerance-bft-and-byzantine-generals39-problem) 中所述，由于各种原因(例如网络延迟、中断、乱序消息或恶意行为)，网络中的节点可能对网络状态有不同的看法。最终，我们希望每个诚实的节点都同意相同的线性历史以及对系统状态的共同看法。该协议的分叉选择规则有助于实现该协议。

#### 区块树
给定区块树和基于节点网络局部视图的决策标准，分叉选择规则旨在选择最有可能成为最终线性规范链的分支。当节点收敛于共同视图时，它选择最不可能被修剪的分支。

<a id="img_blocktree"></a>

<figure class="diagram" style="text-align:center; width:95%">

![Diagram for Block Tree](../../images/cl/blocktree.svg)

<figcaption>

_分叉选择规则从候选人中挑选了一个头颅区块。头部区块标识了一个唯一的线性区块链回到 Genesis 区块._

</figcaption>
</figure>

#### 分叉选择规则

分叉选择规则通过选择分支尖端的区块(称为头区块)来隐式选择分支。对于任何正确的节点，任何分叉选择的第一个规则是所选的区块必须根据协议规则有效，并且其所有祖先也必须有效。任何无效的区块都会被忽略，基于无效区块构建的区块也无效。

有几个不同分叉选择规则的示例：

- **工作量证明**：在以太坊和比特币中，使用“最重链规则”(有时称为“最长链”，尽管不严格准确)。头区块是完成最多累积“工作”的链的尖端。
> 请注意，与普遍看法相反，以太坊的工作量证明协议[未在其分叉选择中使用](https://ethereum.stackexchange.com/questions/38121/why-did-ethereum-abandon-the-ghost-protocol/50693#50693)任何形式的 GHOST。这种误解非常持久，可能是由于[以太坊白皮书](https://ethereum.org/en/whitepaper/#modified-ghost-implementation)。最终，当 Vitalik 被问及此事时，他证实，虽然 GHOST 是在 PoW 下计划的，但由于担心一些未指明的攻击而从未实施。最重链式法则更简单且经过充分测试。效果很好。
- **Casper FFG(权益证明)**：在以太坊的 PoS Casper FFG 协议中，分叉选择规则是“遵循包含**最大高度**的合理检查点的链”并且永远不会恢复最终确定的状态区块。
- **LMD GHOST(权益证明)**：在以太坊的PoS LMD GHOST协议中，分叉选择规则是采用“贪婪最重观察子树”。它涉及计算验证者对 区块及其后代区块的累积投票。它还应用与 Casper FFG 相同的规则。

每个分叉选择规则都会为区块分配一个数字分数。获胜的区块，或头区块，得分最高。目标是所有正确的节点，当他们看到某个区块时，都会同意它是头并遵循它的分支。这样，所有正确的节点最终将就一条可追溯到创世纪的规范链达成一致。

#### 重组和回归

当节点收到新投票(以及权益证明中区块的新投票)时，它会使用此新信息重新评估分叉选择规则。通常，新的区块将是当前头区块的子级，并且它将成为新的头区块。

然而，有时，新的区块可能是区块树中不同区块的后代。如果节点没有新区块的父区块，它将向其对等节点以及任何其他缺失的区块询问。

在更新后的区块树上运行分叉选择规则可能会显示新头区块与之前的头区块位于不同的分支上。当这种情况发生时，节点必须执行reorg(重组)。这意味着它将删除(恢复)之前包含的区块并在新头的分支上采用区块。

例如，如果节点的链中有区块 $A, B, D, E,$ 和 $F$，并且它将 $F$ 视为头区块，则它知道区块 $C$但它并没有出现在它的链条视图中； it is on a side branch.

<a id="img_reorg0"></a>

<figure class="diagram" style="text-align:center">

![Diagram for Reorg-0](../../images/cl/reorg-0.svg)

<figcaption>

_此时，节点认为区块 $F$是最好的头，因此它的链是区块 $[A \leftarrow B \leftarrow D \leftarrow E \leftarrow F]$_

</figcaption>
</figure>

当节点后来收到区块 $G$(它是在区块 $C$ 上构建的，而不是在其当前头区块 $F$ 上构建时)，它必须决定 $G$ 是否应该 be the new head.例如，如果分叉选择规则说 $G$ 是更好的头区块，则节点将恢复区块 $D, E,$ 和 $F$。它将把它们从链中删除，就好像它们从未被收到一样，并返回到区块 $B$ 之后的状态。

然后，节点会将区块、$C$和$G$添加到其链中并对其进行处理。重组后，节点的链将是 $A, B, C,$ 和 $G$。

<a id="img_reorg1"></a>

<figure class="diagram" style="text-align:center">

![Diagram for Reorg-1](../../images/cl/reorg-1.svg)

<figcaption>

_现在节点认为区块 $G$是最好的头，因此它的链必须更改为区块 $[A \leftarrow B \leftarrow C \leftarrow G]$_

</figcaption>
</figure>

稍后，也许会出现一个区块 $H$，它是在 $F$ 上构建的，而分叉选择规则表示 $H$ 应该是新的头，节点将再次重组，恢复为区块 $B$ 并在 $H$ 的分支上重播区块。

由于网络延迟，一两个区块的短重组很常见。除非链受到攻击或者分叉选择规则或其实现中存在错误，否则较长的重组应该很少见。

### Safety and Liveness

在共识机制中，两个关键概念是安全性和活跃性。

**安全**意味着“不会发生任何不好的事情”，例如防止双重支出或最终确定冲突的检查点。它确保一致性，这意味着所有诚实的节点应该始终就区块链的状态达成一致。

**活跃性**意味着“好事最终会发生”，确保区块链始终可以添加新的区块并且永远不会陷入僵局。

**CAP 定理**指出，没有一个分布式系统可以同时提供一致性、可用性和分区容错性。这意味着我们无法设计一个在通信不可靠的所有情况下既安全又活跃的系统。

#### 以太坊 Prioritizes Liveness

以太坊的共识协议旨在在良好的网络条件下提供安全性和活跃性。但是，它会在网络出现问题时优先考虑活性。在网络分区中，每一侧的节点将继续生成区块，但不会实现最终确定性 (安全属性)。如果分裂持续下去，各方可能会最终确定不同的历史，从而导致两条不可调和的独立链条。

因此，虽然以太坊努力实现安全性和活跃性，但它倾向于确保网络保持活跃并继续处理交易，即使以严重网络中断期间潜在的安全问题为代价。

## 机器里的幽灵

以太坊的权益证明共识协议结合了两个独立的协议：[LMD GHOST](/wiki/CL/gasper?id=lmd-ghost.md) 和 [Casper FFG](/wiki/CL/gasper?id=casper-ffg.md)。它们共同形成了称为“Gasper”的共识协议。有关这两种协议以及它们如何组合工作的详细信息将在下一节 [Gasper](/wiki/CL/gasper) 中介绍。

Gasper 旨在结合 LMD GHOST 和 Casper FFG 的优势。 LMD GHOST 提供活跃性，通过定期生成新的区块来确保链保持运行。然而，它很容易出现分叉，而且在形式上并不安全。另一方面，Casper FFG 通过定期完成链来提供安全性，保护其免受长时间的恢复。

本质上，LMD GHOST 保持链向前发展，而 Casper FFG 通过最终确定区块来确保稳定性。这种组合允许以太坊优先考虑活跃度，这意味着即使 Casper FFG 无法最终确定区块，链也会继续增长。尽管这种组合机制并不总是完美的并且具有一定的复杂性，但它是一种实用的工程解决方案，在以太坊的实践中效果良好。

## 建筑

以太坊是 节点的去中心化网络，通过对等节点到 对等节点连接进行通信。这些连接是由运行以太坊的 客户端软件的计算机形成的。

<figure class="diagram" style="text-align:center">

![Diagram for Network](../../images/cl/network.png)

<figcaption>

_节点不需要运行验证者客户端 (绿色节点)即可成为网络的一部分，但是要参与共识，需要质押 32 ETH 并运行验证者客户端 ._

</figcaption>
</figure>

### 共识层的组件

- **Beacon 节点**：Beacon 节点使用客户端软件来协调以太坊的权益证明共识。示例包括 Prysm、Teku、Lighthouse 和 Nimbus。信标节点与其他信标节点、本地执行节点以及可选的本地验证者进行通信。

- **验证者**：验证者客户端是一个允许人们在以太坊的 共识层中质押 32 个 ETH 的软件。 验证者在权益证明系统中提出区块，取代了工作量证明矿工。 验证者仅与本地信标节点通信，该信标指示它们并将其工作广播到网络。

托管实际应用程序的主要以太坊网络称为以太坊主网。 以太坊主网是 以太坊的实时生产实例，它铸造和管理真实的以太坊 (ETH) 并持有真实的货币价值。

还有测试网络为开发人员、节点运行者创建和管理测试以太坊，以及验证者，以便在主网上使用真正的 ETH 之前测试新功能。每个以太坊网​​络都有两层：执行层 (EL) 和共识层 (CL)。每个以太坊节点都包含两层软件：执行层客户端软件(如 Nethermind、Besu、Geth 和 Erigon)和共识层客户端软件(如 Prysm、Teku、Lighthouse、Nimbus 和 Lodestar)。

<a id="img_node-layers"></a>

<figure class="diagram" style="width: 80%;text-align:center">

![Diagram for CL](../../images/cl/cl.png)

</figure>

**共识层** 负责维护共识链(信标链)并处理从其他对等节点收到的共识区块(信标区块)和证明。 **共识客户端** 参与一个单独的 [点对点网络](/wiki/CL/cl-networking.md)，其规范与执行客户端不同。他们需要参与区块八卦才能从对等节点接收新的区块并在轮到他们求婚时广播区块。

EL 和 CL 客户端都是并行运行的，需要连接进行通信。共识客户端向执行客户端提供指令，执行客户端将 交易捆绑包传递给共识客户端以包含在 Beacon 区块中。通信是通过 **Engine API** 使用本地 RPC 连接来实现的。他们共享一个 [ENR](/wiki/CL/cl-networking?id=enr-ethereum-node-records)，每个客户端 (eth1 密钥和 eth2 密钥)都有单独的密钥。

### 控制流程

**当共识客户端不是区块生产者时：**
1. 通过区块 gossip 协议接收区块。
2. 预先验证区块。
3. 将区块中的交易作为执行载荷发送到执行层。
4. 执行层执行交易并验证区块状态。
5. 执行层将验证数据发送回共识层。
6. 共识层将区块添加到其区块链中并对其进行证明，通过网络广播证明。

**当共识客户端是区块生产者时：**
1. 收到成为下一个区块生产人的通知。
2. 在执行客户端时调用create 区块方法。
3. 执行层访问交易内存池。
4. 执行客户端将交易捆绑到区块中，执行它们，并生成区块哈希。
5. 共识客户端将 交易和 区块哈希添加到信标区块中。
6. 共识客户端通过区块 gossip 协议广播区块。
7. 其他客户端验证区块并证明它。
8. 一旦通过足够的验证者证明，区块就会被添加到链的头部，进行合理化并最终确定。


### 状态转换

状态转换函数在 区块链中是必需的。每个节点都保持着反映其世界观的状态。

节点通过使用“状态转换函数”按顺序应用区块来更新其状态。这个函数是“纯”的，意味着它的输出仅取决于输入并且没有副作用。因此，如果每个节点以相同的状态(创世状态)开始并应用相同的区块，那么它们都以相同的状态结束。如果他们不这样做，就会出现共识失败。

如果 $S$ 是信标状态并且 $B$ 是信标区块，则状态转换函数 $f$ 是：

$$S' \equiv f(S, B)$$

这里，$S$ 是前状态，$S'$ 是后状态。使用每个新的区块迭代函数 $f$ 以更新状态。

### 信标链状态转换

与区块驱动的工作量证明不同，信标链是 时隙驱动的。状态更新取决于时隙进度，无论区块是否存在。

信标链的 状态转换函数包括：

1. **每时隙转换**：$S' \equiv f_s(S)$
2. **每区块转换**：$S' \equiv f_b(S, B)$
3. **每 epoch 转换**：$S' \equiv f_e(S)$

每个函数都会在特定时间更新链，如信标链规范中所定义。

### 有效期条件

来自预状态和签名的区块的后状态是 `state_transition(state, signed_block)`。导致未处理异常(例如断言失败或超出范围访问)或 uint64 上溢/下溢的转换无效。

### 信标链状态转换函数

与前状态`state`和带符号的区块 `signed_block`相对应的后状态被定义为`state_transition(state, signed_block)`。触发未处理异常(例如失败的 `assert` 或超出范围的列表访问)的状态转换被视为无效。导致 `uint64` 上溢或下溢的状态转换也被视为无效。

```python
def state_transition(state: BeaconState, signed_block: SignedBeaconBlock, validate_result: bool=True) -> None:
    block = signed_block.message
    # Process slots (including those with no blocks) since block
    process_slots(state, block.slot)
    # Verify signature
    if validate_result:
        assert verify_block_signature(state, signed_block)
    # Process block
    process_block(state, block)
    # Verify state root
    if validate_result:
        assert block.state_root == hash_tree_root(state)
```

```python
def verify_block_signature(state: BeaconState, signed_block: SignedBeaconBlock) -> bool:
    proposer = state.validators[signed_block.message.proposer_index]
    signing_root = compute_signing_root(signed_block.message, get_domain(state, DOMAIN_BEACON_PROPOSER))
    return bls.Verify(proposer.pubkey, signing_root, signed_block.signature)
```

```python
def process_slots(state: BeaconState, slot: Slot) -> None:
    assert state.slot < slot
    while state.slot < slot:
        process_slot(state)
        # Process epoch on the start slot of the next epoch
        if (state.slot + 1) % SLOTS_PER_EPOCH == 0:
            process_epoch(state)
        state.slot = Slot(state.slot + 1)
```


## 资源

- Vitalik Buterin，[“参数化 Casper：去中心化/最终确定性时间/开销权衡”](https://medium.com/@VitalikButerin/parametrizing-casper-the-decentralization-finality-time-overhead-tradeoff-3f2011672735)
- [Engine API 规范](https://hackmd.io/@n0ble/consensus_api_design_space)
- [Vitalik 带注释的以太坊 2.0 规范](https://notes.ethereum.org/@vbuterin/SkeyEI3xv)
- 以太坊，[“Eth2：带注释的规范”](https://github.com/ethereum/annotated-spec)
- Martin Kleppmann，[分布式系统](https://www.youtube.com/playlist?list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB)
- Leslie Lamport 等人，[拜占庭将军问题](https://lamport.azurewebsites.net/pubs/byz.pdf)
- 奥斯汀·格里菲斯，[拜占庭将军 - ETH.BUILD。](https://www.youtube.com/watch?v=c7yvOlwBPoQ)
- 迈克尔·斯普劳尔，[“以太坊内部”](https://www.youtube.com/watch?v=LviEOQD9e8c) 
- [Ben Edgington 的 Eth2 手册](https://eth2book.info/capella/part2/consensus/)
- [Lighthouse共识客户端架构](https://www.youtube.com/watch?v=pLHhTh_vGZ0)
