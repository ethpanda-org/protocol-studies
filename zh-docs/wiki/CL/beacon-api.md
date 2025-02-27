# 信标链规范和 APIs

以太坊权益证明的核心是一个被称为 “Beacon chain” 的系统链。信标链存储和管理验证器的注册表。在权益证明的初始部署阶段，成为验证者的唯一机制是在以太坊工作量证明链上进行单向（在Capella退出后）ETH交易到存款合约。当信标链处理存款收据时，激活作为验证器发生，达到激活余额，并完成排队过程。退出要么是自愿退出，要么是作为对不当行为的惩罚而强制退出。信标链上的主要负载来源是“证明”。证明是对一个分片块的可用性投票（在后续升级中）和对一个信标块的权益证明投票（阶段0）。

本节将介绍Beacon Chain规范和api的一些重要部分。还可以查看complete [Beacon Chain Spec]（https://github.com/ethereum/consensus-specs/blob/dev/specs/phase0/beacon-chain.md）和[Beacon API reference]（https://ethereum.github.io/beacon-APIs/#/）。

Beacon API是由共识层客户端（Beacon节点）提供的REST端点。它是读取关于共识信息的接口，并由验证器客户端使用。
## 容器

`BeaconState`

```python
class BeaconState(Container):
    # Versioning
    genesis_time: uint64
    genesis_validators_root: Root
    slot: Slot
    fork: Fork
    # History
    latest_block_header: BeaconBlockHeader
    block_roots: Vector[Root, SLOTS_PER_HISTORICAL_ROOT]
    state_roots: Vector[Root, SLOTS_PER_HISTORICAL_ROOT]
    historical_roots: List[Root, HISTORICAL_ROOTS_LIMIT]
    # Eth1
    eth1_data: Eth1Data
    eth1_deposit_index: uint64
    application_state_root: Bytes32
    # Registry
    validators: List[Validator, VALIDATOR_REGISTRY_LIMIT]
    balances: List[Gwei, VALIDATOR_REGISTRY_LIMIT]
    # Randomness
    randao_mixes: Vector[Bytes32, EPOCHS_PER_HISTORICAL_VECTOR]
    # Slashings
    slashings: Vector[Gwei, EPOCHS_PER_SLASHINGS_VECTOR]
    # Attestations
    previous_epoch_attestations: List[PendingAttestation, MAX_ATTESTATIONS * SLOTS_PER_EPOCH]
    current_epoch_attestations: List[PendingAttestation, MAX_ATTESTATIONS * SLOTS_PER_EPOCH]
    # Finality
    justification_bits: Bitvector[JUSTIFICATION_BITS_LENGTH]  # Bit set for every recent justified epoch
    previous_justified_checkpoint: Checkpoint  # Previous epoch snapshot
    current_justified_checkpoint: Checkpoint
    finalized_checkpoint: Checkpoint
```

#### `BeaconBlockBody`

```python
class BeaconBlockBody(Container):
    randao_reveal: BLSSignature
    eth1_data: Eth1Data  # Eth1 data vote
    graffiti: Bytes32  # Arbitrary data
    # Operations
    proposer_slashings: List[ProposerSlashing, MAX_PROPOSER_SLASHINGS]
    attester_slashings: List[AttesterSlashing, MAX_ATTESTER_SLASHINGS]
    attestations: List[Attestation, MAX_ATTESTATIONS]
    deposits: List[Deposit, MAX_DEPOSITS]
    voluntary_exits: List[SignedVoluntaryExit, MAX_VOLUNTARY_EXITS]
    application_payload: ApplicationPayload
```
