# 以太坊协议学习小组讲座 1 | 协议简介

学习小组的第一天主要介绍以太坊协议及其研发（R&D）生态系统。

## 录制内容

观看 Mario Havel 的 *Ethereum Protocol 101* 讲座 [StreamEth 上的回放](https://streameth.org/watch?event=&session=65d77e4f437a5c85775fef9d)。[讲座幻灯片](https://github.com/eth-protocol-fellows/protocol-studies/tree/main/docs/eps/presentations/week1_protocol_intro.pdf)

[观看录制](https://streameth.org/embed/?playbackId=9bc1ekw2jk5sz6c7&vod=true&streamId=&playerName=Intro+to+Ethereum+%7C+Mario+Havel+%7C+Week+1 ':include :type=iframe width=100% height=520 frameborder="0" allow="fullscreen" allowfullscreen')

讲座期间的讨论存档可在 [Discord 服务器](https://discord.gg/epfsg) `#study-group` 频道的对应线程中查看。

## 预读材料

这是一次入门级别的讲座，不需要太多先验知识。你可以在 [第 0 周](/eps/week0.md) 了解一些基本要求。此外，这里还有一些入门材料供你参考：

- [不可避免的以太坊——世界计算机](https://inevitableeth.com/home/ethereum/world-computer)
- [30 分钟了解以太坊](https://www.youtube.com/watch?v=UihMqcj-cqc)
- [Ethereum.org 文档](https://ethereum.org/what-is-ethereum)

讲座的第一周会详细介绍这些资源。

## 以太坊的前史与哲学

要理解以太坊的设计，我们需要了解它的前身以及它所依赖的文化背景。现代加密货币生态系统建立在计算机科学家、密码学家和技术活动家数十年的工作基础之上。

### UNIX 与计算范式

从 20 世纪 60 年代开始，UNIX 作为一种操作系统及其哲学，彻底重塑了计算范式。这种范式已经存在超过 50 年，核心思想从未改变。UNIX 的模块化理念对以太坊的设计至关重要，而贝尔实验室的开放协作环境也与今天的以太坊核心开发模式相似。

> 观看 Dennis Ritchie 和 Ken Thompson 讲述 UNIX 发展历程的纪录片：[YouTube 链接](https://www.youtube.com/watch?v=tc4ROCJYbm0)

### 自由软件运动

自由软件运动（Free Software Movement）是以太坊及所有加密货币的基础。以太坊的开放、独立和协作开发文化深植于自由及开源软件（FOSS）。以太坊需要透明的软件实现，以确保用户拥有完全的自由。使用 FOSS 不仅能增强用户和开发者的能力，更是保障以太坊安全性、中立性及去信任化特性的必要条件。

> 推荐观看自由软件及 GNU 项目创始人 Richard Stallman 的演讲：
> - [YouTube 链接](https://www.youtube.com/watch?v=Ag1AKIl_2GM)
> - [自由软件哲学](https://www.gnu.org/philosophy/free-sw.html)
> - 参考书籍：《大教堂与集市》：[阅读链接](http://www.catb.org/~esr/writings/cathedral-bazaar/)

### 密码学与加密朋克

[非对称加密](https://www-ee.stanford.edu/~hellman/publications/24.pdf) 的发明标志着密码学应用新范式的诞生。随着计算机密码学的发展，公众可以使用工具来保护数字隐私和安全通信，彻底改变了网络安全领域。然而，各国政府当时缺乏相关框架来规范这些新概念，因此引发了[加密战争](https://en.wikipedia.org/wiki/Crypto_Wars)，促使技术活动家推动并构建密码学工具。这些人发明的工具和理念成为现代加密货币的基石。

> 推荐阅读早期加密朋克（Cypherpunk）的愿景，他们设想了基于去信任、无国界技术的独立数字领域：
> - [Cypherpunk 宣言](https://activism.net/cypherpunk/manifesto.html)
> - [加密无政府主义](https://activism.net/cypherpunk/crypto-anarchy.html)
> - [电子前哨基金会独立宣言](https://www.eff.org/cyberspace-independence)

[以太坊前史相关资料](https://epf.wiki/#/wiki/protocol/prehistory)

## 以太坊协议设计

以太坊协议的前史、早期理念及技术决策在[Vitalik 的博客](https://vitalik.eth.limo/general/2017/09/14/prehistory.html)中有详细记录。

### 以太坊的设计原则

以太坊最初在其 [白皮书](https://ethereum.org/whitepaper#ethereum-whitepaper) 中提出，它借鉴了比特币的理念，并在此基础上构建了一个通用的区块链计算平台。其技术细节在[黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf)中描述，并随着时间推移不断演化。社区通过 [EIP 过程](https://eips.ethereum.org)跟踪这些变化，当前的规范实现包括：

- [执行层规范](https://github.com/ethereum/execution-specs)
- [共识层规范](https://github.com/ethereum/consensus-specs)

以太坊的发展始终遵循以下核心原则：

- **简单性、通用性、模块化、非歧视性、灵活性**

尽管协议不断演进，这些原则始终引导着以太坊的设计决策。

> 以太坊的设计原则可在[存档](https://web.archive.org/web/20220815014507mp_/https://ethereumbuilders.gitbooks.io/guide/content/en/design_philosophy.html)和[wiki](https://epf.wiki/#/wiki/protocol/design-rationale)中找到。

## 以太坊的实施与开发

### 以太坊客户端

以太坊协议的实现被称为 **客户端（Client）**，运行这些客户端并连接到网络的计算机称为 **节点（Node）**。每个节点运行执行层（EL）和共识层（CL）客户端，以确保网络正常运行。

### 测试

以太坊不断升级，并且存在多种客户端实现，因此测试对于网络安全至关重要。以下是一些关键测试工具：

- [以太坊测试仓库](https://github.com/ethereum/tests)
- [retesteth](https://github.com/ethereum/retesteth)
- [执行层测试](https://github.com/ethereum/execution-spec-tests)

### 协调

以太坊的开发是开放和协作的，并通过 [EIP 过程](https://eips.ethereum.org/EIPS/eip-1)进行改进。此外，核心开发协调主要通过[开发者电话会议](https://github.com/ethereum/pm)进行。

## 进一步阅读

- [以太坊路线图](https://ethereum.org/roadmap)
- [Unix 历史](https://www.theregister.com/2024/02/16/what_is_unix/)
- [《精通以太坊》](https://github.com/ethereumbook/ethereumbook)

---

如果你想进一步探索以太坊协议的各个方面，可以加入学习小组或参与社区讨论！

