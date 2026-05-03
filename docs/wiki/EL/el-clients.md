# 执行客户端

> **执行客户端**，以前称为 *eth1 客户端*，正在实现 Ethereum [Execution Layer](https://github.com/ethereum/execution-specs)，其任务是处理和广播交易并管理状态。
他们通过 p2p 接收交易，使用 [Ethereum Virtual Machine](https://ethereum.org/en/developers/docs/evm/) 运行每个交易的计算以更新状态并确保遵循协议规则。 
执行客户端可以配置为保存状态的全节点、包括收据的历史区块链，或者配置为还保留所有历史状态的存档节点。 

## 概览表

当前生产中使用的执行客户端是：

| 客户端 | 语言 | 开发商 | 状态 |
|-------------|----------|-------------------|------------|
| [Besu](https://github.com/hyperledger/besu) | Java | Hyperledger | 生产 |
| [Erigon](https://github.com/ledgerwatch/erigon) | Go | 分类账表 | 生产 |
| [Geth](https://github.com/ethereum/go-ethereum) | Go | Ethereum Foundation | 生产 |
| [Nethermind](https://github.com/NethermindEth/nethermind) | C# | Nethermind | 生产 |
| [Reth](https://github.com/paradigmxyz/reth) | Rust | Paradigm | 生产 |

还有更多执行客户端正在积极开发中，尚未成熟或过去已经使用过：

| 客户端 | 语言 | 开发商 | 状态 |
| --------------------------------------------------------------- | ---------- | ------------------- | ----------- |
| [Nimbus](https://github.com/status-im/nimbus-eth1) | Nimbus | Nimbus | 开发 |
| [蚕](https://github.com/erigontech/silkworm) | C++ | Erigon | 开发 |
| [JS 客户端](https://github.com/ethereumjs/ethereumjs-monorepo) | TypeScript | Ethereum Foundation | 开发 |
| [ethrex](https://github.com/lambdaclass/ethrex) | Rust | LambdaClass | 开发 |
| [Akula](https://github.com/akula-bft/akula) | Rust | Akula 开发商 | 已弃用 |
| [Aleth](https://github.com/ethereum/aleth) | C++ | Aleth 开发人员 | 已弃用 |
| [法力](https://github.com/mana-ethereum/mana) | 长生不老药 | 法力开发者 | 已弃用 |
| [OpenEthereum](https://github.com/openethereum/parity-ethereum) | Rust | Parity | 已弃用 |
| [三位一体](https://github.com/ethereum/trinity) | 蟒蛇 | 开放 Ethereum | 已弃用 |


## 分布

目前，绝大多数节点操作员都使用 Geth 作为执行客户端。 
为了支持 Execution Layer 的健康，[建议在运行节点时使用不同的客户端](https://clientdiversity.org/#why)。 

## 个人客户端

尽管客户端实现相同的规范，但每个客户端提供不同的功能和优点。它们采用不同的编程语言，使不同背景的开发人员能够做出贡献。 

### Besu

Besu (Hyperledger Besu) 由 Consensys/Hyperledger 基金会用 Java 开发，以其企业级功能以及与各种 Hyperledger 项目的兼容性而著称。
它支持公共和专用网络，提供强大的命令行工具和 JSON-RPC API。

值得注意的特点：
- [私网](https://besu.hyperledger.org/private-networks/)
- [修剪](https://besu.hyperledger.org/public-networks/how-to/bonsai-limit-trie-logs#prune-command-for-mainnet)
- [并行交易执行](https://besu.hyperledger.org/public-networks/concepts/parallel-transaction-execution)

### Erigon

Erigon 最初是作为 Turbo-Geth 推出的 Geth 的一个分支，专注于优化性能、快速同步功能和减少磁盘空间使用。 Erigon 引入了一种管理 MPT 数据库的新方法，使存档节点磁盘大小大约减少了 5 倍。
Erigon 的架构允许其在三天内完成完整存档节点同步，数据存储量少于 3 TB，使其成为运行此类节点的理想选择。它还包含自己的嵌入式 CL 客户端，使其能够独立运行。 

值得注意的特点：
- [支持的网络](https://erigon.gitbook.io/erigon/basic-usage/supported-networks)
- [修剪](https://erigon.gitbook.io/erigon/basic-usage/usage/type-of-node#full-node-or-pruned-node)
- [Caplin CL 客户端](https://erigon.gitbook.io/erigon/advanced-usage/consensus-layer/caplin)

### Geth

作为 Ethereum 的原始 Go 实现，也是最早积极维护的客户端，Geth (Go-Ethereum) 在开发人员和用户中得到了广泛采用。
它支持各种节点类型 (完整、轻型、存档)，并以其广泛的工具集、稳定性和社区支持而闻名。
Geth 的部署灵活性 (通过包管理器、Docker 容器或手动设置) 确保了其在不同区块链环境中的多功能性。

值得注意的特点：
- [修剪](https://geth.ethereum.org/docs/fundamentals/pruning)
- [自定义 EVM 跟踪器](https://geth.ethereum.org/docs/developers/evm-tracing/custom-tracer)
- [监控仪表板](https://geth.ethereum.org/docs/monitoring/dashboards)

### Nethermind
Nethermind 采用 C#.NET 编写，旨在实现稳定性并与现有技术基础设施集成。
它提供优化的虚拟机性能、全面的分析支持和插件系统。
Nethermind 适用于私有 Ethereum 网络和去中心化应用程序 (dApp) 开发，强调数据完整性和性能可扩展性。

值得注意的特点：
- [私网](https://docs.nethermind.io/fundamentals/private-networks)
- [性能调整](https://docs.nethermind.io/fundamentals/performance-tuning)
- [普罗米修斯与格拉法纳](https://docs.nethermind.io/monitoring/metrics/grafana-and-prometheus)

### 雷斯

Reth (Rust Ethereum) 是一个模块化、高效的 Ethereum 客户端，专为用户友好性和高性能而设计。受 Erigon 设计的启发，它围绕新颖的存档节点方法构建，可在较小的磁盘占用空间内实现快速同步。
它强调社区驱动的开发，适合强大的生产环境。

值得注意的特点：
- [修订](https://bluealloy.github.io/revm/)
- [监控](https://reth.rs/run/observability.html)

### Nimbus

Nimbus 作为超轻量级 Ethereum Execution Layer 客户端专注于效率和安全性。最初致力于 Nimbus CL 客户端，一个新团队在 Nim 中开发了一个执行客户端。 
它最大限度地减少了资源消耗，同时支持 Ethereum 的 Execution Layer 功能并与 Fluffy (Portal Network 轻客户端) 集成。
Nimbus 提供增强的内存节省和状态同步机制，非常适合资源受限的环境。

### 蚕
Silkworm 是 Ethereum Execution Layer 协议的 C++ 实现，旨在成为最快的 Ethereum 客户端。
它集成了 libmdbx 数据库引擎，并强调 Erigon 项目 (称为 Erigon++) 内的可扩展性、模块化和性能优化。

### JS 客户端
JavaScript 客户端由 EF JS 团队开发，作为 [EthereumJS monorepo](https://github.com/ethereumjs/ethereumjs-monorepo) 的一部分。它是实验性的，主要用于测试，但由于它被设计为以 JavaScript 为中心，因此它适用于 Web 和节点.js 环境。

## 其他资源

- [ETH Docker](https://eth-docker.net/)
- [以太节点](https://ethernodes.org/)
- [客户端多样性](https://clientdiversity.org/)
- [运行多数客户端，后果自负！](https://dankradfeist.de/ethereum/2022/03/24/)
