# 共识客户端

> **共识客户端**，以前称为 *eth2 客户端*，运行以太坊的权益证明共识算法，允许网络就信标链的头部达成一致。共识客户端不参与验证/广播交易或执行状态转换：这是通过执行客户端来完成的。共识客户端不证明或提出新的区块：这是由验证者客户端完成的，它是共识客户端的可选附加组件。

## 概览表

这些客户端采用不同的编程语言开发，提供独特的功能并提供不同的性能配置文件。所有客户端均支持以太坊主网开箱即用以及活动测试网。各种实施方式使网络能够从客户端多样性中受益。如果您选择使用客户端，则当前客户端的多样性应该是主要因素之一。

| 客户端 |语言 |开发商 |状态 |
| ----------------------------------------------------------------------- | ---------- | ------------------- | ----------- |
| [Lighthouse](https://github.com/sigp/lighthouse) |Rust|Sigma Prime |生产|
| [Lodestar](https://github.com/ChainSafe/lodestar) |TypeScript |ChainSafe |生产|
| [Nimbus](https://github.com/status-im/nimbus-eth2) |Nimbus |Status |生产|
| [Prysm](https://github.com/prysmaticlabs/prysm) |Go |棱镜实验室 |生产|
| [Teku](https://github.com/ConsenSys/teku) |Java |ConsenSys|生产|
| [Grandine](https://github.com/grandinetech/grandine) |Rust|Grandine开发商|生产|
| [Caplin](https://github.com/ledgerwatch/erigon) |Go |Erigon |开发|
| [Lambda类](https://github.com/lambdaclass/lambda_ethereum_consensus) |长生不老药 | LambdaClass |开发|


## 分布

绝大多数节点运营商目前使用 Prysm 或 Lighthouse 作为共识客户端。
为了支持信标链(以前的 ETH2)的健康，建议使用不同的客户端。
[为什么？](https://clientdiversity.org/#why)


### Lighthouse
Lighthouse 由 Sigma Prime 用 Rust 编写，强调安全性和性能。它被广泛采用，但建议谨慎，因为绝对多数可能会导致链分裂。
Lighthouse 在 Apache 2.0 下获得许可，并以其在生产环境中的稳健性而闻名。 

Lighthouse 为每个平台提供二进制文件，包括 ARM 并允许交叉编译。有些可移植版本会牺牲编译器性能选项以实现更好的平台兼容性。发布的二进制文件由来自 security@sigmapprime.io 的 gpg 密钥 `15E66D941F697E28F49381F426416DC3F30674B0` 进行签名。

值得注意的特点：
- [交叉编译](https://lighthouse-book.sigmaprime.io/installation_cross_compiling.html)
- [砍伐保护](https://lighthouse-book.sigmaprime.io/validator_slashing_protection.html)
- [分身保护](https://lighthouse-book.sigmaprime.io/validator_doppelganger.html)
- [运行 Slasher](https://lighthouse-book.sigmaprime.io/advanced_slasher.html)
- [区块提议者-仅](https://lighthouse-book.sigmaprime.io/advanced_proposer_only.html)
- [普罗米修斯与格拉法纳](https://lighthouse-book.sigmaprime.io/api_metrics.html)



### Lodestar
Lodestar 是 ChainSafe 开发的基于 TypeScript 的以太坊共识客户端，专为快速原型设计和浏览器兼容性而定制。
它支持 beacon 节点和 验证者客户端功能，为以太坊协议开发提供 BLS 和 SSZ 等基本库。
Lodestar 根据 Apache License 2.0 和 GNU Lesser General Public License (LGPL) 获得双重许可，允许用户在宽松许可模式和 Copyleft 许可模式之间进行选择。

值得注意的特点：
- [验证者客户端](https://chainsafe.github.io/lodestar/run/validator-management/vc-configuration)
- [MEV 和构建者集成](https://chainsafe.github.io/lodestar/run/beacon-management/mev-and-builder-integration)
- [轻客户端](https://chainsafe.github.io/lodestar/libraries/lightclient-prover/lightclient)
- [证明者](https://chainsafe.github.io/lodestar/libraries/lightclient-prover/prover)
- [普罗米修斯与格拉法纳](https://chainsafe.github.io/lodestar/run/logging-and-metrics/prometheus-grafana)
- [远程监控](https://chainsafe.github.io/lodestar/run/logging-and-metrics/client-monitoring)

### 棱镜
Prysmatic Labs 的 Prysm 客户端用 Go 编写，专注于可用性和可靠性。它包括完整的信标节点实现和验证者客户端，利用 gRPC 进行进程间通信，利用 BoltDB 进行存储，并利用 libp2p 进行网络连接。 Prysm 旨在安全参与以太坊的权益证明共识。

值得注意的特点：
- [验证者客户端](https://docs.prylabs.network/docs/wallet/nondeterministic)
- [配置MEV 构建者](https://docs.prylabs.network/docs/advanced/builder)
- [运行 Slasher](https://docs.prylabs.network/docs/prysm-usage/slasher)
- [普罗米修斯与格拉法纳](https://docs.prylabs.network/docs/prysm-usage/monitoring/grafana-dashboard)
- [最佳安全实践详细](https://docs.prylabs.network/docs/security-best-practices)

### Nimbus
Nimbus 由 Status 在 Nim 中开发，针对资源效率进行了优化。它支持智能手机和 Raspberry Pi 等轻量级设备，在功能强大的服务器上运行时可以为其他任务节省资源。 Nimbus 具有集成的验证者客户端支持、远程签名、性能分析工具和强大的验证者监控功能。

值得注意的特点：
- [运行执行客户端](https://nimbus.guide/eth1.html)
- [验证者客户端](https://nimbus.guide/validator-client.html)
- [MEV 和构建者集成](https://nimbus.guide/external-block-builder.html)
- [普罗米修斯与格拉法纳](https://nimbus.guide/metrics-pretty-pictures.html)

### Teku
ConsenSys 的 Teku 是基于 Java 的以太坊共识客户端，提供全面的企业级功能。它包括完整的信标节点实现、验证者客户端、用于节点管理的 REST API、用于监控的 Prometheus 指标以及用于验证者签名密钥的外部密钥管理。 Teku 适用于需要可扩展性和操作控制的企业级以太坊部署。

值得注意的特点：
- [验证者客户端](https://docs.teku.consensys.io/concepts/proof-of-stake)
- [砍伐保护](https://docs.teku.consensys.io/how-to/prevent-slashing/use-a-slashing-protection-file)
- [构建者和 MEV-boost](https://docs.teku.consensys.io/concepts/builder-network)
- [检测分身](https://docs.teku.consensys.io/how-to/prevent-slashing/detect-doppelgangers)
- [普罗米修斯与格拉法纳](https://docs.teku.consensys.io/how-to/monitor/use-metrics)

### Grandine
Grandine 是一个快速且轻量级的以太坊共识客户端，其设计重点是高性能和简单性。它是用 Rust 编写的，与 Lighthouse 相同，并共享它的一些库。 
Grandine 的开发以并行化和高效的资源利用为核心，旨在通过提供现有客户端的简化替代方案来突破以太坊的 共识层的界限。
其架构经过优化，可以最大限度地减少高端机器上的延迟并最大限度地提高吞吐量，使其非常适合高性能至关重要的环境。

值得注意的特点：
- [验证者客户端](https://docs.grandine.io/validator_client.html)
- [砍伐保护](https://docs.grandine.io/slashing_protection.html)
- [构建者和 MEV](https://docs.grandine.io/builder_api_and_mev.html)
- [运行 Slasher](https://github.com/grandinetech/grandine/tree/develop/slasher)
- [普罗米修斯](https://docs.grandine.io/metrics.html) 和 [Grafana](https://github.com/grandinetech/grandine/tree/develop/metrics)

### Caplin

Caplin 是集成在 Erigon 执行客户端中的共识客户端。它基本上是 Erigon 中的一个额外功能，允许在没有任何外部 CL 的情况下运行它。  

- https://erigon.gitbook.io/erigon/advanced-usage/consensus-layer/caplin
- https://github.com/ledgerwatch/erigon/?tab=readme-ov-file#caplin

### Lambda类

LambdaClass 开发了一个用 Elixir 编写的客户端。它已在 EPF4 期间启动，并发展成为功能齐全的实现。它仍在积极开发中，尚未用于生产。

- https://github.com/lambdaclass/lambda_ethereum_consensus

## 其他资源

- [ETH Docker](https://eth-docker.net/)
- [以太节点](https://ethernodes.org/)
- [客户端多样性](https://clientdiversity.org/)
- [运行多数客户端，后果自负！](https://dankradfeist.de/ethereum/2022/03/24/run-the-majority-client-at-your-own-peril.html)
- [以太坊硬件资源分析](https://www.migalabs.io/blog/post/ethereum-hardware-resource-analysis-update)
