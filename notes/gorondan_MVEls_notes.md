# MINIMUM VIABLE ENSHRINEMENT OF LIQUID STAKING (MVE-LS)

## MVE-LS是什么？
MVE-LS 是可以在短时间内在协议级别实施的最小措施集(最好与正在开发和评估以包含在 Pectra 硬分叉中的其他一致升级并行)。 
根据 [Rainbow Stake POC](https://ethresear.ch/t/unbundling-staking-towards-rainbow-staking/18683/8)，它是 1/3 级的准等效项 - 运营商与委托人分离：

```
[...]enshrining some separation between operator and delegator[...] might be ok with some level of complexity. We could call a basic operator/delegator separation Level 1 [...]
```
[[1]](#resources)

## 可行性 
* 必要性 
    - 过度依赖社会层和道德来保护协议在 Stake 场景中免受中心化，并对抗占主导地位的 LST 的出现及其传入的危险，都存在风险
    -  我们需要一个接口以即插即用的方式集成进一步的“协议服务”
    - 当前质押设计的局限性，以确保单独质押者未来竞争性参与不同类别的协议服务(即经济价值和/或代理)
    - 以太坊需要与 SSF 进行良好的权衡
* 机会 
    - 将已建立的质押模型的变体与两类参与者一起纳入：委托者(质押者)和节点运营商
    - 与目前正在讨论和开发的其他 EIP 一起纳入某种操作者与委托者分离的措施，可以利用目前为指定这些 EIP 所做的大量工作：
        - EIP-6110 [[5]](#resources) 允许在共识层上进行存款处理的协议内机制。它还将添加协议内机制，通过该机制，大型节点操作员可以组合验证者，而无需循环退出和激活队列。
        - EIP-7251 [[4]](#resources) 将允许单个消息携带更多权益；实现运营商委托人分离将为协议提供一种在功能上区分此类消息上的“运营商权益”和“委托人权益”的方法。

## 功能集

| **FEATURE** | **标题** | **描述** | 
| :----------: | :-----------: | :-----------: |
|功能1|提供快速抵押密钥(Q密钥)|允许验证者提供两个抵押密钥：持久密钥(P密钥)和快速抵押密钥(Q密钥) - 智能合约，当被调用时，在每个时隙期间输出辅助抵押密钥

```
The protocol would give powers to these two keys, but the mechanism for choosing the second key in each slot could be left to staking pool protocols.
```
[[2]](#resources)
## 实施POC
MVE-LS POC 可以在 EIP-6110 规范之上完成，以用于未来的验证者存款和 EIP-7251 的向后兼容性(硬分叉) 

## 资源
[[1]分拆质押](https://ethresear.ch/t/unbundling-staking-towards-rainbow-staking/18683)

[[2] 协议和质押池的变化可以改善去中心化并减少共识开销](https://notes.ethereum.org/@vbuterin/staking_2023_10)

[[3] 以太坊是否可以在协议中包含更多内容？](https://vitalik.eth.limo/general/2023/09/30/enshrinement.html#what-do-we-learn-from-all-this)

[[4]EIP-7215](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-7251.md)

[[5]EIP-6110](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-6110.md)

