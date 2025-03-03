# 执行层规范

执行层最初在黄皮书中进行了规范，因为它涵盖了整个以太坊。最新的规范是 [EELS python 规范](https://ethereum.github.io/execution-specs/)。

> - [黄皮书，巴黎版本 705168a – 2024-03-04](https://ethereum.github.io/yellowpaper/paper.pdf) (注意：此版本已过时，未考虑合并后的更新)
> - [Python 执行层规范](https://ethereum.github.io/execution-specs/)
> - EIPs [查看仓库的自述文件](https://github.com/ethereum/execution-specs)

本页面提供了执行层规范的概述、架构以及 Pyspec 的背景信息。

## 状态转换函数

从 EELS 的角度来看，执行层专注于执行状态转换函数（STF）。该角色解决两个主要问题[¹]：

- 是否可以将区块追加到区块链的末尾？
- 状态将因此发生怎样的变化？

简要概述：
<img src="images/el-specs/stf_eels.png" width="800"/>

上图表示了黄皮书中的区块级状态转换函数。

$$
\begin{equation}
\sigma_{t+1} \equiv \Pi(\sigma_t, B)
\qquad (2)  \nonumber
\end{equation}
$$

在此公式中，每个符号分别代表与区块链状态转换相关的特定概念：

- $\sigma_{t+1}$ 代表应用当前区块后的 **区块链状态**，通常称为 "新状态"。
- $\Pi$ 表示 [区块级状态转换函数](https://github.com/ethereum/execution-specs/blob/0f9e4345b60d36c23fffaa69f70cf9cdb975f4ba/src/ethereum/shanghai/fork.py#L145)，它通过应用当前区块中包含的交易，将区块链从一个状态转换到下一个状态。
- $\sigma_t$ 代表添加当前区块之前的 **[区块链](https://github.com/ethereum/execution-specs/blob/0f9e4345b60d36c23fffaa69f70cf9cdb975f4ba/src/ethereum/shanghai/fork.py#L73)** 状态，也称为 "先前状态"。

- $B$ 表示正在发送给执行层处理的 **[当前区块](https://github.com/ethereum/execution-specs/blob/0f9e4345b60d36c23fffaa69f70cf9cdb975f4ba/src/ethereum/shanghai/fork_types.py#L217)**。

此外，理解 $\sigma$ 不应与 Python 规范中定义的 State 类混淆很关键。系统的状态不是存储在特定位置，而是通过应用状态收缩函数动态推导出来的。这突出了区块链状态转换的数学模型与软件规范中实现细节之间的概念性区别。

<img src="images/el-specs/state.png" width="800"/>

上图中的 ID 如黄皮书 (巴黎版本) 所示：

| Id.   | 公式编号 | 黄皮书                                                    | 注释                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| ----- | ------------ | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1     | 7            | $$TRIE(L_I^*(\sigma[a]_s)) \equiv \sigma[a]_s $$           | 在通过函数 $L_I((k,v)) \equiv (KEC(k), RLP(v))$ 映射每个节点后，这给出了帐户存储 Trie 的根，即右侧的 $\sigma[a]_s$。左侧的公式表示映射到帐户存储 $\sigma[a]_s$ 的基础键和值，左侧的 $\sigma[a]_s$ 和右侧的 $\sigma[a]_s$ 这两个对象是不同的，后者表示根哈希。 |
| 2     |              | 第 4 页，第 2 段                                              | 黄皮书中描述了帐户状态 $\sigma[a] $                                                                                                                                                                                                                                                                                                                                                                         |
| 3     | 10           | $$L_s(\sigma) \equiv \{p(a) : \sigma[a] \neq \empty \} $$ | 这是世界状态收缩函数，应用于所有被认为非空的帐户：                                                                                                                                                                                                                                                                                                                                                      |
| 4 & 5 | 36           | $$TRIE(L_s(\sigma)) = P(B_H)_{H_{stateRoot}} $$           | 此公式定义了父区块的状态根头部，作为通过 TRIE 函数得到的根，其中 $P(B_H)$ 是父区块。                                                                                                                                                                                                                                                                                                      |
| 6.    | 33b          | $$H_{stateRoot} \equiv TRIE(L_s(\Pi(\sigma, B))) $$       | 这给出了当前区块的状态根。                                                                                                                                                                                                                                                                                                                                                                                               |

代码文档中状态转换函数的指定流程包括以下步骤：

1. **检索头部信息**：获取链中最新添加区块的头部信息，称为父区块。
2. **超额 Blob Gas 验证**：计算父区块头的超额 Blob Gas，并确保它与当前区块头的 excess_blob_gas 参数相匹配。
3. **头部验证**：将当前区块的头部与父区块的头部进行比较和验证。
4. **Ommers 字段检查**：验证当前区块的 ommers 字段是否为空。注意："ommers" 是一个中性词，取代了之前使用的术语 "uncles（叔块）"。
5. **区块执行**：执行区块内的交易，并产生以下输出：
   - **消耗的 Gas**：执行区块内所有交易所消耗的 Gas 总量。
   - **Trie 根**：区块内所有交易和回执的 Trie 根。
   - **日志布隆过滤器**：区块内所有交易的日志布隆过滤器。
   - **状态**：执行所有交易后，按照 Python 执行规范所指定的状态。
6. **头部参数验证**：确认执行区块后返回的参数是否存在于区块头中。这包括将状态根与区块头中的 `state_root` 字段进行比较。
7. **区块添加**：如果所有检查成功，将区块追加到区块链上。
8. **修剪旧区块**：从区块链中删除 255 个区块前的旧区块。
9. **错误处理**: 如果任何验证检查失败，则抛出 "Invalid Block" 错误。否则，返回 None。

## 区块头验证

区块头验证的过程，在黄皮书和 Python 规范中都进行了严格定义，它根据太坊协议规则 (例如哈希验证、Gas使用情况、时间戳准确性等) 验证区块的完整性。此验证可确保每个区块都符合在规范中定义并且在客户端中实现的以太坊协议。在同步和追加区块时，验证是区块链不可或缺的一项功能，通过独立验证当前和历史数据来确保其正确性。

根据黄皮书的规范，区块头的 [有效性](https://github.com/ethereum/execution-specs/blob/0f9e4345b60d36c23fffaa69f70cf9cdb975f4ba/src/ethereum/shanghai/fork.py#L269) 采用一系列标准来确保每个区块都遵循以太坊协议的要求。父区块，记作 $P(H)$，是验证当前区块头 $H$ 的必要条件。区块头有效性的关键条件包括：

$$V(H) \equiv H_{gasUsed} \leq H_{gasLimit} \qquad (57a)$$
$$\land$$
$$H_{gasLimit} < P(H)_{H_{gasLimit'}} + floor(\frac{P(H)_{H_{gasLimit'}}}{1024} ) \qquad (57b)$$
$$\land $$
$$H_{gasLimit} > P(H)_{H_{gasLimit'}} - floor(\frac{P(H)_{H_{gasLimit'}}}{1024} ) \qquad (57c)$$
$$\land$$
$$H_{gasLimit} > 5000\qquad (57d)$$
$$\land  $$
$$H_{timeStamp} > P(H)_{H_{timeStamp'}} \qquad (57e)$$
$$\land$$
$$H_{numberOfAncestors} = P(H)_{H_{numberOfAncestors'}} + 1 \qquad (57f)$$
$$\land$$
$$length(H_{extraData}) \leq 32_{bytes} \qquad (57g)$$
$$\land$$
$$H_{baseFeePerGas} = F(H) \qquad (57h)$$
$$\land$$
$$H_{parentHash} = KEC(RLP( P(H)_H )) \qquad (57i) $$
$$\land$$
$$H_{ommersHash} = KEC(RLP(())) \qquad (57j)$$
$$\land$$
$$H_{difficulty} = 0\qquad (57k)$$
$$\land $$
$$H_{nonce} = 0x0000000000000000 \qquad (57l)$$
$$\land$$
$$H_{withdrawalHash} \neq nil \qquad (57n)$$
$$\land$$
$$H_{blobGasUsed} \neq nil \qquad (57o)$$
$$\land$$
$$H_{blobGasUsed} \leq  MaxBlobGasPerBlock_{=786432}  \qquad (57p)$$
$$\land $$
$$H_{blobGasUsed} \% GasPerBlob_{=2^{17}} = 0  \qquad (57q)$$
$$\land $$
$$H_{excessBlobGas} = CalcExcessBlobGas(P(H)_H) \qquad (57r)$$
$$\land $$
$$
CalcExcessBlobGas(P(H)_H) \equiv \nonumber \\
\begin{aligned}
&\begin{cases}
0,  \qquad \text{if} \space P(H)_{blobGasUsed} < TargetBlobGasPerBlock \\
P(H)_{blobGasUsed} - TargetBlobGasPerBlock
\end{cases}
\quad (57s)
\end{aligned}
$$
$$\land $$
$$
\begin{aligned}
P(H)_{blobGasUsed} \equiv  P(H)_{H_{excessBlobGas}} + P(H)_{H_{blobGasUsed}} \\
TargetBlobGasPerBlock =  393216
\end{aligned}
\quad (57t)
$$

- **Gas 使用量**：区块使用的 Gas 数量 $H_{gasUsed}$ 必须不超过 Gas 限制 $H_{gasLimit'}$，以确保交易符合区块容量（57a）。
- **Gas 限制约束**：区块的 Gas 限制必须保持在相对于父区块 Gas 限制 ${P(H){H{gasLimit'}}}$ 的指定范围内，允许逐步变化而不是突然调整（57b，57c）。
- **最小 Gas 限制**：最小 Gas 限制为 5000，以确保基本的交易处理能力（57d）。
- **时间戳验证**：每个区块的时间戳 $H_{timeStamp}$ 必须大于其父区块的时间戳 $P(H){H{timeStamp'}}$，以确保时间的顺序（57e）。
- **祖先和额外数据**：区块通过 $H_{numberOfAncestors'}$ 字段维持其血统，并将 $H_{extraData}$ 的大小限制为32字节（57f，57g）。
- **经济模型合规性**：每单位 Gas 的基本费用 $H_{baseFeePerGas}$ 是根据 EIP-1559 中建立的规则计算的，反映了网络当前的交易处理需求（57h）。这与 a、b、c、d 和 h 一起构成了经济模型的一部分。

### 头部验证和以太坊经济模型

[EIP-1559](https://eips.ethereum.org/EIPS/eip-1559) 中概述的以太坊经济模型引入了一系列机制，旨在提高网络效率和稳定性：

- **设定 Gas 限制以降低波动性**：通过将目标 Gas 设置为最大 Gas 限制的一半，以太坊旨在减少完整区块可能带来的波动性，从而确保交易处理环境更加可预测。
- **防止不必要的延迟**：该模型通过优化交易处理时间，为用户消除不必要的延迟，从而改善网络上的整体用户体验。
- **稳定区块奖励发放**：区块奖励的发放有助于增强系统的稳定性，为参与者提供更可预测的经济环境。
- **可预测的基本费用调整**：EIP-1559 引入了一种可预测的基本费用变化机制，这一功能对钱包应用程序非常有益。这种可预测性有助于提前准确估算交易费用，简化交易创建过程。
- **基本费用销毁与优先费用**：在此模型下，矿工有权保留优先费用作为激励，而基本费用则被销毁，从而有效地将其从流通中移除。这一方法用于对抗以太坊的通货膨胀，随着时间的推移减少总供应量，从而促进更健康的经济环境。

其他检查确保了向后兼容性和安全性，例如将 ommers（叔块）哈希和难度字段设置为预定义值，体现了从工作量证明向权益证明的转变（57j-57l）。

这些标准构成了以太坊经济模型的一部分，尤其受到 EIP-1559 的影响，该机制引入了一种动态基本费用机制。该机制旨在优化网络使用和费用的可预测性，从而增强用户体验和经济稳定性。此外，[EIP-4844](https://eips.ethereum.org/EIPS/eip-4844) 引入了一种新交易类型——blob 交易，进一步增强了 EIP-1559 的经济模型。

让我们更深入地探讨该模型，并尝试更好地理解这些公式中所发生的事情，它们在 Python 规范或黄皮书中不容易看到。

让我们从扩展 57h 开始，此公式在黄皮书中指定如下:

$$
\begin{equation}
F(H) \equiv
\begin{cases}
10^9 & \text{if } H_{number} = F_{London} \nonumber \\
P(H)_{H_{baseFeePerGas}} & \text{if } P(H)_{H_{gasUsed}} = \tau \nonumber \\
P(H)_{H_{baseFeePerGas}} - \nu & \text{if } P(H)_{H_{gasUsed}} < \tau \nonumber \\
P(H)_{H_{baseFeePerGas}} + \nu & \text{if } P(H)_{H_{gasUsed}} > \tau
\end{cases}
\qquad (45)
\end{equation}
$$

$$
\tau \equiv \frac {P(H)_{H_{gasLimit}}}  {\rho} \qquad (46)
$$

$$
\rho \equiv 2 \qquad (47)
$$

$$
\nu^* \equiv
\begin{cases}
\frac{P(H)_{H_{baseFeePerGas}} \times (\tau - P(H)_{H_{gasUsed}})}{\tau} & \text{if } P(H)_{H_{gasUsed}} < \tau \\
\frac{P(H)_{H_{baseFeePerGas}} \times (P(H)_{H_{gasUsed}} - \tau)}{\tau} & \text{if } P(H)_{H_{gasUsed}} > \tau
\end{cases} \qquad (48)
$$

$$
\nu \equiv
\begin{cases}
\left\lfloor \frac{\nu^*}{\xi} \right\rfloor & \text{if } P(H)_{H_{gasUsed}} < \tau \\
\max\left(\left\lfloor \frac{\nu^*}{\xi} \right\rfloor, 1\right) & \text{if } P(H)_{H_{gasUsed}} > \tau
\end{cases} \qquad (49)
$$

$$
\xi \equiv 8 \qquad (50)
$$

| 符号          | 代表的内容                         | 值                                              | 注释                                                                                                                   |
| --------------- | ------------------------------------------ | -------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| $F(H) $ | 每单位 Gas 的基本费用                          |                                                    | 由发送者支付，作为总费用的一部分，基本费用最终由执行层销毁并从系统中移除 |
| $\nu $  | 基本费用的增减幅度 |                                                    | 与父区块的 Gas 消耗量和 Gas 目标之间的差异成正比                                       |
| $\tau $ | Gas 目标                                 | $\frac {P(H)_{H_{gasLimit}}}  {\rho}_{= 2}$ | 为了降低波动性，Gas 目标设置为 Gas 限制的一半，以调节每个区块的交易吞吐量。    |
| $\rho $ | 弹性系数                      | 2                                                  | 帮助调整 Gas 目标，以维持网络的响应性、容量和价格可预测性。                    |
| $\xi $  | 基本费用最大分母	                   | 8                                                  | 控制基本费用的最大变化率，以确保逐步调整。                                     |

此外，黄皮书中还对这些对象的类型进行了一些重要定义，这些定义将用于推导这些公式：

首先，它为我们提供无限的区块限制，即这些限制可以无限扩展。

$$
H_{\text{gasUsed}} , H_{\text{gasLimit}}, H_{\text{baseFeePerGas}} \in \mathbb{N} \qquad (41)
$$

然后，它为我们提供了交易参数的类型，这些参数的最大值为 $2^{256}$ 或大约 $10^{77}$，这是这些数字能够达到的最大值

$$T_{\text{maxPriorityFeePerGas}} , T_{\text{maxFeePerGas}}, T_{\text{gasLimit}}, T_{\text{gasPrice}} \in\mathbb{N}_{256}$$

#### 附加见解：自然数的重要性

以太坊协议将区块级参数——例如已用的 gas ($H_{gasUsed}$)、gas 限制 ($H_{gasLimit}$) 和每单位 gas 的基本费用 ($H_{baseFeePerGas}$)——指定为自然数 $\mathbb{N}$。这一决定绝非随意，而是在区块链的基础经济中嵌入了一层直观的逻辑。

- 自然数和区块链逻辑

自然数从 0 开始，无限延伸，为理解和操作这些参数提供了一个直接的框架。与实数不同，实数在任意两点之间都有不可数的无限个数，而自然数允许精确的、离散的步进——这使得它们成为精度敏感的区块链交易的理想选择。这一特性简化了操作这些参数的函数的推理，有助于精确计算和预测交易成本与网络容量。

- 简单性与精度

考虑增量的简单性：每个自然数都可以被视为（0 + 1 + 1 + ... + 1）的和，这为智能合约或交易处理中的递增或递减提供了明确的路径。自然数的这种原子性质，以 0 和后继（+1）为基本构建块，使得以太坊区块链中能够构建健壮且可证明的逻辑。换句话说，自然数使得证明更加容易。

- 与实数 (小数) 的对比

与实数的无限可分性不同，以太坊经济模型中自然数的离散性确保操作保持在可计算的范围内。这一区别对于维持网络效率和安全至关重要，避免了处理实数时可能出现的计算复杂性和潜在的漏洞。

**交易参数与有界自然数**

此外，以太坊在有界自然数的子集 $\mathbb{N}_{256}$ 内指定交易参数，例如每单位 gas 最大优先费用和每单位 gas 最大费用。此边界上限被设定为 $2^{256}$ 或大约 $10^{77}$，在允许交易处理广泛的值范围的同时，确保这些值保持在安全且可管理的限制内。

#### 区块间的 Gas 价格动态

让我们深入探讨 gas 价格计算函数的动态，探索其在不同 gas 使用场景下的影响，范围从可能的最小值（5000 单位）到设定的 gas 限制。我们的重点是理解该函数在单个区块范围内的表现。

我们旨在分析 "计算每单位 gas 基本费用" 函数，这对于理解以太坊的 gas 定价机制至关重要。以下是该函数实现的 R 代码片段：

<img src="images/el-specs/gasused-basefee.png" width="800"/>

从图表中观察到：

- 该函数呈现出阶梯式线性进展，中点的方差最大。这反映了 gas 目标的设置，即为 gas 限制的一半（本例中为 15,000 单位）。
- 基本费用的最大上涨幅度约为 12.5%，可在图表的最右侧看到。这代表当基本费用从 100 开始时，可能的最大上涨幅度。
- 基本费用的最大下降幅度约为 10%，可在图表的最左侧看到。这代表当基本费用从 100 开始时，可能的最大下降幅度。
- 精确达到 gas 目标会使得基本费用增加 1%。即使略微超过目标（例如，使用的 gas 在 15,000 和 17,000 单位之间），基本费用的增加仍然只有 1%，这展示了该函数围绕目标的弹性设计。

#### 扩展模拟：Gas 限制和费用的长期影响

在可视化了 gas 价格计算函数在不同 gas 使用场景中的即时影响后，让我们考虑其在更长时间范围内的影响。具体来说，这种动态如何影响拥有数万个区块的以太坊网络，尤其是在每个区块都达到其 gas 限制的最大需求情况下？

下图模拟了 100,000 个区块内的情景，假设最大需求不变，以预测 gas 限制和基本费用的演变：

<img src="images/el-specs/gas-limit-max.png" width="800"/>

从模拟中得出的观察结果揭示了几个关键的见解：

- 基本费用敏感性：以 wei 为单位的基本费用迅速上涨，在持续的最大需求下，可能在仅仅 200 个区块内就达到一枚以太币。
- 达到上限的可能性：在持续高需求下，基本费用可能在 2000 个区块内接近其理论最大值。
- 无限的 Gas 限制增长：与基本费用不同，gas 限制本身没有上限，允许其持续增长以适应不断增加的网络需求。
- 市场动态和平衡：现实世界中的需求增加，最初体现在区块超出其 gas 目标，导致基本费用上升。然而，随着 gas 限制逐渐增加，gas 目标（即 gas 限制的一半）也会上升，最终稳定需求以抵消更高的基本费用，达到新的平衡。

为了通过更细致地检查模型的基础原理，使我们的分析具有前瞻性。具体来说，我们关注改变模型中关键常数的影响，特别是弹性系数（$\rho$）和基本费用最大变化分母（$\xi$）。这些常量预计不会在分叉中发生变化，但在未来的协议升级中可能会重新指定：

让我们从 $\xi$ 开始:

<img src="images/el-specs/xi.png" width="800"/>

这是区块之间的快照，像我们的第一个图表一样，它代表了经济模型潜力的最小部分，同时根据协议升级通过 $\xi$ 进行额外参数化。

$\xi$ 对基本费用的影响：

- 拐点与步幅变化：随着 $\xi$ 的变化，"弯曲" 或拐点变得尤为明显，且随着 $\xi$ 的增加而变得更宽。因此，增加 $\xi$ 会导致步幅变宽，表示费用调整更为渐进。相反，减少 $\xi$ 会导致步幅变窄，费用变化更加剧烈。
- 敏感性：基本费用调整曲线在拐点后发生显著变化。随着 $\xi$ 值减小，我们观察到费用调整的速率急剧上升，表示敏感性增强。
- 目标范围内的线性趋势：曲线的中间部分，特别是由浅绿色线突出显示的当前以太坊协议中 $\xi$ ，随着交易接近或超过 gas 目标，呈现出一个相对线性的趋势。

接下来，我们将注意力转向弹性系数（$\rho$），这是以太坊经济模型中的另一个关键常数，它直接影响 gas 限制调整的灵活性和响应性。为了理解其影响，我们探索了 $\rho$ 在 1 到 6 之间的不同取值，并结合了基本费用最大变化分母（$\xi$）的变化。

<img src="images/el-specs/rho-xi.png" width="800"/>

$\rho$ 和 $\xi$ 对基本费用的影响：

- 即时分析：与我们最初的观察相似，该图提供了 $\rho$ 和 $\xi$ 的调整如何在每个区块上塑造经济模型行为的精细视图，
特别是在协议升级的背景下。
- $\rho$ 的独特影响：每个子图展示了不同 $\rho$ 值的影响。作为弹性系数，$\rho$ 显著改变了基本费用调整曲线的拐点，凸显了其在调节网络对交易量波动响应能力中的作用。
- $\rho$ 和 $\xi$ 之间的相互作用：弹性系数（$\rho$）不仅改变了拐点的位置，还调节了由于基本费用最大变化分母（$\xi$）的变化而导致的调整的灵敏度。这种相互作用凸显了以太坊在不断变化的需求下确保网络效率和稳定性时所保持的微妙平衡。

<img src="images/el-specs/gas-header.png" width="800"/>

#### Blob Gas 价格动态

Blob Gas 价格动态在以下场景中进行了建模，从零开始，并且每个区块的 gas 使用量以 1000 的常数倍逐块增加。

- 图 E：展示了 blob gas 与其价格之间的关系。所有图形的代码都可在附录中找到。

<img src="images/el-specs/blob-gas-and-price.png" width="800"/>

- 图 F：对数据进行了规范化处理，以突出显示价格相对于 gas 使用量的动态关系。
  <img src="images/el-specs/blob-gas-and-price-norm.png" width="800"/>

- 当父区块的 gas 使用量低于目标值时（约 400K，对应约 400KB 或每区块 3 个 blob），blob gas 价格保持在 1。
- 超过目标并不会立即影响 gas 价格，但超额的 gas 会开始累积。
- 持续的需求增加导致累积的超额 gas 超过阈值，触发 gas 价格的指数性增加，以作为一种调节措施。
- 如果前一个区块的 gas 使用量低于目标，累积的超额 gas 可以在一个区块内清除，重置调节机制。

## 区块执行流程

在初始的头部验证之后，区块进入执行阶段([apply_body](https://github.com/ethereum/execution-specs/blob/804a529b4b493a61e586329b440abdaaddef9034/src/ethereum/cancun/fork.py#L437))。提前执行头部检查允许状态转换函数（STF）向共识层（CL）返回可能的 "Invalid Payload" 消息，而无需进行计算密集型的区块/交易执行阶段。

1. **初始化 `blobGasUsed` 为 0。** 这会将区块中交易所消耗的 gas 初始值设为零。
2. **将 `gasAvailable` 设为 $H_{gasLimit}$。** 这会将区块执行所能使用的 gas 设置为区块的 gas 限制。T
3. **初始化额外的执行组件：** 这包括设置回执的 trie、提现的 trie 和区块日志元组（其行为类似于不可不变的列表），确保可用的 gas 与区块的 gas 限制一致。
4. 通过指定 **信标根地址** 的 EL 常量 **访问信标区块根合约代码**：
   - 这一功能在 Duncan 中引入，并在 [EIP 4788](https://eips.ethereum.org/EIPS/eip-4788) 中详细描述，它允许使用信标链区块根作为共识层状态的加密累加器，以构建共识层状态的证明。这为 EVM 提供了一种信任最小化的方式来访问共识层数据，支持质押池、再质押操作、智能合约桥接和 MEV 缓解等应用。从规范创建者处 [了解更多信息](https://www.youtube.com/watch?v=GriLSj37RdI)。
5. **构造系统交易消息：** 以 **系统地址** 作为调用者，**信标根地址** 作为目标，包含 $H_{parentBeaconBlockRoot}$ 和检索到的合约代码。这在 Duncan 中引入了一种 "系统合约"，它是一种有状态的智能合约，不同于只有系统地址可以插入数据的无状态预编译合约。
6. **设置虚拟机环境并处理消息调用，** 将 $H_{parentBeaconBlockRoot}$ 存储在合约的存储中，以便稍后通过提供该时隙时间戳的交易进行检索。
7. **删除之前步骤中涉及的空帐户**，以清理状态。
8. **处理区块内的交易：**
   - 交易被解码并添加到交易 trie 中以便执行。
   - **执行 [交易](/wiki/EL/transaction)：** 此过程对于区块执行至关重要，涉及：
     1. 使用交易签名组件 $T_v, T_r, T_s$ 恢复交易发送者的地址。
     2. 验证交易的内在有效性。
     3. 计算有效的 gas 价格。
     4. 初始化执行环境。
     5. 在虚拟机内 **执行解码的交易**，包括针对当前状态的验证、gas 计算以及在成功后应用状态变化。
9. **处理由信标链验证的验证者提现** ([EIP-4895](https://eips.ethereum.org/EIPS/eip-4895))：
   - 遍历每个 [提现](https://github.com/ethereum/execution-specs/blob/119208cf1a13d5002074bcee3b8ea4ef096eeb0d/src/ethereum/shanghai/fork_types.py#L178)，将其添加到 trie 中。
   - 将提款从 Gwei 转换为 Wei，并存入指定的地址。
   - 销毁空的提现帐户以保持状态清洁。

### 环境初始化

$$
I_{caller} = T_{Sender_{address}}, \nonumber \\
I_{origin} = T_{Sender_{address}}, \nonumber \\
I_{blockHashes} = blockHashes_{Last255}, \nonumber \\
I_{coinbase} = H_{coinbase}, \nonumber \\
I_{number} = H_{number}, \nonumber \\
I_{gaslimit} = Header_{gasLimit} - cumulativeGasUsed, \nonumber \\
I_{baseFeePerGas} = H_{baseFeePerGas}, \nonumber \\
I_{gasPrice} = effectiveGasPrice, \nonumber \\
I_{time} = H_{timeStamp}, \nonumber \\
I_{prevRandao} = H_{prevRandao}, \nonumber \\
I_{state} = state, \nonumber \\
I_{chainId} = H_{chainId}, \nonumber \\
I_{traces} = [], \nonumber \\
I_{excessBlobGas} = excessBlobGas, \nonumber \\
I_{blobVersionedHashes} = T_{blobVersionedHashes}, \nonumber \\
$$

| 变量                         | 描述                                                                                                              |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| $I_{caller}$              | 发起代码执行的地址；通常是交易的发送者。                                      |
| $I_{origin}$              | 发起此执行上下文的交易的原始发送者地址。                                        |
| $I_{blockHashes}$         | 包含最近255个区块的哈希值集合。                                                                     |
| $I_{coinbase}$            | 区块奖励和交易费用的受益地址。                                                          |
| $I_{number}$              | 当前区块在区块链中的顺序列号。                                                        |
| $I_{gasLimit}$            | 执行交易时可用的最大 gas 量，包括当前区块已使用的 gas。 |
| $I_{baseFeePerGas}$       | 每单位 gas 的基本费用，这是一个动态参数，会根据区块空间需求进行调整。                                     |
| $I_{gasPrice}$            | 有效的 gas 价格，受当前网络状况和交易紧急程度的影响。                               |
| $I_{time}$                | 标记区块产生时间的时间戳，以 Unix 纪元以来的秒数计算。                             |
| $I_{prevRandao}$          | 上一个 RANDAO（随机性）值，帮助增加信标链区块生产的熵。           |
| $I_{state}$               | 当前状态，包括所有帐户余额、存储和合约代码。                                        |
| $I_{chainId}$             | 区块链的标识符，确保交易为特定链签名。                                    |
| $I_{traces}$              | 执行痕迹的占位符，供将来使用或调试。                                       |
| $I_{excessBlobGas}$       | 从父区块进行计算，它表示 blob 交易分配的超额 gas 量。                          |
| $I_{blobVersionedHashes}$ |                                                                                                                          |

## Gas 计算

### 内在 Gas 计算

内在 gas 是指交易开始执行所需的最小 gas。此成本包括 EVM 所需的计算资源以及数据传输的相关成本。内在 gas 从交易的 $T_{gasLimit}$ 中扣除，用于设置 EVM 中的执行上下文。

根据上海规范更新后的内在 gas 公式，其中 $T$ 表示交易，$G$ 表示 gas 成本，公式如下：

$$
g_0 \equiv
$$

$$
\begin{aligned}
G_{\text{initCodeWordCost}} \times
&\begin{cases}
\text{length}, & \text{if length} \mod 32 = 0 \\
\text{length} + 32 - (\text{length} \mod 32), & \text{otherwise}
\end{cases}\\
&\qquad\text{if } \text{CALLDATA} = T_{\text{initializationCode}}
\end{aligned}
$$

$$+$$

$$
\begin{aligned}
&\begin{cases}
\sum_{i \in \{T_{\text{inputData}}\}}
\begin{cases}
G_{\text{txdatazero}} & \text{if } i = 0 \\
G_{\text{txdatanonzero}} & \text{otherwise}
\end{cases}
\end{cases}\\
&\qquad\text{if } \text{CALLDATA} = T_{inputData} \lor T_{initializationCode}
\end{aligned}
$$

$$+$$

$$
\{ \begin{array}{ll}
G_{\text{txcreate}} & \text{if } T_{to} = \emptyset \\
0 & \text{otherwise}
\end{array}
$$

$$+$$

$$
G_{\text{transaction}}
$$

$$+$$

$$ 
\sum_{j=0}^{ length(T_{accessList}) - 1} \left( G_{\text{accesslistaddress}} + length(T_{accessList}[j]_s) *  G_{\text{accessliststorage}} \right)
$$

#### 内在 Gas 组件：

| 组件                                                           | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $g_0$                                                        | 代表交易的总内在gas成本，涵盖了初始代码执行和数据传输。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| $G_{\text{transaction}}$                                     | 每笔交易的基础成本，设定为 21000 gas。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| $T_{\text{initializationCode}}$                              | 当 $T_{to} = 0_{\text{Bytes}}$ 时，CALLDATA 被视为 $T_{\text{initializationCode}}$。成本初始化为 32 字节的间隔。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| $T_{inputData}$ and $T_{initializationCode}$          | 总体而言，$T_{inputData}$ 和 $T_{initializationCode}$ 共同表示交易的 CallData 参数。如果 $T_{to} \neq 0_{Bytes}$，则 CALLDATA 被视为合约的入口点的输入。处理 CALLDATA 的 gas 成本定义为每个非零字节 16 gas，每个零字节 4 gas，这会影响区块大小，并可能因处理量增加而导致网络延迟。此 gas 成本模型基于区块创建速率、链增长速率和网络延迟之间的平衡，最初针对工作量证明系统进行优化。将此模型适配于权益证明仍然是一个研究机会和未来优化的领域。这些参数被定义为一个无限大小的字节数组，初始化成本设定为每个非零字节 16 gas，每个零字节 4 gas。更多 |
| $G_{\text{txCreate}}$                                        | 合约创建交易需要额外的 32000 gas。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| $G_{\text{accesslistaddress}}, G_{\text{accessliststorage}}$ | 访问列表中指定的每个地址和存储键的额外 gas 成本，有助于优化状态访问。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |

### 有效 Gas 价格与优先费

下列公式经过修改，包含了 blob 交易（$T_{type} = 3$）

$$ p \equiv effectiveGasPrice \equiv
\begin{aligned}
&\begin{cases}
T_{gasPrice}, & \text{if} \space T_{type} = 0 \lor 1\\
priorityFee + H_{baseFeePerGas} , & \text{if} \space T_{type} = 2 \lor 3
\end{cases}\\
\end{aligned} \qquad (62)
$$

$$ f \equiv priorityFee \equiv
\begin{aligned}
&\begin{cases}
T_{gasPrice} - H_{baseFeePerGas}, & \text{if} \space T_{type} = 0 \lor 1\\
min(T_{maxPriorityFeePerGas} , T_{maxFeePerGas} -  H_{baseFeePerGas}) , & \text{if} \space T_{type} = 2 \lor 3
\end{cases}\\
\end{aligned}
$$

|                   |                                                                                                                             |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------- |
| effectiveGasPrice | 交易签名者在执行交易时每消耗一单位 Gas 需要支付的 wei 金额             |
| priorityFee       | 交易受益人在交易执行过程中每消耗一单位 Gas 将收到的 wei 金额 |

### 有效 Gas 费用

$$effectiveGasFee \equiv effectiveGasPrice \times T_{gasLimit} $$
作为预付成本的一部分进行扣除

### 总 Blob Gas

$$totalBlobGas  \equiv  (G_{gasPerBlob = 2^{17}} \times length(T_{blobVersionedHashes}) ) $$

### Blob Gas 价格

Blob Gas 价格通过一个公式确定，该公式基于网络中生成的超额 blob gas 进行调整，公式如下：

$$
blobGasPrice  \\  \approx  \\ 
factor_{minBlobBaseFee = 1} \times e^{numerator_{excessBlobGas} / denominator_{blobGaspriceUpdateFraction = 3338477}}
$$

- 对于任何低于当前每区块最大 blob gas（设为 786432）的输入，如果没有积累超额的 gas，则此公式返回 1。
- 然而，当超过目标一定数量的区块后，它就会开始增加，这会导致超额的 Blob Gas 参数开始积累，从而触发 Blob Gas 价格的指数级增长。
- 当目标设置为每区块最大 blob gas 的大约一半（393216）时，该函数从十倍目标时开始上升至 2，然后呈指数增长。


### Blob Gas 费用

$$blobGasFee \equiv totalBlobGas \times blobGasPrice $$

### 最大 Gas 费用

$$
 maxGasFee \equiv
\begin{aligned}
&\begin{cases}
T_{gasLimit} \times  T_{gasPrice}    , & \text{if} \space T_{type} = 0 \lor 1\\
T_{gasLimit} \times  T_{maxFeePerGas}   , & \text{if} \space T_{type} = 2 \\
(T_{gasLimit} \times  T_{maxFeePerGas}) +  maxBlobFee  , & \text{if} \space T_{type} =  3
\end{cases}\\
\end{aligned}
$$

$$
maxBlobFee \equiv
T_{maxFeePerBlobGas} \times totalBlobGas
$$

### 预付成本

$$
v_0 \equiv upfrontCost \equiv  effectiveGasFee + blobGasFee
$$

## 交易执行

在以太坊网络中，执行交易的过程由交易级的状态转换函数控制：

$$\Upsilon(\sigma_t, T_{index}) \qquad (4)$$

在调用 $\Upsilon$ 时，系统首先验证交易的内在有效性。验证通过后，[以太坊虚拟机](/wiki/EL/evm) (EVM) 将根据交易指令启动状态修改。

### 交易内在有效性

交易的内在有效性通过一系列检查来确定：

$$
\begin{align}
(65)\quad Sender(T) &\neq EMPTY(\sigma, account) \\ \land \nonumber\\
\sigma[Sender(T)]_{code} &= \text{KEC}(\emptyset) \\ \land \nonumber\\
T_{nonce} &< 2^{64} - 1 \\ \land \nonumber \\
T_{inputData} &\leq 2 \times MaxCodeSize_{=24576}\\ \land \nonumber \\
T_{nonce} &= \sigma[Sender(T)]_{nonce} \\ \land \nonumber \\
intrinsicGas &\leq T_{gasLimit}\\ \land \nonumber \\
maxGasFee + T_{value} &\leq \sigma[Sender(T)]_{balance}\\ \land \nonumber \\
m &\geq H_{baseFeePerGas}\\ \land \nonumber \\
\text{if} \space T_{type} = 2 \lor 3 : T_{maxFeePerGas} &\geq T_{maxPriorityFeePerGas} \\ \land \nonumber \\
T_{gasLimit} \leq Header_{gasLimit} \nonumber \\ &− last( \left[ Block_{reciept} \right] )_{cumulativeGasUsed} \\
\end{align}
$$

$$ \text{Where, }m \equiv
\begin{aligned} \\
&\begin{cases}
T_{gasPrice}, & \text{if} \space T_{type} = 0 \lor 1\\
T_{maxFeePerGas} , & \text{if} \space T_{type} = 2 \lor 3
\end{cases}\\
\end{aligned}
$$

并且 $EMPTY(\sigma, account)$ 被定义为一个无代码、零随机数 (nonce) 和零余额的帐户：

$$
\begin{align}
EMPTY(\sigma, account) \nonumber \\ \equiv \nonumber \\
\sigma[account]_{code} = \text{KEC}(\emptyset) \nonumber \\ \land  \nonumber \\
\sigma[account]_{nonce} = 0 \nonumber \\ \land  \nonumber \\
\sigma[account]_{balance} = 0 \nonumber \\
\end{align}
$$

|     |                                                                                                                  |
| --- | ---------------------------------------------------------------------------------------------------------------- |
| 1   | 交易发送者必须存在，且不能是一个未初始化的帐户。                                         |
| 2   | 发送者不能是一个合约                                                                                  |
| 3   | 帐户的交易次数有上限，确保随机数 小于 $2^{64} - 1$。                         |
| 4   | 输入数据或 CALLDATA 的大小不得超过最大代码大小的两倍 (24576 字节)。                   |
| 5   | 交易的随机数必须与当前状态中发送者的随机数相匹配。                                  |
| 6   | 内在 gas 计算不能超过交易的 gas 限制。                                       |
| 7   | 发送者必须有足够的余额来支付最高 gas 费用以及发送的价值。                  |
| 8   | 确保交易满足区块的每单位 gas 最低基本费用。                                         |
| 9   | 对于 EIP-1559 交易，每单位 gas 最高费用必须至少与每单位 gas 最高优先费相等          |
| 10  | 交易的 gas 限制，加上区块中先前交易消耗的 gas，不得超过区块的 gas 限制 |

### $T$ 执行阶段 1：检查点状态 $\sigma_0$

交易执行的初始阶段包括以下步骤：

1. **验证交易**：评估交易的有效性；如果通过验证，则状态更改将不可逆地启动。
2. **扣除内在 Gas**：从交易的 gas 限制中扣除内在 gas 数量 $g_0$，以建立消息准备的 gas 参数：$gas = T_{gasLimit} - g_0$。
3. **增加发送者的随机数**：通过增加随机数来反映发送者状态的不可逆变化。
4. **扣除预付成本**：发送者的余额会减去预付成本，这也是对状态的不可逆变化。

$$\sigma_0 \equiv \sigma \space \text{except:} $$
$$\sigma_0[Sender]_{balance} \equiv \sigma[Sender]_{balance} - upfrontCost_{\nu_0} $$
$$\sigma_0[Sender]_{nonce} \equiv \sigma[Sender]_{nonce} + 1 $$

这个检查点状态代表在初步验证和扣除后的修改状态，为随后的执行步骤奠定了基础。

### $T$ 执行阶段 2 : 交易规范化和子状态初始化

EVM 执行本质上只需要一个环境和一条消息。因此，交易信封内的交易被精简为四种主要类型。然后，这些交易被统一为一个单一的消息数据结构，主要包括两项操作：启动合约创建以及执行对地址的调用。值得注意的是，对于早于 EIP-1559 的缺乏基本费用的交易，会在转换成消息格式的过程中通过标准化处理整合 [Gas 价格](https://github.com/ethereum/go-ethereum/blob/100c0f47debad7924acefd48382bd799b67693cf/core/state_transition.go#L168)。此外，执行路径根据 $T_{to}$ 参数来决定：

- 如果 $T_{to} = 0Bytes$ : 继续执行合约创建
- 如果 $T_{to} = Address$ : 继续执行调用

这映射到 EELS 中的内部消息类型如下：

$$
message(caller, target, gas, value,\\  data, code, depth, current Target ,\\ codeAddress,  shouldTransferValue, isStatic,\\ preAccessedAddresses, preAccesedStorageKeys,\\ parentEVM)
$$

| 消息字段参数 | 初始调用值                                                                                                      | 初始创建值                                                                                                  | 执行环境前向映射                                         |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| caller                  | 恢复的发送者地址                                                                                                | 恢复的发送者地址                                                                                                | $I_origin$ or $I_{sender}$                                      |
| target                  | $T_{to}$ 是一个有效地址                                                                                      | $T_{to} = 0_{bytes}$                                                                                             |                                                                               |
| gas                     | $T_{gasLimit} - intrinsicCost$                                                                                   | $T_{gasLimit} - intrinsicCost$                                                                                   |
| value                   | $T_{value}$                                                                                                      | $T_{value}$                                                                                                      | 黄皮书：$I_v$ 或 $I_{value}$                                        |
| data                    | $T_{data}$                                                                                                       | $0_{bytes}$                                                                                                      | 黄皮书：$I_d$ 或 $I_{data}$                                         |
| code                    | $(T_{to})_{code}$                                                                                                | $T_{data}$                                                                                                       | 黄皮书：$I_b$ 或 $I_{[byteCode]}$                                   |
| depth                   | $0$                                                                                                              | $0$                                                                                                              | 黄皮书：$I_e$ 或 $I_{depth}$                                        |
| currentTarget           | $T_{to}$                                                                                                         | 我们通过取 $KEC(RLP([Sender_{address}, Sender_{nonce} - 1])) $的最后 20 字节来计算合约地址	 |
| codeAddress             | 默认为 $T_{to}$，除非需要执行其他帐户的代码。例如：'CALLCODE' 调用预编译合约 |                                                                                                                         | 黄皮书：$I_a$ 或 $I_{codeOwnerAddress}$                             |
| shouldTransferValue     | 默认为True，表示在执行此消息时是否应该转移 ETH                                   | 默认为 True                                                                                                         |
| isStatic                | 默认为 False，表示是否允许状态修改（False 表示允许状态修改）            | 默认为 False                                                                                                        | 与黄皮书成反比：$I_w$ 或 $I_{permissionToModifyState}$ |
| accesslistAddress       | 见下文                                                                                                               | -                                                                                                                       |
| accesslistStorageKeys   | -                                                                                                                       | -                                                                                                                       |
| parentEvm               | 最初为 None                                                                                                          | 最初为 None                                                                                                          |

#### 子状态初始化

子状态的初始化为交易执行奠定了基础，定义如下：

- **自毁集合**：最初为空，表示没有合约被标记为自毁。
- **日志序列**：最初为空元组，准备记录执行过程中产生的日志。
- **涉及用户**: 最初也为空，列出通过交易而 "被涉及" 的帐户。
- **退款余额**：设置为0，用于计算可能积累的 Gas 退款。

根据交易类型，访问的地址会有不同的初始化方式：

- 对于 $T_{type} = 0$，coinbase 地址、调用者、当前目标和所有预编译合约地址被添加到访问的帐户子状态中
- 对于 $T_{type} = 1, 2, 或 \space 3$，coinbase地址、调用者、当前目标、所有预编译合约和访问列表中的条目都被添加到访问的帐户子状态中。

$$A^*  \equiv (A^{*}_{selfDestructSet} = \empty, $$
$$A^{*}_{logSeries} = (), $$
$$A^{*}_{touchedAccounts} = \empty, $$
$$A^{*}_{refundBalance} = 0 , $$
if $T_{type} = 0$:
$$A^{*}_{accesedAccountAddresses} =  \{ H_{coinBase},$$
$$Message_{caller}, Message_{current_target}  $$
$$allPrecompiledContract_{addresses}\}$$
$$A^{*}_{accesedStorageKeys} = \empty $$
if $T_{type} = 1 \lor 2 \lor 3$:
$$A^{*}_{accesedAccountAddresses} =  \{ H_{coinBase},$$
$${ \bigcup_{Entry \in T_{accessList}} \{ Entry_{address}  \}},$$
$$Message_{caller}, Message_{current_target}  $$
$$allPrecompiledContract_{addresses}\}$$
$$A^{*}_{accesedStorageKeys}= $$
$${ \bigcup_{Entry \in T_{accessList}} \{ \forall i < length(Entry_{storageKeys}), i \in \mathbb{N} : (Entry_{address_{20byte}}, Entry_{storageKeys}[i]_{32byte}  \}}$$

`A_{accessedAccountAddresses}` 和 `A_{accessedStorageKeys}` 利用了 [以太坊访问列表 (EIP-2930)](https://eips.ethereum.org/EIPS/eip-2930) 中引入的机制，可在 [此 EIP-2930 概述](https://www.rareskills.io/post/eip-2930-optional-access-list-ethereum) 中找到进一步的详细说明。该机制在交易的访问列表中声明的地址和存储键（会产生 "热" 成本）与未包含的地址和存储键（会产生 "冷" 成本）之间做出了区分。有关冷访问和热访问的 Gas 成本的详细信息，请参阅 [EIP-2929：对状态访问操作码的 Gas 成本增加](https://eips.ethereum.org/EIPS/eip-2929)，该提案调整了成本以考虑 EVM 中的状态访问操作。

$A_{accesedAccountAddresses}$ 和 $A_{accesedStorageKeys}$ 属于 [以太坊访问列表](https://www.rareskills.io/post/eip-2930-optional-access-list-ethereum)[ EIP ](https://eips.ethereum.org/EIPS/eip-2930)，该提案将交易声明的调用地址和其他地址之间进行了区分。不在访问列表中的地址，每次调用时产生的帐户访问冷成本为 2600，每次状态访问时为 2100。而访问列表 EIP 指定对状态和帐户访问的后续调用 (称为 "热成本") 将花费 100 gas。[EIP 3651](https://eips.ethereum.org/EIPS/eip-3651) 将 coinbase 地址添加到了执行开始前需要 "预热" 的帐户列表中。

#### 消息类型 : 合约创建

在以太坊黄皮书的上下文中，合约创建通过以下函数表示：

$$
\Lambda(\sigma, A, s, o, g, p, v, i, e, \zeta, w) \\ or \\
\Lambda(state_{\sigma}, AccruedSubState_{A} , sender_s , originalTransactor_o ,\\  availableGas_g , effectiveGasPrice_p , \\ endowment_v, []evmInitCodeByteArray_i , stackDepth_e , \\  saltForNewAccountAddress_{\zeta}, stateModificationPermission_w)
$$

| $\Lambda$ 调用参数          | 映射                                                                                            | 备注                                                                                               |
| ----------------------------------------- | -------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| $state_{\sigma}$                   | $I_{state}$                                                                                 | 合约创建开始前的当前状态。                                                  |
| $AccruedSubState_{A}$              | $A^0$                                                                                       | 表示初始子状态。                                                                    |
| $sender_s$                         | $I_{origin}$                                                                                | 交易的原始地址，启动合约创建过程。                            |
| $originalTransactor_o$             | $I_{origin}$                                                                                | 原始交易者，与直接交易中的发送者相同。                            |
| $availableGas_g$                   | $T_{gasLimit} - intrinsicCost$                                                              | 扣除交易的内在成本后, 可用于创建合约的 gas。 |
| $effectiveGasPrice_p$              | $I_{gasPrice}$                                                                              | 交易设置的 gas 价格。                                                              |
| $endowment_v$                      | $T_{value}$                                                                                 | 转移到新合约的值。                                                          |
| $[]evmInitCodeByteArray_i$         | $T_{CALLDATA}$                                                                              | 新合约的初始化字节码。                                                   |
| $stackDepth_e$                     | 0                                                                                                  | 合约创建时的调用栈深度，最初为 0。                         |
| $saltForNewAccountAddress_{\zeta}$ | $\emptyset$                                                                                 | 用于生成新合约地址的盐值，如果是 create2 操作，则不为空。              |
| $stateModificationPermission_w$    | True，与 Message 对象中的 `is_static` 参数相反，默认设置为 false	 | 表示合约创建是否可以修改状态。                                            |

注意：当消息不是直接由交易触发，而是来自 EVM 代码的执行时，$originalTransactor_o$ 可能与 $sender_s$ 不同，这表明 EVM 执行上下文中消息的来源具有多样性。

合约创建的过程从确定合约地址开始。在 EELS 中，此任务是消息准备的一部分。其他客户端可能会在不同的阶段处理它。具体步骤如下：

1. **计算合约地址：**

   - 合约地址是通过对发送者地址和随机数（减去 1，因为随机数在该操作之前已经递增）进行哈希处理来 [计算](https://github.com/ethereum/execution-specs/blob/db87f1b1d21f61275fc34b08de1735889c01f018/src/ethereum/cancun/utils/address.py#L42) 的，公式为：$KEC(RLP([Sender_{address} , Sender_{nonce} - 1]))$。这一调整考虑了交易发布时的随机数。
   - 接下来，提取此哈希的最后 20 个字节：$KEC(RLP([Sender_{address} , Sender_{nonce} - 1]))[-20:]$。
   - 如果结果的长度小于 20 字节，则左侧用零字节填充，直到形成一个 20 字节的地址。

2. **初始化新帐户：**

$$
\sigma^*[newAccount] \equiv ( Nonce_{=1}, Balance_{=preexistingValue + T_{value} }, Storage_{=TRIE(\empty)}, CodeHash_{=KEC(())})
$$

新帐户的状态构建如下：

- 随机数设置为 1。
- 余额设置为转移值和任何现有余额的总和。
- 空存储。
- 源自空元组的代码哈希：KEC(())。

3. **更新发送者余额：**
   发送者余额调整为当前余额减去交易值：

$$
\sigma^*[sender]_{balance} \equiv \sigma[sender]_{balance} - T_{value}
$$

4. **帐户初始化：**
   最后，在主执行周期内通过执行 EVM 初始化代码字节数组 $[]evmInitCodeByteArray_i$ 来初始化帐户。

#### 消息类型：调用

待完成

### $T$ 执行阶段 3 : 主执行 ($\Xi)  $

#### [机器](/wiki/EL/evm?id=evm) 状态 $\mu$

$$\mu \equiv (\mu_{gasAvailable}, \mu_{programCounter},\\ \mu_{memoryContents}, \mu_{activeWordsInMemory},\\ \mu_{stackContents} ) $$
| | 初始值 | 备注 |
|-|-|-|
|$$\mu$$ | | 初始机器状态 |
|$$\mu'$$ | | 操作后得到的机器状态，其中 $\mu' \equiv \mu \space \text{except :} \ \mu'{gas} \equiv \mu{gas} - C_{generalGasCost}(\sigma, \mu, A, I)$ |
|$$\mu_{gasAvailable}$$ | | 交易 [可用的总 gas](/wiki/EL/evm?id=gas) |
|$$\mu_{programCounter}$$ | 0 | [自然数计数器](/wiki/EL/evm?id=program-counter)，用于追踪我们所处的代码位置，最大数值为 256 字节 |
|$$\mu_{memoryContents}$$ | $$[0_{256Bit}, ..., 0_{256Bit}]$$ | [256 位字长寻址字节数组](/wiki/EL/evm?id=memory) |
|$$\mu_{activeWordsInMemory}$$ | 0 | 内存中活动字的长度，以 32 字节为单位扩展  |
|$$\mu_{stackContents}$$ | | [堆栈](/wiki/EL/evm?id=stack) 项 : 256 位字长, 最大项数 = 1024 |
|$$\mu_{outputFromNormalHalting}$$ | () | 表示来自上一个函数调用的输出（字节），由正常终止函数决定。EELS pyspec 在 EVM 对象中为输出提供了一个专门字段，而 Geth 则没有，它使用 returnData 字段来实现相同功能。

#### 当前操作

`currentOperation` 由 `programCounter` 在 [字节码](/wiki/EL/evm?id=evm-bytecode) 数组中的位置确定：

$$ currentOperation \equiv \ w \equiv
\begin{cases}
I_{[byteCode]}[\mu_{programCounter} ] & \text{if} \space  \mu_{programCounter} < length(I_{[byteCode]}) \\
STOP & \text{otherwise}
\end{cases}
$$

此逻辑通过访问字节码数组中 programCounter 的位置来获取当前操作。如果 programCounter 超过字节码的长度，则发出 STOP 操作以停止执行。

以黄皮书中对加法运算符的定义为例： 
$$\mu'_{stackContents}[0] \equiv \mu_{stackContents}[0] + \mu_{stackContents}[1]$$

这种表示意味着在堆栈左侧添加和移除，类似于队列操作。然而，传统堆栈操作是从右侧添加和移除元素。将其转换为基于堆栈操作：

$$
Add \Rightarrow
$$

$$
x =  Pop(\mu_{stackContents}) \\
y =  Pop(\mu_{stackContents_{itemsRemoved=1}}) \\
result = x + y  \\
Push(\mu_{stackContents_{itemsRemoved=2}}, result)
$$

$$
\Rightarrow \mu_{stackContents^{itemsAdded_{\alpha}=1}_{itemsRemoved_{\delta}=2}}
$$

在转换为代码时，符号 $\mu_{s}[number]$ 对应于 $\mu_{stackContents}[stackLength - 1 - number]$，这与堆栈操作的传统理解保持一致。

黄皮书优雅地表示了基于堆栈的操作，并提供了一个在执行周期内解释这些操作的框架。它规定，堆栈项的操作是从数组最左侧、索引较低的部分进行的，而未受影响的项保持不变：

$$
\begin{align}
\Delta \equiv \alpha^{itemsAdded}_w - \delta^{itemsRemoved}_w \quad (160) \nonumber\\
& \nonumber \\
\mu'_{stackContents}.length \equiv \mu_{stackContents}.length + \Delta \quad (161) \nonumber \\
& \nonumber \\
\forall x \in [\alpha^{itemsAdded}_w , \mu'_{stackContents}.length) : \mu'_{stackContents}[x] \equiv \mu_{stackContents}[x - \Delta] \quad (162) \nonumber
\end{align}
$$

公式 162 证明，对于指定范围内的每个 x，修改后的堆栈在位置 $x - \Delta$ 处反映了原始堆栈，这有效地跟踪了操作后堆栈项的原始位置。例如，将项 [2] 添加到现有堆栈 [10] 产生 [2,10]，其中原始项的新位置对应于 $x=Delta$，保持了操作后堆栈顺序的完整性。

#### 单个执行周期

$$
O((\sigma, \mu, A, I)) \equiv (\sigma', \mu', A', I) \quad (159)\\
$$

其中，$O$ 表示执行周期，封装了状态机中单个周期的结果。此周期可以修改 $\mu$ 的所有组件，并对 $\mu_{gas}$ and $\mu_{programCounter}$ 的更改进行了明确规定：

##### 单个执行周期的结果程序计数器

以下公式概述了执行周期如何逐条处理指令：

$$
\mu'_{programCounter} \equiv
\begin{cases}
J_{JUMP}(\mu) \space \text{if }  currentOperation = JUMP \\
J_{JUMP1}(\mu) \space \text{if }  currentOperation = JUMP1 \\
N(\mu_{programCounter}, currentOperation) \space \text{otherwise}
\end{cases}
$$

$$
\text{Where, } \\
NextValidInstruction_N(i_{=programCounter}, w_{=currentOperation}) \equiv \\
\begin{cases}
&\\
programCounter + NumberOfBytes(currentOperation + Data_{currentOperation}) - NumberOfBytes(Operation_{PUSH1}) + 2 \\
\qquad  \text{if } currentOperation \in [PUSH1,PUSH32] \\
&\\
programCounter + 1 \space \text{otherwise}
\end{cases}
$$

- 如果操作是 $JUMP$，则 $J_{JUMP}$ 函数会将程序计数器设置为堆栈顶部的值。
- 对于 $JUMP1$ 操作，$J_{JUMP1}$ 函数仅在堆栈中的相邻值不为 0 时，才会将程序计数器设置为堆栈顶部的值。否则，它会将程序计数器增加 1。如果当前操作既不是 $JUMP$ 也不是 $JUMP1$，程序计数器将通过 NextValidInstruction 函数增加。

NextValidInstruction 函数会确定当前操作是否属于所有 PUSH 操作的范围内。如果是，则程序计数器增加到紧随当前操作字节之后的字节，以记录与该操作相关的数据。根据具体的 PUSH 操作，这些数据的大小可以在 1 到 32 字节之间。如果操作不是 PUSH 操作，则程序计数器仅增加 1，以进入代码的下一个字节。此过程强调了 PUSH 指令的作用，即将数据从代码加载到堆栈上。

当程序计数器执行跳转操作时，它必须指向一个有效的跳转目标。$ValidJumpDestinations_{D}$ 函数指定了所有有效跳转目标的集合。

$$
ValidJumpDestinations_{D}(byteCode) \equiv \\
ValidJumpDestinations_{D_J}(byteCode,index) \equiv \\
\begin{cases}
\{\}  \quad \text{  if } index \geq Length(byteCode) \\
&\\
\{i\} \cup ValidJumpDestinations_{D_J}(byteCode,NextValidInstruction(index, byteCode[index])) \\
\space \qquad \text{if }  byteCode[index] = JUMPDEST \\
&\\
ValidJumpDestinations_{D_J}(byteCode,NextValidInstruction(index, byteCode[index])) \space \text{otherwise}
\end{cases}
$$

这表明，如果该索引处的字节码对应于 JUMPDEST 操作，则将该索引包含在集合中。我们通过递归调用 $ValidValidJumpDestinations_{D_J}(byteCode, index)$ 函数，并使用 $NextValidInstruction$ 函数确定的索引，继续添加这些索引。

##### 单个执行周期的结果 Gas 消耗计算

$$
\mu'_{gas} \equiv \mu_{gas} - C_{gasCost}(\sigma, \mu, AccruedSubState, Environment_I)
$$

虽然 gas 成本函数并不复杂，但它涵盖了不同操作的各种情况。它在以太坊黄皮书的附录 H 中进行了简要定义。本质上，它通过将当前操作的成本加上执行周期前后活动内存中活跃字数成本的差值（即内存扩展成本），来计算当前执行周期的总成本。

不同的以太坊客户端对 gas 成本的处理方式不同。在 PySpec 中，各种类型的成本处理都被集成到操作中，而在 Geth 中， gas 成本则在操作执行之前进行处理。此外，Geth 还区分了用于内存扩展的 [动态](https://github.com/ethereum/go-ethereum/blob/7bb3fb1481acbffd91afe19f802c29b1ae6ea60c/core/vm/interpreter.go#L257) 成本和与操作基础成本相关的 [恒定](https://github.com/ethereum/go-ethereum/blob/7bb3fb1481acbffd91afe19f802c29b1ae6ea60c/core/vm/interpreter.go#L224) gas。这两种成本均使用 [UseGas](https://github.com/ethereum/go-ethereum/blob/7bb3fb1481acbffd91afe19f802c29b1ae6ea60c/core/vm/contract.go#L161) 函数进行扣除。

#### 程序执行 $\Xi$ :

$$(\sigma^{'}_{resultantState}, gas_{remaining}, A^{resultantAccruedSubState}, \omicron^{Output})$$ $$\equiv \Xi(\sigma,gas,A^{accruedSubState}, Environment_I)$$

程序执行函数由函数 X 正式定义，唯一的区别是 $\Xi$ 调用 X 并返回 X 的输出，同时从输出元组中移除了 $Environment_I$。

##### 递归执行函数 X

X 协调整个代码的执行。这通常由客户端作为主循环对代码进行迭代来实现。但从定义上，它是递归的：

$$
X((\sigma,\mu,AccruedSubState,Environment_I)) \equiv  \nonumber \\
\begin{cases}
&\\
(\empty, \mu, AccruedSubState, Environment_I) \\ \qquad \text{if } Z_{exceptionalHalting}(\sigma, \mu, AccruedSubState, Environment_I) \\
&\\
(\empty, \mu', AccruedSubState, Environment_I, output ) \\ \qquad \text{if }  currentOperation_w = REVERT \\
&\\
O(\sigma, \mu', AccruedSubState, Environment_I) . output  \\ \qquad \text{if }  output  \neq \empty \\
&\\
X(O(\sigma, \mu', AccruedSubState, Environment_I)) \\ \qquad \text{otherwise}
&\\
\end{cases}
$$

$$
\text{Where}, \\
\mu'_{returnData} \equiv \mu'_{outputFromNormalHalting} \equiv output \equiv H_{normalHaltingFunction}(\mu,Environment_I)
$$

$$
O(\sigma,\mu,A,I).output \equiv O(\sigma,\mu,A,I,output)
$$

$$
\mu' \equiv \mu \text{ except:} \\
\mu'_{gas} \equiv \mu_{gas} - C_{gasCostFunction}(\sigma,\mu,A,I) \\
\mu'_{activeWordsInMemory} \equiv 32 * M_{memoryExpansionForRangeFunction}(\mu_{activeWordsInMemory}, \mu_{stackContents}[0], \mu_{stackContents}[1])
$$

1. 如果满足异常停止的条件，则返回一个由空状态、机器状态、累计子状态、环境和空输出组成的元组。
2. 如果当前操作是 $REVERT$，则返回一个由空状态、扣除 gas 后的机器状态、累积子状态、环境和机器输出组成的元组。
3. 如果机器输出不为空，则执行迭代函数 $O$ 会消耗该输出。

- 例如，如果当前操作是系统操作（如 CALL、CALLCODE、[DELEGATECALL](https://github.com/ethereum/execution-specs/blob/9c24cd78e49ce6cb9637d1dabb679a5099a58169/src/ethereum/cancun/vm/instructions/system.py#L542) 或 STATICCALL），这些调用会触发 [通用调用函数](https://github.com/ethereum/execution-specs/blob/9c24cd78e49ce6cb9637d1dabb679a5099a58169/src/ethereum/cancun/vm/instructions/system.py#L267)，设置一个新消息和子 EVM 进程。然后，该进程的输出会被 [写回到父 EVM 进程的内存中](https://github.com/ethereum/execution-specs/blob/9c24cd78e49ce6cb9637d1dabb679a5099a58169/src/ethereum/cancun/vm/instructions/system.py#L325)，在 $O$ 的一次迭代中被有效地消耗，并且可能在下一次迭代中被使用。

4. 在所有其他情况下，我们只需继续递归调用迭代函数。简而言之，这意味着我们继续执行主解释器循环。

##### 正常暂停 H

$H_{normalHaltingFunction}$ 定义了正常情况下的 EVM 暂停行为：

$$
H_{normalHaltingFunction}(\mu, Environment_I) \equiv
$$

$$
\begin{cases}
H_{RETURN}(\mu) & \text{if } \text{currentOperation} \in \{ \text{RETURN}, \text{REVERT} \} \\
() & \text{if } \text{currentOperation} \in \{ \text{STOP}, \text{SELFDESTRUCT} \} \\
\empty & \text{otherwise}
\end{cases}
$$

其中：

- $H_{RETURN}(\mu) \equiv \mu'$

- $\Delta_{expansion}$ is calculated as:

  - $\Delta_{expansion} \equiv 32 \times M_{memoryExpansionForRangeFunction}(length(\mu_{memoryContents}), startPos, memorySize)$
  - $\Delta_{expansion} \in \mathbb{N}$

- $\mu'$ is defined as:
  - $\mu' \equiv \mu$ except:
    - $\mu'_{memoryContents} \equiv \mu_{memoryContents} + [0_{\text{word}_{256\text{bit}}} ... 0_{\text{word}_{256\text{bit}}}]_{\text{length}=\Delta_{expansion}}$
    - $\mu'_{output} \equiv \mu'_{memoryContents}[startPos : startPos + memorySize]$
    - $\mu'_{gas} \equiv \mu_{gas} - \text{memoryExpansionCost}$
    - $\mu'_{running} \equiv false$

其中：

- $startPos \equiv  \mu_{stackContents}[0]$
- $memorySize \equiv  \mu_{stackContents}[1]$

函数 $M_{memoryExpansionForRangeFunction}(s,f,l)$ 会确定需要适应指定范围的内存扩展：

$$
M_{memoryExpansionForRangeFunction}(s,f,l) \equiv
$$

$$
\begin{cases}
S & \text{if } l = 0 \\
\text{max}(s, \lceil (f + l) / 32 \rceil) & \text{otherwise}
\end{cases}
$$

从本质上讲，$H_{normalHaltingFunction}$ 首先根据堆栈顶的两个元素设置输出的起始索引和长度。如果需要扩展内存以容纳输出，则相应地扩展内存，并在必要时产生内存扩展成本。最后，它将 EVM 的输出设置为指定的内存范围。

##### 异常停止 Z

### $T$ 执行状态 4 : 临时状态 $\sigma_p$

待完成

### $T$ 执行状态 5 : 预最终状态 $\sigma^*$

待完成

### $T$ 执行状态 6 : 最终状态 $\sigma'$

待完成

## 区块整体有效性
待完成

## 附录

### 代码 A

```R
## 导入

library(plotly)
library(dplyr)

## xi 和 rho 的值
## 这就是 R 语言中 '<-' 赋值的工作方式

ELASTICITY_MULTIPLIER <- 2
BASE_FEE_MAX_CHANGE_DENOMINATOR <- 8

## 从规范进行略微修改的函数

calculate_base_fee_per_gas <- function(parent_gas_limit, parent_gas_used, parent_base_fee_per_gas, max_change_denom = BASE_FEE_MAX_CHANGE_DENOMINATOR , elasticity_multiplier = ELASTICITY_MULTIPLIER) {

  #  %/% == // (python) == floor

  parent_gas_target <- parent_gas_limit %/% elasticity_multiplier
  if (parent_gas_used == parent_gas_target) {
    expected_base_fee_per_gas <- parent_base_fee_per_gas
  } else if (parent_gas_used > parent_gas_target) {
    gas_used_delta <- parent_gas_used - parent_gas_target
    parent_fee_gas_delta <- parent_base_fee_per_gas * gas_used_delta
    target_fee_gas_delta <- parent_fee_gas_delta %/% parent_gas_target
    base_fee_per_gas_delta <- max(target_fee_gas_delta %/% max_change_denom, 1)
    expected_base_fee_per_gas <- parent_base_fee_per_gas + base_fee_per_gas_delta
  } else {
    gas_used_delta <- parent_gas_target - parent_gas_used
    parent_fee_gas_delta <- parent_base_fee_per_gas * gas_used_delta
    target_fee_gas_delta <- parent_fee_gas_delta %/% parent_gas_target
    base_fee_per_gas_delta <- target_fee_gas_delta %/% BASE_FEE_MAX_CHANGE_DENOMINATOR
    expected_base_fee_per_gas <- parent_base_fee_per_gas - base_fee_per_gas_delta
  }
  return(expected_base_fee_per_gas)
}
```

在 R 中定义了模型后，我们继续在一系列 gas 使用场景中模拟该函数：

````R
parent_gas_limit <- 30000  # 固定以简化

## 让我们看看对 100 的影响，以了解该函数对费用百分比的影响
parent_base_fee_per_gas <- 100

## 注意，gas使用量不能低于最小限制 5k，
## 因此我们可以从 5k 到 30k 逐个单位地计算，以获得完全精确的结果

seq_parent_gas_used <- seq(5000, parent_gas_limit, by = 1) # creates a vector / column

## 将这个向量/列添加到数据框架中

data <- expand.grid(parent_gas_used = seq_parent_gas_used)

## 应用我们上面创建的函数，并将其收集到一个新列中

data$expected_base_fee <- mapply(calculate_base_fee_per_gas, parent_gas_limit, data$parent_gas_used, parent_base_fee_per_gas)
````

这些是准备工作，现在让我们通过散点图进行绘制和观察，这将揭示该函数在一定范围内产生的任何形状；考虑到约束条件。

```R
fig <- plot_ly(data, x = ~parent_gas_used, y = ~expected_base_fee, type = 'scatter', mode = 'markers')  # scatter plot

## %>% 是 dplyr 中的管道操作符，在 R 代码库中被广泛使用，类似于 shell 中使用的管道 | 操作符

fig <- fig %>% layout(xaxis = list(title = "Parent Gas Used"),
                      yaxis = list(title = "Expected Base Fee "))

## 显示图表
fig
```

### 代码 B

````r

library(forcats)
library(ggplot2)
library(scales)
library(viridis)

## 初始参数
initial_gas_limit <- 30000000
initial_base_fee <- 100
num_blocks <- 100000

## 区块顺序
blocks <- 1:num_blocks

max_natural_number <- 2^256

## 计算每个区块的 gas 限制
gas_limits <- numeric(length = num_blocks)
expected_base_fee <- numeric(length = num_blocks)
gas_limits[1] <- initial_gas_limit
expected_base_fee[1] <- initial_base_fee

for (i in 2:num_blocks) {

   # 将最大更改应用到每个区块的 gas_limit
    gas_limits[i] <- gas_limits[i-1] + gas_limits[i-1] %/% 1024


  # 检查先前的 expected_base_fee 是否已经达到阈值
  if (expected_base_fee[i-1] >= max_natural_number) {
    # 一旦达到或超过 max_natural_number，则停止增加 expected_base_fee
    expected_base_fee[i] <- expected_base_fee[i-1]
  } else {
    # 正常计算 expected_base_fee 直到达到阈值
    expected_base_fee[i] <- calculate_base_fee_per_gas(gas_limits[i-1], gas_limits[i], expected_base_fee[i-1])
  }
}

## 创建用于绘图的数据框架
data <- data.frame(Block = blocks, GasLimit = gas_limits, BaseFee = expected_base_fee)

## 合理化标签
label_custom <- function(labels) {
  sapply(labels, function(label) {
    if (is.na(label)) {
      return(NA)
    }
    if (label >= 1e46) {
      paste(format(round(label / 1e46, 2), nsmall = 2), "× 10^46", sep = "")
    } else if (label >= 1e12) {
      paste(format(round(label / 1e12, 2), nsmall = 2), "T", sep = "")  # Trillions
    } else if (label >= 1e9) {
      paste(format(round(label / 1e9, 1), nsmall = 1), "Billion", sep = "")  # Million
    } else if (label >= 1e6) {
      paste(format(round(label / 1e6, 1), nsmall = 1), "Mil", sep = "")  # Million
    } else if (label >= 1e3) {
      paste(format(round(label / 1e3, 1), nsmall = 1), "k", sep = "")  # Thousand
    } else {
      as.character(label)
    }
  })
}

## 将我们想要的观察范围分箱
data_ranges <- data %>%
  mutate(Range = case_when(
    Block <= 1000 ~ "1-1000",
    Block <= 10000 ~ "1001-10000",
    Block <= 100000 ~ "10001-100000"
  ))

## 重新排列数据箱以控制绘图的显示位置
data_ranges$Range <- fct_relevel(data_ranges$Range, "1-1000", "1001-10000", "10001-100000")

## 图形语法，我们可以通过 + 来将我们想要的特性添加到图表中
plot <- ggplot(data_ranges, aes(x = Block, y = GasLimit, color = BaseFee)) +
  geom_line() +
  facet_wrap(~Range, scales = "free") +  # 使用 free 来让每个面都有自己的 x 轴刻度
  labs(title = "Gas Limit Over Different Block Ranges",
       x = "Block Number",
       y = "Gas Limit") +
  scale_x_continuous(labels = label_custom) +  # 为 x 轴使用自定义标签函数
  scale_y_continuous(labels = label_custom) +  # 为 y 轴使用自定义标签函数
  scale_color_gradientn(colors = viridis(8), trans = "log10",
                        breaks = c(1e3, 1e10, 1e20, 1e40, 1e60, 1e76),
                        labels = c("100", "10^10", "10^20", "10^40", "10^60", "10^76")) +
  theme_bw()

## 查看
plot

## 保存到文件
ggsave("plot_gas_limit.png", plot, width = 7, height = 5)

````

### 代码 C

````r
## 我们正在观察该参数的影响
## 它被设置为 8，但让我们看看它在 [2,4, .. ,8, .. ,12] 范围内的影响
seq_max_change_denom <- seq(2, 12, by = 2)

parent_gas_limit <- 3 * 10^6
seq_parent_gas_used <- seq(5000, parent_gas_limit, by = 100)

parent_base_fee_per_gas <- 100

data <- expand.grid( parent_gas_used = seq_parent_gas_used, base_fee_max_change_denominator = seq_max_change_denom)

data$expected_base_fee <- mapply(calculate_base_fee_per_gas, parent_gas_limit, data$parent_gas_used, parent_base_fee_per_gas, data$  base_fee_max_change_denominator)
$`

这就是数据准备的全部内容，现在让我们开始绘制：

```r
plot <- ggplot(data, aes(x = parent_gas_used, y = expected_base_fee, color = as.factor(base_fee_max_change_denominator))) +
    geom_point() +
    scale_color_brewer(palette = "Spectral") +
    theme_minimal() +
    labs(color = "Base Fee Max Change Denominator") +
    theme_bw()

plot
```

### 代码 D

````r
seq_elasticity_multiplier <- seq(1, 6, by = 1)
seq_max_change_denom <- seq(2, 12, by = 2)

parent_gas_limit <- 3 * 10^6
seq_parent_gas_used <- seq(5000, parent_gas_limit, by = 500)

parent_base_fee_per_gas <- 100

data <- expand.grid( parent_gas_used = seq_parent_gas_used, base_fee_max_change_denominator = seq_max_change_denom, elasticity_multiplier = seq_elasticity_multiplier)

data```expected_base_fee <- mapply(calculate_base_fee_per_gas, parent_gas_limit, data$parent_gas_used, parent_base_fee_per_gas, data$base_fee_max_change_denominator, data$  elasticity_multiplier)

plot <- ggplot(data, aes(x = parent_gas_used, y = expected_base_fee, color = as.factor(base_fee_max_change_denominator))) +
    geom_point() +
    facet_wrap(~elasticity_multiplier) +  #  我们通过这个分面来拆分图表
    scale_color_brewer(palette = "Spectral") +
    theme_minimal() +
    labs(color = "Base Fee Max Change Denominator") +
    theme_bw()

ggsave("rho-xi.png", plot, width = 14, height = 10)

$`

### 代码 E

````r
library(ggplot2)
library(tidyr)

## 伪指数或泰勒级数展开函数
fake_exponential <- function(factor, numerator, denominator) {
    i <- 1
    output <- 0
    numerator_accum <- factor * denominator
    while(numerator_accum > 0){
      output <- output + numerator_accum
      numerator_accum <- (numerator_accum * numerator) %/% (denominator * i)
      i <- i + 1
    }
    output %/% denominator
}

## Blob Gas 目标
target_blob_gas_per_block <- 393216

## Blob Gas 最大限制
max_blob_gas_per_block <- 786432

 # 用于头部验证
 calc_excess_blob_gas <- function(parent_excess_blob_gas, parent_gas_used) {
   if (parent_gas_used  + parent_excess_blob_gas < target_blob_gas_per_block) {
     return(0)
   } else {
     return(parent_excess_blob_gas + parent_gas_used - target_blob_gas_per_block)
   }
 }

## 这就是 EL 确定 Blob Gas Price 的方式
cancun_blob_gas_price <- function(excess_blob_gas) {
  fake_exponential(1, excess_blob_gas, 3338477)
}

## 我们以 1000 为单位，从 0 逐步增加到最大值
parent_gas_used <- seq(0, max_blob_gas_per_block, by = 1000)
## 相同长度的列
excess_blob_gas <- numeric(length = length(parent_gas_used))
excess_blob_gas[1] <- 0

## 我们通过使用之前的值得到 T+1（时间 + 1）的超额 gas。
for (i in 2:length(parent_gas_used)) {
  excess_blob_gas[i] <- calc_excess_blob_gas(excess_blob_gas[i - 1],
                                             parent_gas_used[i - 1])
}

data_blob_price <- expand.grid(parent_gas_used = parent_gas_used)
data_blob_price```excess_blob_gas <- excess_blob_gas

## 应用 EL gas 价格函数
data_blob_price$  blob_gas_price <- mapply(cancun_blob_gas_price,
                                         data_blob_price$excess_blob_gas)

## 每一行表示一个区块
data_blob_price$BlockNumber <- seq_along(data_blob_price$parent_gas_used)

## 我们将 3 列折叠为 1 个参数列
data_long <- pivot_longer(data_blob_price,
                          cols = c(parent_gas_used,
                                   excess_blob_gas,
                                   blob_gas_price),
                          names_to = "Parameter",
                          values_to = "Value")

ggplot(data_long, aes(x = BlockNumber, y = Value)) +
  geom_line() +
  facet_wrap(~ Parameter, scales = "free_y") +   # 我们根据参数列分解图表
  theme_minimal() +
  scale_y_continuous(labels = scales::label_number()) +
  labs(title = "Dynamic Trends in Blob Gas Consumption & Price Over Time",
       x = "Block Number",
       y = "Parameter Value") +
  geom_text(data = subset(data_long, Parameter == "blob_gas_price" &
                            BlockNumber == min(BlockNumber)),
            aes(label = "blobGasPrice = 1", y = 0),
            vjust = -1, hjust = -0.1, size = 3)

````

### 代码 F

````r
normalize <- function(x) {
  return((x - min(x)) / (max(x) - min(x)))
}

data_blob_price$parent_gas_used_normalized <- normalize(data_blob_price$parent_gas_used)
data_blob_price$excess_blob_gas_normalized <- normalize(data_blob_price$excess_blob_gas)
data_blob_price$blob_gas_price_normalized <- normalize(data_blob_price$blob_gas_price)

ggplot(data_blob_price, aes(x = BlockNumber)) +
  geom_line(aes(y = parent_gas_used_normalized, color = "Parent Gas Used")) +
  geom_line(aes(y = excess_blob_gas_normalized, color = "Excess Blob Gas")) +
  geom_line(aes(y = blob_gas_price_normalized, color = "Blob Gas Price")) +
  theme_minimal() +
  labs(title = "Normalized Trends Over Blocks", x = "Block Number", y = "Normalized Value", color = "Parameter")
````

### 格式化文档的代码

格式化将破坏此文档中的 LaTeX 代码，下面的脚本可以正确格式化 katex 文档。

````bash
#!/bin/bash

sed -i.bck -E ':a;N;$!ba;s/\$\$([^$]+)\$\$/```code2 \1```/g; s/\$([^$]+)\$/```code1 \1```/g' $1
prettier --write $1
sed -i -E ':a;N;$!ba;s/```code1([^`]*)```/\$\1\$/g' $1
sed -i -E ':a;N;$!ba;s/```code2([^`]*)```/\$\$\1\$\$/g' $1
sed -i -E ':a;N;$!ba;s/`code1([^`]*)`/\$\1\$/g' $1
sed -i -E ':a;N;$!ba;s/`code2([^`]*)`/\$\$\1\$\$/g' $1
sed -i -E 's/(\$+)\s*([^$]+?)\s*(\$+)/\1\2\3/g' $1
````

[¹]: https://archive.devcon.org/archive/watch/6/eels-the-future-of-execution-layer-specifications/?tab=YouTube

> [!注意]
> 此 PR 中的所有主题都可以在单独的分支上进行协作

$$

$$
