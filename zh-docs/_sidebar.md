# 以太坊协议学习小组

- [首页](readme.md)
- **学习小组**
  - [从这里开始](/eps/intro.md)
  - [学习小组内容](/eps/schedule.md)
    - **第 1 周：介绍**
      - [学习准备](/eps/week0.md)
      - [第 1 讲：介绍](/eps/week1.md)
      - [第 2 讲：共识层](/eps/week3.md)
      - [第 3 讲：执行层](/eps/week2.md)
      - [第 4 讲：测试](/eps/week4.md)
      - [第 5 讲：路线图](/eps/week5.md)
      - [节点工作坊](/eps/nodes_workshop.md)
    - **第 2 周：开发概览**
      - [第 6 讲：技术规范](/eps/week6-dev.md)
      - [第 7 讲：执行层客户端](/eps/week7-dev.md)
      - [第 8 讲：共识层客户端](/eps/week8-dev.md)
      - [第 9 讲：运维](/eps/week9-dev.md)
      - [第 10 讲：预编译合约](/eps/week10-dev.md)
    - **第 3 周：研究概览**
      - [第 11 讲：DAS](/eps/week6-research.md)  
      - [第 12 讲：Verkle 树](/eps/week7-research.md)
      - [第 13 讲：MEV](/eps/week8-research.md)
      - [第 14 讲：Portal 网络](/eps/week9-research.md)
      - [第 15 讲：SSF](/eps/week10-research.md)
    - **第 4 周：核心特性** 
      - [第 16 讲：Gasper](/eps/day16.md)
      - [第 17 讲：EVM](/eps/day17.md)
    - **第 5 周：网络**
      - [第 18 讲：devp2p](/eps/day18.md)
      - [第 19 讲：libp2p](/eps/day19.md)
- [贡献指南](contributing.md)

## 协议百科

- **协议**
  - [史前时代](/wiki/protocol/prehistory.md)
  - [架构](/wiki/protocol/architecture.md)
  - [设计理念](/wiki/protocol/design-rationale.md)
  - [演进历程](/wiki/protocol/history.md)

- **执行层**
  - [执行层规范](/wiki/EL/el-specs.md)
  - [客户端架构](/wiki/EL/el-architecture.md)
  - [执行层客户端](/wiki/EL/el-clients.md)
    - [Besu](/wiki/EL/clients/besu.md)
    - [Reth](/wiki/EL/clients/reth.md)
  - [EVM](/wiki/EL/evm.md)
    - [预编译合约](/wiki/EL/precompiled-contracts.md)
  - [区块构建](/wiki/EL/block-production.md)
  - [数据结构](/wiki/EL/data-structures.md)
  - [交易解析](/wiki/EL/transaction.md)
  - [JSON-RPC](/wiki/EL/JSON-RPC.md)
  - [DevP2P](/wiki/EL/devp2p.md)
  - [RLP 序列化](/wiki/EL/RLP.md)
  - [EOF](/wiki/EL/eof.md)

- **共识层**
  - [概览](/wiki/CL/overview.md)
  - [客户端架构](/wiki/CL/cl-architecture.md)
  - [共识层网络](/wiki/CL/cl-networking.md)
  - [共识层规范](/wiki/CL/cl-specs.md)
  - [Beacon API](/wiki/CL/beacon-api.md)
  - [共识层客户端](/wiki/CL/cl-clients.md)
  - [SSZ 序列化](/wiki/CL/SSZ.md)
    - [默克尔化](/wiki/CL/merkleization.md)
  - [弱主观性](/wiki/CL/syncing.md)

- **开发**
  - [核心开发](/wiki/dev/core-development.md)
  - [协调工作](/wiki/dev/pm.md)
  - [开发资源](/wiki/dev/cs-resources.md)

- **测试与安全**
  - [测试概览](/wiki/testing/overview.md)
  - [安全事件](/wiki/testing/incidents.md)
  - [Hive](/wiki/testing/hive.md)
  - [Kurtosis](/wiki/testing/kurtosis.md)
  - [形式化验证](/wiki/testing/formal-verification.md)

- **研究**
  - [路线图概览](/wiki/research/roadmap.md)
  - [扩展性](/wiki/research/scaling/scaling.md)
    - [核心变更](/wiki/research/scaling/core-changes/core-changes.md)
    - [EIP-4844](/wiki/research/scaling/core-changes/eip-4844.md)
  - [MEV](/wiki/research/PBS/mev.md)
    - [MEV-Boost](/wiki/research/PBS/mev-boost.md)
  - [PBS](/wiki/research/PBS/pbs.md)
    - [ePBS](/wiki/research/PBS/ePBS.md)
      - [ePBS 设计规范](/wiki/research/PBS/ePBS-Specs.md)
    - [PTC](/wiki/research/PBS/PTC.md)
    - [PEPC](/wiki/research/PBS/PEPC.md)
    - [TBHL](/wiki/research/PBS/TBHL.md)
    - [ET](/wiki/research/PBS/ET.md)
  - [eODS](/wiki/research/eODS.md)
  - [PeerDAS](/wiki/research/peerdas.md)
  - **预确认**
    - [预确认概览](/wiki/research/Preconfirmations/Preconfirmations.md)
    - [基于预确认的排序](/wiki/research/Preconfirmations/BasedSequencingPreconfs.md)
  - [轻客户端](/wiki/research/light-clients.md)
  - **账户抽象**
    - [EIP-7702](/wiki/research/account-abstraction/eip-7702.md)

- **密码学**
  - [概览](/wiki/Cryptography/intro.md)
  - [ECDSA](/wiki/Cryptography/ecdsa.md)
  - [BLS](/wiki/Cryptography/bls.md)
  - [Keccak256](/wiki/Cryptography/keccak256.md)
  - **承诺方案**
    - [KZG](/wiki/Cryptography/KZG.md)
  - [后量子密码学](/wiki/Cryptography/post-quantum-cryptography.md)

- [协议研究员项目](/wiki/epf.md)
- [Pectra 常见问题](/wiki/pectra-faq.md)

---

## 维基信息

- [GitHub 仓库](https://github.com/eth-protocol-fellows/protocol-studies)

<form action="https://epf.wiki/#/eps/intro" target="_blank">
  <input type="submit" value="加入协议学习小组" class="btn-primary" />
</form>
