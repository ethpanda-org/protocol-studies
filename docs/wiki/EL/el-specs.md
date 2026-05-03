# Execution Layer 规范

Execution Layer 最初是在黄皮书中指定的，因为它包含整个 Ethereum。最新的规范是 [EELS python 规范](https://ethereum.github.io/execution-specs/)。

> - [黄皮书，巴黎版本 705168a – 2024-03-04](https://ethereum.github.io/yellowpaper/paper.pdf)(注意：这已过时，未考虑合并后更新)
> - [Python Execution Layer 规范](https://ethereum.github.io/execution-specs/)
> - EIP [查看仓库的自述文件](https://github.com/ethereum/execution-specs)

本页面概述了 EL 规范、其架构和 pyspec 的上下文。

## 状态转换函数

Execution Layer，从 EELS 的角度来看，专门专注于执行状态转换函数(STF)。该角色解决两个主要问题[1]：

- 是否可以将区块附加到区块链的末尾？
- 结果状态如何变化？

简化概述：
<img src="images/el-specs/stf_eels.png" width="800"/>

上图代表黄皮书中的区块级别状态转换函数。

$$
\begin{equation}
\sigma_{t+1} \equiv \Pi(\sigma_t, B)
\qquad (2) \nonumber
\end{equation}
$$

等式中，每个符号代表与区块链状态转换相关的特定概念：

- $\sigma_{t+1}$ 表示应用当前区块后 区块链的**状态，通常称为“新状态”。
- $\Pi$ 表示 [区块 level 状态转换函数](https://github.com/ethereum/execution-specs/blob/0f9e4345b60d36c23fffaa69f70cf9cdb975f4ba/src/ethereum/shanghai/fork.py#L145)，它负责通过应用当前区块中包含的交易将 区块链从一个状态转换到下一个状态。
- $\sigma_t$ 表示添加当前区块之前的 **[区块链](https://github.com/ethereum/execution-specs/blob/0f9e4345b60d36c23fffaa69f70cf9cdb975f4ba/src/ethereum/shanghai/fork.py#L73)** 的状态，也称为“先前状态”。

- $B$ 表示 **[当前区块](https://github.com/ethereum/execution-specs/blob/0f9e4345b60d36c23fffaa69f70cf9cdb975f4ba/src/ethereum/shanghai/fork_types.py#L217)** 正在发送到 Execution Layer 进行处理。

此外，重要的是要理解 $\sigma$ 不应与 Python 规范中定义的 `State` 类混淆。系统的状态不是存储在特定位置，而是通过应用状态崩溃函数动态导出。这突出了区块链状态转换的数学模型与软件规范中的实际实现细节之间的概念分离。

<img src="images/el-specs/state.png" width="800"/>

上图中的 id 如黄皮书(巴黎版)所示：

| ID。 |等式编号|黄纸|评论 |
| ----- | ------------ | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1 | [7](https://ethereum.github.io/yellowpaper/paper.pdf#page=4) | $$TRIE(L_I^*(\sigma[a]_s)) \equiv \sigma[a]_s $$ |在使用函数 $L_I((k,v)) \equiv (KEC(k), RLP(v))$ 映射每个节点后，这给出了帐户存储 Trie、$\sigma[a]_s $ 的根。左边的等式指的是帐户存储 $\sigma[a]_s $ 的基础键和值的映射，这是两个不同的对象，左边的 $\sigma[a]_s $ 和右边的 $\sigma[a]_s $ 代表根哈希 |
| 2 | | [第 4 页](https://ethereum.github.io/yellowpaper/paper.pdf#page=4)，第 2 段 |黄皮书描述账户状态$\sigma[a] $ |
| 3 | [10](https://ethereum.github.io/yellowpaper/paper.pdf#page=4) | $$L_s(\sigma) \equiv \{p(a) : \sigma[a] \neq \empty \} $$ |这是世界状态折叠函数，应用于所有被认为不为空的帐户： |
| 4 & 5 | [39](https://ethereum.github.io/yellowpaper/paper.pdf#page=7) | $$TRIE(L_s(\sigma)) = P(B_H)_{H_{stateRoot}} $$ |该方程将 Parent 区块的 状态根标头定义为 TRIE 函数给出的根，其中 $P(B_H)$ 是 Parent 区块 |
| 6.| [35b](https://ethereum.github.io/yellowpaper/paper.pdf#page=7) | $$H_{stateRoot} \equiv TRIE(L_s(\Pi(\sigma, B))) $$ |这给了我们当前区块的 状态根 |

代码文档中对状态转换函数的指定过程包括以下步骤：

1. **检索 Header**：获取最近添加到链上的区块的 header，称为父级区块。
2. **多余的 blob gas 验证**：从父头中计算多余的 blob gas 并确保其与当前的区块头参数 excess_blob_gas 匹配
3. **标头验证**：将当前区块的标头与父区块的标头进行比较和验证。
4. **Ommers 字段检查**：验证当前区块中的 ommers 字段是否为空。注意：“ommers”是中性术语，取代了之前使用的术语“uncles”。
5. **区块执行**：在区块中执行交易，产生以下输出：
   - **gas 使用**：执行区块中的所有交易消耗的总 gas。
   - **Trie 根**：所有交易和 区块中包含的收据的 Trie 的根。
   - **Logs Bloom**：来自区块内所有交易的日志的布隆过滤器。
   - **状态**：执行完所有交易后的状态，如 python 执行规范中所指定。
6. **标头参数验证**：确认执行区块返回的参数存在于区块标头中。这包括将状态的根与区块标头中的 `state_root` 字段进行比较。
7. **区块添加**：如果所有检查都成功，则将区块附加到区块链。
8. **修剪旧的区块**：从区块链中删除早于最新 255 区块的 区块。
9. **错误处理**：如果任何验证检查失败，则会引发“无效区块”错误。否则，返回 None。

## 区块标头验证

区块标头验证过程在黄皮书和 python 规范中严格定义，基于 Ethereum 协议规则验证区块完整性，例如哈希验证、gas 用法、时间戳准确性等。此验证确保每个区块符合规范中定义的 Ethereum 协议并在客户端中实现。在同步和附加区块期间，通过独立验证当前和历史数据，验证是区块链的一个完整功能。

根据黄皮书的规定，区块标头的[有效性](https://github.com/ethereum/execution-specs/blob/0f9e4345b60d36c23fffaa69f70cf9cdb975f4ba/src/ethereum/shanghai/fork.py#L269) 采用一系列标准来确保每个区块都遵守 Ethereum 的协议要求。父区块 (表示为 $P(H)$)是验证当前区块标头 $H$ 所必需的。有效性的关键条件包括：

$$V(H) \equiv H_{gasUsed} \leq H_{gasLimit} \qquad (57a)$$
$$\land$$
$$H_{gasLimit} < P(H)_{H_{gasLimit'}} + floor(\frac{P(H)_{H_{gasLimit'}}}{1024}) \qquad (57b)$$
$$\land $$
$$H_{gasLimit} > P(H)_{H_{gasLimit'}} - floor(\frac{P(H)_{H_{gasLimit'}}}{1024}) \qquad (57c)$$
$$\land$$
$$H_{gasLimit} > 5000\qquad (57d)$$
$$\land $$
$$H_{timeStamp} > P(H)_{H_{timeStamp'}} \qquad (57e)$$
$$\land$$
$$H_{numberOfAncestors} = P(H)_{H_{numberOfAncestors'}} + 1 \qquad (57f)$$
$$\land$$
$$length(H_{extraData}) \leq 32_{bytes} \qquad (57g)$$
$$\land$$
$$H_{baseFeePerGas} = F(H) \qquad (57h)$$
$$\land$$
$$H_{parentHash} = KEC(RLP(P(H)_H)) \qquad (57i) $$
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
$$H_{blobGasUsed} \leq MaxBlobGasPerBlock_{=786432} \qquad (57p)$$
$$\land $$
$$H_{blobGasUsed} \% GasPerBlob_{=2^{17}} = 0 \qquad (57q)$$
$$\land $$
$$H_{excessBlobGas} = CalcExcessBlobGas(P(H)_H) \qquad (57r)$$
$$\land $$
$$
CalcExcessBlobGas(P(H)_H) \equiv \nonumber \\
\begin{aligned}
&\begin{cases}
0, \qquad \text{if} \space P(H)_{blobGasUsed} < TargetBlobGasPerBlock \\
P(H)_{blobGasUsed} - TargetBlobGasPerBlock
\end{cases}
\quad (57s)
\end{aligned}
$$
$$\land $$
$$
\begin{aligned}
P(H)_{blobGasUsed} \equiv P(H)_{H_{excessBlobGas}} + P(H)_{H_{blobGasUsed}} \\
TargetBlobGasPerBlock = 393216
\end{aligned}
\quad (57t)
$$

- **gas 用法**：区块 $H_{gasUsed}$ 使用的 gas 不得超过 gas 限制 $H_{gasLimit'}$，确保交易适合区块的容量(57a)。
- **gas 限制约束**： 区块的 gas 限制必须保持在相对于父区块的 gas 限制 ${P(H)_{H_{gasLimit'}}}$ 的指定范围内，允许逐渐变化而不是突然调整(57b， 57c)。
- **最小 gas 限制**：最小 gas 限制为 5000，可确保交易处理能力的基本水平 (57d)。
- **时间戳验证**：每个区块的时间戳 $H_{timeStamp}$ 必须大于其父 $P(H)_{H_{timeStamp'}}$ 的时间戳，确保时间顺序(57e)。
- **祖先和额外数据**：区块通过 $H_{numberOfAncestors'}$ 字段维护沿袭，并将 $H_{extraData}$ 大小限制为 32 字节(57f、57g)。
- **经济模型合规性**：每个 gas $H_{baseFeePerGas}$ 的基本费用是根据 EIP-1559 中建立的规则计算的，反映了网络当前对交易处理(57h)的需求。这与 a、b、c、d 和 h 一起定义了经济模型的一部分

### 标头验证和 Ethereum 经济模型

Ethereum 经济模型，如[EIP-1559](https://eips.ethereum.org/EIPS/eip-1559)所述，引入了一系列旨在提高网络效率和稳定性的机制：

- **降低波动性的目标 gas 限制**：通过将 gas 目标设置为最大 gas 限制的一半，Ethereum 旨在减少完整区块可能导致的波动性，确保更可预测的交易处理环境。
- **防止不必要的延迟**：该模型旨在通过优化交易处理时间来消除用户的不当延迟，从而改善网络上的整体用户体验。
- **稳定区块奖励发放**：区块奖励的发放有助于增强系统的稳定性，为参与者提供更可预测的经济格局。
- **可预测的基本费用调整**：EIP-1559 引入了可预测的基本费用变化的机制，这一功能对钱包特别有利。这种可预测性有助于提前准确估计交易成本，从而简化交易创建过程。
- **基本费用销毁和优先费用**：在这种模式下，矿工有权保留优先费用作为激励，而基本费用则被销毁，从而有效地将其从流通中去除。这种方法可以作为 Ethereum 通货膨胀的对策，通过随着时间的推移减少总体供应来促进更健康的经济环境。

其他检查可确保旧版本的兼容性和安全性，例如将 ommer (uncle 区块) 哈希和难度字段设置为预定义值，反映从 Proof-of-Work 到 Proof-of-Stake (57j-57l) 的过渡。

这些标准构成了 Ethereum 经济模型的一部分，特别受到 EIP-1559 的影响，该模型引入了动态基本费用机制。该机制旨在优化网络使用和费用可预测性，增强用户体验和经济稳定性。此外，[EIP-4844](https://eips.ethereum.org/EIPS/eip-4844)引入了一种新型交易，blob 交易，它增强了 EIP-1559 的经济模型。

让我们更深入地探讨这个问题，并尝试更好地理解这些方程的情况，这些方程在 Python 规范或黄皮书中都不容易看到。

让我们从扩展 57h 开始，黄皮书中指定为：

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
\tau \equiv \frac {P(H)_{H_{gasLimit}}} {\rho} \qquad (46)
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

|符号|它代表什么|价值|评论 |
| --------------- | ------------------------------------------ | -------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| $F(H) $ |每 gas 的基本费用 | |作为总费用的一部分支付给发送者，基本费用最终被 Execution Layer 销毁并从系统中取出 |
| $\nu $ |基本费用的幅度增加或减少| |与 Parent 区块的 gas 消耗和 gas 目标之间的差异成正比|
| $\tau $ | gas 目标 | $\frac {P(H)_{H_{gasLimit}}} {\rho}_{= 2}$ |为了减少波动性，gas 目标设置为 gas 限制的一半，以中等每个区块的 交易吞吐量。 |
| $\rho $ |弹性乘数| 2 |有助于调整 gas 目标，以保持网络响应能力、容量和价格可预测性。 |
| $\xi $ |基本费用最大分母 | 8 |它控制基本费用的最大变化率，确保逐步调整。 |

此外，黄皮书对这些对象的类型有一些重要的定义，这些定义将用于推理这些方程：

首先它为我们提供了无界的区块限制，即这些限制可以无限扩展

$$
H_{\text{gasUsed}} , H_{\text{gasLimit}}, H_{\text{baseFeePerGas}} \in \mathbb{N} \qquad (41)
$$

然后它为我们提供了交易参数的类型，它们的最大值为 2^256 或大约 10^77，这是这些数字可以达到的最大值

$$T_{\text{maxPriorityFeePerGas}} , T_{\text{maxFeePerGas}}, T_{\text{gasLimit}}, T_{\text{gasPrice}} \in\mathbb{N}_{256}$$

#### 其他见解：自然数的意义

Ethereum 协议指定区块级参数，例如使用的 gas ($H_{gasUsed}$)、gas 限制 ($H_{gasLimit}$) 以及每个 gas 的基本费用($H_{baseFeePerGas}$)—作为自然数 $\mathbb{N}$ 这个决定远非任意；它将一层直观的逻辑嵌入到区块链的基础经济学中。

- 自然数和区块链逻辑

从 0 开始并无限延伸的自然数为理解和操作这些参数提供了一个简单的框架。与实数不同，实数在任意两点之间包含不可数的无穷大，而自然数允许精确、离散的步骤，这使得它们非常适合精度至关重要的区块链交易。此属性简化了有关操纵这些参数的函数的推理，有助于精确计算和预测交易成本和网络容量。

- 简单而精确

考虑递增的简单性：每个自然数可以被视为 (0 + 1 + 1 + ... + 1) 的总和，为智能合约或 交易处理中的递增或递减提供清晰的路径。自然数的这种原子性质，以及 0 和后继(+ 1)基础构建区块，使得能够在 Ethereum 区块链内构造健壮且可证明的逻辑，换句话说，自然数导致更容易的证明。

- 与实数(小数)对比

与实数的无限可分性相反，Ethereum 经济模型中自然数的离散性质确保了运算保持在可计算的范围内。这种区别对于维护网络效率和安全性、避免与处理实数相关的计算复杂性和潜在漏洞至关重要。

**交易参数和有界自然数**

此外， Ethereum 指定交易参数，例如在自然数 $\mathbb{N}_{256}$ 的有界子集中每个 gas 的最大优先级费用和每个 gas 的最大费用。此边界上限为 $2^{256}$ 或大约 $10^{77}$，在允许交易处理的大范围值与确保这些值保持在安全、可管理的限制内之间取得平衡。

#### gas 价格区块至 区块动态

让我们深入了解 gas 价格计算函数的动态，探索其对一系列 gas 使用场景的影响，范围从最小可能(5,000 个单位)到设置的 gas 限制。我们的重点是了解该函数在单个区块范围内的执行情况。

我们的目标是分析“计算每个 gas 的基本费用”函数，这对于理解 Ethereum 的 gas 定价机制至关重要。下面的 R 代码片段说明了该函数的实现：

<img src="images/el-specs/gasused-basefee.png" width="800"/>

从情节中观察：

- 该函数呈现阶梯状线性级数，在中点处方差最大。这反映了 gas 目标，设置为 gas 限制的一半(在本例中为 15,000 个单位)。
- 基本费用的最大向上变化约为 12.5%，在图的最右侧观察到。这代表基本费用从 100 起时可能增加的最大金额。
- 基本费用的最大向下变化约为 10%，在图的最左侧观察到。这代表当基本费用从 100 开始时可能的最大减少量。
- 精确命中 gas 目标会导致基本费用增加 1%。稍微超过目标(例如，使用 15,000 到 17,000 个单位的 gas)仍然只会导致 1% 的增长，这说明了该函数围绕目标设计的弹性。

#### 扩展模拟：对 gas 限额和费用的长期影响

可视化了 gas 价格计算函数对一系列 gas 使用场景的直接影响后，让我们考虑一下它在较长一段时间内的影响。具体来说，这种动态如何影响数以万计的区块上的 Ethereum 网络，特别是在每个区块达到其 gas 限制的最大需求条件下？

下图模拟了超过 100,000 区块的情况，假设最大需求恒定，以预测 gas 限制和基本费用的演变：

<img src="images/el-specs/gas-limit-max.png" width="800"/>

模拟观察揭示了几个重要的见解：

- 基本费用敏感性：以 wei 为单位的基本费用迅速上升，在持续最大需求下，可能在 200 区块内达到 1 个以太币。
- 达到上限的潜力：在持续高需求的情况下，基本费用可能会在 2,000 区块以内接近其理论最大值。
- 无限制的 gas 限制增长：与基本费用不同，gas 限制本身没有上限，允许持续增长以适应不断增长的网络需求。
- 市场动态和均衡：现实世界的需求增加(最初反映在区块超过了 gas 目标)导致基本费用上涨。然而，随着 gas 限额逐渐提高，gas 目标(gas 限额的一半)也随之上升，最终稳定了较高基础费用的需求，达到新的平衡。

通过更精细地检查模型的基础来确保我们的分析面向未来。具体来说，我们关注改变模型核心常数的影响，特别是弹性乘数 ($\rho$) 和基本费用最大变化分母 ($\xi$)。这些常量预计不会在分叉内改变，但可以在未来的协议升级中重新指定：

让我们从 $\xi$ 开始：

<img src="images/el-specs/xi.png" width="800"/>

这是区块之间的快照，就像我们的第一个图一样，它代表了跨协议升级由 $\xi$ 额外参数化的经济模型潜力的最小部分

$\xi$ 对基本费用的影响：

- 拐点和步宽变化：随着 $\xi$ 的变化，“扭结”或拐点变得特别明显，随着 $\xi$ 的增加而变得更宽。因此，增加 $\xi$ 会导致更宽的步长，表明费用调整更加渐进。相反，减少 $\xi$ 会导致步幅更窄，费用变化更不稳定。
- 敏感性：基本费用调整曲线的斜率在拐点之后发生显着变化。随着 $\xi$ 值的下降，我们观察到费用调整率急剧增加，表明敏感性提高。
- 目标范

接下来，我们将注意力转向弹性乘数($\rho$)，它是 Ethereum 经济模型中的另一个关键常数，直接影响 gas 限额调整的灵活性和响应能力。为了了解其影响，我们结合基本费用最大变化分母 ($\xi$) 的变化，探索了从 1 到 6 的 $\rho$ 值范围。

<img src="images/el-specs/rho-xi.png" width="800"/>

$\rho$ 和 $\xi$ 对基本费用的影响：

- 即时分析：与我们最初的观察类似，该图提供了 $\rho$ 和 $\xi$ 的调整如何在每个区块的基础上塑造经济模型的行为的精细视图，特别是在协议升级的背景下。
- $\rho$ 的独特影响：每个子图代表不同 $\rho$ 值的影响。作为弹性乘数，$\rho$ 显着地改变了基本费用调整曲线的拐点，突出了其在调整网络对交易交易量波动的响应能力方面的作用。
- $\rho$ 和 $\xi$ 之间的相互作用：弹性乘数 ($\rho$) 不仅会移动拐点，还会调节由于基本费用最大变化分母 ($\xi$) 变化而导致的调整敏感性。这种互动强调了 Ethereum 保持的微妙平衡，以确保在不同需求下的网络效率和稳定性。

<img src="images/el-specs/gas-header.png" width="800"/>

#### blob gas 价格动态

blob gas 价格的动态在以下场景中建模，从零开始，然后将每个区块使用的 gas 按常数因子 1000 从一个区块增加到下一个。

- 图 E：说明了 blob gas 与其价格之间的关系。所有数字的代码在附录中

<img src="images/el-specs/blob-gas-and-price.png" width="800"/>

- 图 F：标准化数据以突出显示与 gas 使用相关的价格动态。
  <img src="images/el-specs/blob-gas-and-price-norm.png" width="800"/>

- 当父 gas 的 gas 使用率低于目标(~400K，相当于每个区块大约 400KB 或 3 个 blob)时，blob gas 价格保持在 1。最大大约 800K 映射到大约 800KB 或每个区块 6 个 blob。
- 超过目标不会立即影响 gas 价格，但超出的 gas 开始累积。
- 持续的需求增加，导致累积的过剩 gas 超过阈值，引发 gas 价格指数级上涨作为监管措施。
- 如果前一个区块的 gas 使用率低于目标，则可以在一个区块中清除累积的多余 gas，从而重置调整机制。

## 区块执行流程

初始标头验证后，区块进入执行阶段([apply_body](https://github.com/ethereum/execution-specs/blob/804a529b4b493a61e586329b440abdaaddef9034/src/ethereum/cancun/fork.py#L437))。尽早执行标头检查允许状态转换函数 (STF) 有可能向 Consensus Layer (CL) 返回“无效载荷”消息，而无需继续进行计算密集型阶段区块/交易执行。

1. **将 `blobGasUsed` 初始化为 0。** 这会将区块中的交易使用的 gas 的起点设置为零。
2. **将 `gasAvailable` 设置为 $H_{gasLimit}$。** 这会将可用于区块执行的 gas 初始化为区块的 gas 限制。
3. **初始化其他执行组件：** 这包括设置收据的 Trie、提款的 Trie 和区块日志元组(其行为类似于不可变列表)，确保可用的 gas 与区块的一致 gas 限制。
4. **通过指定 **BEACON ROOTS ADDRESS** 的 EL 常量访问 Beacon 区块根合约代码**：
   - 此功能在 Cancun 中介绍，并在 [EIP 4788](https://eips.ethereum.org/EIPS/eip-4788) 中详细介绍，允许使用 Beacon Chain 区块根作为加密累加器来构造 Consensus Layer 状态的证明。这为 EVM 提供了一种信任最小化的方式来访问 Consensus Layer 数据，支持质押池、重新质押操作、智能合约桥接和 MEV 缓解等应用程序。 [了解更多](https://www.youtube.com/watch?v=GriLSj37RdI) 来自规范创建者。
5. **构建系统交易消息：**以**系统地址**为调用方，**BEACON ROOTS ADDRESS**为目标，包含$H_{parentBeaconBlockRoot}$和检索到的合约代码。这在 Cancun 中引入了一个“系统契约”，一个有状态的智能合约，与无状态预编译不同，无状态预编译只有系统地址才能插入数据。
6. **设置 VM 环境并处理消息调用，**将 $H_{parentBeaconBlockRoot}$ 存储在合约的存储中，以便稍后通过提供时隙时间戳的交易进行检索。
7. **删除前面步骤中触及的空帐户**以清理状态。
8. **在区块中处理交易：**
   - 交易被解码并添加到交易 Trie 中执行。
   - **执行[交易](/wiki/EL/transaction)：**对于区块执行过程至关重要，这涉及到：
     1. 使用签名组件 $T_v, T_r, T_s$ 恢复交易发件人地址。
     2. 验证内在交易有效性。
     3. 计算有效 gas 价格。
     4. 初始化执行环境。
     5. **在虚拟机内执行解码后的交易**，包括针对当前状态进行验证、gas 计算以及成功后应用状态更改。
9. **处理验证者提款**由 Beacon Chain ([EIP-4895](https://eips.ethereum.org/EIPS/eip-4895)) 验证：
   - 迭代每个 [Withdrawal](https://github.com/ethereum/execution-specs/blob/119208cf1a13d5002074bcee3b8ea4ef096eeb0d/src/ethereum/shanghai/fork_types.py#L178)，将它们添加到 Trie。
   - 将 Gwei 提款转换为 Wei 并存入指定地址。
   - 销毁空的提款账户以保持干净的状态。

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

|变量|描述 |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| $I_{caller}$ |启动代码执行的地址；通常是交易的发送者。 |
| $I_{origin}$ |发起此执行上下文的交易的原始发送者地址。 |
| $I_{blockHashes}$ |最后 255 个区块中的哈希的集合。 |
| $I_{coinbase}$ | 区块奖励和交易费用的收款人地址。 |
| $I_{number}$ | 区块链中当前区块的序号。 |
| $I_{gasLimit}$ |可用于执行交易的 gas 的最大数量，占当前区块中已使用的 gas。 |
| $I_{baseFeePerGas}$ |每个 gas 单位的基本费用，是一个根据区块空间需求进行调整的动态参数。 |
| $I_{gasPrice}$ | gas 的有效价格，受当前网络状况和交易紧急程度的影响。 |
| $I_{time}$ |生成区块时的时间戳标记，自 Unix epoch 开始以秒为单位测量。 |
| $I_{prevRandao}$ |先前的 RANDAO(随机性)值，有助于从 Beacon Chain 生成区块中的熵。 |
| $I_{state}$ |当前状态，包括所有账户余额、存储和合约代码。 |
| $I_{chainId}$ | 区块链的标识符，确保交易为特定链签名。 |
| $I_{traces}$ |执行跟踪的占位符，用于将来使用或调试目的。 |
| $I_{excessBlobGas}$ |从父区块计算，它代表为 blob 交易分配的剩余 gas。 |
| $I_{blobVersionedHashes}$ | 附加到当前交易的 blob 的版本化哈希的有序列表。 |

## gas 会计

### 内在 gas 计算

内在 gas 表示交易开始执行所需的最小 gas。该成本包括 EVM 所需的计算资源以及与数据传输相关的成本。从交易的 $T_{gasLimit}$ 中减去内在 gas，以在 EVM 内设置执行上下文。

更新以与上海规范保持一致，内在 gas 公式如下：

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
\sum_{j=0}^{ length(T_{accessList}) - 1} \left(G_{\text{accesslistaddress}} + length(T_{accessList}[j]_s) * G_{\text{accessliststorage}} \right)
$$

#### 内在 gas 组件：

|组件|描述 |
| ------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $g_0$ |表示交易的总内在 gas 成本，涵盖初始代码执行和数据传输。 |
| $G_{\text{transaction}}$ |每个交易的基本成本设置为 21000 gas。 |
| $T_{\text{initializationCode}}$ |当 $T_{to} = 0_{\text{Bytes}}$ 时，CALLDATA 被视为 $T_{\text{initializationCode}}$。成本标准化为 32 字节间隔。 |
| $T_{inputData}$ 和 $T_{initializationCode}$ | $T_{inputData}$ 和 $T_{initializationCode}$ 共同表示交易的 CallData 参数。如果 $T_{to} \neq 0_{Bytes}$，则 CALLDATA 被视为合约入口点的输入。处理 CALLDATA 的 gas 成本被定义为每个非零字节 16 个 gas 和每个零字节 4 个 gas，影响区块大小，并由于处理增加而可能产生网络延迟。这个 gas 成本模型基于区块创建率、链增长率和网络延迟的平衡，最初针对 Proof-of-Work 系统进行了优化。将该模型应用于 Proof-of-Stake 仍然是一个研究机会和未来优化的领域。这些参数被定义为无限大小的字节数组，每个非零字节的初始化成本设置为 16 gas，每个零字节的初始化成本设置为 4 gas。更多 |
| $G_{\text{txCreate}}$ |创建合约交易需要额外 32000 gas。 |
| $G_{\text{accesslistaddress}}, G_{\text{accessliststorage}}$ |访问列表中指定的每个地址和存储密钥的额外 gas 成本，有利于优化状态访问。 |

### 有效 gas 价格及优先费

下面的等式已修改为包括 blob 交易 ($T_{type} = 3$)

$$ p \equiv effectiveGasPrice \equiv
\开始{对齐}
&\开始{案例}
T_{gasPrice}, & \text{if} \space T_{type} = 0 \lor 1\\
优先费 + H_{baseFeePerGas} , & \text{if} \space T_{type} = 2 \lor 3
\end{案例}\\
\end{对齐} \qquad (62)
$$

$$ f \equiv priorityFee \equiv
\begin{aligned}
&\begin{cases}
T_{gasPrice} - H_{baseFeePerGas}, & \text{if} \space T_{type} = 0 \lor 1\\
min(T_{maxPriorityFeePerGas} , T_{maxFeePerGas} - H_{baseFeePerGas}) , & \text{if} \space T_{type} = 2 \lor 3
\end{cases}\\
\end{aligned}
$$

| | |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------- |
|有效汽油价格 | 交易签名者在执行交易期间将为消耗的每单位 gas 支付的 wei 金额 |
|优先费| 交易的受益人在执行交易期间消耗的每单位 gas 将收到的 wei 金额 |

### 有效 gas 费用

$$effectiveGasFee \equiv effectiveGasPrice \times T_{gasLimit} $$
作为前期成本的一部分扣除

### 总计 blob gas

$$totalBlobGas \equiv (G_{gasPerBlob = 2^{17}} \times length(T_{blobVersionedHashes})) $$

### blob gas 价格

blob gas 价格是通过根据网络中产生的多余 blob gas 进行调整的公式确定的。公式如下：

$$
blobGasPrice \\ \approx \\
factor_{minBlobBaseFee = 1} \times e^{numerator_{excessBlobGas} / denominator_{blobGaspriceUpdateFraction = 3338477}}
$$

- 对于每区块(设置为 786432)当前最大 blob gas 下的任何输入，如果尚未累积超出的 gas，则该公式返回 1。
- 然而，当目标超过区块时，它开始增加，这导致超出 blob gas 参数开始累积，这会触发 blob gas 价格呈指数级增长
- 将目标设置为每个区块 (393216) 的最大值 blob gas 的大约一半时，该函数开始显示在目标的 10 倍处增加到值 2，之后呈指数增长。


### blob gas 费用

$$blobGasFee \equiv totalBlobGas \times blobGasPrice $$

### 最大 gas 费用

$$
 maxGasFee \equiv
\begin{aligned}
&\begin{cases}
T_{gasLimit} \times T_{gasPrice} , & \text{if} \space T_{type} = 0 \lor 1\\
T_{gasLimit} \times T_{maxFeePerGas} , & \text{if} \space T_{type} = 2 \\
(T_{gasLimit} \times T_{maxFeePerGas}) + maxBlobFee , & \text{if} \space T_{type} = 3
\end{cases}\\
\end{aligned}
$$

$$
maxBlobFee \equiv
T_{maxFeePerBlobGas} \times totalBlobGas
$$

### 前期成本

$$
v_0 \equiv upfrontCost \equiv effectiveGasFee + blobGasFee
$$

## 交易执行

在 Ethereum 网络中执行交易的过程由交易级别状态转换函数控制：

$$\Upsilon(\sigma_t, T_{index}) \qquad (4)$$

在调用$\Upsilon$时，系统首先验证交易的内在有效性。验证后，[Ethereum 虚拟机](/wiki/EL/evm) (EVM) 将根据交易的指令启动状态修改。

### 交易内在有效性

交易的内在有效性是通过一系列检查确定的：

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
T_{gasLimit} \leq Header_{gasLimit} \nonumber \\ &− last(\left[ Block_{receipt} \right])_{cumulativeGasUsed} \\
\end{align}
$$

$$ \text{哪里，}m \equiv
\开始{对齐} \\
&\开始{案例}
T_{gasPrice}, & \text{if} \space T_{type} = 0 \lor 1\\
T_{maxFeePerGas} , & \text{if} \space T_{type} = 2 \lor 3
\end{案例}\\
\结束{对齐}
$$

And $EMPTY(\sigma, account)$ is defined as an account with no code, zero nonce, and zero balance:

$$
\开始{对齐}
EMPTY(\sigma, 帐户) \nonumber \\ \equiv \nonumber \\
\sigma[account]_{code} = \text{KEC}(\emptyset) \nonumber \\ \land \nonumber \\
\sigma[account]_{nonce} = 0 \nonumber \\ \land \nonumber \\
\sigma[账户]_{余额} = 0 \nonumber \\
\结束{对齐}
$$

| | |
| --- | ---------------------------------------------------------------------------------------------------------------- |
| 1 | The transaction sender must exist and cannot be an uninitialized account |
| 2 | The sender cannot be a contract |
| 3 | Transactions from an account are capped, ensuring a nonce less than $2^{64} - 1$. |
| 4 | The size of input data or CALLDATA must not exceed twice the maximum code size (24576 bytes). |
| 5 | The transaction's nonce must match the current nonce of the sender in the state |
| 6 | The intrinsic gas calculation must not exceed the transaction's gas limit. |
| 7 | The sender must have sufficient balance to cover the maximum gas fee plus the value being sent. |
| 8 | Ensures the transaction meets the minimum base fee per gas of the block |
| 9 | For EIP-1559 transactions, the max fee per gas must be at least as high as the max priority fee per gas |
| 10 | The transaction's gas limit, plus the gas used by previous transactions in the block, must not exceed the block' |

### $T$ Execution stage 1 : checkpoint state $\sigma_0$

The initial stage of transaction execution includes the following steps:

1. **Validate Transaction**: Assess the transaction's validity; if it passes, changes to the state are irrevocably initiated.
2. **Deduct Intrinsic Gas**: The intrinsic gas amount $g_0$ is subtracted from the transaction's gas limit to establish the gas parameter for message preparation: $gas = T_{gasLimit} - g_0$.
3. **Increment Sender's Nonce**: Reflect an irrevocable change in the sender's state by incrementing the nonce.
4. **Deduct Upfront Cost**: The sender's balance is reduced by the upfront cost, another irreversible change to the state.

$$\sigma_0 \equiv \sigma \space \text{except:} $$
$$\sigma_0[Sender]_{balance} \equiv \sigma[Sender]_{balance} - upfrontCost_{\nu_0} $$
$$\sigma_0[Sender]_{nonce} \equiv \sigma[Sender]_{nonce} + 1 $$

This checkpoint state represents the modified state after initial validations and deductions, setting the groundwork for subsequent execution steps.

### $T$ Execution stage 2 : Transaction Normalization and Substate initialization

EVM executions fundamentally require just an environment and a message. Therefore, transactions within a transaction envelope, which categorize transactions by type, are streamlined into four main types. These transactions are then unified into a singular Message Data structure, delineating two main actions: initiating contract creations and executing calls to addresses. Notably, for transactions predating EIP-1559 that lack a base fee, they undergo normalization to integrate the [Gas price](https://github.com/ethereum/go-ethereum/blob/100c0f47debad7924acefd48382bd799b67693cf/core/state_transition.go#L168) during their transformation into the message format. Moreover, the execution path is determined based on the $T_{to}$ parameter:

- if $T_{to} = 0Bytes$ : Proceed with execution of contract creation
- if $T_{to} = Address$ : Proceed with execution of a call

This maps to the internal Message type in EELS as :

$$
消息(调用者，目标，gas，值，\\数据，代码，深度，当前目标，\\ codeAddress，shouldTransferValue，isStatic，\\ preAccessedAddresses，preAccesedStorageKeys，\\parentEVM)
$$

| Message Field parameter | Initial Call Value | Initial Creation Value | Execution Environment Forward Mapping |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| caller | Recovered Sender Address | Recovered Sender Address | $I_origin$ or $I_{sender}$ |
| target | $T_{to}$ is a valid Address | $T_{to} = 0_{bytes}$ | |
| gas | $T_{gasLimit} - intrinsicCost$ | $T_{gasLimit} - intrinsicCost$ |
| value | $T_{value}$ | $T_{value}$ | yellow paper: $I_v$ or $I_{value}$ |
| data | $T_{data}$ | $0_{bytes}$ | yellow paper: $I_d$ or $I_{data}$ |
| code | $(T_{to})_{code}$ | $T_{data}$ | yellow paper: $I_b$ or $I_{[byteCode]}$ |
| depth | $0$ | $0$ | yellow paper: $I_e$ or $I_{depth}$ |
| currentTarget | $T_{to}$ | We compute the contract address by taking the last 20 bytes of $KEC(RLP([Sender_{address}, Sender_{nonce} -1]))$ |
| codeAddress | $T_{to}$ default except when an alternative accounts code needs execution . e.g. 'CALLCODE' calling a precompile | | yellow paper: $I_a$ or $I_{codeOwnerAddress}$ |
| shouldTransferValue | default is True, indicates if ETH should be transferred during executing this message | default is True |
| isStatic | default is False, indicates is State Modifications are allowed (false means state modifications are allowed) | default is False | inversely related to yellow paper: $I_w$ or $I_{permissionToModifyState}$ |
| accesslistAddress | See below | - |
| accesslistStorageKeys | - | - |
| parentEvm | initially None | initially None |

#### Substate initialization

The initialization of the substate sets the groundwork for transaction execution, defined as follows:

- **Self-Destruct Set**: Initially empty, indicating no contracts are marked for self-destruction.
- **Log Series**: Starts as an empty tuple, ready to record logs produced during execution.
- **Touched Accounts**: Also begins empty, listing accounts that become "touched" through the transaction.
- **Refund Balance**: Set to 0, accounting for gas refunds that may accumulate.

Depending on the transaction type, accessed addresses are initialized differently:

- For $T_{type} = 0$, the coinbase address, the caller , the current target and all the pre-compile contract addressees are added to the accessed account substate
- For $T_{type} = 1, 2, or \space 3$, the coinbase address , the caller , the current target, all the pre-compiles and those in the access list are added

$$A^* \equiv (A^{*}_{selfDestructSet} = \empty, $$
$$A^{*}_{logSeries} = (), $$
$$A^{*}_{touchedAccounts} = \empty, $$
$$A^{*}_{refundBalance} = 0 , $$
if $T_{type} = 0$:
$$A^{*}_{accesedAccountAddresses} = \{ H_{coinBase},$$
$$Message_{caller}, Message_{current_target} $$
$$allPrecompiledContract_{addresses}\}$$
$$A^{*}_{accesedStorageKeys} = \empty $$
if $T_{type} = 1 \lor 2 \lor 3$:
$$A^{*}_{accesedAccountAddresses} = \{ H_{coinBase},$$
$${ \bigcup_{Entry \in T_{accessList}} \{ Entry_{address} \}},$$
$$Message_{caller}, Message_{current_target} $$
$$allPrecompiledContract_{addresses}\}$$
$$A^{*}_{accesedStorageKeys}= $$
$${ \bigcup_{Entry \in T_{accessList}} \{ \forall i < length(Entry_{storageKeys}), i \in \mathbb{N} : (Entry_{address_{20byte}}, Entry_{storageKeys}[i]_{32byte} \}}$$

`A_{accessedAccountAddresses}` and `A_{accessedStorageKeys}` leverage the mechanism introduced by [Ethereum Access Lists (EIP-2930)](https://eips.ethereum.org/EIPS/eip-2930), detailed further in [this EIP-2930 overview](https://www.rareskills.io/post/eip-2930-optional-access-list-ethereum). This approach creates a distinction in gas costing between addresses and storage keys declared within the transaction's access list (incurring a "warm" cost) and those not included (incurring a "cold" cost). For comprehensive details on the gas costs associated with cold and warm accesses, please refer to [EIP-2929: Gas cost increases for state access opcodes](https://eips.ethereum.org/EIPS/eip-2929), which adjusts the costs to account for state access operations within the EVM.

$A_{accesedAccountAddresses}$ and $A_{accesedStorageKeys}$ belong to [Ethereum Access lists](https://www.rareskills.io/post/eip-2930-optional-access-list-ethereum) [ EIP ](https://eips.ethereum.org/EIPS/eip-2930) which makes a [cost](https://eips.ethereum.org/EIPS/eip-2929) distinction between the addresses the transaction declares it will call and others. The ones outside the access list have have only a cold cost of account access set at 2600 each time we call the address or 2100 when we access the state. Where as the access list eip specifies that the subsequent calls to the state and account access ,termed "warm cost", will incur a gas of 100. [EIP 3651](https://eips.ethereum.org/EIPS/eip-3651) added the coinbase to the list of accounts that need to be warm before the start of the execution.

#### Message Type : Contract Creation

In the context of the Ethereum Yellow Paper, contract creation is represented by the function:

$$
\Lambda(\sigma, A, s, o, g, p, v, i, e, \zeta, w) \\ 或 \\
\Lambda(state_{\sigma}, AccruedSubState_{A} , sender_s , originalTransactor_o ,\\ availableGas_g , effectiveGasPrice_p , \\ endowment_v, []evmInitCodeByteArray_i , stackDepth_e , \\ saltForNewAccountAddress_{\zeta}, stateModificationPermission_w)
$$

| $\Lambda$ Call Parameter | Mapping | Notes |
| ----------------------------------------- | -------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| $state_{\sigma}$ | $I_{state}$ | The current state before contract creation begins. |
| $AccruedSubState_{A}$ | $A^0$ | Represents the initial substate. |
| $sender_s$ | $I_{origin}$ | The origin address of the transaction, initiating the contract creation. |
| $originalTransactor_o$ | $I_{origin}$ | The original transactor, identical to the sender in direct transactions. |
| $availableGas_g$ | $T_{gasLimit} - intrinsicCost$ | The gas available for the contract creation, after deducting the intrinsic cost of the transaction. |
| $effectiveGasPrice_p$ | $I_{gasPrice}$ | The gas price set for the transaction. |
| $endowment_v$ | $T_{value}$ | The value transferred to the new contract. |
| $[]evmInitCodeByteArray_i$ | $T_{CALLDATA}$ | The initialization bytecode for the new contract. |
| $stackDepth_e$ | 0 | The depth of the call stack at the point of contract creation; initially 0. |
| $saltForNewAccountAddress_{\zeta}$ | $\emptyset$ | Salt used for generating the new contract's address, non empty for create2 operations. |
| $stateModificationPermission_w$ | True, inversely referred by the `is_static` parameter in the Message object, which is set to false | Indicates if the contract creation can modify the state. |

Note: $originalTransactor_o$ can differ from $sender_s$ when the message is not directly triggered by a transaction but rather comes from the execution of EVM code, indicating the versatility of message origination within the EVM execution context.

The process of creating a contract begins with determining the contract's address. In EELS, this task is part of message preparation. Other clients may handle it at a different stage. The steps are as follows:

1. **Compute the Contract Address:**

   - The contract address is [computed](https://github.com/ethereum/execution-specs/blob/db87f1b1d21f61275fc34b08de1735889c01f018/src/ethereum/cancun/utils/address.py#L42) by hashing the sender's address and nonce (decremented by one, as the nonce is incremented prior to this operation) using the formula: $KEC(RLP([Sender_{address} , Sender_{nonce} - 1]))$. This adjustment accounts for the nonce at the transaction's issuance.
   - Next, extract the last 20 bytes of this hash: $KEC(RLP([Sender_{address} , Sender_{nonce} - 1]))[-20:]$.
   - If the resulting length is less than 20 bytes, left-pad with zero-byte words to form a 20-byte address.

2. **Initialize the New Account:**

$$
\sigma^*[newAccount] \equiv (Nonce_{=1}, Balance_{=preexistingValue + T_{value} }, Storage_{=TRIE(\empty)}, CodeHash_{=KEC(())})
$$

The state of the new account is established with:

- A nonce set to 1.
- The balance set to the sum of the transferred value and any pre-existing balance.
- An empty storage.
- A code hash derived from an empty tuple: KEC(()).

3. **Update the Sender's Balance:**
   The sender's balance is adjusted by subtracting the transaction value:

$$
\sigma^*[发件人]_{余额} \equiv \sigma[发件人]_{余额} - T_{值}
$$

4. **Account Initialization:**
   Finally, the account is initialized through the execution of the EVM initialization code byte array $[]evmInitCodeByteArray_i$ during the main execution cycle.

#### Message Type: Call

In the context of the Ethereum Yellow Paper, a message call is represented by the function:

$$
\Theta(\sigma, A, s, o, r, c, g, p, v, \tilde{v}, d, e, w) \\ 或 \\
\Theta(state_{\sigma}, AccruedSubState_{A}, sender_s, originalTransactor_o, recipient_r, \\ codeAddress_c, availableGas_g, effectiveGasPrice_p, value_v, \\ 表观值_{\tilde{v}}, callData_d, stackDepth_e, stateModificationPermission_w)
$$

| $\Theta$ Call Parameter | Mapping | Notes |
| ----------------------- | ------- | ----- |
| $state_{\sigma}$ | $I_{state}$ | The current global state before message call execution begins. |
| $AccruedSubState_{A}$ | $A$ | Represents the accumulated substate prior to the message call, including logs and refunds. |
| $sender_s$ | $I_{sender}$ | The immediate caller of the message call, which may be an externally owned account or contract. |
| $originalTransactor_o$ | $I_{origin}$ | The original external account that initiated the transaction and remains constant across calls. |
| $recipient_r$ | $I_{recipient}$ | The address whose balance is adjusted and whose storage may be modified by the call. |
| $codeAddress_c$ | $I_{code}$ | The address whose code is executed, typically equal to the recipient. |
| $availableGas_g$ | $I_{gas}$ | The amount of gas available for execution of the message call. |
| $effectiveGasPrice_p$ | $I_{gasPrice}$ | The gas price used for gas accounting during execution. |
| $value_v$ | $I_{value}$ | The amount of ether transferred from the sender to the recipient. |
| $apparentValue_{\tilde{v}}$ | $I_{apparentValue}$ | The value visible to the executing code, differing in DELEGATECALL. |
| $callData_d$ | $I_{callData}$ | Arbitrary-length byte array supplied as input data to the call. |
| $stackDepth_e$ | $I_{depth}$ | The depth of the message call stack at the point of execution. |
| $stateModificationPermission_w$ | $I_{isStatic}$ | Indicates whether state modification is permitted; false for STATICCALL. |

---

### Execution Result

The evaluation of a message call produces a 5-tuple: $(σ′, g′, A′, z, o)$. After execution begins, the EVM evaluates the call using the following logic:

1. **Execution of Account Code**
   If the recipient account $r$ exists and contains code, the EVM executes the bytecode $\sigma[r]_c$ using the execution function $\Xi$. If no code exists, it is treated as a no-op (only value transfer occurs).

2. **Exceptional Halting and State Reversion**
   If execution halts due to an exception (e.g., OOG), the state reverts to the point before the transfer:
   $$\sigma' = \sigma \text{ if } \sigma'' = \emptyset, \text{ else } \sigma''$$

3. **Gas and Substate Commitment**
   - **Gas:** If failed, all gas is consumed ($g'=0$). If successful, remaining gas $g''$ is returned.
   - **Substate:** Logs and refunds ($A'$) are only committed if execution is successful ($\sigma'' \neq \emptyset$).

4. **Execution Status Code ($z$)**
   - $z = 0$ if execution failed ($\sigma'' = \emptyset$).
   - $z = 1$ if execution succeeded.

5. **Precompiled Contracts**
   If the recipient $r$ is within the set $\pi = \{1, 2, 3, 4, 5, 6, 7, 8, 9\}$, execution is redirected to native functions:

| Address | Function |
| ------- | -------- |
| 1 | ECDSA public key recovery |
| 2 | SHA-256 hashing |
| 3 | RIPEMD-160 hashing |
| 4 | Identity function |
| 5–8 | Elliptic curve operations (alt_bn128) |
| 9 | BLAKE2 compression function |

---

**Implementation Reference:**
The semantics described above are implemented in the [Ethereum Execution Layer Specification (EELS)](https://github.com/ethereum/execution-specs). Relevant modules include:
- `ethereum/execution/message.py`
- `ethereum/execution/call.py`
- `ethereum/execution/evm.py`
### $T$ Execution Stage 3 : Main Execution ($\Xi) $

#### [Machine](/wiki/EL/evm?id=evm) State $\mu$

$$\mu \equiv (\mu_{gasAvailable}, \mu_{programCounter},\\ \mu_{memoryContents}, \mu_{activeWordsInMemory},\\ \mu_{stackContents}) $$
| | initial Value | notes |
|-|-|-|
|$$\mu$$ | | Initial Machine State |
|$$\mu'$$ | | Resultant Machine State after an operation where $\mu' \equiv \mu \space \text{except :} \\ \mu'_{gas} \equiv \mu_{gas} - C_{generalGasCost}(\sigma, \mu, A, I) $ |
|$$\mu_{gasAvailable}$$ | | total [gas available](/wiki/EL/evm?id=gas) for the transaction |
|$$\mu_{programCounter}$$ | 0 | [Natural number counter](/wiki/EL/evm?id=program-counter) to track the code position we are in , max number size is 256 bits |
|$$\mu_{memoryContents}$$ | $$[0_{256Bit}, ..., 0_{256Bit}]$$ | [word(256bit) Addressed byte array](/wiki/EL/evm?id=memory) |
|$$\mu_{activeWordsInMemory}$$ | 0 | Length of the active words in memory, expanded in chunks of 32bytes |
|$$\mu_{stackContents}$$ | | [Stack](/wiki/EL/evm?id=stack) item : word(256bit), Max Items = 1024 |
|$$\mu_{outputFromNormalHalting}$$ | () | Represents the output(bytes) from the last function call, determined by the normal halting function. While the EELS pyspec features a dedicated field in the EVM object for the output , Geth doesn't; instead, it utilizes the returnData field, which serves the same purpose.|

#### Current Operation

The `currentOperation` is determined based on the position of the `programCounter` within the [bytecode](/wiki/EL/evm?id=evm-bytecode) array:

$$ currentOperation \equiv \ w \equiv
\begin{cases}
I_{[byteCode]}[\mu_{programCounter} ] & \text{if} \space \mu_{programCounter} < length(I_{[byteCode]}) \\
STOP & \text{otherwise}
\end{cases}
$$

该逻辑通过访问字节码数组中 programCounter 位置处的字节来获取当前操作。如果 programCounter 超过字节码的长度，则发出 STOP 操作来停止执行。

考虑黄皮书对加法运算符的定义作为说明性示例：
$$\mu'_{stackContents}[0] \equiv \mu_{stackContents}[0] + \mu_{stackContents}[1]$$

这种表示意味着堆栈中的左侧添加和删除，类似于队列操作。然而，传统的堆栈操作从右侧添加和删除项目。将其转换为基于堆栈的操作：

$$
Add \Rightarrow
$$

$$
x = Pop(\mu_{stackContents}) \\
y = Pop(\mu_{stackContents_{itemsRemoved=1}}) \\
result = x + y \\
Push(\mu_{stackContents_{itemsRemoved=2}}, result)
$$

$$
\Rightarrow \mu_{stackContents^{itemsAdded_{\alpha}=1}_{itemsRemoved_{\delta}=2}}
$$

转换为代码时，符号 $\mu_{s}[number]$ 转换为 $\mu_{stackContents}[stackLength - 1 - number]$，与堆栈操作的传统理解一致。

黄皮书优雅地注释了基于堆栈的操作，并提供了一个在执行周期内解释这些操作的框架。它指定从数组最左边、索引较低的部分操作堆栈项，不受影响的项保持不变：

$$
\begin{align}
\Delta \equiv \alpha^{itemsAdded}_w - \delta^{itemsRemoved}_w \quad (160) \nonumber\\
& \nonumber \\
\mu'_{stackContents}.length \equiv \mu_{stackContents}.length + \Delta \quad (161) \nonumber \\
& \nonumber \\
\forall x \in [\alpha^{itemsAdded}_w , \mu'_{stackContents}.length) : \mu'_{stackContents}[x] \equiv \mu_{stackContents}[x - \Delta] \quad (162) \nonumber
\end{align}
$$

公式 162 表明，对于指定范围内的每个 x，修改后的堆栈在位置 $x - \Delta$ 处镜像原始堆栈，从而有效跟踪操作后堆栈项的原始位置。例如，将项目 [2] 添加到现有堆栈 [10] 会产生 [2,10]，其中原始项目的新位置与 $x=Delta$ 对齐，从而保持操作后堆栈顺序的完整性。

#### 单个执行周期

$$
O((\sigma, \mu, A, I)) \equiv (\sigma', \mu', A', I) \quad (159)\\
$$

其中 $O$ 表示执行周期，将单个周期的结果封装在状态机内。该循环可以修改$\mu$的所有组件，对$\mu_{gas}$和$\mu_{programCounter}$的更改有明确的规范：

##### 单个执行周期的结果程序计数器

下面的等式概述了执行周期如何一次处理一条指令：

$$
\mu'_{programCounter} \equiv
\begin{cases}
J_{JUMP}(\mu) \space \text{if } currentOperation = JUMP \\
J_{JUMP1}(\mu) \space \text{if } currentOperation = JUMP1 \\
N(\mu_{programCounter}, currentOperation) \space \text{otherwise}
\end{cases}
$$

$$
\text{Where, } \\
NextValidInstruction_N(i_{=programCounter}, w_{=currentOperation}) \equiv \\
\begin{cases}
&\\
programCounter + NumberOfBytes(currentOperation + Data_{currentOperation}) - NumberOfBytes(Operation_{PUSH1}) + 2 \\
\qquad \text{if } currentOperation \in [PUSH1,PUSH32] \\
&\\
programCounter + 1 \space \text{otherwise}
\end{cases}
$$

- 这里，如果操作是 $JUMP$，则 $J_{JUMP}$ 函数会将程序计数器设置为堆栈顶部的值。
- 对于 $JUMP1$ 操作，仅当堆栈中的相邻值不为 0 时，$J_{JUMP1}$ 函数才将程序计数器设置为堆栈顶部的值。否则，程序计数器加 1。如果当前操作既不是 $JUMP$ 也不是 $JUMP1$，则程序计数器将由 NextValidInstruction 函数递增。

NextValidInstruction 函数确定当前操作是否在所有 PUSH 操作的范围内，我们将程序计数器递增到紧随当前操作字节之后的字节，以考虑与该操作相关的数据。该数据的范围可以是 1 到 32 个字节，具体取决于具体的 PUSH 操作。如果该操作不是 PUSH 操作，我们只需将程序计数器加 1，前进到代码的下一个字节。此过程强调 PUSH 指令负责将数据从代码加载到堆栈上。

当程序计数器执行跳转操作时，它必须瞄准有效的跳转目的地。 $ValidJumpDestinations_{D}$ 函数指定所有有效跳转目标的集合。

$$
ValidJumpDestinations_{D}(byteCode) \equiv \\
ValidJumpDestinations_{D_J}(byteCode,index) \equiv \\
\begin{cases}
\{\} \quad \text{ if } index \geq Length(byteCode) \\
&\\
\{i\} \cup ValidJumpDestinations_{D_J}(byteCode,NextValidInstruction(index, byteCode[index])) \\
\space \qquad \text{if } byteCode[index] = JUMPDEST \\
&\\
ValidJumpDestinations_{D_J}(byteCode,NextValidInstruction(index, byteCode[index])) \space \text{otherwise}
\end{cases}
$$

这表明如果该索引处的字节码对应于 JUMPDEST 操作，则我们将该索引包含在集合中。我们通过递归调用 $ValidValidJumpDestinations_{D_J}(byteCode, index)$ 函数以及由 $NextValidInstruction$ 函数确定的索引来继续添加这些索引。

##### 单个执行周期中的结果 gas 消耗

$$
\mu'_{gas} \equiv \mu_{gas} - C_{gasCost}(\sigma, \mu, AccruedSubState, Environment_I)
$$

gas 成本函数虽然不太复杂，但包含不同操作的各种情况。黄皮书附录 H 对其进行了简洁的定义。本质上，它通过将当前操作的成本加上该周期前后内存中活动字的成本之差(内存扩展成本)来计算当前周期的总成本。

不同的客户端处理 gas 的成本不同。在 PySpec 中，各种类型的成本处理被集成到操作中，而在 Geth 中，gas 成本在操作执行之前处理。此外，Geth 区分用于内存扩展的 [dynamic](https://github.com/ethereum/go-ethereum/blob/7bb3fb1481acbffd91afe19f802c29b1ae6ea60c/core/vm/interpreter.go#L257) 成本和与操作的基本成本相关的 [constant](https://github.com/ethereum/go-ethereum/blob/7bb3fb1481acbffd91afe19f802c29b1ae6ea60c/core/vm/interpreter.go#L224) gas。两种类型的成本均使用 [UseGas](https://github.com/ethereum/go-ethereum/blob/7bb3fb1481acbffd91afe19f802c29b1ae6ea60c/core/vm/contract.go#L161) 函数扣除

#### 程序执行$\Xi$：

$$(\sigma^{'}_{resultantState}, gas_{remaining}, A^{resultantAccruedSubState}, \omicron^{Output})$$ $$\equiv \Xi(\sigma,gas,A^{accruedSubState}, Environment_I)$$

程序执行函数由函数 X 正式定义，唯一的区别是 $\Xi$ 调用 X 并返回 X 的输出，从输出元组中删除 $Environment_I$。

##### 递归执行函数 X

X 协调整个代码的执行。这通常由客户端实现作为迭代代码的主循环。然而，它的定义是递归的：

$$
X((\sigma,\mu,AccruedSubState,Environment_I)) \equiv \nonumber \\
\begin{cases}
&\\
(\empty, \mu, AccruedSubState, Environment_I) \\ \qquad \text{if } Z_{exceptionalHalting}(\sigma, \mu, AccruedSubState, Environment_I) \\
&\\
(\empty, \mu', AccruedSubState, Environment_I, output) \\ \qquad \text{if } currentOperation_w = REVERT \\
&\\
O(\sigma, \mu', AccruedSubState, Environment_I) . output \\ \qquad \text{if } output \neq \empty \\
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

1. 如果满足异常停止的条件，则返回一个由空状态、机器状态、应计子状态、环境和空输出组成的元组。
2. 如果当前 Operation 为$REVERT$，则返回一个由空状态、扣除 gas 后的机器状态、累积的子状态、环境和机器输出组成的元组。
3. 如果机器输出不为空，则执行迭代器函数 $O$ 会消耗输出。

- 例如，如果当前操作是 CALL、CALLCODE、[DELEGATECALL](https://github.com/ethereum/execution-specs/blob/9c24cd78e49ce6cb9637d1dabb679a5099a58169/src/ethereum/cancun/vm/instructions/system.py#L542) 或 STATICCALL 等系统操作，则这些调用将调用 [通用调用函数](https://github.com/ethereum/execution-specs/blob/9c24cd78e49ce6cb9637d1dabb679a5099a58169/src/ethereum/cancun/vm/instructions/system.py#L267)，设置一条新消息和一个子消息 EVM 进程。然后，该进程的输出被[写回父 EVM 进程的内存](https://github.com/ethereum/execution-specs/blob/9c24cd78e49ce6cb9637d1dabb679a5099a58169/src/ethereum/cancun/vm/instructions/system.py#L325)，有效地消耗 $O$ 的一次迭代中的输出，该输出可以在下一次迭代中使用。

4. 在所有其他场景中，我们只需继续递归调用迭代器函数即可。简单来说，这意味着我们继续主解释器循环

##### 正常停止 H

$H_{normalHaltingFunction}$ 定义了 EVM 在正常情况下的停止行为：

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

- $\Delta_{expansion}$ 计算如下：

  - $\Delta_{expansion} \equiv 32 \times M_{memoryExpansionForRangeFunction}(length(\mu_{memoryContents}), startPos, memorySize)$
  - $\Delta_{expansion} \in \mathbb{N}$

- $\mu'$ 定义为：
  - $\mu' \equiv \mu$ 除外：
    - $\mu'_{memoryContents} \equiv \mu_{memoryContents} + [0_{\text{word}_{256\text{bit}}} ... 0_{\text{word}_{256\text{bit}}}]_{\text{length}=\Delta_{expansion}}$
    - $\mu'_{output} \equiv \mu'_{memoryContents}[startPos : startPos + memorySize]$
    - $\mu'_{gas} \equiv \mu_{gas} - \text{memoryExpansionCost}$
    - $\mu'_{running} \equiv false$

其中：

- $startPos \equiv \mu_{stackContents}[0]$
- $memorySize \equiv \mu_{stackContents}[1]$

函数 $M_{memoryExpansionForRangeFunction}(s,f,l)$ 确定容纳指定范围所需的内存扩展：

$$
M_{memoryExpansionForRangeFunction}(s,f,l) \equiv
$$

$$
\begin{cases}
S & \text{if } l = 0 \\
\text{max}(s, \lceil (f + l) / 32 \rceil) & \text{otherwise}
\end{cases}
$$

本质上，$H_{normalHaltingFunction}$ 首先根据顶部两个堆栈项设置输出的起始索引和长度。如果需要扩展内存来容纳输出，则相应地扩展内存，必要时会产生内存扩展成本。最后，它将 EVM 的输出设置为指定的内存范围。

##### 异常停止 Z

### $T$ 执行阶段 4：临时状态 $\sigma_p$

TODO

### $T$ 执行阶段 5：预最终状态 $\sigma^*$

TODO

### $T$ 执行阶段 6：最终状态 $\sigma'$

TODO

## 区块整体有效性

区块整体有效性是指区块作为单个原子状态转换的正确性，通过组合所有交易的有序执行并验证其聚合效果与区块标头中声明的承诺一致来获得。

尽管黄皮书通过不同的函数指定了交易的执行和区块的有效性，但区块的整体有效性来自于它们的组合，并通过区块级别的执行结果的重构和验证来形式化。

---

### 状态初始化

区块的执行从源自父区块的初始状态开始。函数 $\Gamma$ 将区块映射到其初始执行状态：

$$
\Gamma(B) \equiv 
\begin{cases} 
\sigma_0 & \text{if } P(B_H) = \emptyset \\
\sigma_i \text{ such that } \text{TRIE}(LS(\sigma_i)) = P(B_H)_H & \text{otherwise}
\end{cases}
$$

|符号|描述 |
| ------ | ----------- |
| $\sigma_0$ |创世状态。 |
| $P(B_H)_H$ |父级区块的 状态根。 |
| $LS$ |状态映射函数 (世界状态)。 |

这可确保所有节点从相同的加密提交状态开始执行。

---

### 区块转换函数

令$B_T$表示区块中交易的有序列表。 交易按顺序执行，其中每个交易都对其前一个生成的状态进行操作。该顺序执行由区块转换函数 $\Pi$ 捕获：

$$\Pi(\sigma, B) \equiv \sigma'$$

其中 $\sigma'$ 是通过按顺序应用所有交易获得的最终交易后状态，包括在区块级别定义的任何执行后状态转换。因此，交易的有效性依赖于上下文，并且对排序和累积效应敏感。

### gas Accumulation and Receipts

每次交易执行都会生成一个收据，其中包含执行状态、日志和累计 gas 使用情况。令$R[n]$表示执行第$n$次交易后使用的累积 gas。 gas 累加是递归定义的：

$$
R[n] \equiv 
\begin{cases} 
0 & \text{if } n < 0 \\
\Upsilon^g(\sigma[n-1], B_T[n]) + R[n-1] & \text{otherwise}
\end{cases}
$$

其中 $\Upsilon^g$ 提取在状态 $\sigma[n-1]$ 下执行交易 $B_T[n]$ 所消耗的 gas。

---

### 承诺验证

交易收据的有序序列和最终的世界状态使用 Merkle–Patricia Trie 提交。仅当这些计算出的根与区块标头匹配时，区块才有效：

1.  **收据根：** $B_{H_r} = \text{TRIE}(LS(R))$
2.  **状态根:**$B_{H_s} = \text{TRIE}(LS(\sigma'))$

**Validity Requirements:**
* Computed receipts root must match the header.
* Final 状态根 must match the header.
* gas 的累积使用量必须遵守区块 gas 限制 ($R[n] \le B_{H_l}$)。

区块有效性是**原子**。 区块级转换函数 $\Phi$ 将初始状态和区块映射到完整的区块结果。当且仅当所有重建、执行、累积和承诺检查成功时，区块才被接受。在区块中不存在部分接受交易的概念。

---

**Implementation Reference:**
上述语义基于[Ethereum 黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf)：
- **第 12 节：** 区块最终确定
- **Section 12.1:** Executing Withdrawals
- **第 12.2 节：** 交易验证
- **Section 12.3:** State Validation

### gas Accounting Examples
到目前为止，我们已经讨论了各种场景中的 EL 合并后 gas 机制。 Let's tie it all together with some examples.

注意：每个交易类型都有不同的参数和费用处理行为。

### 示例 1：简单 ETH 转账
####  支持的交易类型
- **类型 0**：旧版交易  
- **类型 1**：旧版 + 访问列表 ([EIP-2930](https://eips.ethereum.org/EIPS/eip-2930))  
- **类型 2**：EIP-1559 交易 ([EIP-1559](https://eips.ethereum.org/EIPS/eip-1559))

#### 交易参数(由发送方定义)

|发送类型 |参数|描述 |
|--------------|-------------------------|-------------------------------------------------------------------|
|类型 0 / 1 / 2 | `gasLimit` | 交易可以消耗的最大 gas |
|类型 0 / 1 | `gasPrice` |支付给提议者的完整 gas 价格 |
|类型 2 | `maxFeePerGas` |每 gas 单位的最高总费用(包括基本费用 + 小费)|
|类型 2 | `maxPriorityFeePerGas` |激励区块纳入的可选提示 |

#### 区块参数(协议定义)

|发送类型 |参数|描述 |
|------------|-------------|------------------------------------------------------|
|类型 2 | `baseFee` |动态基础 gas 每单位价格(由协议烧毁)|

#### 提前预订
此时，交易已准备好在区块内进行处理。 最初，预付金额会被保留，这意味着它会从发件人处扣除。
|发送类型 |公式|
|------------|----------------------------------|
|类型 0 / 1 | `gasLimit × gasPrice` |
|类型 2 | `gasLimit × maxFeePerGas` |

#### 执行阶段
在初始扣除之后，确定交易的执行成本，并且 gas 要么被烧毁，要么奖励给提议者，要么返回给发送者。

|发送类型 |有效 gas 价格|实际成本|
|------------|------------------------------------------------------|--------------------------------|
|类型 0 / 1 | `gasPrice` | `gasUsed × gasPrice` |
|类型 2 | `baseFee + min(maxPriorityFeePerGas, maxFeePerGas − baseFee)` | `gasUsed × effectiveGasPrice` |

注意：  
- 对于类型 0/1，全额直接支付至提议者。  
对于类型 2，基本费用被销毁，小费 `min(maxPriorityFeePerGas, maxFeePerGas - baseFee)` 进入提议者。 effectiveGasPrice 确保 gas 总成本保持在 maxFeePerGas 范围内，如果基本费用较高，则可能会减少小费。

退款金额通过 `reserved − actualCost` 计算并返回给发件人。

### 示例 2：blob 交易

携带交易的 blob 既支付通常的 EVM gas 费用，又支付大数据 blob 的单独 blob gas 费用。请注意，EIP-1559 交易之前的类型没有 blob。 在此示例中，我们将仅讨论与 blob 相关的费用。

####  blob 交易类型
- **类型 3**：EIP-4844 交易 ([EIP-4844](https://eips.ethereum.org/EIPS/eip-4844))

#### 交易参数
- `blobVersionedHashes` – 标识每个数据 blob。  
- `totalBlobGas` – 计算为 `GasPerBlob × numberOfBlobs`。  
- `maxFeePerBlobGas` – 发件人将支付的每个 blob gas 单位的最大 gwei。

#### 区块参数
- `blobGasPrice` – 每个区块 blob gas 动态单价。

最初，预付金额会被保留，这意味着它会从发件人处扣除。
- `reserved_blob = totalBlobGas × maxFeePerBlobGas`

#### 执行成本
- `blobFee = totalBlobGas × blobGasPrice` 并已被协议完全烧录。

#### 退款给发件人计算
- `refund_blob = reserved_blob − blobFee` 并返回给发件人。

这些示例应有助于将 gas 在交易生命周期中的处理方式联系起来。

## 附录

### 代码 A

```R
##imports

library(plotly)
library(dplyr)

## values for xi and rho
## this is how '<-' assignment works in R

ELASTICITY_MULTIPLIER <- 2
BASE_FEE_MAX_CHANGE_DENOMINATOR <- 8

## Slightly modified function from the spec

calculate_base_fee_per_gas <- function(parent_gas_limit, parent_gas_used, parent_base_fee_per_gas, max_change_denom = BASE_FEE_MAX_CHANGE_DENOMINATOR , elasticity_multiplier = ELASTICITY_MULTIPLIER) {

  #  %/% == // (in python) == floor

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

在 R 中定义模型后，我们继续在一系列使用 gas 的场景中模拟该函数：

````R
parent_gas_limit <- 30000  # Fixed for simplification

## lets see the effect on 100 to see the percentage effect this function has on fee
parent_base_fee_per_gas <- 100

## note gas used can not go below the minimum limit of 5k ,
## therefore we can just count from 5k to 30k by ones for complete precision

seq_parent_gas_used <- seq(5000, parent_gas_limit, by = 1) # creates a vector / column

## add the vector / column to the data frame

data <- expand.grid(parent_gas_used = seq_parent_gas_used)

## apply the function we created above and collect it in a new column

data$expected_base_fee <- mapply(calculate_base_fee_per_gas, parent_gas_limit, data$parent_gas_used, parent_base_fee_per_gas)
````

这就是 prep 的全部内容，现在让我们通过散点图进行绘制和观察，该散点图将显示该函数在一定范围内产生的任何形状；考虑到约束条件。

```R
fig <- plot_ly(data, x = ~parent_gas_used, y = ~expected_base_fee, type = 'scatter', mode = 'markers')  # scatter plot

## %>% is a pipe operater from dplyr , used extensively in R codebases it's like the pipe | operator used in shell

fig <- fig %>% layout(xaxis = list(title = "Parent Gas Used"),
                      yaxis = list(title = "Expected Base Fee "))

## display the plot
fig
```

### 代码 B

````r

library(forcats)
library(ggplot2)
library(scales)
library(viridis)

## Initial parameters
initial_gas_limit <- 30000000
initial_base_fee <- 100
num_blocks <- 100000

## Sequence of blocks
blocks <- 1:num_blocks

max_natural_number <- 2^256

## Calculate gas limit for each block
gas_limits <- numeric(length = num_blocks)
expected_base_fee <- numeric(length = num_blocks)
gas_limits[1] <- initial_gas_limit
expected_base_fee[1] <- initial_base_fee

for (i in 2:num_blocks) {

   # apply max change to gas_limit at each block
    gas_limits[i] <- gas_limits[i-1] + gas_limits[i-1] %/% 1024


  # Check if the previous expected_base_fee has already reached the threshold
  if (expected_base_fee[i-1] >= max_natural_number) {
    # Once max_natural_number is reached or exceeded, stop increasing expected_base_fee
    expected_base_fee[i] <- expected_base_fee[i-1]
  } else {
    # Calculate expected_base_fee normally until the threshold is reached
    expected_base_fee[i] <- calculate_base_fee_per_gas(gas_limits[i-1], gas_limits[i], expected_base_fee[i-1])
  }
}

## Create data frame for plotting
data <- data.frame(Block = blocks, GasLimit = gas_limits, BaseFee = expected_base_fee)

## Saner labels
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

## Bin the ranges we want to observe
data_ranges <- data %>%
  mutate(Range = case_when(
    Block <= 1000 ~ "1-1000",
    Block <= 10000 ~ "1001-10000",
    Block <= 100000 ~ "10001-100000"
  ))

## Rearrange the bins to control where the plots are displayed
data_ranges$Range <- fct_relevel(data_ranges$Range, "1-1000", "1001-10000", "10001-100000")

## Grammar of graphics we can just + the features we want in the plot
plot <- ggplot(data_ranges, aes(x = Block, y = GasLimit, color = BaseFee)) +
  geom_line() +
  facet_wrap(~Range, scales = "free") +  # Using free to allow each facet to have its own x-axis scale
  labs(title = "Gas Limit Over Different Block Ranges",
       x = "Block Number",
       y = "Gas Limit") +
  scale_x_continuous(labels = label_custom) +  # Use custom label function for x-axis
  scale_y_continuous(labels = label_custom) +  # Use custom label function for y-axis
  scale_color_gradientn(colors = viridis(8), trans = "log10",
                        breaks = c(1e3, 1e10, 1e20, 1e40, 1e60, 1e76),
                        labels = c("100", "10^10", "10^20", "10^40", "10^60", "10^76")) +
  theme_bw()

## To view
plot

## Save to file
ggsave("plot_gas_limit.png", plot, width = 7, height = 5)

````

### 代码 C

````r
## we are observing the effects of this parameter
## it's set at 8 but lets see its effect in the range of [2,4, .. ,8, .. ,12]
seq_max_change_denom <- seq(2, 12, by = 2)

parent_gas_limit <- 3 * 10^6
seq_parent_gas_used <- seq(5000, parent_gas_limit, by = 100)

parent_base_fee_per_gas <- 100

data <- expand.grid( parent_gas_used = seq_parent_gas_used, base_fee_max_change_denominator = seq_max_change_denom)

data$expected_base_fee <- mapply(calculate_base_fee_per_gas, parent_gas_limit, data$parent_gas_used, parent_base_fee_per_gas, data$  base_fee_max_change_denominator)
$`

That's all for data prep , now lets plot:

```r
绘图 <- ggplot(数据, aes(x = parent_gas_used, y = expected_base_fee, 颜色 = as.factor(base_fee_max_change_denominator))) +
    geom_point() +
    scale_color_brewer(调色板=“光谱”)+
    theme_minimal() +
    labs(color = "基本费用最大变化分母") +
    theme_bw()

情节
```

### Code D

````r
seq_elasticity_multiplier <- seq(1, 6, by = 1)
seq_max_change_denom <- seq(2, 12, by = 2)

parent_gas_limit <- 3 * 10^6
seq_parent_gas_used <- seq(5000, parent_gas_limit, by = 500)

parent_base_fee_per_gas <- 100

数据 <- 展开.grid(parent_gas_used = seq_parent_gas_used, base_fee_max_change_denominator = seq_max_change_denom, elasticity_multiplier = seq_elasticity_multiplier)

data```expected_base_fee <- mapply(calculate_base_fee_per_gas, parent_gas_limit, data$parent_gas_used, parent_base_fee_per_gas, data$base_fee_max_change_deminator, 数据$ elasticity_multiplier)

绘图 <- ggplot(数据, aes(x = parent_gas_used, y = expected_base_fee, 颜色 = as.factor(base_fee_max_change_denominator))) +
    geom_point() +
    facet_wrap(~elasticity_multiplier) + # 我们通过这个方面分解图
    scale_color_brewer(调色板=“光谱”)+
    theme_minimal() +
    labs(color = "基本费用最大变化分母") +
    theme_bw()

ggsave(“rho-xi.png”，情节，宽度= 14，高度= 10)

$`

### 代码 E

````r
library(ggplot2)
library(tidyr)

## fake exponential or taylor series expansion function
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

## Blob Gas Target
target_blob_gas_per_block <- 393216

## Blob Gas Max Limit
max_blob_gas_per_block <- 786432

 # Used in header Verificaton
 calc_excess_blob_gas <- function(parent_excess_blob_gas, parent_gas_used) {
   if (parent_gas_used  + parent_excess_blob_gas < target_blob_gas_per_block) {
     return(0)
   } else {
     return(parent_excess_blob_gas + parent_gas_used - target_blob_gas_per_block)
   }
 }

## This is how EL determines the Blob Gas Price
cancun_blob_gas_price <- function(excess_blob_gas) {
  fake_exponential(1, excess_blob_gas, 3338477)
}

## we got from zero to Max each step increasing by 1000
parent_gas_used <- seq(0, max_blob_gas_per_block, by = 1000)
## A column of the same Length
excess_blob_gas <- numeric(length = length(parent_gas_used))
excess_blob_gas[1] <- 0

## We get the T+1(time + 1) excess gas by using values from before
for (i in 2:length(parent_gas_used)) {
  excess_blob_gas[i] <- calc_excess_blob_gas(excess_blob_gas[i - 1],
                                             parent_gas_used[i - 1])
}

data_blob_price <- expand.grid(parent_gas_used = parent_gas_used)
data_blob_price```excess_blob_gas <- excess_blob_gas

## Apply the EL gas price function
data_blob_price$  blob_gas_price <- mapply(cancun_blob_gas_price,
                                         data_blob_price$excess_blob_gas)

## Each row represents a block
data_blob_price$BlockNumber <- seq_along(data_blob_price$parent_gas_used)

## we collapse the 3 columns into 1 Parameter Column
data_long <- pivot_longer(data_blob_price,
                          cols = c(parent_gas_used,
                                   excess_blob_gas,
                                   blob_gas_price),
                          names_to = "Parameter",
                          values_to = "Value")

ggplot(data_long, aes(x = BlockNumber, y = Value)) +
  geom_line() +
  facet_wrap(~ Parameter, scales = "free_y") +   # We break the charts out based on the Parameter Column
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

格式化弄乱了本文档中的 Latex 代码，下面的脚本正确地格式化了 katex 文档。

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

### 资源
- https://archive.devcon.org/archive/watch/6/eels-the-future-of-execution-layer-specifications/?tab=YouTube
- [EIP-1559](https://eips.ethereum.org/EIPS/eip-1559) • [已存档](https://web.archive.org/web/20230101000000/https://eips.ethereum.org/EIPS/eip-1559)
- [EIP-4844](https://eips.ethereum.org/EIPS/eip-4844) • [已存档](https://web.archive.org/web/20230701000000/https://eips.ethereum.org/EIPS/eip-4844)
- [黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf) • [已存档](https://web.archive.org/web/20240310000000/https://ethereum.github.io/yellowpaper/paper.pdf)
- [EL 规范](https://github.com/ethereum/execution-specs) • [已存档](https://web.archive.org/web/20240501000000/https://github.com/ethereum/execution-specs)


> [!NOTE]
> PR 中的所有主题都开放用于在单独的分支上进行协作

$$

$$
