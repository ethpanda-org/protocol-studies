# 学习小组第 1 讲 |协议介绍

学习小组的第一天致力于协议和研发生态系统的总体介绍。

## 录音

观看 Mario Havel [在 StreamEth 上](https://streameth.org/watch?event=&session=65d77e4f437a5c85775fef9d) 的 **以太坊协议 101** 演讲。 [幻灯片](https://github.com/eth-protocol-fellows/protocol-studies/tree/main/docs/eps/presentations/week1_protocol_intro.pdf)

[录音](https://streameth.org/embed/?playbackId=9bc1ekw2jk5sz6c7&vod=true&streamId=&playerName=Intro+to+Ethereum+%7C+Mario+Havel+%7C+Week+1 ':include :type=iframe width=100% height=520 frameborder="0" allow="fullscreen" allowfullscreen')

如需查看演讲期间讨论的存档，请在 [Discord 服务器](https://discord.gg/epfsg) 的 `#study-group` 频道中查看相应的线程。

## 预读

这是一个介绍性演讲，不需要太多先验知识。检查[第 0 周](/eps/week0.md) 中的一些一般要求。这里还有一些可以帮助您入门的介绍性材料：

- [不可避免的以太坊 - 世界计算机](https://inevitableeth.com/home/ethereum/world-computer)
- [30分钟后以太坊](https://www.youtube.com/watch?v=UihMqcj-cqc)
- [以太坊.org 文档](https://ethereum.org/en/what-is-ethereum/)

查看以下文本和附加阅读中的资源。所有资源将在第一周的演示中进行解释。

## 史前史和哲学

要理解以太坊设计，我们需要了解前辈及其所建立的文化。现代加密货币生态系统建立在计算机科学家、密码学家和活动家数十年的努力之上。

早在 20 世纪 60 年代，UNIX 就是一个重新定义了整个计算Paradigm的操作系统和哲学。这是我们 50 多年来一直使用的范例，从未真正改变过。其模块化的基本概念对于以太坊设计非常重要，贝尔实验室的开放协作环境类似于今天的以太坊核心开发。

> 与 Dennis Ritchie 和 Ken Thompson 一起观看这部纪录片，它完美地捕捉了 UNIX 背后的精神和想法：https://www.youtube.com/watch?v=tc4ROCJYbm0

自由软件运动是以太坊和所有加密货币的基础。 以太坊的开放、独立和协作开发文化深深植根于 FOSS(自由开源软件)。 以太坊需要在软件中透明地实现，为用户提供充分的自由。使用任何 FOSS 都可以为每个用户和开发人员提供支持，但这对于以太坊的安全性、中立性和去信任性是必要的。

> 要了解其重要性，请观看自由软件和 GNU 项目创始人 Richard Stallman 的演讲：
>
> - https://www.youtube.com/watch?v=Ag1AKIl_2GM
> - 更多阅读：https://www.gnu.org/philosophy/free-sw.html, _《大教堂与集市》_ 书籍：http://www.catb.org/~esr/writings/cathedral-bazaar/

[非对称密码学](https://www-ee.stanford.edu/~hellman/publications/24.pdf)的发明标志着密码学应用新Paradigm的诞生。后来，密码学在大众计算领域的兴起使每个人都能够利用工具来实现数字隐私、安全通信和从根本上改变网络安全。虽然民族国家没有这些新概念的框架，但[加密货币战争](https://en.wikipedia.org/wiki/Crypto_Wars)导致了倡导和构建加密工具的激进运动。最终，这些人发明了工具和想法，成为构建现代加密货币区块的基础。

> 从早期的密码朋克那里获得灵感，他们设想建立一个基于去信任和无边界技术的独立数字领域：
>
> - https://activism.net/cypherpunk/manifesto.html
> - https://activism.net/cypherpunk/crypto-anarchy.html
> - https://www.eff.org/cyberspace-independence

[在 EPF wiki 页面上了解有关以太坊史前史的更多信息。](https://epf.wiki/#/wiki/protocol/prehistory)

## 以太坊协议设计

以太坊中协议的实际史前史、早期想法和技术决策的灵感在 [V 的博客](https://vitalik.eth.limo/general/2017/09/14/prehistory.html) 中有详细记录。

以太坊最初在其[白皮书](https://ethereum.org/whitepaper#ethereum-whitepaper) 中概述，以太坊从比特币及其背景(如上所述)中汲取灵感，创建了一个基于区块链的通用计算平台。该设计在[黄皮书](https://ethereum.github.io/yellowpaper/paper.pdf)中进行了技术说明，并随着时间的推移而不断发展。在 [EIPs](https://eips.ethereum.org) 的社区进程中跟踪更改，当前规范在 Python 中实现为：

- [执行规范](https://github.com/ethereum/execution-specs)
- [共识规范](https://github.com/ethereum/consensus-specs)

该规范纯粹是技术性的，不为读者提供任何上下文或解释。要了解共识，请查看带注释的版本 [by Ben](https://eth2book.info/capella/annotated-spec/) 和 [by V](https://github.com/ethereum/annotated-spec)。

该协议随着时间的推移而变化，每次网络升级都会带来新的改进。尽管不断变化，架构的演变反映了某些基本原则。这些可以概括如下：

- 简单性、通用性、模块化、非歧视性、敏捷性

这些核心原则始终引领着以太坊的开发，并且在设计变更的每一个决策中都应予以考虑。除此之外，它还通过封装和/或夹层来管理日益增长的复杂性。目前的网络架构是[网络升级历史](https://ethereum.org/history)多次迭代的结果。

> 在 [存档](https://web.archive.org/web/20220815014507mp_/https://ethereumbuilders.gitbooks.io/guide/content/en/design_philosophy.html) 和 [此 wiki](/wiki/protocol/design-rationale.md) 中了解有关以太坊设计原理的更多信息

以太坊网络利用去中心化来实现无需许可、可信中立和抗审查。它不断发展以应对最新的研究、新闻和始终存在的挑战。为了保持安全性和去中心化，区块链技术有一定的限制，主要是其可扩展性。因为以太坊需要始终坚持其原则，所以它激励我们为这些问题找到巧妙的解决方案，而不是接受权衡。

[路线图](https://twitter.com/VitalikButerin/status/1741190491578810445/photo/1)概述总结了当前的研究和发展，但并不完全准确。 以太坊的研发没有单一的路径，[路线图](https://ethroadmap.com/) 仅总结了其当前的情况。核心生态系统是一个永远生长的[无限花园](https://ethereum.foundation/infinitegarden)。然而，随着越来越多的进步，以太坊可能会慢慢走向僵化。

> 当前以太坊设计的简化概述可以在 [节点架构](https://ethereum.org/developers/docs/nodes-and-clients/node-architecture) 和第 1 周演示中找到文档

如上所述，以太坊的主要高级组件是执行和共识层。这是两个相互连接且相互依赖的网络。执行层提供执行引擎，处理用户交易和所有状态(地址、合约数据)，而共识则实现权益证明机制，确保安全性和[容错](https://inevitableeth.com/home/concepts/bft)。

## 实施与发展

上面提到的所有内容——想法、设计和规范都归结为实际应用的实现。 执行层 (EL) 或共识层 (CL) 的实现称为_client_。运行此客户端并连接到网络的计算机称为_节点_。因此，节点是积极参与网络的一对 EL 和 CL 客户端。

由于以太坊是正式指定的，因此可以在任何语言中以不同的方式实现。这导致多年来出现了各种各样的实现，其中一些已经被弃用，一些刚刚开发。当前的生产就绪实现列表可以在 [节点和 客户端的文档](https://ethereum.org/en/developers/docs/nodes-and-clients#execution-clients) 和第 1 周演示中找到。

> 该策略称为[客户端多样性](https://ethereum.org/developers/docs/nodes-and-clients/client-diversity)。 以太坊不依赖于单个“官方”实现，但用户可以选择任何客户端并确保它能完成工作。如果单个客户端实现中出现问题，它不会影响网络的其余部分。

### 测试

通过定期更改和多次客户端实施，测试对于网络安全至关重要。在任何网络升级之前，都会大量使用各种测试工具和场景。

测试是根据规范生成的，并创建各种场景。有些是单独测试客户端，有些是用所有客户端对模拟测试网。对于开发周期的不同部分和客户端的不同部分，使用不同的测试工具。这包括状态转换测试、模糊测试、影子分叉、RPC 测试、客户端单元测试和 CI/CD 等。

> 以下是专用于测试的仓库的简短列表：
>
> - https://github.com/ethereum/tests
> - https://github.com/ethereum/retesteth
> - https://github.com/ethereum/execution-spec-tests
> - https://github.com/ethereum/hive
> - https://github.com/kurtosis-tech/kurtosis
> - https://github.com/MariusVanDerWijden/FuzzyVM
> - https://github.com/lightclient/rpctestgen

### 协调

以太坊开发与传统的企业类型项目非常不同。首先，它是开放和公开的，任何人都可以评论或贡献。其次，有许多不同的团队在不同的部分工作。总共有大约 20 个来自不同组织的不同团队在以太坊上工作。

与专有技术不同，以太坊开发人员不是竞争而是合作。随着复杂性不断增加，基本上没有人能成为所有以太坊的专家。相反，人们在特定领域发展专业知识并与他人合作。这要归功于以太坊的模块化，并允许开发人员专注于他们个人喜欢的挑战。

> 新功能或更改的传统开发周期是 `Idea - Research - Development - Testing - Adoption`。然而，这个周期的任何时刻都可能出现问题，导致从头开始迭代。

为了能够进行网络升级并就当前的发展重点达成一致，需要进行一定的协调。所有这些都是公开的，任何有兴趣了解核心协议的人都可以关注。协调主要通过 [PM 仓库](https://github.com/ethereum/pm) 中安排的定期调用进行。开发者呼吁有多种类型，其中最大的一种是“所有核心开发者”(ACD)。所有参与团队的代表都在这里讨论共识的当前发展或执行层。

社区的想法和提议的更改是使用 [EIP 流程](https://eips.ethereum.org/EIPS/eip-1) 进行协调的。此外，还有一些讨论论坛。讨论核心升级的最大论坛是 https://ethresear.ch. 另一个与 EIP 进程相连并用于讨论具体提案的论坛是 [以太坊 Magicians](https://ethereum-magicians.org/)。
R&D Discord 服务器(在 EPFsg Discord 中 ping 我们以获得邀请)和客户端团队群组中也正在进行许多重要的讨论。还有一些场外或研讨会，许多核心开发人员亲自会面，以面对面地加快流程。

## 额外阅读和练习

要测试您对以太坊基础知识的了解，请尝试以下测验：
https://ethereum.org/quizzes

了解有关路线图的更多信息：
https://ethereum.org/roadmap

要探索以太坊协议的各个部分，请查看人们在 EPF 中所做的工作：
https://github.com/eth-protocol-fellows/cohort-three/tree/master/projects
https://github.com/eth-protocol-fellows/cohort-four/tree/master/projects

[Devcon archive](https://archive.devcon.org/archive/) 充满了深入探讨以太坊的各种技术和非技术方面的令人难以置信的演讲。

Alex 和 Mike 关于最近路线图的[回顾性文件](https://notes.ethereum.org/@mikeneuder/rcr2vmsvftv) 提供了对以太坊发展、价值观和目标的深刻见解。

了解有关 Unix 和贝尔实验室历史的更多信息：
https://www.theregister.com/2024/02/16/what_is_unix/
https://www.deusinmachina.net/p/history-of-unix

另外推荐几本书：

如果您对以太坊的早期、其创始人和创作的故事感兴趣，请查看书籍[无限机器](https://www.camirusso.com/book)。另一个类似的是[Out of the Ether](https://www.goodreads.com/book/show/55360267-out-of-the-ether)。 (请联系我获取 epub)

最近发布的一份提供全面见解的出版物是 [以太坊的绝对要点](https://www.routledge.com/Absolute-Essentials-of-Ethereum/Dylan-Ennis/p/book/9781032334189)。它涵盖了[各种主题](https://www.coindesk.com/consensus-magazine/2024/02/07/absolute-essentials-of-ethereum-by-paul-dylan-ennis-an-excerpt/)，我强烈推荐它。

[掌握以太坊](https://github.com/ethereumbook/ethereumbook) 是 aantop 出版的一本很棒的区块链书籍，涵盖从基础知识到技术细节的所有内容。它已有几年历史并且已经过时，但仍然可以成为一个很好的灵感。
