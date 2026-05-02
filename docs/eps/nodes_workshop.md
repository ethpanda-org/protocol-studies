# 学习小组讲座 |使用以太坊客户端  

第三天，除了[常规演示](https://epf.wiki/#/eps/week3)之外，[Mario](https://twitter.com/TMIYChao)准备了一个研讨会，让您亲身体验使用以太坊客户端。我们将学习如何通过运行执行和共识客户端来启动以太坊节点。 

您可以[参加我们的现场研讨会](https://meet.ethereum.org/eps-office-hours)，周三正常办公时间下午 3 点 UTC 或观看去年研讨会的录像。

[录音](https://www.youtube.com/embed/KxXowOZ2DLs?si=yLpNoczrUmxj4kE4 ':include :type=iframe width=100% height=560 frameborder="0" allow="fullscreen" allowfullscreen encrypted-media gyroscope picture-in-picture web-share')

确保准备好您的环境并学习了解该过程所需的基础知识： 

## 先决条件

让自己熟悉第 1 天中所述的以太坊客户端架构，您可以查看第 2 天和第 3 天，以便更好地了解研讨会执行的内容。本研讨会将默认使用 geth+lighthouse，但如果您有其他首选客户端可以尝试，请熟悉其文档。 

- https://ethereum.org/en/developers/docs/nodes-and-clients/node-architecture/
- https://ethereum.org/en/developers/docs/nodes-and-clients/run-a-node/
- 客户端您首选客户端对的文档 
- 基本的 bash/cli shell 技能 
    - https://btholt.github.io/complete-intro-to-linux-and-the-cli/, https://ubuntu.com/tutorials/command-line-for-beginners
- 让自己熟悉[Ephemery 测试网](https://ephemery.dev)

### 准备您的环境 

要参加研讨会，请确保准备好您的环境。 

- 更新系统并安装依赖项，这样在研讨会期间就不会区块您了。  
- 安装我们需要的基本实用程序，例如curl、git、gpg、docker
- 安装您想要尝试编译的客户端语言的编译器(我们默认使用预构建的二进制文件)
- 研讨会环境将是一个全新的 Debian 12 实例，但您可以使用任何首选发行版、Mac 或 WSL。在其他基于 UNIX 的系统上，过程可能非常相似，您始终可以设置 VM 来复制相同的环境。 

我们只会在测试网上运行客户端，因此硬件要求极低 - 研讨会的目标不是同步链的尖端，而只是演示节点的工作原理。默认客户端对将是 geth+lighthouse 但如果有足够的时间我们可以演示切换对。 

## 概要

- 运行节点简介
    - 选择客户端对和环境
- 获取客户端 
    - 下载并验证二进制文件
    - 编译客户端？ 
    - Docker 设置？ 
- 客户端配对设置
    - 在 Holesky 上运行 EL+CL 客户端
    - 使用自定义创世在 Ephemery 上运行 
    - 切换 CL 或 EL 客户端
        - Nimbus？ 
        - Erigon？ 
- 使用客户端
    - 访问 RPC
        - http、控制台、钱包
        - 添加验证者 
- 如果有时间可以补充运动
    - 系统服务
    - 监控节点
- 加入[Office Hours 通话](https://meet.ethereum.org/eps-office-hours) 关注研讨会、提出问题并获得故障排除帮助。

## 额外阅读和练习 

有很多事情我们不会在研讨会期间演示，因此您可以在之后自己尝试。 

- 使用 Grafana 仪表板设置监控，该仪表板显示各种客户端参数和功能的详细信息
- 启用更高的日志详细程度，阅读客户端调试日志以了解其低级别进程 
- 使用钱包、开发工具、web3.py 或 JS 控制台连接到您的节点 
- 将您的节点连接到[爬虫](https://www.ethernets.io/help/)并检查它了解了哪些详细信息，发现新的对等节点

- https://github.com/eth-educators/ethstaker-guides/blob/main/docs/holesky-node.md
- https://notes.ethereum.org/@launchpad/node-faq-merge
- https://www.coincashew.com/coins/overview-eth/guide-or-how-to-setup-a-validator-on-eth2-mainnet/part-i-installation/monitoring-your-validator-with-grafana-and-prometheus




