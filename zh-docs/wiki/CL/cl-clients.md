# 共识层的实现

本页面涵盖了所有共识客户端实现的资源，无论是生产环境还是开发环境。它提供了每个客户端的独特特性、体系结构、基本指南和资源的概述。

> 共识客户端最初被称为**eth2.0客户端**，现在已经废弃了，但你仍然可以在他们的仓库中找到这个引用。

有多个共识层客户端被开发来参与以太坊权益证明（PoS）机制。最受欢迎的自由/开源软件和产品版本是[Lighthouse](https://lighthouse-book.sigmaprime.io/), [Lodestar](https://lodestar.chainsafe.io/), [Nimbus](https://nimbus.team/index.html), [Prysm](https://prysmaticlabs.com/) 和 [Teku](https://consensys.io/teku)。 这些客户端使用不同的编程语言开发，提供独特的功能并提供不同的性能配置。所有客户端都开箱即用地支持以太坊主网以及主动测试网。实现的多样性使网络受益于客户机的多样性。如果您正在选择使用的客户端，当前客户端的多样性应该是主要因素之一。

## 生产环境中的客户端

## LightHouse

[Lighthouse](https://lighthouse-book.sigmaprime.io/) 是用Rust编程语言开发的客户端。它是一个功能齐全的以太坊共识客户端，可以用作信标节点或验证客户端。它是由[Sigma Prime](https://sigmaprime.io/)开发的。

### 特性

它还可以提供一个web界面[Siren](https://lighthouse-book.sigmaprime.io/lighthouse-ui.html)，从而能够轻松地监视信标节点和验证器性能。

### 安装客户端

Lighthouse客户端可以通过多种方式安装——源代码构建、运行预构建的二进制文件或者使用docker。他们还提供不同架构的构建，如[ARM](https://lighthouse-book.sigmaprime.io/pi.html)和[交叉编译](https://lighthouse-book.sigmaprime.io/cross-compiling.html)指南。[Validator](https://lighthouse-book.sigmaprime.io/mainnet-validator.html)客户端与主二进制文件捆绑在一起。
#### 使用 Docker

Lighthouse提供了一个使用docker安装客户端的指南。他们有[Docker Hub](https://lighthouse-book.sigmaprime.io/docker.html#docker-hub)和[from source](https://lighthouse-book.sigmaprime.io/docker.html#building-the-docker-image)构建Docker Hub的选项。
#### 源代码构建

和Prysm一样，它也支持多种硬件和操作系统，可以从源代码构建客户端。[文档](https://lighthouse-book.sigmaprime.io/installation-source.html)。在构建客户端之前，请确保安装了正确的依赖项。

#### 预构建二进制文件

许多不同操作系统和体系结构的预构建二进制文件都是可用的。它们还提供了可移植版本，这些版本为了更好的平台兼容性而牺牲了编译器性能选项。发布的二进制文件由来自 security@sigmaprime.io 的 gpg 密钥‘ 15E66D941F697E28F49381F426416DC3F30674B0 ’签名。阅读[文档][https://lighthouse-book.sigmaprime.io/installation-binaries.html]关于使用预构建的二进制文件安装客户端的步骤。

### 附加功能和安全注意事项

Lighthouse客户端相当高级，还具备以下特性。

- [削减保护](https://lighthouse-book.sigmaprime.io/faq.html#what-is-slashing-protection)
- [ Doppelganger 保护](https://lighthouse-book.sigmaprime.io/validator-doppelganger.html#doppelganger-protection)
- [ 运行 Slasher](https://lighthouse-book.sigmaprime.io/slasher.html)
- [MEV 的 Builder API](https://lighthouse-book.sigmaprime.io/builders.html#maximal-extractable-value-mev)

### 最常见问题

有关客户的更多常见问题，请参阅 [FAQ](https://lighthouse-book.sigmaprime.io/faq.html)。

## Lodestar

通过TypeScript中的ChainSafe

## Nimbus

通过 Nim 中的 Status
## Prysm

[Prysm](https://docs.prylabs.network/docs/getting-started)  是用Go编程语言开发的客户端。它是最受欢迎的客户之一，并且有一个很大的社区。使用此客户端，验证者可以参与以太坊PoS机制。Prysm可以用作信标节点或验证客户端。它可以辅助执行层客户处理交易和数据块。当执行客户端与Prysm集成时，它首先与区块头同步，因为作为信标节点，它对链有完整的视图。它向EL客户端传递最新的区块头信息。然后，EL客户端可以从其p2p网络请求块主体。这在所有的共识层客户端中很常见。

除了以太坊主网，Prysm 还可以运行在 Goerli， Holesky， Pyrmont等测试网络上。Prysm 可以与不同的 EL 客户端集成，例如 [Geth](https://geth.ethereum.org/)，[Nethermind](https://www.nethermind.io/nethermind-client)，和 [Besu](https://besu.hyperledger.org/) 等等。它有一个 web 界面来监控信标链和验证器的性能。它还有 RESTful API 与信标链和验证器客户端交互。

### 安装客户端

安装过程可以使用 docker 自动化过程完成，也可以使用源代码手动构建过程。这两种方法都可以灵活地在不同的操作系统、硬件和角色（信标节点和/或验证器客户端）上运行客户机。

####使用Docker

安装客户端最简单快捷的方法是[使用docker]（https://docs.prylabs.network/docs/install/install-with-docker）。以这种方式进行的大多数与客户机相关的活动都使用[配置文件]（https://docs.prylabs.network/docs/install/install-with-docker#configure-ports-optional）。

####从源头构建

通过[源代码]（https://docs.prylabs.network/docs/install/install-with-bazel）构建客户端，可以进一步了解客户端。人们还需要注意硬件规格和[要求](https://docs.prylabs.network/docs/install/install-with-bazel#review-system-requirements)，以便客户端顺利运行。

###示例运行

下面是在以太坊主网上运行Prysm客户端的示例，Geth 节点作为执行客户端：

```bash
条款与条件：https://github.com/prysmaticlabs/prysm/blob/develop/TERMS_OF_SERVICE.md


键入 “接受” 以接受本条款与条件[接受/拒绝]:（默认：拒绝）：
接受
[2024-03-10 23:42:11]  INFO Finished reading JWT secret from /home/userDemo/code/jwt.hex
[2024-03-10 23:42:11]  WARN flags: Running on Ethereum Mainnet
[2024-03-10 23:42:11]  WARN node: In order to receive transaction fees from proposing blocks, you must provide flag --suggested-fee-recipient with a valid ethereum address when starting your beacon node. Please see our documentation for more information on this requirement (https://docs.prylabs.network/docs/execution-node/fee-recipient).
[2024-03-10 23:42:11]  INFO node: Checking DB database-path=/home/userDemo/.eth2/beaconchaindata
[2024-03-10 23:42:11]  INFO db: Opening Bolt DB at /home/userDemo/.eth2/beaconchaindata/beaconchain.db
[2024-03-10 23:42:12]  WARN genesis: database contains genesis with htr=0x7e76880eb67bbdc86250aa578958e9d0675e64e714337855204fb5abaaf82c2b, ignoring remote genesis state parameter
[2024-03-10 23:42:21]  INFO detected supported config in remote finalized state, name=mainnet, fork=capella
[2024-03-10 23:42:22]  INFO Downloaded checkpoint sync state and block. block_root=0xe6c065b28ef4826da69ba234394f1e293473a5fb56fa3e053bd73d650dd6061a block_slot=8607136 state_root=0x42ef0d1f525b019097e0b30904215703cb784474ad7a186087fd7bdf3ba9c25d state_slot=8607136
[2024-03-10 23:42:22]  INFO db: detected supported config for state & block version, config name=mainnet, fork name=capella
[2024-03-10 23:42:23]  INFO db: saving checkpoint block to db, w/ root=0xe6c065b28ef4826da69ba234394f1e293473a5fb56fa3e053bd73d650dd6061a
[2024-03-10 23:42:23]  INFO db: calling SaveState w/ blockRoot=e6c065b28ef4826da69ba234394f1e293473a5fb56fa3e053bd73d650dd6061a
[2024-03-10 23:42:23]  INFO node: Deposit contract: 0x00000000219ab540356cbb839cbe05303d7705fa
[2024-03-10 23:42:23]  INFO p2p: Running node with peer id of 16Uiu2HAmPa9EyCpvNwFizaRY7PXdedPgnUPYAuXwdiVcQ83h1uoQ
[2024-03-10 23:42:24]  INFO rpc: gRPC server listening on port address=127.0.0.1:4000
[2024-03-10 23:42:24]  WARN rpc: You are using an insecure gRPC server. If you are running your beacon node and validator on the same machines, you can ignore this message. If you want to know how to enable secure connections, see: https://docs.prylabs.network/docs/prysm-usage/secure-grpc
[2024-03-10 23:42:24]  INFO node: Starting beacon node version=Prysm/v5.0.1/a1a81d1720a0a3b850992d4825d0a023baa8e65a. Built at: 2024-03-08 20:31:40+00:00
[2024-03-10 23:42:24]  INFO initial-sync: Waiting for state to be initialized
[2024-03-10 23:42:24]  INFO blockchain: Blockchain data already exists in DB, initializing...
[2024-03-10 23:42:24]  INFO Backfill service not enabled.
[2024-03-10 23:42:24]  INFO gateway: Starting gRPC gateway address=127.0.0.1:3500
[2024-03-10 23:42:24]  INFO initial-sync: Received state initialized event
[2024-03-10 23:42:24]  INFO initial-sync: Starting initial chain sync...
[2024-03-10 23:42:24]  INFO initial-sync: Waiting for enough suitable peers before syncing required=3 suitable=0
[2024-03-10 23:42:24]  INFO p2p: Started discovery v5 ENR=enr:-MK4QLql3XgYbbOfu3gcaUijzcz2RwBE9utUVhR1YhcvqgErfjfjs88JqJIr7FzwpyoclMP8pBXWcUKCWtKKpKnoio-GAY4qiBRfh2F0dG5ldHOIAAAAAAAAAACEZXRoMpC7pNqWBAAAAAAdBAAAAAAAgmlkgnY0gmlwhMCoAZqJc2VjcDI1NmsxoQOiMrSMfZgcZfphI6hjf84nwmq7wMne1wML_H_EQdtn04hzeW5jbmV0cwCDdGNwgjLIg3VkcIIu4A
[2024-03-10 23:42:24]  INFO p2p: Node started p2p server multiAddr=/ip4/192.168.1.154/tcp/13000/p2p/16Uiu2HAmPa9EyCpvNwFizaRY7PXdedPgnUPYAuXwdiVcQ83h1uoQ
[2024-03-10 23:42:34]  INFO blockchain: Called new payload with optimistic block payloadBlockHash=0x492df9344dbd slot=8607137
[2024-03-10 23:42:38]  WARN blockchain: Could not update head error=head at slot 8607136 with weight 97641 is not eligible, finalizedEpoch, justified Epoch 268971, 268972 != 268973, 268973
[2024-03-10 23:42:38]  WARN blockchain: could not determine node weight root=0x0000000000000000000000000000000000000000000000000000000000000000
[2024-03-10 23:42:38]  INFO blockchain: Synced new block block=0xc74dfd56... epoch=268973 finalizedEpoch=268973 finalizedRoot=0xe6c065b2... slot=8607137
[2024-03-10 23:42:38]  INFO blockchain: Finished applying state transition attestations=111 payloadHash=0x492df9344dbd slot=8607137 syncBitsCount=400 txCount=153
```

#### 没有轻量级客户端支持

目前Prysm还没有轻量级客户端支持。

### 安全性考虑因素和最佳实践
共识客户端安全性在某种程度上比执行层客户端安全性更重要，因为共识客户端不仅负责网络的安全性，还负责验证器的安全性。负责有效的区块执行，选择正确的链管理相关财务。Prysm 列出了一些[最佳实践](https://docs.prylabs.network/docs/security-best-practicespractices)来确保客户端和网络的安全性。其中，以下是最重要的：

#### 避免削减
验证者要对他们在链上的行为负责，以保证协议的安全性和活性。[文档](https://docs.prylabs.network/docs/security-best-practices#slash-avoidance)中列出了避免大幅删减的指导方针。

#### 钱包和密钥管理

虽然用于下注和取款的凭据是分开的，但保证密钥的安全是很重要的。[文档](https://docs.prylabs.network/docs/security-best-practices#slash-avoidance)概述了保持密钥安全的最佳实践。

### 最常见的问题

有关客户端的更多常见问题，请参阅 [FAQ](https://docs.prylabs.network/docs/faq) 。

## Teku

[Teku](https://consensys.io/teku) 是一个用Java编程语言开发的客户端。它是由 [ConsenSys]（https://consensys.net/）开发的。它是一个功能齐全的以太坊 2.0 客户端，可以用作信标节点或验证器客户端。它可以与 Besu 等执行客户端一起工作。它可以在以太坊主网和测试网（如 Goerli ， Sepolia 和 Holesky ）上工作。它有一个 web 界面来监控信标链和验证器的性能。Teku 提供 beacon 客户端和 validator 客户端，也可以作为 docker 容器运行。

### 安装客户端

Teku 客户端可以使用三种主要方式安装——使用 docker 、从源代码构建和使用预构建的二进制文件。

#### 使用 Docker

Teku 提供了一个更具有说明性的指南来使用 docker 安装客户端。他们有 [Docker Hub](https://docs.teku.consensys.io/get-started/install/run-docker-image#run-teku-docker-image)和使用 [Docker compose](https://docs.teku.consensys.io/get-started/install/run-docker-image#run-teku-using-docker-compose) 的选项。

#### 从源代码构建

从源代码构建客户端也是 Teku 的一个选项。客户端是用 Java 编写的。[文档](https://docs.teku.consensys.io/get-started/install/build-from-source)提供了针对不同操作系统（如 Linux 、 MacOS 和 Windows）从源代码构建客户端的步骤。

#### 预构建的二进制文件

也许在生产环境中运行客户端最简单的方法是使用预构建的二进制文件。基于Java的二进制文件非常稳定，可以在不同的操作系统上运行。[文档](https://docs.teku.consensys.io/get-started/install/install-binaries)提供了使用预构建的二进制文件安装和运行客户端的步骤。

### 附加功能和安全注意事项

从最近的状态运行 Teku 客户端非常容易，并且同步速度更快。使用最近确定的检查点状态，Teku 可以在弱主观性时期内同步。以太坊 Beacon chain 检查点列表可以在[这里](https://eth-clients.github.io/checkpoint-sync-endpoints/)找到。有关如何使用最近确定的检查点状态运行 Teku 客户端，请参阅[文档](https://docs.teku.consensys.io/get-started/checkpoint-start)。

Teku 还提供了精简保护机制，特别是在从另一个客户端迁移到 Teku 的情况下。[文档](https://docs.teku.consensys.io/reference/cli/subcommands/slashing-protection#import)提供了从其他客户端迁移到 Teku 的步骤。

## 开发中的客户端

### Caplin

一个嵌入在 Erigon 的共识客户端。

### Grandine

最初是 Rust 中的一个专有客户端，最近变成了开源

### lambda 类客户端

由LC在Elixir

### 附加阅读

[Analysis of CL clients performance, outdated](https://mirror.xyz/0x934e6B4D7eee305F8C9C42b46D6EEA09CcFd5EDc/b69LBy8p5UhcGJqUAmT22dpvdkU-Pulg2inrhoS9Mbc)
