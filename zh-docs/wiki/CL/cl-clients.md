# 共识客户端（Consensus Client）

> **共识客户端**（Consensus Clients），曾称为 *Eth2 客户端*，负责运行以太坊的权益证明（Proof-of-Stake）共识算法，使网络能够就信标链（Beacon Chain）的最新区块达成一致。  
> 共识客户端不会参与交易验证、广播或执行状态转换，这些工作由执行客户端（Execution Clients）完成。  
> 共识客户端也不会进行区块提议或验证，这些由验证者客户端（Validator Client）负责，后者是共识客户端的可选附加组件。

---

## 总览表

不同的共识客户端采用不同的编程语言开发，具备各自的特点，并提供不同的性能表现。所有客户端均默认支持以太坊主网及活跃测试网。多样化的客户端实现使以太坊网络受益于**客户端多样性**。  
如果你正在选择使用哪款客户端，**当前的客户端多样性应是一个重要考量因素**。

| 客户端                                                                  | 语言       | 开发团队             | 状态        |
| ----------------------------------------------------------------------- | ---------- | ------------------- | ----------- |
| [Lighthouse](https://github.com/sigp/lighthouse)                        | Rust       | Sigma Prime         | 生产可用    |
| [Lodestar](https://github.com/ChainSafe/lodestar)                       | TypeScript | ChainSafe           | 生产可用    |
| [Nimbus](https://github.com/status-im/nimbus-eth2)                      | Nim        | Status              | 生产可用    |
| [Prysm](https://github.com/prysmaticlabs/prysm)                         | Go         | Prysmatic Labs      | 生产可用    |
| [Teku](https://github.com/ConsenSys/teku)                               | Java       | ConsenSys           | 生产可用    |
| [Grandine](https://github.com/grandinetech/grandine)                    | Rust       | Grandine Developers | 生产可用    |
| [Caplin](https://github.com/ledgerwatch/erigon)                         | Go         | Erigon              | 开发中      |
| [LambdaClass](https://github.com/lambdaclass/lambda_ethereum_consensus) | Elixir     | LambdaClass         | 开发中      |

---

## 客户端分布情况

目前，大多数以太坊节点运营商主要使用 **Prysm** 或 **Lighthouse** 作为共识客户端。  
为了支持信标链（曾称为 ETH2）的健康发展，**建议尽可能使用不同的客户端**。  
[为什么？](https://clientdiversity.org/#why)

---

## 主要共识客户端简介

### Lighthouse

Lighthouse 由 Sigma Prime 团队使用 Rust 开发，强调**安全性和性能**。目前其用户占比较高，但需要注意的是，**如果形成超级多数，可能会导致链分叉风险**。  
Lighthouse 采用 **Apache 2.0 许可证**，在生产环境中具有极高的稳定性。

**特点：**
- [跨平台编译](https://lighthouse-book.sigmaprime.io/cross-compiling.html)
- [惩罚保护](https://lighthouse-book.sigmaprime.io/slashing-protection.html)
- [双生验证者保护](https://lighthouse-book.sigmaprime.io/validator-doppelganger.html#doppelganger-protection)
- [运行 Slasher](https://lighthouse-book.sigmaprime.io/slasher.html)
- [仅作为区块提议者运行](https://lighthouse-book.sigmaprime.io/advanced-proposer-only.html)
- [Prometheus 和 Grafana 监控](https://lighthouse-book.sigmaprime.io/advanced_metrics.html)

---

### Lodestar

Lodestar 由 ChainSafe 团队基于 **TypeScript** 开发，专为**快速原型设计**和**浏览器兼容性**优化。  
它提供完整的信标节点（Beacon Node）和验证者客户端（Validator Client）功能，同时包含 BLS 和 SSZ 等关键库，可用于以太坊协议开发。  
Lodestar 采用 **Apache 2.0 + LGPL 双许可证**，允许用户在**宽松许可**与**强制开源（Copyleft）**之间选择。

**特点：**
- [验证者客户端](https://chainsafe.github.io/lodestar/run/validator-management/vc-configuration)
- [MEV 与构建者集成](https://chainsafe.github.io/lodestar/run/beacon-management/mev-and-builder-integration)
- [轻客户端](https://chainsafe.github.io/lodestar/libraries/lightclient-prover/lightclient)
- [Prover 证明生成](https://chainsafe.github.io/lodestar/libraries/lightclient-prover/prover)
- [Prometheus 和 Grafana 监控](https://chainsafe.github.io/lodestar/run/logging-and-metrics/prometheus-grafana)
- [远程监控](https://chainsafe.github.io/lodestar/run/logging-and-metrics/client-monitoring)

---

### Prysm

Prysm 由 **Prysmatic Labs** 使用 **Go 语言** 开发，专注于**易用性和可靠性**。  
Prysm 提供完整的信标节点和验证者客户端实现，采用 **gRPC 进行进程间通信**，使用 **BoltDB 作为存储**，并基于 **libp2p 进行网络通信**。  
该客户端为参与以太坊权益证明（PoS）提供了一个**安全且可靠的解决方案**。

**特点：**
- [验证者客户端](https://docs.prylabs.network/docs/wallet/nondeterministic)
- [MEV 构建者配置](https://docs.prylabs.network/docs/advanced/builder)
- [运行 Slasher](https://docs.prylabs.network/docs/prysm-usage/slasher)
- [Prometheus 和 Grafana 监控](https://docs.prylabs.network/docs/prysm-usage/monitoring/grafana-dashboard)
- [安全最佳实践](https://docs.prylabs.network/docs/security-best-practices)

---

### Nimbus

Nimbus 由 **Status** 团队使用 **Nim 语言** 开发，专为**资源效率优化**。  
它可运行在 **智能手机、树莓派等轻量级设备**，同时在高性能服务器上运行时也能节省资源。  
Nimbus 集成了验证者客户端、远程签名、性能分析工具，并提供强大的验证者监控能力。

**特点：**
- [运行执行客户端](https://nimbus.guide/eth1.html)
- [验证者客户端](https://nimbus.guide/validator-client.html)
- [MEV 与构建者集成](https://nimbus.guide/external-block-builder.html)
- [Prometheus 和 Grafana 监控](https://nimbus.guide/metrics-pretty-pictures.html)

---

### Teku

Teku 由 **ConsenSys** 团队基于 **Java** 开发，适用于**企业级以太坊部署**。  
它包含完整的信标节点实现和验证者客户端，支持 **REST API 进行管理**，并提供 **Prometheus 指标监控**和**外部密钥管理**。

**特点：**
- [验证者客户端](https://docs.teku.consensys.io/concepts/proof-of-stake)
- [惩罚保护](https://docs.teku.consensys.io/how-to/prevent-slashing/use-a-slashing-protection-file)
- [MEV 与构建者集成](https://docs.teku.consensys.io/concepts/builder-network)
- [双生检测](https://docs.teku.consensys.io/how-to/prevent-slashing/detect-doppelgangers)
- [Prometheus 和 Grafana 监控](https://docs.teku.consensys.io/how-to/monitor/use-metrics)

---

## 其他资源

- [ETH Docker](https://eth-docker.net/)
- [Ethernodes](https://ethernodes.org/)
- [客户端多样性](https://clientdiversity.org/)
- [使用主流客户端的风险](https://dankradfeist.de/ethereum/2022/03/24/run-the-majority-client-at-your-own-peril.html)
- [以太坊硬件资源分析](https://www.migalabs.io/blog/post/ethereum-hardware-resource-analysis-update)
