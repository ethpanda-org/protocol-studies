# Kurtosis 用于本地 Devnets

## 概述

[**Kurtosis**](https://docs.kurtosis.com/) 是一个开发和测试平台，旨在高效地打包和启动容器化服务环境。它作为多容器测试环境的构建系统，帮助开发者轻松搭建可复现的以太坊网络实例。[**Ethereum package**](https://github.com/ethpandaops/ethereum-package) 基于 Kurtosis 构建，支持使用 Docker 或 Kubernetes 快速搭建可自定义、可扩展的私有以太坊测试网，并兼容所有主流的执行层（EL）和共识层（CL）客户端，高效管理本地端口映射及服务连接，以验证和测试以太坊核心基础设施。

本文简要介绍 Kurtosis 的安装、使用方法，以及在以太坊 Devnets 相关的基本操作。

## 安装 Kurtosis

安装 Kurtosis 之前，请确保系统已安装以下依赖：

- Docker（用于运行 Kurtosis 容器）
- Git（用于克隆仓库）

按照[官方安装指南](https://docs.kurtosis.com/install)进行安装。

安装完成后，可运行以下命令验证安装是否成功：

```sh
kurtosis version
