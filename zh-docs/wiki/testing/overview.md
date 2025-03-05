# 测试

以太坊客户端实现在不同层面上持续接受测试，以确保网络的安全性和稳定性。对于一个去中心化网络来说，确保所有客户端正确通信、以相同方式运行，并按照协议定义的方式对交易结果达成共识是必不可少的。状态转换出现任何差异都可能会导致网络分裂，引发最终确定性失败，并给用户带来一系列问题。为了实现稳定性，以太坊客户端必须通过一整套标准化的测试用例进行严格测试。

这些测试用于验证客户端是否符合 [执行层](/wiki/EL/el-specs.md) 和 [共识层](/wiki/CL/cl-specs.md) 的规范，确保所有客户端以相同的方式解释和执行交易。这种严格的测试还可以作为主动错误检测工具，防止网络分叉（即规范区块链状态出现分歧）。

## 资源

### 讲解
- [测试与安全概览](https://www.youtube.com/watch?v=PQVW5dJ8J0c)

### 通用测试套件
- [pytest: Python 测试框架](https://docs.pytest.org/en/8.0.x/)
- [以太坊测试：所有实现的通用测试](https://github.com/ethereum/tests)
- [Hive: 以太坊端到端测试工具](https://github.com/ethereum/hive)

### 执行层测试
- [执行层规范测试：执行客户端测试用例](https://github.com/ethereum/execution-spec-tests)
- [FuzzyVM：EVM 差分模糊测试工具](https://github.com/MariusVanDerWijden/FuzzyVM).
- [retesteth：测试生成工具](https://github.com/ethereum/retesteth)
- [EVM 实验室工具](https://github.com/ethereum/evmlab)
- [Go evmlab：受 EVMLAB 启发的 EVM 实验工具](https://github.com/holiman/goevmlab)
- [执行层 API 集合](https://github.com/ethereum/execution-apis)

### 共识层测试
- [共识层规范测试](https://github.com/ethereum/consensus-specs/tree/dev/tests)

### 测试链工具
- [Assertoor: 以太坊测试网测试工具](https://github.com/ethpandaops/assertoor)
- [Ethereum package: 测试网部署工具](https://github.com/kurtosis-tech/ethereum-package) (推荐初学者使用)

### 仪表板
- [Hive 测试结果](https://hivetests.ethdevops.io/)