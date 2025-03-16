# 以太坊客户端使用讲座

在第 3 天，除了 [常规讲座](https://epf.wiki/#/eps/week3)，[Mario](https://twitter.com/TMIYChao) 还准备了一个工作坊，带领大家亲手操作，了解如何使用以太坊客户端。我们将学习如何通过运行执行层（EL）和共识层（CL）客户端来启动以太坊节点。

你可以在 [办公时间后](https://meet.ethereum.org/eps-office-hours) 于 UTC 时间 3PM 加入我们的直播工作坊，或观看去年的工作坊录制。

[观看录制](https://www.youtube.com/embed/KxXowOZ2DLs?si=yLpNoczrUmxj4kE4 ':include :type=iframe width=100% height=560 frameborder="0" allow="fullscreen" allowfullscreen encrypted-media gyroscope picture-in-picture web-share')

确保你准备好环境，并了解必要的基础知识，以便理解操作过程：

## 前提条件

请先熟悉以太坊客户端架构，如第 1 天所述，并参考第 2 天和第 3 天的内容，以更好地理解工作坊执行的操作。工作坊将默认使用 geth + lighthouse 客户端，如果你希望使用其他客户端，请提前查看相关文档。

- [以太坊节点架构](https://ethereum.org/en/developers/docs/nodes-and-clients/node-architecture/)
- [如何运行以太坊节点](https://ethereum.org/en/developers/docs/nodes-and-clients/run-a-node/)
- 你选择的客户端对的文档
- 基本的 bash/cli 命令行技能
    - [Linux 和命令行入门](https://btholt.github.io/complete-intro-to-linux-and-the-cli/)，[Ubuntu 命令行教程](https://ubuntu.com/tutorials/command-line-for-beginners)
- 熟悉 [Ephemery 测试网](https://ephemery.dev)

### 准备环境

为了跟上工作坊，请确保你已经准备好环境。

- 更新系统并安装所需的依赖，以免在工作坊中遇到阻碍。
- 安装工作坊所需的基本工具，如 curl、git、gpg 和 docker。
- 安装你希望尝试编译的客户端语言的编译器（我们默认使用预构建的二进制文件）。
- 工作坊环境将基于全新的 Debian 12 实例，但你可以使用任何你喜欢的发行版、Mac 或 WSL。其它基于 Unix 的系统的过程应该类似，你也可以设置虚拟机来模拟相同的环境。

我们将只在测试网上运行客户端，因此硬件要求非常低——工作坊的目的是展示节点的工作原理，而不是同步链的最新状态。默认的客户端对将是 geth + lighthouse，但如果时间足够，我们也可以展示如何切换客户端对。

## 大纲

- 介绍运行节点
    - 选择客户端对和环境
- 获取客户端
    - 下载和验证二进制文件
    - 编译客户端？
    - 使用 Docker 设置？
- 客户端对设置
    - 在 Holesky 上运行 EL + CL 客户端
    - 使用自定义创世块在 Ephemery 上运行
    - 切换 CL 或 EL 客户端
        - Nimbus？
        - Erigon？
- 使用客户端
    - 访问 RPC
        - http、控制台、钱包
        - 添加验证者
- 如果时间允许，额外的练习
    - 使用 systemd 服务
    - 监控节点
- 加入 [办公时间通话](https://meet.ethereum.org/eps-office-hours) 参与工作坊，提问并获得故障排除帮助。

## 额外阅读和练习

有一些内容我们在工作坊中不会演示，你可以在之后自己尝试。

- 使用 Grafana 仪表盘进行监控，查看客户端各项参数和功能的详细信息。
- 启用更高的日志详细度，阅读客户端调试日志，了解其底层过程。
- 使用钱包、开发工具、web3.py 或 JS 控制台连接到你的节点。
- 将节点连接到 [爬虫](https://www.ethernets.io/help/)，查看它收集到的节点详情，并发现新的对等节点。

- [Holesky 节点设置指南](https://github.com/eth-educators/ethstaker-guides/blob/main/docs/holesky-node.md)
- [以太坊节点常见问题解答](https://notes.ethereum.org/@launchpad/node-faq-merge)
- [以太坊验证者监控设置指南](https://www.coincashew.com/coins/overview-eth/guide-or-how-to-setup-a-validator-on-eth2-mainnet/part-i-installation/monitoring-your-validator-with-grafana-and-prometheus)
