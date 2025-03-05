# 简介：形式化验证

## 概述

形式化方法是用于对软件和硬件系统进行数学分析的技术。形式化方法的哲学根源可以追溯到古希腊，柏拉图在其著作《Sophist》中探讨了形式理论；在17世纪，数学家通过抽象代数进一步发展了这一概念。德国博学家戈特弗里德·莱布尼茨的愿景为我们现在所称的形式推理奠定了基础。19世纪，乔治·布尔和戈特洛布·弗雷格在分析和命题逻辑方面的开创性工作为形式化方法提供了基础。

在形式化方法下，形式化验证是一种 _验证技术_，它帮助找到一个简单问题的答案：“一个系统是否正确满足其规定的规格？” 它通过[将系统抽象化](https://in.mathworks.com/discovery/abstract-interpretation.html)为数学模型，进而证明或反驳其正确性。

“系统”被定义为能够执行其外部接口所规定的所有功能的机制。对于一个系统，“不变性”是一个属性，它在系统的任何状态下都保持不变。例如，自动售货机的一个不变性是：没有人可以免费获取产品。形式化验证通过检查系统的所有不变性是否成立来测试系统的正确性。

这种严谨的系统检查方法在[评估保证级别（EAL）](https://en.wikipedia.org/wiki/Evaluation_Assurance_Level#EAL7:_Formally_Verified_Design_and_Tested)等级上获得了最高评级，标志着其对安全性的深远影响。

形式化验证的类型：

- **模型检查 / 基于断言的检查**：将系统建模为有限状态机，并使用命题逻辑验证其正确性和活性。
- **时序逻辑**：将系统建模为其命题随时间变化的系统。
- **等价性检查**：验证两个相同规格但实现不同的模型是否产生相同结果。

## 流行工具

### Coq

[Coq](https://coq.inria.fr/) 是一个广泛使用的开源证明管理系统。它被用于指定和形式化验证 C 编程语言的 CompCert 编译器。该编译器用于[开发安全关键的程序](https://www.inria.fr/en/compcert-software-program-receives-prestigious-award)，如航空、汽车和核电厂的程序。

### TLA+

[TLA+](https://lamport.azurewebsites.net/tla/tla.html) 是由图灵奖得主 Leslie Lamport 开发的形式化规范语言。它主要用于建模并发和分布式系统。亚马逊网络服务 [使用 TLA+](https://www.amazon.science/publications/how-amazon-web-services-uses-formal-methods) 来验证其分布式系统的鲁棒性。

### Alloy

[Alloy](https://alloytools.org/) 是一个开源的软件建模语言和分析工具。特别地，闪存文件系统设计曾[与 POSIX 标准进行分析对比](https://eskang.github.io/assets/papers/ijsi09_kang_jackson.pdf)，使用的就是 Alloy。

### Z3

[Z3](https://www.microsoft.com/en-us/research/project/z3-3/) 是微软研究院开发的符号逻辑求解器。它广泛应用于软件工程的多个领域，包括[程序验证](https://www.aon.com/cyber-solutions/aon_cyber_labs/exploring-soliditys-model-checker/)、编译器验证、测试、模糊测试和优化等。

## 示例

形式化验证一个系统从选择性地抽象系统开始，以创建一个集中测试正确性的模型。

[Dijkstra](https://en.wikipedia.org/wiki/Edsger_W._Dijkstra) 优雅地描述了这个过程：

> 我已经逐渐将程序视为一串有序的珍珠，称之为“项链”。最顶端的珍珠描述了程序的最抽象形式，下面的每颗珍珠都以下方要解释（或细化）的概念来解释（或细化）上面使用的一个或多个概念，而最底部的珍珠最终用标准接口（=机器）来解释剩下的部分。整个家族成为一组珍珠，能够串成一条合适的项链。

下面是一个用 TLA+ 建模的交通控制器：

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
理解 TLA+ 语义并不是本讨论的重点。下面是它所做的简要说明：

`Init` 初始化系统，表示没有汽车在等待。`Arrive` 模拟汽车的到达，如果最大容量未达到，则增加等待汽车的数量。相反，`Depart` 模拟汽车离开控制器，如果有等待的汽车，则减少等待汽车的数量。最后，`ChangeSignal` 规定如果有汽车在等待，交通信号灯将切换为绿灯。

不变性 `Invariant == carsWaiting <= MaxCars` 确保等待的汽车数量永远不会超过 `MaxCars`（一个定义的常量）。

请注意，这种抽象方便地忽略了交通信号灯处的所有无关互动（比如喇叭声？）。

**高效的抽象是一种艺术。**


## 以太坊与形式化验证

安全性和活跃性保证是以太坊去中心化基础设施的核心。形式化验证在以下方面起着关键作用：

- 协议的[执行层](./wiki/EL/el-specs.md)和[共识层](./wiki/CL/cl-specs.md)规格验证。
- [客户端](./wiki/EL/el-clients.md)实现验证。
- 用户交互的链上智能合约应用程序验证。

### 协议验证

形式化验证被[运行时验证团队](https://github.com/runtimeverification)用于验证[信标链规范](https://runtimeverification.com/blog/a-formal-model-in-k-of-the-beacon-chain-ethereum-2-0s-primary-proof-of-stake-blockchain)和[Gasper 最终性机制](https://runtimeverification.com/blog/formally-verifying-finality-in-gasper-the-core-of-the-beacon-chain)。

[KEVM](https://github.com/runtimeverification/evm-semantics) 基于[K框架](https://kframework.org/)构建，用于制定形式语义并对[以太坊虚拟机 (EVM)](/wiki/EL/evm.md)规范进行正确性验证。

形式化验证是测试套件中的一个重要工具，并被用来发现状态转换组件中的[数组越界运行时错误](https://consensys.io/blog/formal-verification-of-ethereum-2-0-part-1-fixing-the-array-out-of-bound-runtime-error)。
### 智能合约验证

智能合约中的漏洞或缺陷可能带来毁灭性的后果，导致财务损失并破坏用户信任。像[Certora Prover](https://docs.certora.com/en/latest/docs/prover/index.html)和[halmos](https://github.com/a16z/halmos)这样的形式化验证工具可以帮助识别这些问题。

例如，运行时验证团队正式验证了一个[存款智能合约应用](https://runtimeverification.com/blog/formal-verification-of-ethereum-2-0-deposit-contract-part-1)，并发现了一个[细微的漏洞](https://github.com/ethereum/deposit_contract/issues/26)。

形式化验证一直是[Solidity](https://soliditylang.org/)语言的一个重要组成部分。这里是来自Solidity团队的Christian在早期研讨会中的分享：

<iframe width="560" height="315" src="https://www.youtube.com/embed/rx0NPckEWGI?si=GYGPPGGA7aY2k4Ci" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

---

Solidity 编译器还实现了一个基于 SMT（可满足性模理论）和 Horn 求解的[形式化验证方法](https://docs.soliditylang.org/en/latest/smtchecker.html)。

EF 形式化验证团队的Leo解释了如何使用这个功能：

<iframe width="560" height="315" src="https://www.youtube.com/embed/QQbWpN76HEg?si=CI0cPCVgAkfAM_V2" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

---

## 形式化验证的挑战

形式化验证是困难的。这个过程本身可能是[复杂且耗时的](https://www.hillelwayne.com/post/why-dont-people-use-formal-methods)，需要专门的技能和工具。此外，形式化验证只能保证模型的正确性，而不一定是底层实现本身的正确性。在模型与代码之间的转换过程中出现的错误仍然可能引入漏洞。

形式化验证依赖于对系统的有效抽象。而抽象是困难的。如果你在抽象中遗漏了一个重要的细节，可能会引入安全问题。因此，许多工程师通常使用[如模糊测试](https://blog.trailofbits.com/2024/03/22/why-fuzzing-over-formal-verification/)之类的补充模拟方法，利用随机输入来测试系统。

尽管存在这些挑战，形式化验证仍然是一种强大的技术，可以帮助设计安全且高效的系统。我们将以 Dijkstra 的一句深刻的话作为结束：

> “程序测试可以用来显示bug的存在，但永远不能证明它们的不存在！”

## 资源

### 🗣️ 演讲

- Grigore R., [智能合约和协议的形式化验证：什么，为什么，如何](https://www.youtube.com/watch?v=xggtkB7w3es)
- Roberto S., [分布式验证者技术协议的形式化规范和验证](https://www.youtube.com/watch?v=xdEo5Tc6TiY)
- Grant P., [迈向 Imandra 合约：以太坊的形式化验证](https://www.youtube.com/watch?v=xeg_Q5uN73Q)
- Rikard H., [为工作中的 DeFi 开发者提供的形式化方法](https://www.youtube.com/watch?v=ETlNhV9jYJw)
- Dimitar D. 等人, [让智能合约的形式化验证变得简单](https://www.youtube.com/watch?v=tq5XH3JedqM)
- Yoichi H., [智能合约的形式化验证](https://www.youtube.com/watch?v=cCUGMAnCh7o)
- Yan M., [通过形式化验证看以太坊的漏洞](https://www.youtube.com/watch?v=Ru6X043Q63U)
- Pawel S., [应用形式化验证（使用 TLA+）](https://www.youtube.com/watch?v=l9XZYI3jta0)

### 📄 发表的文章

- NASA, [什么是形式化方法](https://shemesh.larc.nasa.gov/fm/fm-what.html)
- Andrew H., [形式化验证，通俗解释](https://ahelwer.ca/post/2018-02-12-formal-verification/)
- Bernie C., [形式化方法的简史](https://www.researchgate.net/publication/233960390_A_Brief_History_of_Formal_Methods)
- Martin D., [马丁·戴维斯关于可计算性、计算逻辑与数学基础的观点](https://link.springer.com/book/10.1007/978-3-319-41842-1)
- Ashish D., [形式化验证的简史](http://homepages.cs.ncl.ac.uk/brian.randell/NATO/nato1969.PDF)
- Serokell, [形式化验证：历史与方法](https://serokell.io/blog/formal-verification-history)
- Amazon, [亚马逊网络服务如何使用形式化方法](https://cacm.acm.org/research/how-amazon-web-services-uses-formal-methods/)
- Codasip, [实现目标的形式化验证最佳实践](https://codasip.com/2023/09/19/formal-verification-best-practices-to-reach-your-targets/)
- Siemens, [如何证明形式化验证是全面的？](https://blogs.sw.siemens.com/verificationhorizons/2021/09/16/how-can-you-say-that-formal-verification-is-exhaustive/)
- Siemens, [2020年三篇值得注意的形式化验证会议论文](https://blogs.sw.siemens.com/verificationhorizons/2021/02/09/3-notable-formal-verification-conference-papers-of-2020/)
- Siemens, [形式化验证方法](https://verificationacademy.com/topics/formal-verification/)
- Stanford, [一阶逻辑介绍](https://plato.stanford.edu/entries/logic-classical/)
- NYU, [可满足性模理论介绍](https://cs.nyu.edu/~barrett/pubs/BT14.pdf)
- Sebastian U, [对 Rust 二分搜索实现的形式化验证](https://kha.github.io/2016/07/22/formally-verifying-rusts-binary-search.html)
- Jack V., [TLA+ 入门](https://jack-vanlightly.com/blog/2023/10/10/a-primer-on-formal-verification-and-tla)
- Martin L., [hevm 的符号执行](https://fv.ethereum.org/2020/07/28/symbolic-hevm-release)

