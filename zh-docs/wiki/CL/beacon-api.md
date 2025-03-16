# 信标链规范与 API

以太坊权益证明（Proof-of-Stake，PoS）的核心是一个称为“信标链”（Beacon Chain）的系统链。信标链存储并管理验证者注册表。在 PoS 的初始部署阶段，成为验证者的唯一方式是向以太坊工作量证明（Proof-of-Work，PoW）链上的存款合约进行一次性（在 Capella 之后可提现）的 ETH 交易。验证者的激活发生在信标链处理存款收据、达到激活余额并完成排队过程后。退出可以是自愿的，也可以因不当行为受到强制惩罚。

信标链的主要负载来源是“认证”（Attestations）。认证同时是对分片区块（未来升级后）的可用性投票，也是对信标区块（Phase 0）的 PoS 投票。

本节将介绍信标链规范与 API 的重要部分。完整的[信标链规范](https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/beacon-chain.md)和[信标 API 参考](https://ethereum.github.io/beacon-APIs/#/)可供进一步查阅。

信标 API 是由共识层客户端（信标节点）提供的 REST 端点。它是用于读取共识信息的接口，并被验证者客户端使用。

## 数据结构

### `BeaconState`

```python
class BeaconState(Container):
    # 版本信息
    genesis_time: uint64
    genesis_validators_root: Root
    slot: Slot
    fork: Fork
    # 历史数据
    latest_block_header: BeaconBlockHeader
    block_roots: Vector[Root, SLOTS_PER_HISTORICAL_ROOT]
    state_roots: Vector[Root, SLOTS_PER_HISTORICAL_ROOT]
    historical_roots: List[Root, HISTORICAL_ROOTS_LIMIT]
    # 以太坊 1.0 相关数据
    eth1_data: Eth1Data
    eth1_data_votes: List[Eth1Data, EPOCHS_PER_ETH1_VOTING_PERIOD * SLOTS_PER_EPOCH]
    eth1_deposit_index: uint64
    # 验证者注册表
    validators: List[Validator, VALIDATOR_REGISTRY_LIMIT]
    balances: List[Gwei, VALIDATOR_REGISTRY_LIMIT]
    # 随机性
    randao_mixes: Vector[Bytes32, EPOCHS_PER_HISTORICAL_VECTOR]
    # 惩罚机制
    slashings: Vector[Gwei, EPOCHS_PER_SLASHINGS_VECTOR]  # 每个 epoch 的惩罚总额
    # 参与情况
    previous_epoch_participation: List[ParticipationFlags, VALIDATOR_REGISTRY_LIMIT]
    current_epoch_participation: List[ParticipationFlags, VALIDATOR_REGISTRY_LIMIT]
    # 最终性
    justification_bits: Bitvector[JUSTIFICATION_BITS_LENGTH]  # 记录每个 epoch 的最终性状态
    previous_justified_checkpoint: Checkpoint
    current_justified_checkpoint: Checkpoint
    finalized_checkpoint: Checkpoint
    # 惰性惩罚
    inactivity_scores: List[uint64, VALIDATOR_REGISTRY_LIMIT]
    # 同步委员会
    current_sync_committee: SyncCommittee
    next_sync_committee: SyncCommittee
    # 执行层数据
    latest_execution_payload_header: ExecutionPayloadHeader
    # 提现相关
    next_withdrawal_index: WithdrawalIndex
    next_withdrawal_validator_index: ValidatorIndex
    # Capella 之后的深度历史数据
    historical_summaries: List[HistoricalSummary, HISTORICAL_ROOTS_LIMIT]
    deposit_requests_start_index: uint64
    deposit_balance_to_consume: Gwei
    exit_balance_to_consume: Gwei
    earliest_exit_epoch: Epoch
    consolidation_balance_to_consume: Gwei
    earliest_consolidation_epoch: Epoch
    pending_deposits: List[PendingDeposit, PENDING_DEPOSITS_LIMIT]
    pending_partial_withdrawals: List[PendingPartialWithdrawal, PENDING_PARTIAL_WITHDRAWALS_LIMIT]
    pending_consolidations: List[PendingConsolidation, PENDING_CONSOLIDATIONS_LIMIT]
```

### `BeaconBlockBody`

```python
class BeaconBlockBody(Container):
    randao_reveal: BLSSignature
    eth1_data: Eth1Data  # 以太坊 1.0 数据投票
    graffiti: Bytes32  # 任意数据
    # 操作
    proposer_slashings: List[ProposerSlashing, MAX_PROPOSER_SLASHINGS]
    attester_slashings: List[AttesterSlashing, MAX_ATTESTER_SLASHINGS_ELECTRA]
    attestations: List[Attestation, MAX_ATTESTATIONS_ELECTRA]
    deposits: List[Deposit, MAX_DEPOSITS]
    voluntary_exits: List[SignedVoluntaryExit, MAX_VOLUNTARY_EXITS]
    sync_aggregate: SyncAggregate
    # 执行层数据
    execution_payload: ExecutionPayload
    bls_to_execution_changes: List[SignedBLSToExecutionChange, MAX_BLS_TO_EXECUTION_CHANGES]
    blob_kzg_commitments: List[KZGCommitment, MAX_BLOB_COMMITMENTS_PER_BLOCK]
    execution_requests: ExecutionRequests
```

以上是信标链的一部分核心数据结构。信标链 API 允许开发者查询这些数据，并与以太坊的共识层进行交互。

