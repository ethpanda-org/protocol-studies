# 测试

以太坊客户端的实现经过不同级别的不断测试，确保网络的安全性和稳定性。对于去中心化网络，确保所有客户端正确通信、行为方式相同并因此就协议定义的交易结果达成一致是必不可少的。单个状态转换的差异会导致网络分裂，从而导致最终确定失败并给用户带来许多问题。为了实现稳定性，以太坊客户端必须针对标准化测试用例套件进行严格的测试。 

这些测试验证是否遵守[执行](/wiki/EL/el-specs.md)和[共识](/wiki/CL/cl-specs.md)规范，保证所有客户端以相同的方式解释和执行交易。这种严格的测试还可以作为主动错误检测工具，防止网络分叉(对规范区块链状态的分歧)。

## 资源

### 演练
- [测试与安全概述](https://www.youtube.com/watch?v=PQVW5dJ8J0c)

### 通用测试套件
- [pytest：Python测试框架](https://docs.pytest.org/en/8.0.x/)
- [以太坊测试：所有实现的通用测试](https://github.com/ethereum/tests)
- [Hive：以太坊端到端测试工具](https://github.com/ethereum/hive)

### 执行层测试
- [执行规范测试：执行客户端的测试用例](https://github.com/ethereum/execution-spec-tests)
- [FuzzyVM：EVM 的差分模糊器](https://github.com/MariusVanDerWijden/FuzzyVM)。
- [retesteth：测试生成工具](https://github.com/ethereum/retesteth)
- [EVM 实验室实用程序](https://github.com/ethereum/evmlab)
- [Go evmlab：受 EVMLAB 启发的 Evm 实验室](https://github.com/holiman/goevmlab)
- [执行API集合](https://github.com/ethereum/execution-apis)

### 共识层测试
- [共识规范测试](https://github.com/ethereum/consensus-specs/tree/dev/tests)

### 测试链条的工具
- [断言者：以太坊测试网测试工具](https://github.com/ethpandaops/assertoor)
- [以太坊包：测试网部署器](https://github.com/kurtosis-tech/ethereum-package)(推荐初学者使用)

### 仪表板
- [Hive测试结果](https://hivetests.ethdevops.io/)