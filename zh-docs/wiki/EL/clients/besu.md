# Besu 执行客户端

这是开始为 Besu（一个 Java 实现的执行客户端）贡献代码时需要了解的关键信息简要总结。

代码库仓库：https://github.com/hyperledger/besu/
文档：https://besu.hyperledger.org

## 代码目录说明

### 模块
+  这是一个多模块的 [gradle](https://gradle.org/) 项目。你可以查看 settings.gradle 文件来查看所有模块：
    + 每个模块都有自己的 build.gradle 文件：
		+ 你可以指定模块名称：`archiveBaseName`
		+ 你可以指定模块的依赖，但不带版本号。
	+ 每个模块都有自己的源代码，位于：/src/main/java
+ **有以下顶级模块**：
	+ `config`：
		+ 大部分配置在这里进行汇总和验证。
		+ 你可以找到创世区块的信息。
	+ `besu:`：
		+ 所有的命令行参数（CL 参数）在这里定义。
		+ 这里是 main 方法所在的位置。
+ **有补充模块**:
	+ `crypto`：
		+ 所有与加密密钥相关的内容。
	+ `data types`：
		+ Besu 使用的数据类型。
	+ `metrics`:
		+ OpenTelemetry/Prometheus 与 Besu 内部不紧密相关。
	+ `ethereum`：
		+ 不是一个独立的模块，但包含以下模块：
			+ api：
				+ 与以太坊、世界状态交互的所有内容。
			+ core: 
				+ 存储数据、设置法定人数。
	+ `evm`:
		+ EVM 行为
		+ 在此模块中，你可以找到每个操作码（opcode）操作的实现。
+ **有企业模块：** 
	+ enclave, plugin-api, privacy-contracts

### Gradle
+ gradlew（文件）：
	+ 这是一个 bash 脚本，用于检查是否安装了 Gradle（如果没有，它会为你安装 Gradle Wrapper 并下载整个分发包）。
	+ Gradle 本身作为一个 wrapper 被管理，通过调用这个脚本来使用。
+ gradle（文件夹）：
	+ gradle-wrapper.properties:
		+ distributionURL：指向在调用 ./gradlew 时使用的分发包。
	+ versions.gradle（文件）：
		+ 定义了所有模块的版本。gradlew 使用此文件。
+ build.gradle（文件）：
	+ plugins:
		+ `spotless`：代码格式化、检查许可等，
			+ 命令： `./gradlew spotlessApply`
		+ `errorprone`：确保遵循 Java 的最佳实践。
			+ 命令：`./gradlew errorProne`
	+ distribution:
		+ 它定义了构建输出的存放位置，将所有项目打包成应用程序：
			+ .tar 和 .zip 分发包。你可以在 builder/distributions 下看到 .tar 或 .zip 文件。
+ build（文件夹）：
	+ 这不是一个模块。
	+ distributions（文件夹）：
		+ Besu 分发包的位置。
		+ 如果你进入 build/distribution/besu-{version}-SNAPSHOT/lib，你可以看到每个组件和库的每个版本。

### 测试
+ 单元测试：
	+ 每个模块在 src/test/java 下都有其单元测试。
+ 集成测试：
	+ 每个模块在 src/integration-test/java 下都有其集成测试：
	+ 相对较少。
	+ 在代码内部结构之外运行。
	+ 涉及更复杂的执行。
+ 验收测试：
	+ 位于 acceptance-test-module 下。
	+ 运行多个 Beau 节点，在它们之间创建共识算法，并执行任务调整和区块传播。
+ 参考测试：
	+ 从以太坊测试中获得，借用自以太坊基金会。
	+ 所有客户端都相同：https://github.com/ethereum/tests
	+ 以 JSON 格式存储：
		+ 位置：`ethereum/referencetests/`
+ 其他信息：
	+ 使用 JUnit 4。

###  开发任务：
+ 一些有用的命令：
	+ `git pull --recurse-submodules`.
	+ `./gradlew spotlessApply`
	+ `./gradlew check`（每次向 Besu 仓库提交 PR 时，CI 会运行此命令）
	+  `./gradlew assemble`
	+  如果你想将你的 MM 连接到本地的 Besu 节点，应该使用以下选项运行 Besu：
		+ bin/besu --network=dev --rpc-http-enabled --rpc-http-cors-origins=chrome-extension://nkbihfbeogaeaoehlefnkodbefgpgknn
		+ RPC URL: http://localhost:8545

###  重要类：
+ `BesuControllerBuilder`:
	+ 管理将用于设置客户端的所有组件。
	+ 在 build 方法中，你可以检查是否正在构建正确的域对象。
	+ 返回一个 BesuController。
+ `BesuCommand`:
	+ 代表主要的 Besu CLI 命令。
+ `ForkIdManager`:
    + 负责构建并表示最新同步的分叉。
	+ 我们始终需要知道自己链上的位置。此检查会不断进行。
	+ 它在 EthProtocolManager 中创建，而 EthProtocolManager 是在 BesuControllerBuilder 中创建的。
+ `ProtocolSchedule`:
	+ 跟踪区块链中与特定区块号范围相关的所有配置项。
+ `ProtocolSpec`:
	+ 允许你配置协议内部如何工作的各个方面。
+ `MainnetProtocolSpec`:
	+ 你将会找到自 Frontier 起的所有规范。
	+ 每个新的规范都是在之前的基础上构建的，并且添加或更改必要的内容。
+ `MainnetEVMs`:
	+ 为主网硬分叉提供适当的 EVM 操作。
	+ 它是一个聚合状态，通常你会在这里为规范添加新特性。
	+ 新操作将在这里注册。
+ `JsonRpcMethodsFactory`:
	+ 用于 RPC 方法的构建类。
	+ 通过它你可以了解如何创建新的 RPC 方法。


### 重要库：
+ https://doc.libsodium.org/:
	+ Sodium 是一个现代且易于使用的软件库，提供加密、解密、签名、密码哈希等功能。
+ https://github.com/nss-dev/nss:
	+ 网络安全服务（NSS）是一组旨在支持跨平台开发安全客户端和服务器应用程序的库。NSS 支持 TLS 1.2、TLS 1.3、PKCS #5、PKCS #7、PKCS #11、PKCS #12、S/MIME、X.509 v3 证书以及其他安全标准。

## 参考资料

+ https://www.youtube.com/watch?v=4pCxwuNRaKg
+ https://github.com/hyperledger/besu
+ https://wiki.hyperledger.org/display/besu
