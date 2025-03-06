# 学习小组 第1周 | 协议简介

学习小组的第一周专注于对协议和研发生态系统的概述介绍。

## 录音

观看 *以太坊协议101* 讲座，由 Mario Havel 主讲，[在 StreamEth 上](https://streameth.org/watch?event=&session=65d77e4f437a5c85775fef9d)。 [幻灯片](https://github.com/eth-protocol-fellows/protocol-studies/tree/main/docs/eps/presentations/week1_protocol_intro.pdf)

[录音](https://streameth.org/embed/?playbackId=9bc1ekw2jk5sz6c7&vod=true&streamId=&playerName=Intro+to+Ethereum+%7C+Mario+Havel+%7C+Week+1 ':include :type=iframe width=100% height=520 frameborder="0" allow="fullscreen" allowfullscreen')

若要查看讲座期间的讨论存档，请访问 [Discord 服务器](https://discord.gg/epfsg) 中的 `#study-group` 频道相关讨论线程。

## 预阅读

这是一次入门讲座，不假设有很多先前的知识。查看[第0周](/eps/week0.md)中的一些基本要求。这里有更多的入门材料帮助你开始：

- [Inevitable Ethereum - 世界计算机](https://inevitableeth.com/home/ethereum/world-computer)
- [30分钟了解以太坊](https://www.youtube.com/watch?v=UihMqcj-cqc)
- [以太坊官方文档](https://ethereum.org/what-is-ethereum)

查看以下文字中的资源和额外的阅读材料。所有资源将在第1周的演讲中进行解释。

## 前史与哲学

要理解以太坊的设计，我们需要了解其前身以及它所构建的文化。现代加密货币生态系统建立在计算机科学家、密码学家和活动家的几十年工作基础之上。

从1960年代开始，UNIX是一种操作系统和哲学，重新定义了整个计算范式。这就是我们使用了50多年的范式，而且从未真正改变。它的模块化基本概念对以太坊设计至关重要，而贝尔实验室的开放协作环境与今天以太坊核心开发的环境类似。

> 查看这部由 Dennis Ritchie 和 Ken Thompson 主持的纪录片，完美捕捉了 UNIX 背后的精神和理念：[链接](https://www.youtube.com/watch?v=tc4ROCJYbm0)

自由软件运动是以太坊和所有加密货币的基础。以太坊的开放、独立和协作开发文化深深扎根于FOSS（自由与开放源代码软件）。以太坊必须以透明的方式实现软件，赋予用户完全的自由。使用任何FOSS可以赋能每个用户和开发者，但对于以太坊的安全性、中立性和无需信任的本质而言，这是必要的。

> 为了理解其重要性，观看自由软件和GNU项目创始人 Richard Stallman 的演讲：
> - [视频](https://www.youtube.com/watch?v=Ag1AKIl_2GM)
> - 更多阅读：[GNU的哲学](https://www.gnu.org/philosophy/free-sw.html)，*The Cathedral and the Bazaar* 书籍：[链接](http://www.catb.org/~esr/writings/cathedral-bazaar/)

[非对称加密学](https://www-ee.stanford.edu/~hellman/publications/24.pdf)的发明标志着加密学应用新范式的诞生。后来，公众在计算中应用加密学使得每个人都能使用数字隐私工具、安全通信工具，并从根本上改变了网络安全。虽然国家并未为这些新概念提供框架，[加密战争](https://en.wikipedia.org/wiki/Crypto_Wars)催生了提倡并构建加密工具的活动家运动。最终，这些人发明的工具和思想成为了现代加密货币的基本构建块。

> 从早期的Cypherpunks那里获得灵感，他们设想了一个建立在无需信任和无国界技术基础上的独立数字领域：
> - [Manifesto](https://activism.net/cypherpunk/manifesto.html)
> - [Crypto Anarchy](https://activism.net/cypherpunk/crypto-anarchy.html)
> - [EFF关于网络独立的倡导](https://www.eff.org/cyberspace-independence)

## 以太坊协议设计

协议的实际前史、早期思想和以太坊技术决策的灵感在[V的博客](https://vitalik.eth.limo/general/2017/09/14/prehistory.html)中得到了很好的记录。

最初在[白皮书](https://ethereum.org/whitepaper#ethereum-whitepaper)中概述的以太坊，从比特币及其背景（如上所述）中汲取灵感，创建了一个通用的基于区块链的计算平台。该设计在[黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf)中进行了技术规范，并随着时间的推移不断发展。变更通过社区的[EIPs](https://eips.ethereum.org)进行追踪，当前的规范由Python实现：

- [执行规范](https://github.com/ethereum/execution-specs)
- [共识规范](https://github.com/ethereum/consensus-specs)

该规范纯粹是技术性的，不提供任何上下文或解释。要了解共识，可以查看[Ben的注释版](https://eth2book.info/capella/annotated-spec/)和[V的注释版](https://github.com/ethereum/annotated-spec)。

协议随着时间的推移发生变化，每次网络升级都会带来新的改进。尽管不断变化，架构的演变仍然反映了一些基本原则。可以总结为以下几点：

- 简单性、普遍性、模块化、不歧视、敏捷性

这些核心原则一直引领着以太坊的发展，每次设计变更时都应考虑这些原则。除此之外，它通过封装和/或夹层管理日益增长的复杂性。当前的网络架构是多次[网络升级历史](https://ethereum.org/history)的结果。

> 阅读更多关于以太坊设计原则的内容：[存档](https://web.archive.org/web/20220815014507mp_/https://ethereumbuilders.gitbooks.io/guide/content/en/design_philosophy.html)，并考虑为[此维基](/wiki/protocol/design-rationale.md)重新编写。

以太坊网络利用去中心化来实现无需许可、可信中立和抗审查性。它不断发展以应对最新的研究和新的、始终存在的挑战。为了维持安全性和去中心化，区块链技术有一定的局限性，主要体现在其可扩展性上。由于以太坊需要始终遵守其原则，这促使我们为这些问题寻找巧妙的解决方案，而不是接受妥协。

当前的研究和开发总结在[路线图](https://twitter.com/VitalikButerin/status/1741190491578810445/photo/1)中，但无法完全准确地反映。以太坊R&D没有单一的路径，[路线图](https://ethroadmap.com/)仅总结了当前的格局。核心生态系统是一个永远发展的[无限花园](https://ethereum.foundation/infinitegarden)。然而，随着越来越多的进展，以太坊可能会慢慢接近它的固化。

> 当前以太坊设计的简化概述可以在[节点架构文档](https://ethereum.org/developers/docs/nodes-and-clients/node-architecture)和第1周演讲中找到。

如上所示，以太坊的主要高层组件是执行层和共识层。这是两个相互连接和依赖的网络。执行层提供执行引擎，处理用户交易和所有状态（地址、合约数据），而共识层实现了证明-股份机制，确保安全性和[容错性](https://inevitableeth.com/home/concepts/bft)。

## 实现与开发

以上提到的一切——思想、设计和规范，都归结为实际应用，在于其实现。执行层（EL）或共识层（CL）的实现被称为*客户端*。运行此客户端并连接到网络的计算机被称为*节点*。因此，节点是一个EL和CL客户端的组合，积极参与网络。

由于以太坊有正式的规范，它可以用任何语言实现。这导致了多种实现的出现，随着时间的推移，有些已经被弃用，有些则仍在开发中。当前的生产级实现列表可以在[节点与客户端文档](https://ethereum.org/en/developers/docs/nodes-and-clients#execution-clients)和第1周演讲中找到。

> 这种策略称为[客户端多样性](https://ethereum.org/developers/docs/nodes-and-clients/client-diversity)。以太坊不依赖于单一的“官方”实现，但用户可以选择任何客户端，并确保它能完成任务。如果单一客户端实现出现问题，其他客户端不会受到影响。

### 测试

由于经常变化和多个客户端实现，测试对网络的安全至关重要。在任何网络升级之前，都会使用各种测试工具和场景，所有这些都得到充分利用。

测试是基于规范生成的，并创建各种场景。一些测试单独测试客户端，有些则模拟一个包含所有客户端对的测试网。有不同的测试工具用于开发周期和客户端各个部分。这包括状态转换测试、模糊测试、影子分叉、RPC测试、客户端单元测试和CI/CD等。

> 以下是一些专门用于测试的仓库：
> - https://github.com/ethereum/tests
> - https://github.com/ethereum/retesteth
> - https://github.com/ethereum/execution-spec-tests
> - https://github.com/ethereum/hive
> - https://github.com/kurtosis-tech/kurtosis
> - https://github.com/MariusVanDerWijden/FuzzyVM
> - https://github.com/lightclient/rpctestgen

### 协调

以太坊的开发与传统的企业项目截然不同。首先，它是完全开放和公开的，任何人都可以审查或贡献。其次，许多不同的团队在不同部分进行工作。总的来说，大约有20个来自不同组织的团队在以太坊上工作。

与专有技术不同，以太坊开发者不是在竞争，而是在合作。随着复杂性不断增长，几乎没有人能成为以太坊的全能专家。相反，人们在特定领域发展专业知识，并与他人合作。这得益于以太坊的模块化，使开发者能够
