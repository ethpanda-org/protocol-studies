# 形式化验证简介

## 概述

形式化方法是用于软件和硬件系统数学分析的技术。形式方法的哲学根源可以追溯到古希腊，柏拉图在他的《诡辩家》一书中对形式理论的探索；整个 17 世纪，数学家通过抽象代数进一步发展了这一概念。德国博学者戈特弗里德·莱布尼茨的远见为我们现在所说的形式推理奠定了基础。 19 世纪，[George Boole](https://en.wikipedia.org/wiki/George_Boole) 在分析方面的开创性工作和 [Gottlob Frege](https://en.wikipedia.org/wiki/Gottlob_Frege) 在命题逻辑方面的开创性工作为形式方法奠定了基础。

在形式化方法下，形式化验证是一种_验证技术_，有助于找到一个简单问题的答案：“系统是否正确满足其所需的规范？”。它通过 [抽象一个系统](https://in.mathworks.com/discovery/abstract-interpretation.html) 作为数学模型并证明或反驳其正确性来实现这一点。

“系统”被定义为能够执行其外部接口给出的所有功能的机制。对于系统来说，“不变量”是指无论其当前状态如何都保持不变的属性。例如，自动售货机的一个不变量是：没有人应该能够免费分发产品。形式验证通过检查系统的所有不变量是否成立来测试系统的正确性。

这种严格的系统审查方法在 [EAL 等级](https://en.wikipedia.org/wiki/Evaluation_Assurance_Level#EAL7:_Formally_Verified_Design_and_Tested) 上得分最高，这表明它对安全性产生了深远的影响。

形式验证的类型：

- **模型检查/基于断言的检查**：将系统建模为有限状态机，并使用命题逻辑验证其正确性和活跃性。
- **时间逻辑**：对命题随时间变化的系统进行建模。
- **等效性检查**：验证相同规范但不同实现的两个模型是否产生相同的结果。

## 热门工具

### 柯克

[Coq](https://coq.inria.fr/) 是一种广泛采用的开源证明管理系统。它用于指定和正式验证 C 编程语言的 CompCert 编译器。该编译器用于为飞机、汽车和核电站 [开发安全关键程序](https://www.inria.fr/en/compcert-software-program-receives-prestigious-award)。

### TLA+

[TLA+](https://lamport.azurewebsites.net/tla/tla.html) 是由图灵奖获得者 Leslie Lamport 开发的形式化规范语言。它主要用于对并发和分布式系统进行建模。 Amazon Web 服务 [使用 TLA+](https://www.amazon.science/publications/how-amazon-web-services-uses-formal-methods) 来验证其分布式系统的稳健性。

### 合金

[Alloy](https://alloytools.org/) 是一种用于软件建模的开源语言和分析器。值得注意的是，闪存文件系统设计是使用 Alloy [根据 POSIX 标准进行分析](https://eskang.github.io/assets/papers/ijsi09_kang_jackson.pdf)。

### Z3

[Z3](https://www.microsoft.com/en-us/research/project/z3-3/) 是微软研究院开发的符号逻辑求解器。它广泛用于软件工程应用，包括 [程序验证](https://www.aon.com/cyber-solutions/aon_cyber_labs/exploring-soliditys-model-checker/)、编译器验证、测试、模糊测试和优化。

## 示例

系统的形式验证首先是有选择地抽象系统以创建用于正确性测试的重点模型。

[Dijkstra](https://en.wikipedia.org/wiki/Edsger_W._Dijkstra) 优雅地描述了这个过程：

> 我逐渐将程序视为一组有序的珍珠，一条“项链”。顶级珍珠在其描述中描述了该计划
> 最抽象的形式，在所有低级珍珠中，上面使用的一个或多个概念都以概念的形式进行了解释 (精炼)
> 需要在其下方的珍珠中进行解释 (精炼)，而底部的珍珠最终会解释仍然需要解释的内容
> 就标准接口 (=机器) 而言。家庭变成了可以串起来的特定珍珠集合
> 变成一条合适的项链。

用于模拟交通控制器的 TLA+ 规范：

```bash
-------------- MODULE TrafficController --------------

CONSTANTS MaxCars
VARIABLES carsWaiting, greenSignal

Init == /\ carsWaiting = 0
        /\ greenSignal = FALSE

Arrive(car) == IF carsWaiting < MaxCars THEN carsWaiting' = carsWaiting + 1 ELSE UNCHANGED carsWaiting

Depart == IF carsWaiting > 0 THEN carsWaiting' = carsWaiting - 1 ELSE UNCHANGED carsWaiting

ChangeSignal == /\ carsWaiting > 0
                /\ greenSignal' = TRUE

Next == \/
         \E car \in {0, 1}: Arrive(car)
         \/ Depart
         \/ ChangeSignal

Invariant == carsWaiting <= MaxCars

Spec == Init /\ [][Next]_<<carsWaiting, greenSignal>> /\ []Invariant

=======================================================

```

理解 TLA+ 语义对于本次讨论并不重要。下面简要介绍一下它的作用：

`Init` 在没有车辆等待的情况下初始化系统。 `Arrive` 模拟汽车的到达，如果未达到最大容量，则增加等待汽车的数量。相反，`Depart` 模拟离开控制器的汽车，减少等待汽车的数量 (如果有)。最后，`ChangeSignal` 指示如果汽车在等待，交通信号灯将切换为绿色。

不变的 `Invariant == carsWaiting <= MaxCars` 确保等待的汽车数量永远不会超过 `MaxCars`(一个定义的常量)。

请注意，这种抽象如何方便地忽略交通信号处所有不相关的交互 (鸣喇叭，有人吗？)。

**有效的抽象是一门艺术。**

## Ethereum 及形式化验证

安全和活力保障是 Ethereum 去中心化基础设施的核心。形式验证在验证以下内容的正确性方面发挥着关键作用：

- 协议的 [执行](/wiki/EL/el-specs.md) 和 [共识](/wiki/CL/cl-specs.md) 规范。
- [客户端](/wiki/EL/el-clients.md) 实现。
- 最终用户与链上智能合约应用程序进行交互。

### 协议验证

形式化验证由 [运行时验证团队](https://github.com/runtimeverification) 用来验证 [培根链规范](https://runtimeverification.com/blog/a-formal-model-in-k-of-the-beacon-chain-ethereum-2-0s-primary-proof-of-stake-blockchain)，以及 [Gasper 最终确定性机制](https://runtimeverification.com/blog/formally-verifying-finality-in-gasper-the-core-of-the-beacon-chain)。

[KEVM](https://github.com/runtimeverification/evm-semantics) 基于 [K 框架](https://kframework.org/) 构建，用于构建形式语义并对 [Ethereum Virtual Machine (EVM)](/wiki/EL/evm.md) 规范的正确性进行验证。

形式验证是测试套件中的重要工具，用于发现状态转换组件中微妙的 [数组越界运行时错误](https://consensys.io/blog/formal-verification-of-ethereum-2-0-part-1-fixing-the-array-out-of-bound-runtime-error)。

![Formal verification as part of testing suite](./img/fv-and-testing.jpg)
> 在测试套件中正式验证一块瑞士奶酪模型 – [来源：Codasip](https://codasip.com/2023/09/19/formal-verification-best-practices-to-reach-your-targets/)。

### 验证优化

等价检查广泛用于软件优化。例如，可以针对其先前版本测试优化后的智能合约的正确性，以确认优化未引入任何意外行为。

### 智能合约验证

智能合约中的错误或漏洞可能会产生毁灭性后果，导致财务损失并破坏用户信任。 [Certora Prover](https://docs.certora.com/en/latest/docs/prover/index.html) 和 [halmos](https://github.com/a16z/halmos) 等正式验证工具可帮助识别这些问题。

例如，运行时验证正式验证了 [存款智能合约应用程序](https://runtimeverification.com/blog/formal-verification-of-ethereum-2-0-deposit-contract-part-1) 并发现了一个 [微妙的错误](https://github.com/ethereum/deposit_contract/issues/26)。

形式化验证一直是 [Solidity](https://soliditylang.org/) 语言不可或缺的一部分。来自 Solidity 团队的 Christian 来自早期研讨会：

<iframe width="560" height="315" src="https://www.youtube.com/embed/rx0NPckEWGI?si=GYGPPGGA7aY2k4Ci" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

---

Solidity 编译器还实现了 [基于 SMT (可满足性模理论) 和 Horn 求解的形式验证方法](https://docs.soliditylang.org/en/latest/smtchecker.html)。

来自 EF 形式验证团队的 Leo 解释了如何使用此功能：

<iframe width="560" height="315" src="https://www.youtube.com/embed/QQbWpN76HEg?si=CI0cPCVgAkfAM_V2" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

---

## 形式验证的挑战

形式化验证很难。该过程本身可能 [复杂且耗时](https://www.hillelwayne.com/post/why-dont-people-use-formal-methods)，需要专门的技能和工具。此外，形式验证只能保证模型的正确性，不一定保证底层实现本身的正确性。模型和代码之间的转换过程中的错误仍然可能会引入漏洞。

形式验证依赖于系统的有效抽象。抽象是很难的。如果您忽略了抽象中的重要细节，则可能会带来安全问题。因此，[工程师经常使用模糊测试等补充模拟方法](https://blog.trailofbits.com/2024/03/22/why-fuzzing-over-formal-verification/) 来使用随机输入来测试系统。

尽管存在这些挑战，形式验证是一种强大的技术，可以帮助设计安全高效的系统。我们将以 Dijkstra 的这句富有洞察力的引言作为结束语：

> “程序测试可以用来显示错误的存在，但永远不能显示错误的不存在！”

## 资源

### 🗣️讲座

- Grigore R.，[智能合约和协议的形式验证：什么、为什么、如何](https://www.youtube.com/watch?v=xggtkB7w3es)
- Roberto S.，[分布式验证者技术协议的正式规范和验证](https://www.youtube.com/watch?v=xdEo5Tc6TiY)
- Grant P.，[走向 Imandra 合约：Ethereum 的形式验证](https://www.youtube.com/watch?v=xeg_Q5uN73Q)
- Rikard H.，[工作 DeFi 开发的正式方法](https://www.youtube.com/watch?v=ETlNhV9jYJw)
- Dimitar D. 等人，[智能合约的形式验证变得简单](https://www.youtube.com/watch?v=tq5XH3JedqM)
- Yoichi H.，[智能合约的形式验证](https://www.youtube.com/watch?v=cCUGMAnCh7o)
- Yan M.，[Ethereum 通过形式验证的视角发现错误](https://www.youtube.com/watch?v=Ru6X043Q63U)
- Pawel S.，[已应用形式验证 (使用 TLA+)](https://www.youtube.com/watch?v=l9XZYI3jta0)

### 📄 出版物

- NASA, [什么是形式化方法](https://shemesh.larc.nasa.gov/fm/fm-what.html)
- Andrew H.，[形式化验证，随意解释](https://ahelwer.ca/post/2018-02-12-formal-verification/)
- Bernie C.，[形式化方法简史](https://www.researchgate.net/publication/233960390_A_Brief_History_of_Formal_Methods)
- Martin D.，[马丁·戴维斯论可计算性、计算逻辑和数学基础](https://link.springer.com/book/10.1007/978-3-319-41842-1)
- Ashish D.，[形式化验证简史](http://homepages.cs.ncl.ac.uk/brian.randell/NATO/nato1969.PDF)
- Serokell，[形式化验证：历史和方法](https://serokell.io/blog/formal-verification-history)
- Amazon，[Amazon Web Services 如何使用形式化方法](https://cacm.acm.org/research/how-amazon-web-services-uses-formal-methods/)
- Codasip，[实现目标的形式验证最佳实践](https://codasip.com/2023/09/19/formal-verification-best-practices-to-reach-your-targets/)
- 西门子，[你怎么能说形式验证是详尽无遗的？](https://blogs.sw.siemens.com/verificationhorizons/2021/09/16/how-can-you-say-that-formal-verification-is-exhaustive/)
- 西门子，[2020 年 3 篇著名形式验证会议论文](https://blogs.sw.siemens.com/verificationhorizons/2021/02/09/3-notable-formal-verification-conference-papers-of-2020/)
- 西门子，[形式验证方法](https://verificationacademy.com/topics/formal-verification/)
- 斯坦福大学，[一阶逻辑导论](https://plato.stanford.edu/entries/logic-classical/)
- NYU, [可满足性模理论简介](https://cs.nyu.edu/~barrett/pubs/BT14.pdf)
- Sebastian U，[Rust 二进制搜索实现的形式验证](https://kha.github.io/2016/07/22/formally-verifying-rusts-binary-search.html)
- Jack V.，[TLA+ 入门](https://jack-vanlightly.com/blog/2023/10/10/a-primer-on-formal-verification-and-tla)
- Martin L.，[hevm 的符号执行](https://fv.ethereum.org/2020/07/28/symbolic-hevm-release)
- 英国皇家学会，[正式验证：幼苗会开花吗？](https://royalsocietypublishing.org/doi/10.1098/rsta.2015.0402)
