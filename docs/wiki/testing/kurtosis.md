# Kurtosis 本地开发网

## 概述 
[**Kurtosis**](https://docs.kurtosis.com/) 是一个开发和测试平台，旨在高效打包和启动容器化服务环境。 Kurtosis 作为多容器测试环境的构建系统，帮助开发人员设置可以在本地轻松复制的 Ethereum 网络实例。 [**Ethereum 包**](https://github.com/ethpandaops/ethereum-package) 构建于 Kurtosis 之上，支持使用 Docker 或 Kubernetes 快速设置可定制、可扩展的私有 Ethereum 测试网。它支持所有主要的 Execution Layer (EL) 和 Consensus Layer (CL) 客户端，有效管理本地端口映射和服务连接，以验证和测试 Ethereum 核心基础设施。

本文简要介绍了 Kurtosis、如何安装和使用它，以及使用 Ethereum 开发网的一些基本命令。

## 安装 Kurtosis

在安装 Kurtosis 之前，请确保您的系统上预安装了以下依赖项：

- Docker (运行 Kurtosis 容器所需)
- Git (克隆仓库)

您可以按照 [此处](https://docs.kurtosis.com/install) 的官方安装指南安装 Kurtosis。

安装后，通过运行以下命令验证您的设置：

```sh
kurtosis version
```

有关升级说明，请参阅 [Kurtosis 升级指南](https://docs.kurtosis.com/upgrade)。

## Kurtosis 引擎

Kurtosis 引擎是运行开发网基础设施的核心服务。一旦您启动开发网，它就会自动启动。但是，这里有一些与引擎交互的有用命令：

```sh
# Start the engine
kurtosis engine start

# Stop the engine
kurtosis engine stop

# Check the status of the engine
kurtosis engine status
```

## 快速入门：Ethereum 包

您可以按照 [ethereum-package GitHub 页面](https://github.com/kurtosis-tech/ethereum-package) 中的说明，使用 Ethereum 包快速设置默认 Ethereum 开发网。

安装后启动开发网的最短方法是使用默认配置运行包：

```sh
kurtosis run github.com/ethpandaops/ethereum-package
```

会出现运行 enclave 状态：

![Kurtosis quick start terminal](./img/kurtosis-quick-start.png)

运行以下命令打开 Kurtosis Web 界面：

```sh
kurtosis web
```

![Kurtosis web interface](./img/kurtosis-web.png)

## Kurtosis enclave

Kurtosis 中的**enclave**是一个部署服务的隔离环境。它允许开发者创建多个开发网而不会受到干扰。关键命令包括：

```sh
# List existing enclaves
kurtosis enclave ls

# Inspect an enclave
kurtosis enclave inspect <enclave-name>

# Example
kurtosis enclave inspect my-testnet

# Delete an enclave
kurtosis enclave rm <enclave-name> -f

```

通过单击 enclave 的名称，在 Web 界面中探索 enclave：

![Kurtosis enclave in web interface](./kurtosis-web-enclave.png)

您可以同时运行多个 enclave，但要小心计算机的资源，以避免出现性能问题。此外，在同时管理多个 enclave 时，为每个 enclave 分配自定义名称非常有用。

```sh
# assigning your name to enclave
kurtosis --enclave my-testnet run github.com/ethpandaops/ethereum-package
```

## 清理资源

测试后，您可能需要使用新参数重新启动开发网或清理资源以释放磁盘空间。使用以下命令删除已停止的 enclave 以及已停止的引擎容器：

```sh
kurtosis clean -a
```

> [!CAUTION]
> 所有 enclave 将被删除。

## 定制开发网

Kurtosis 中的开发网是使用本地或网络中存储的 YAML 文件进行配置的。这些文件允许自定义参数，例如参与者、网络参数、附加服务。 如果文件中未指定参数，则使用默认值。所有可能的配置参数的详尽列表可以在 [此处](https://github.com/ethpandaops/ethereum-package?tab=readme-ov-file#configuration) 找到。 常见的做法是在本地构建客户端 Docker 映像，并使用 `cl_image` 或 `el_image` 参数将它们添加到配置文件中以进行测试。

示例文件：

```yaml
participants:
 - el_type: geth   
   el_image: geth:latest   
   cl_type: teku
   cl_image: consensys/teku:develop
additional_services
  - dora
  - prometheus_grafana
network_params:
  seconds_per_slot: 4
global_log_level: debug

```

要使用这些参数运行 Kurtosis，请使用 `--args-file` 标志。对于远程文件，请提供所需文件的原始 URL：

```sh
# local file
kurtosis run github.com/ethpandaops/ethereum-package --args-file ./custom.yaml

# remote file
kurtosis run github.com/ethpandaops/ethereum-package --args-file https://raw.githubusercontent.com/ethpandaops/ethereum-package/main/.github/tests/mix.yaml
```

您可以在 [此处](https://github.com/ethpandaops/ethereum-package?tab=readme-ov-file#configuration) 找到所有参数的详细说明。

## 自定义 Ethereum 包

您可以克隆 Ethereum 软件包仓库并在其之上进行开发。要运行您自己的修改，请使用以下命令：

```sh
kurtosis run .

# Example
kurtosis run . --args-file ./custom.yaml
```

## 工装

Kurtosis 提供了几个内置工具来与 Ethereum 开发网进行交互。以下是一些示例：

- [**dora**](https://github.com/ethpandaops/dora)：一个轻量级的 Beacon Chain 浏览器。

- [**xatu**](https://github.com/ethpandaops/xatu)：用于数据管道的集中式 Ethereum 网络监控工具。

- [**assertoor**](https://github.com/ethpandaops/assertoor)：用于编写断言来验证网络行为。

YAML 文件示例：

```sh
- el_type: nethermind
  cl_type: prysm
additional_services:
  - dora
  - assertoor
```

完整的服务列表可以在 [此处](https\://ethpandaops.io/posts/kurtosis-deep-dive/#tooling) 找到。

其中一些工具具有网络界面。要打开该界面，请使用用户服务列表中提供的链接：

![Kurtosis tools](./kurtosis-tools.png)

Dora 是 Beacon Chain 最常用的工具之一。它的界面如下所示：

![Dora](./kurtosis-dora.png)

## 使用日志

读取日志是调试和监控开发网的重要组成部分。可以使用 CLI 访问日志：

```sh
kurtosis service logs <enclave-id> <service-name> -f

# Example
kurtosis service logs my-testnet cl-1-teku-geth -f
````

日志也可以写入文件以进行进一步分析：

```sh
kurtosis service logs <enclave-name> <instance> > <file-name>

# Example
kurtosis service logs my-testnet cl-1-teku-geth > kurtosis.log
```

或者，Docker 日志可用于检查 Kurtosis 容器：

```sh
docker logs <container-id>
```

## 参考文献

- [Ethereum 包 GitHub 仓库](https://github.com/kurtosis-tech/ethereum-package)
- [Kurtosis GitHub 仓库](https://github.com/kurtosis-tech/kurtosis)
- [Kurtosis 官方文档](https://docs.kurtosis.com)
- [Kurtosis 深入探究 (EthPandaOps 博客文章)](https://ethpandaops.io/posts/kurtosis-deep-dive/)
- [EthPandaOps 团队的 Kurtosis 研讨会 (Youtube)](https://www.youtube.com/watch?v=mywpmp2sPt0)
- [James (Prysm) 使用 ethereum-package 运行本地开发网的 3 分钟介绍](https://www.loom.com/share/f7d32d0af14f4f24b63bf3cebfdc796a)
