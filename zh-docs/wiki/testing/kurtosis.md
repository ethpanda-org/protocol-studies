# Kurtosis 用于本地 Devnet

## 概述  
[**Kurtosis**](https://docs.kurtosis.com/) 是一个开发和测试平台，旨在高效地打包和启动容器化服务环境。Kurtosis 作为多容器测试环境的构建系统，帮助开发人员设置可在本地轻松复现的以太坊网络实例。[**Ethereum package**](https://github.com/ethpandaops/ethereum-package) 是基于 Kurtosis 构建的，以支持使用 Docker 或 Kubernetes 快速设置可定制、可扩展的私有以太坊测试网络。它兼容所有主流的执行层（EL）和共识层（CL）客户端，并高效管理本地端口映射及服务连接，以便验证和测试以太坊核心基础设施。

本文简要介绍 Kurtosis 的基本概念，如何安装和使用它，以及在以太坊 Devnet 环境中的一些关键命令。

## 安装 Kurtosis

在安装 Kurtosis 之前，请确保你的系统已预装以下依赖项：

- Docker（运行 Kurtosis 容器所必需）
- Git（用于克隆代码仓库）

你可以按照官方安装指南[这里](https://docs.kurtosis.com/install)安装 Kurtosis。

安装完成后，可通过以下命令验证安装是否成功：

```sh
kurtosis version
```

如需升级 Kurtosis，请参考 Kurtosis 升级指南。

## Kurtosis 引擎

Kurtosis 引擎是运行 Devnet 基础设施的核心服务。通常，在启动 Devnet 时会自动运行引擎。以下是一些用于管理 Kurtosis 引擎的命令：

```sh
# 启动引擎
kurtosis engine start

# 停止引擎
kurtosis engine stop

# 检查引擎状态
kurtosis engine status
```

快速启动：Ethereum Package
你可以使用 Ethereum package 快速启动一个默认的以太坊 Devnet，具体操作可参考 ethereum-package GitHub 页面。

安装完成后，可运行以下命令启动默认配置的 Devnet：

```sh
kurtosis run github.com/ethpandaops/ethereum-package
```

运行后，你将看到类似以下的 enclave 状态输出：

![Kurtosis quick start terminal](/docs/wiki/testing/img/kurtosis-quick-start.png)

要打开 Kurtosis Web 界面，可以运行：

```sh
kurtosis web
```

![Kurtosis web interface](/docs/wiki/testing/img/kurtosis-web.png)

## Kurtosis Enclave

**Enclave** 在 Kurtosis 中是一个隔离的环境，用于部署服务。它允许开发者创建多个 Devnet，而不会互相干扰。以下是一些关键命令：

```sh
# 列出现有的 enclaves
kurtosis enclave ls

# 查看特定 enclave 的详细信息
kurtosis enclave inspect <enclave-name>

# 示例
kurtosis enclave inspect my-testnet

# 删除特定 enclave
kurtosis enclave rm <enclave-name> -f


```

通过点击 enclave 的名称，你可以在 Web 界面中查看该 enclave

![Kurtosis enclave in web interface](/docs/wiki/testing/img/kurtosis-web-enclave.png)

你可以同时运行多个 enclaves，但请注意你的计算机资源，以避免性能问题。此外，在创建多个 enclaves 时，为每个 enclave 分配一个自定义名称将有助于管理多个 Enclave。

```sh
# assigning your name to enclave
kurtosis --enclave my-testnet run github.com/ethpandaops/ethereum-package
```

## 清理资源
测试完成后，你可能希望使用新参数重新启动 devnets，或清理资源以释放磁盘空间。使用以下命令删除已停止的 enclaves，以及已停止的引擎容器：
```sh
kurtosis clean -a
```

> [!CAUTION]
> 所有的 enclaves 都将被删除。

## Customizing Devnets

Kurtosis 中的 Devnets 通过本地或网络上的 YAML 文件进行配置。这些文件允许自定义诸如参与者、网络参数和附加服务等参数。如果文件中没有指定某个参数，则会使用默认值。所有可能的配置参数的详细列表可以在 [这里](https://github.com/ethpandaops/ethereum-package?tab=readme-ov-file#configuration) 找到。常见的做法是在本地构建客户端 Docker 镜像，并使用 `cl_image` 或 `el_image` 参数将它们添加到配置文件中，以进行测试。

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

要使用这些参数运行 Kurtosis，可以使用 `--args-file` 标志。对于远程文件，提供所需文件的原始 URL：

```sh
# local file
kurtosis run github.com/ethpandaops/ethereum-package --args-file ./custom.yaml

# remote file
kurtosis run github.com/ethpandaops/ethereum-package --args-file https://raw.githubusercontent.com/ethpandaops/ethereum-package/main/.github/tests/mix.yaml
```

您可以在[这里](https://github.com/ethpandaops/ethereum-package?tab=readme-ov-file#configuration)找到所有参数的详细描述。

## 自定义以太坊包

您可以克隆以太坊包的仓库并在其基础上进行开发。要运行您自己的修改，请使用以下命令：

```sh
kurtosis run .

# 例子
kurtosis run . --args-file ./custom.yaml
```

## 工具

Kurtosis 提供了多个内置工具来与以太坊开发网进行交互。以下是一些示例：

- [**dora**](https://github.com/ethpandaops/dora)：一个轻量级的 Beacon Chain 浏览器。

- [**xatu**](https://github.com/ethpandaops/xatu)：一个集中式的以太坊网络监控工具，用于数据管道化。

- [**assertoor**](https://github.com/ethpandaops/assertoor)：用于编写断言以验证网络行为。

示例 YAML 文件:

```sh
- el_type: nethermind
  cl_type: prysm
additional_services:
  - dora
  - assertoor
```

完整的服务列表可以在[此处](https://ethpandaops.io/posts/kurtosis-deep-dive/#tooling)找到。

其中一些工具具有 Web 界面。要打开该界面，请使用用户服务列表中提供的链接：

![Kurtosis 工具](/docs/wiki/testing/img/kurtosis-tools.png)

Dora 是最常用的 Beacon Chain 工具之一。以下是其界面的外观：

![Dora](/docs/wiki/testing/img/kurtosis-dora.png)

## 查看日志

阅读日志是调试和监控开发网的重要部分。可以使用 CLI 访问日志：

```sh
kurtosis service logs <enclave-id> <service-name> -f

# 例子
kurtosis service logs my-testnet cl-1-teku-geth -f
````

Logs can also be written to a file for further analysis:

```sh
kurtosis service logs <enclave-name> <instance> > <file-name>

# 例子
kurtosis service logs my-testnet cl-1-teku-geth > kurtosis.log
```

或者，可以使用 Docker 日志来检查 Kurtosis 容器：

```sh
docker logs <container-id>
```

## 参考资料

- [Ethereum Package GitHub Repository](https://github.com/kurtosis-tech/ethereum-package)
- [Kurtosis GitHub Repository](https://github.com/kurtosis-tech/kurtosis)
- [Kurtosis Official Documentation](https://docs.kurtosis.com)
- [Kurtosis Deep Dive (EthPandaOps blog post)](https://ethpandaops.io/posts/kurtosis-deep-dive/)
- [Kurtosis Workshop by EthPandaOps Team (Youtube)](https://www.youtube.com/watch?v=mywpmp2sPt0)
