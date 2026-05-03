# Besu 执行客户端

这是您在开始为 Besu 做出贡献时需要了解的重要信息的简要总结，Besu 是执行客户端的 Java 实现。 

代码库：https://github.com/hyperledger/besu/
文件：https://besu.hyperledger.org

## 代码目录说明

### 模块
+  它是一个多模块 [gradle](https://gradle.org/) 项目。您可以查看 settings.gradle 以查看所有模块：
    + 每个模块都有自己的 build.gradle：
		+ 您可以指定其模块名称：`archiveBaseName`
		+ 您可以指定其依赖项，但不指定版本。
	+ 每个模块都有自己的源代码：/src/main/java
+ **有顶级模块**：
	+ `config`：
		+ 大部分配置都已组装、验证。
		+ 您可以找到创世信息。
	+ `besu:`
		+ 所有 CL 参数均已定义。
		+ 是 main 方法所在的地方。
+ **有补充模块**：
	+ `crypto`：
		+ 与加密密钥相关的一切。
	+ `data types`：
		+ Besu 使用的数据类型。
	+ `metrics`：
		+ OpenTelemetry/Prometheus 与内部 Besu 不紧密。
	+ `ethereum`：
		+ 不是模块但包含模块：
			+ 应用程序编程接口：
				+ 您想要与 Ethereum 进行的所有交互，世界状态。
			+ 核心： 
				+ 存储日期、法定人数设置。
	+ `evm`：
		+ EVM 行为
		+ 在此模块中您可以找到每个操作码操作的实现。
+ **有企业模块：** 
	+ enclave、插件 API、隐私合约

### 摇篮
+ gradlew (文件)：
	+ 这是一个 bash 脚本，将检查 gradle 是否已安装 (将为您安装包装器并下载整个发行版)
	+ Gradle 本身作为通过调用此脚本使用的包装器的一部分进行管理。
+ 梯度 (文件夹)：
	+ gradle-wrapper.properties：
		+ distributionURL：指向调用./gradlew 时要使用的发行版
	+ 版本.gradle (文件)：
		+ 这是定义所有模块版本的地方。它由 gradlew 使用。
+ 构建.gradle (文件)：
	+ 插件：
		+ `spotless`：代码格式化、检查许可等，
			+ 命令：`./gradlew spotlessApply`
		+ `errorprone`：符合 Java 最佳实践。
			+ 命令：`./gradlew errorProne`
	+ 分布：
		+ 它通过将所有项目放入应用程序来定义构建输出的保留位置：
			+ .tar.zip 发行版。您可以在构建者/distributions 下看到.tar 或.zip。
+ 构建 (文件夹)：
	+ 它不是一个模块。
	+ 发行版 (文件夹)：
		+ Besu 分布的位置。
		+ 如果您深入 build/distribution/besu-{version}-SNAPSHOT/lib 您可以看到每个组件和库的每个版本。

### 测试
+ 单元测试：
	+ 每个模块在 src/test/java 下都有其单元测试
+ 集成测试：
	+ 每个模块在 src/integration-test/java 下都有其集成测试：
	+ 较为罕见。
	+ 在代码的内部运行之外。
	+ 参与更昂贵的跑步。
+ 验收测试：
	+ 位于验收测试模块下。
	+ 运行多个 Beau 的节点以在它们之间创建共识算法并执行任务调整和传播区块。
+ 参考测试：
	+ 经过 Ethereum 测试后拍摄，由 Ethereum Foundation 借用。
	+ 所有客户端都相同：https://github.com/ethereum/tests
	+ 存储在 JSON 中：
		+ 地点：`ethereum/referencetests/`
+ 其他信息：
	+ J 单元 4

###  开发任务：
+ 一些有用的命令：
	+ `git pull --recurse-submodules`。
	+ `./gradlew spotlessApply`
	+ `./gradlew check`(每次您向 Besu 仓库创建 PR 时，它都会由 CI 运行)
	+  `./gradlew assemble`
	+  如果您想将 MM 连接到本地 Besu 节点，您应该使用以下选项运行 Besu：
		+ bin/besu --network=dev --rpc-http-enabled --rpc-http-cors-origins=chrome-extension://nkbihfbeogaeaoehlefnkodbefgpgknn
		+ RPC URL: http://localhost:8545

###  重要课程：
+ `BesuControllerBuilder`：
	+ 管理设置客户端将要使用和需要的所有组件。
	+ 在构建方法中，您可以查看是否正在构建正确的域对象。
	+ It returns a BesuController.
+ `BesuCommand`：
	+ Represents the main Besu CLI command.
+ `ForkIdManager`：
    + 负责构建并代表最新的同步分叉。
	+ 我们总是需要知道我们自己的链条处于什么位置。 This check is done constantly.
	+ 它是在 EthProtocolManager 上创建的，而 EthProtocolManager 是在 BesuControllerBuilder 上创建的。 
+ `ProtocolSchedule`：
	+ 跟踪所有配置项作为链的区块编号特定范围的一部分。
+ `ProtocolSpec`：
	+ 让您配置协议内部工作方式的各个方面。
+ `MainnetProtocolSpec`：
	+ You will find every spec since frontier.
	+ 每个新规范都建立在前一个规范的基础上，并添加或更改必要的内容。
+ `MainnetEVMs`：
	+ Provides to the EVM the appropriate operations for 主网 hard forks.
	+ 这是一种聚合状态，大多数时候您都会向规范本身添加新功能。
	+ New operations will be registered here.
+ `JsonRpcMethodsFactory`：
	+ A 构建者 class for RPC methods.
	+ From here you can understand how to create new RPC methods.


### 重要的库：
+ https://doc.libsodium.org/:
	+ Sodium is a modern, easy-to-use software library for encryption, decryption, 签名, password hashing, and more.
+ https://github.com/nss-dev/nss:
	+ 网络安全服务 (NSS) 是一组库，旨在支持启用安全功能的客户端和服务器应用程序的跨平台开发。 NSS 支持 TLS 1.2、TLS 1.3、PKCS #5、PKCS#7、PKCS #11、PKCS #12、 S/MIME、X.509 v3 证书和其他安全标准。

## 参考文献

+ https://www.youtube.com/watch?v=4pCxwuNRaKg
+ https://github.com/hyperledger/besu
+ https://wiki.hyperledger.org/display/besu
