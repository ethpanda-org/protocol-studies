# 学习小组 第5周 | 使用以太坊客户端

在第5周，除了[常规演讲](https://epf.wiki/#/eps/week5)之外，我们还准备了一个工作坊，给大家提供亲身体验使用以太坊客户端的机会。我们将学习如何通过运行执行客户端和共识客户端来启动以太坊节点。

可以在StreamEth或Youtube上观看由[Mario](https://twitter.com/TMIYChao)主持的工作坊录音。

[录音](https://www.youtube.com/embed/KxXowOZ2DLs?si=yLpNoczrUmxj4kE4 ':include :type=iframe width=100% height=560 frameborder="0" allow="fullscreen" allowfullscreen encrypted-media gyroscope picture-in-picture web-share')

确保准备好你的环境，并学习理解该过程所需的基本知识：

## 前提条件

熟悉以太坊客户端架构，具体请参见第1周内容。如果你想更好地理解工作坊的执行内容，可以查看第2周和第3周。工作坊默认使用geth和lighthouse，但如果你有其他偏好的客户端，确保了解其文档。

- https://ethereum.org/en/developers/docs/nodes-and-clients/node-architecture/
- https://ethereum.org/en/developers/docs/nodes-and-clients/run-a-node/
- 你所偏好的客户端对的文档
- 基本的bash/cli shell技能
    - https://btholt.github.io/complete-intro-to-linux-and-the-cli/，https://ubuntu.com/tutorials/command-line-for-beginners

准备好你的环境，更新系统并安装依赖项，以确保在工作坊期间不会卡住。

- 工作坊环境将是一个全新的Debian 12实例，但你也可以使用任何偏好的发行版。这个过程在其他基于Unix的系统（如Mac）上也非常相似，但你可以设置一个虚拟机来复制该环境。
- 安装我们所需的基本工具，如curl、git、gpg、docker、编译器

我们只在测试网运行客户端，因此硬件要求最低——工作坊的目标不是同步区块链的最新状态，而是演示节点如何工作。默认客户端对将是geth和lighthouse，但如果时间允许，我们可以演示切换客户端对。

## 大纲

- 运行节点简介
    - 选择客户端对和环境
- 获取客户端
    - 下载和验证二进制文件
    - 编译客户端？
    - Docker设置？
- 客户端对设置
    - 在Holesky上运行EL+CL客户端
    - 使用自定义创世文件在Ephemery上运行
    - 切换CL或EL客户端
        - Nimbus？
        - Erigon？
- 使用客户端
    - 访问RPC
        - http，控制台，钱包
        - 添加验证者
- 如果时间充裕，额外练习
    - Systemd服务
    - 监控节点
- 在直播结束后，我们可以切换到[jitsi](meet.ethquokkaops.io/EPFsgWorkshop)进行进一步讨论和故障排除

## 额外阅读和练习

工作坊中我们无法演示的很多内容，您可以在之后自行尝试。

- 使用Grafana仪表板设置监控，显示有关各种客户端参数和功能的详细信息
- 启用更高的日志详细程度，阅读客户端调试日志，了解其底层过程
- 使用钱包、开发工具、web3.py或JS控制台连接到你的节点
- 将节点连接到[爬虫](https://www.ethernets.io/help/)，查看它学习到的细节，发现新节点

- https://github.com/eth-educators/ethstaker-guides/blob/main/holesky-node.md
- https://notes.ethereum.org/@launchpad/node-faq-merge
- https://www.coincashew.com/coins/overview-eth/guide-or-how-to-setup-a-validator-on-eth2-mainnet/part-i-installation/monitoring-your-validator-with-grafana-and-prometheus
