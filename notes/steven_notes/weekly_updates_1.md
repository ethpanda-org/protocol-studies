# 每周更新 #1

> 详细了解 [Steven 的 EPF 旅程](https://hackmd.io/@qz8qnEAKSzCXkPyX1NRrAg)

## 目标

本周的重点是对以太坊的架构进行基础了解，特别强调执行层和 共识层。为了实现这一目标，我计划参与精选的教育资源。此外，我的目标是深入研究引擎 API 的复杂性，并探索以太坊开发路线图，以确定最引起我兴趣的领域。详细目标包括：

- **了解执行层和共识层**： 
  - 通过精选的学习材料回顾并理解执行层和共识层的基本概念。 

- **探索引擎 API**：
  - 记录引擎 API 的功能和工作流程，突出显示代码流程和关键操作。本次探索旨在阐明引擎 API 如何促进执行层和共识层之间的交互。

- **研究以太坊的路线图**
  - 调查以太坊当前的发展路线图，目的是了解战略目标和即将推出的增强功能。这项研究将侧重于确定对以太坊的改进做出重大贡献的举措，并选择与我感兴趣的领域相符的举措进行更深入的探索。

通过实现这些目标，我希望建立坚实的知识基础，以支持我成为以太坊生态系统的核心贡献者。

## 输出
**了解执行层和共识层**

我本周使用的主要资源是 [Eth2 共识初步书籍](https://eth2book.info/capella/part2/consensus/preliminaries/)。之前读过，我又浏览了一下，特别注意了几个新增加的部分。这本书为理解以太坊 2.0 共识机制的复杂细节提供了坚实的基础，我强烈推荐给所有对以太坊技术基础感兴趣的开发人员。

**探索引擎 API**：

一个特别有用的资源是博客 [Engine API：视觉指南](https://hackmd.io/@danielrachi/engine_api#Block-Building)。我通过此博客探索了 Prysm 代码库，下面是对源代码的一些带注释的见解：

- **prysm 客户端初始化流程：prysm/cmd/beacon-chain/main.go**
    1. app.New() // cli 应用程序/实例
    2. 解析标志
    3. `app.before(ctx)`
    4. `app.Action(ctx)`
        - 节点.New() // 注册所有需要的服务
        - `startNode(ctx, cancel)`
            - beacon, err := 节点.New(...) // beacon 节点处理整个系统的生命周期
            - 信标.Start()
                - beacon.services.StartAll() // 按照注册顺序初始化每个服务
                    - 点对点
                    - 初始同步
                    - 回填
                    - 证明
                    - 区块链
                    - ...
    5. `app.After(ctx)`

- **引擎API接口：/prysm/beacon-chain/execution/engine_client.go**
```
type EngineCaller interface {
	NewPayload(ctx context.Context, payload interfaces.ExecutionData, versionedHashes []common.Hash, parentBlockRoot *common.Hash) ([]byte, error)
	ForkchoiceUpdated(
		ctx context.Context, state *pb.ForkchoiceState, attrs payloadattribute.Attributer,
	) (*pb.PayloadIDBytes, []byte, error)
	GetPayload(ctx context.Context, payloadId [8]byte, slot primitives.Slot) (interfaces.ExecutionData, *pb.BlobsBundle, bool, error)
	ExecutionBlockByHash(ctx context.Context, hash common.Hash, withTxs bool) (*pb.ExecutionBlock, error)
	GetTerminalBlockHash(ctx context.Context, transitionTime uint64) ([]byte, bool, error)
}
```

- **验证者生命周期**
    1. 从其他验证者接收区块： **/prysm/beacon-chain/blockchain/receive_block.go**
        1. 提取执行载荷
        2. **s.validateExecutionOnBlock**：调用 engine_newPayload
        3. **s.postBlockProcess**：调用 engine_engine_forkchoiceUpdated
    2. 建议区块: **/prysm/beacon-chain/rpc/prysm/v1alpha1/验证者/提议者.go**
        1. **vs.ExecutionEngineCaller.ForkchoiceUpdate**：调用 engine_forkchoiceUpdated
        2. **vs.ExecutionEngineCaller.GetPayload**：调用 engine_getPayload
        3. 计算根并传播区块


**路线图**
供参考：[以太坊路线图](https://ethereum.org/en/roadmap/)

该路线图旨在为用户带来四个好处：
- 更便宜交易(部分完成)
    - 原始 Danksharding(最近在 EIP-4844 中完成)
    - Danksharding
    - 去中心化 Rollup
- 额外的安全性(需要最终确定规范并开始构建原型)
    - 单个时隙最终确定性 
    - DVT 
    - 提议者-构建者分离 
    - 秘密领导人选举
- 更好的用户体验
    - 账户抽象(EIP-4337)
    - 节点给大家
        - 韦尔克尔树
        - 无状态性(首选弱无状态性，但依赖 Verkle 树和提议者-构建者分离)
        - 数据过期(门户网络是一个选项)
- 面向未来(仍处于研究阶段)
    - 抗量子

**我目前对PBS和Portal网络更感兴趣，在做出最终决定之前需要进行更多研究。**