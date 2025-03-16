# 执行客户端（Execution Client）

> **执行客户端**（Execution Clients），前称 *eth1 客户端*，负责实现以太坊[执行层（Execution Layer）](https://github.com/ethereum/execution-specs)，主要用于处理和广播交易并管理区块链状态。  
它们通过点对点（p2p）网络接收交易，并使用[以太坊虚拟机（Ethereum Virtual Machine, EVM）](https://ethereum.org/en/developers/docs/evm/)执行计算，以更新链上状态并确保遵循协议规则。  
执行客户端可配置为**全节点**（Full Node），存储状态、历史区块及交易回执，或**归档节点**（Archive Node），额外存储所有历史状态。  

## 客户端概览

当前用于生产环境的执行客户端包括：

| 客户端 | 语言 | 开发者 | 状态 |
|--------|------|--------|------|
| [Besu](https://github.com/hyperledger/besu) | Java | Hyperledger | 生产（Production） |
| [Erigon](https://github.com/ledgerwatch/erigon) | Go | Ledgerwatch | 生产（Production） |
| [Geth](https://github.com/ethereum/go-ethereum) | Go | 以太坊基金会 | 生产（Production） |
| [Nethermind](https://github.com/NethermindEth/nethermind) | C# | Nethermind | 生产（Production） |
| [Reth](https://github.com/paradigmxyz/reth) | Rust | Paradigm | 生产（Production） |

此外，还有一些仍在开发中或已被废弃的执行客户端：

| 客户端 | 语言 | 开发者 | 状态 |
|--------|------|--------|------|
| [Nimbus](https://github.com/status-im/nimbus-eth1) | Nim | Nimbus | 开发中 |
| [Silkworm](https://github.com/erigontech/silkworm) | C++ | Erigon | 开发中 |
| [JS Client](https://github.com/ethereumjs/ethereumjs-monorepo) | TypeScript | 以太坊基金会 | 开发中 |
| [ethrex](https://github.com/lambdaclass/ethrex) | Rust | LambdaClass | 开发中 |
| [Akula](https://github.com/akula-bft/akula) | Rust | Akula Developers | 已废弃 |
| [Aleth](https://github.com/ethereum/aleth) | C++ | Aleth Developers | 已废弃 |
| [Mana](https://github.com/mana-ethereum/mana) | Elixir | Mana Developers | 已废弃 |
| [OpenEthereum](https://github.com/openethereum/parity-ethereum) | Rust | Parity | 已废弃 |
| [Trinity](https://github.com/ethereum/trinity) | Python | OpenEthereum | 已废弃 |

## 客户端分布情况

目前，大多数节点运营者仍然使用 Geth 作为执行客户端。  
为了保持执行层的健康状态，[建议运行不同的客户端](https://clientdiversity.org/#why) 以增强网络的多样性。  

## 各个客户端简介

尽管所有客户端都实现了相同的协议规范，但它们在特性和功能上有所不同。不同的编程语言实现也使得来自不同背景的开发者能够贡献代码。  

### Besu

由 Consensys/Hyperledger 基金会开发，使用 Java 语言。Besu（Hyperledger Besu）以其企业级特性而闻名，并兼容多个 Hyperledger 项目。  
它支持公有链和私有链，并提供强大的 CLI 工具和 JSON-RPC API。  

**主要特性：**
- [私有网络支持](https://besu.hyperledger.org/private-networks/)
- [状态修剪（Pruning）](https://besu.hyperledger.org/public-networks/how-to/bonsai-limit-trie-logs#prune-command-for-mainnet)
- [并行交易执行](https://besu.hyperledger.org/public-networks/concepts/parallel-transaction-execution)

### Erigon

Erigon 最初作为 Geth 的一个分支（Turbo-Geth）推出，专注于优化性能、加快同步速度并减少磁盘占用。  
其独特的 MPT（Merkle Patricia Trie）数据库管理方式使得**归档节点的存储需求减少了约 5 倍**。  
Erigon 允许归档节点在**三天内完成同步，占用空间小于 3 TB**，并包含内置的共识层（CL）客户端 Caplin，可独立运行。  

**主要特性：**
- [支持的网络](https://erigon.gitbook.io/erigon/basic-usage/supported-networks)
- [状态修剪](https://erigon.gitbook.io/erigon/basic-usage/usage/type-of-node#full-node-or-pruned-node)
- [Caplin 共识客户端](https://erigon.gitbook.io/erigon/advanced-usage/consensus-layer/caplin)

### Geth

Geth（Go-Ethereum）是以太坊最早的 Go 语言实现，也是维护时间最长的客户端。  
它支持多种节点类型（全节点、轻节点、归档节点），拥有广泛的用户群体和强大的社区支持。  
Geth 可通过包管理器、Docker 容器或手动安装进行部署，适用于各种区块链环境。  

**主要特性：**
- [状态修剪](https://geth.ethereum.org/docs/fundamentals/pruning)
- [自定义 EVM 追踪器](https://geth.ethereum.org/docs/developers/evm-tracing/custom-tracer)
- [监控仪表盘](https://geth.ethereum.org/docs/monitoring/dashboards)

### Nethermind

Nethermind 由 C# .NET 语言开发，专注于稳定性和与现有技术栈的集成。  
其优化的虚拟机性能、插件系统及全面的分析支持，使其适用于私有链和 dApp 开发。  

**主要特性：**
- [私有网络支持](https://docs.nethermind.io/fundamentals/private-networks)
- [性能优化](https://docs.nethermind.io/fundamentals/performance-tuning)
- [Prometheus 与 Grafana 监控](https://docs.nethermind.io/monitoring/metrics/grafana-and-prometheus)

### Reth

Reth（Rust Ethereum）是一个模块化、高性能的 Rust 语言实现的以太坊客户端。  
它受 Erigon 启发，采用新的归档节点存储方案，实现**快速同步与低磁盘占用**。  
Reth 以**社区驱动开发**为核心，适用于高可靠性生产环境。  

**主要特性：**
- [Revm 虚拟机](https://bluealloy.github.io/revm/)
- [监控支持](https://reth.rs/run/observability.html)

### 其他客户端

- **Nimbus**：超轻量级客户端，专注于资源受限环境，支持 Portal Network 轻客户端（Fluffy）。
- **Silkworm**：C++ 语言实现，以最高性能为目标，集成 libmdbx 数据库引擎。
- **JS Client**：TypeScript 实现，主要用于测试，适用于 Web 和 Node.js 环境。

## 相关资源

- [ETH Docker](https://eth-docker.net/)
- [Ethernodes](https://ethernodes.org/)
- [客户端多样性](https://clientdiversity.org/)
- [请谨慎运行主流客户端！](https://dankradfeist.de/ethereum/2022/03/24/)

