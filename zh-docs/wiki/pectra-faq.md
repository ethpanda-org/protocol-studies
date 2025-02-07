# Pectra 常见问题

**Pectra 是什么?**
Pectra, (Prague - Electra), 是以太坊计划的下一次网络升级。可以在[此处](https://notes.ethereum.org/@ethpandaops/mekong#What-is-in-the-Mekong-testnet)找到完整的 EIP 列表以及功能介绍。

**本指南适合哪些人?**
对即将到来的 Pectra 升级感兴趣的应用程序开发者、质押者和节点运营者。

总览
---
**常见问题**:
#### **问题:** Prague/Electra 是什么?
**回答:** Prague 和 Electra 是即将到来的以太坊硬分叉的名称。可以在[此处](https://eips.ethereum.org/EIPS/eip-7600)找到分叉包含的 EIP。Prague 是执行客户端分叉的名称，而 Electra 是共识层客户端升级的名称。

Pectra 包含 3 个主要功能，以及一些较小的 EIP。他们分别是: [EIP-7251](https://eips.ethereum.org/EIPS/eip-7251) (也称为最大有效余额 (MaxEB))、[EIP-7702](https://eips.ethereum.org/EIPS/eip-7702)，以及  [EIP-7002](https://eips.ethereum.org/EIPS/eip-7002) (也称为执行层触发退出)。

[MaxEB](https://eips.ethereum.org/EIPS/eip-7251) 功能将允许用户拥有大于 32ETH 的有效余额。这使用户能够将多个验证器合并为一个（或新增验证器），最大有效余额为 2048ETH。使用此功能需要设置 `0x02` 提现凭证。用户可以直接使用 `0x02` 凭证进行存款，也可以从 `0x01` 迁移至 `0x02`。

通过 [EIP-7702](https://eips.ethereum.org/EIPS/eip-7702), 用户钱包能够将控制权委托给智能合约。这种模式为新的钱包和应用程序交互设计提供了可能，为未来的完整帐户抽象解决方案铺平了道路。

[执行层 (EL) 触发退出](https://eips.ethereum.org/EIPS/eip-7002) 是一项新功能，允许 `0x01` 或 `0x02` 提现凭证中设置的提现地址直接在执行层执行退出，无需依赖预签名 BLS 密钥。该功能主要面向质押池，使其能够使用智能合约完全控制验证器生命周期。

用户/开发者
---
**常见问题**:
#### **问题:** EIP-7702 是什么？它与帐户抽象有哪些联系?
虽然 [EIP-7702](https://eips.ethereum.org/EIPS/eip-7702) 并不完全是帐户抽象，但它确实提供了执行抽象，即为外部拥有帐户 (EOA) 添加了额外功能。这使你的 EOA 能够执行如发送批量交易和委托给其他加密密钥方案 (如 passkeys) 等操作。它通过将与 EOA 关联的代码设置为协议级别的代理标识来实现这一点。可以在[此处](https://eips.ethereum.org/EIPS/eip-7702)找到完整的规范。EIP-7702 引入了一种新的交易类型，可在单笔交易期间临时授权 EOA 特定的合约代码，使其能够像智能合约帐户一样运作。这为用户提供了多个应用场景，包括批量交易、gas 赞助和权限降级。

#### **问题:** 我能够在哪里找到 EIP-7702 的规范? 作为一名钱包开发者，我应该如何使用它?
可以在[此处](https://eips.ethereum.org/EIPS/eip-7702)找到 [EIP-7702](https://eips.ethereum.org/EIPS/eip-7702) 的规范。作为钱包开发者入门时，你需要确定一个与 EOA 一起使用的智能合约钱包核心 。请特别注意钱包[应该如何初始化](https://eips.ethereum.org/EIPS/eip-7702#front-running-initialization)。一旦你确定了要使用的核心钱包，你需要暴露类似 `eth_sendTransaction` 的行为以及其他用于 [EIP-7702](https://eips.ethereum.org/EIPS/eip-7702) 特定功能 (如批量交易) 的自定义方法。

#### **问题:** 作为一名用户，我要如何使用 EIP-7702?
为了享受 EIP-7702 的[好处](https://ethereum.org/en/roadmap/account-abstraction/)，即对帐户抽象的早期尝试，你需要使用一个支持该 EIP 的钱包。一旦你选择的钱包支持帐户抽象，你就可以使用它了。

#### **问题:** 我必须等待我的钱包支持 EIP-7702 吗?
很不幸，是的，除非你的钱包集成了 [EIP-7702](https://eips.ethereum.org/EIPS/eip-7702)，否则将无法使用此 EIP 提供的新功能。

#### **问题:** 作为一名智能合约开发者，我需要了解有关 EIP-7702 的哪些信息?
作为智能合约开发者，你应该知道，在 Prague 升级之后，以太坊的大多数用户将能够使用比之前更复杂的方式与区块链进行交互。为了克服 EOA 的局限性，已经开发了许多标准，例如 [ERC-2612 Permit](https://eips.ethereum.org/EIPS/eip-2612)。

#### **问题:** 作为一名安全工程师/审计者，我需要了解有关 EIP-7702 的哪些信息？
作为安全工程师 / 审计者，你必须意识到，之前关于在 `msg.sender == tx.origin` 时框架无法重入的假设已不再成立。这意味着该检查已不再适用于重入保护或闪电贷保护。
#### **问题:** EIP-2537 BLS 预编译在 pectra 中添加了什么?
[EIP-2537](https://eips.ethereum.org/EIPS/eip-2537) 将 BLS12-381 曲线上的操作作为预编译引入以太坊。BLS12-381 预编译使得 BLS 签名验证更加高效。这对于需要验证多个签名的应用程序非常有用，例如证明检查系统。

#### **问题:** 我要如何使用 `BLOCKHASH` 操作码?
现在，最后 8192 个区块的哈希值被存储并可以通过 `BLOCKHASH` 系统合约进行访问。`BLOCKHASH` 操作码的语义与之前保持一致，只是现在可以使用大端编码来指定区块号码。该区块哈希系统合约还可以通过 ethCall RPC 方法调用，相关的区块号码作为 calldata 传递。

#### **问题:** 系统合约是什么?
系统合约是被定义为合约的接口，对于某些以太坊功能的执行至关重要。采用合约的方式而非让每个客户端实现相同的逻辑，是为了简化逻辑，并且在未来能够以最小的开销进行升级。

质押者
---
**常见问题**:
#### **问题:** 存款有哪些改变?
存款的创建和提交过程不会改变。你可以继续使用之前的工具。然而，以太坊上处理存款的机制将会进行改进。[EIP-6110](https://eips.ethereum.org/EIPS/eip-6110) 描述了这项改进，它将使得存款处理几乎即时完成。

#### **问题:** 我需要等待多久才能让我的存款被包含进链中?
在 [EIP-6110](https://eips.ethereum.org/EIPS/eip-6110) 中的改动生效后，存款应该会在链的常规最终确定期后 20 分钟内显示。然而，仍然只有一个存款队列供你的验证器激活，此 EIP 只是确保存款被链更快、更安全地看到，并不会影响验证器激活的速度。

#### **问题:** `0x02` 提现凭证是什么?
在 Pectra 分叉前，以太坊接受两种类型的提现凭证: `0x00` 和 `0x01`。 主要的变化是，`0x01` 现在包含一个执行层地址，用于接收部分和全部提现。`0x02` 提现凭证是将在 Pectra 升级中引入的一种新型提现凭证。`0x02` 提现凭证将允许大于 32 ETH 且小于 2048ETH 的最大有效余额，支持通过更大的存款或整合现有验证器来实现。`0x02` 提现凭证还使得通过执行层提现地址退出验证器成为可能，从而实现通过执行层完全控制验证器的能力。

#### **问题:** 我要如何切换到 `0x02` 提现凭证? 它对我有什么帮助?
验证器可以通过两种方式获得 `0x02` 提现凭证:
1. 使用 `0x02` 提现凭证新增一个验证器
2. 通过向需要整合的地址发送一笔交易，将现有验证器整合为 `0x02` 提现凭证

虽然 `0x01` 和 `0x02` 提现凭证都允许你通过执行层地址控制验证器退出， 但只有 `0x02` 允许验证器拥有大于 32ETH 且小于 2048ETH 的最大有效余额。这意味着你可以运行一个验证器，并使其最大余额达到 2048 ETH。

#### **问题:** 我可以直接使用 `0x02` 凭证新增一个验证器吗?
是的，你可以直接使用 `0x02` 凭证新增一个验证器。这将使你拥有一个最大余额为 2048ETH 的验证器。

要立即尝试存款，你可以使用 ethstaker 的 [`staking-cli`](https://github.com/eth-educators/ethstaker-deposit-cli)，此工具已通过 `--compounding` 标志支持 `0x02` 凭证。你还可以通过 `--amount` 标志指定大于或小于 32 ETH 的存款数量。 
官方的 [`staking-deposit-cli`](https://github.com/ethereum/staking-deposit-cli) 将在未来几个月内支持 `0x02` 提款凭证，之后 Pectra 主网以太坊分叉将会推出。

生成的存款数据文件可以像往常一样发送到网络启动板进行存款交易。
对于测试网，你可以使用 Dora 中的 [`Submit Deposits`](https://dora.mekong.ethpandaops.io/validators/deposits/submit) 页面来提交生成的存款。官方启动板的支持也将在未来几个月内推出。

#### **问题:** 我拥有一个使用 `0x00` 凭证的验证器，我该如何迁移到 `0x02`?
There is no direct way to move from `0x00` to `0x02`. You will need to first move your validator from `0x00` to `0x01` withdrawal credentials with a BLS change operation, then consolidate your validators to `0x02` withdrawal credentials. You can alternatively exit the validator and make a new deposit with `0x02` withdrawal credentials during the deposit.

#### **问题:** 我拥有一个使用 `0x01` 凭证的验证器，我该如何迁移到 `0x02`?
You can consolidate your validator to `0x02` withdrawal credentials via a self consolidation with both, the source and target pointing to your validator. This will change your withdrawal credentials to `0x02` and allows you to have a single validator with a balance of up to 2048 ETH. For new deposits, the `staking-cli` will support the `0x02` withdrawal credentials in the coming months before the Pectra mainnet Ethereum fork.

#### **Q:** What is MaxEB?
[EIP-7251](https://eips.ethereum.org/EIPS/eip-7251) or MaxEB increases the `MAX_EFFECTIVE_BALANCE` to 2048 ETH while keeping the minimum staking balance at 32 ETH. Before MaxEB, any entity that wanted to contribute a large amount of ETH to consensus had to spin up multiple validators because each was capped at a maximum of 32 ETH. [EIP-7251](https://eips.ethereum.org/EIPS/eip-7251) will allow large stake operators to consolidate their ETH into fewer validators, using the same stake with up to 64 times less individual validators. It also allows solo stakers' ETH to be compounded into their existing validator and contribute to their rewards without having to use the exact validator amount. For example, 35 ETH will be considered the validator's effective balance by the protocol, instead of leaving out 3 ETH ineffective and waiting till 64 ETH for 2 validators. Overall, consolidating validators will allow for fewer attestations in the consensus network and easing the bandwidth usage by nodes.

#### **Q:** How do I consolidate my validators?
To consolidate your validators, you first need to ensure that both the source and target validators have either `0x01` or `0x02` credentials assigned.
Validators with withdrawal credentials using the `0x00` prefix or pointing to different execution layer addresses cannot be consolidated.

To consolidate two validators, send a transaction from your withdrawal address to the consolidation system contract, including the public keys of the source and target validators you wish to consolidate.

We expect this functionality to be added to various tools in the coming months.
For testing right now, you can use the [Submit Consolidations](https://dora.mekong.ethpandaops.io/validators/submit_consolidations) page in Dora. Connect with the wallet used as the withdrawal address for your validators, and you should be able to select your validators and craft an appropriate consolidation transaction.

A consolidation where the source and target point to the same validator is called a self-consolidation.
In this situation, the validator will not be exited, and no funds will be moved. It will simply be assigned 0x02 credentials.

#### **Q:** What are the validator requirements for consolidation?
The validators must be active on the beacon chain at the time of consolidation execution. This means they cannot be exiting, pending activation, or in any state other than active.
Both the source and target validators must have `0x01` or `0x02` withdrawal credentials pointing to the same withdrawal address. If these two conditions are met, the validators may be consolidated.

#### **Q:** What happens to my original, individual validators?
During a consolidation, there is a source and a target validator. The source validator is completely exited and the balance is then transferred to the target validator. The target validator will have the sum of the balances of the source validator and the target validator and will continue to perform its beacon chain duties without any change. 

#### **Q:** When does the balance appear on my consolidated validator?
Once the source validator has completely exited and ceased performing all duties, the balance will be credited to the target validator. 

#### **Q:** When happens if I consolidate one validator with`0x01` and another with `0x00` credentials?
The consolidation request will be deemed invalid and will not be processed. It will fail if both validators don't contain a `0x01` withdrawal credential with the exact same execution layer address. 

#### **Q:** What happens if I consolidate validators that are exited?
The consolidation will fail as the validators must be active on the beacon chain at the time of consolidation execution.

#### **Q:** How can I partially withdraw some ETH from my `0x02` validator?
You can issue a EL triggered partial withdrawal to withdraw some ETH from the `0x02` validator.
Send a transaction to the withdrawal system contract (pending address to be finalized when Electra goes live on mainnet) with your validator `pubkey` and the `amount` (a positive non-zero Gwei amount).
As with consolidations, this transaction must be sent from the withdrawal address set in your validator's withdrawal credentials.

We expect this functionality to be added to various tools in the coming months.
For testing right now, you can use the [Submit Withdrawals](https://dora.mekong.ethpandaops.io/validators/submit_withdrawals) page in Dora. Connect with the wallet that is used as the withdrawal address for your validators, and you should be able to select between your validators and craft an appropriate withdrawal transaction.

#### **Q:** How much ETH can I withdraw from my validator?
You can partially withdraw the portion above the full validator amount, as long as the validator contains >32 ETH at the time of withdrawal completion. For example, if you currently have 34 ETH and request a partial withdrawal, a maximum of 2 ETH can be withdrawn.
You may also decide to request a full withdrawal by specifying an amount of `0` in the request. When sending such a full withdrawal request, your validator will be exited, and the full balance withdrawn.

#### **Q:** What happens to the ETH balance if my validator has `0x02` credentials and goes below 32 ETH?
A normally behaved validator will not have its balance dropped below 32ETH even if you initiate a partial withdrawal request. This can only be achieved if validator receives penalty. Nothing will happen except reduced rewards. However if balance drops below 16ETH, the validator will be exited and the balance will be transferred to the execution layer withdrawal address.

#### **Q:** What happens to the ETH balance if my validator has `0x02` credentials and goes above 2048 ETH?
The balance will continue to collect at the validator until the next partial withdrawal is triggered. The validator will however contain a maximum effective balance of 2048 ETH, the remaining balance will be considered ineffective in the beacon chain.

#### **Q:** What balances between 32ETH and 2048ETH can I earn on?
The effective balance increments 1 ETH at a time. This means the accrued balance needs to meet a threshold before the effective balance changes. E.g, if the accrued balance is 33.74 ETH, the effective balance will be 33 ETH. If the accrued balance increases to 33.75 ETH, then the effective balance will also be 33 ETH. Consequently, an accrued balance of 34.25 ETH would correspond to an effective balance of 34 ETH.

#### **Q:** Can I top up ETH in my `0x02` validator?
You can either consolidate a validator into the `0x02` validator to increase its balance or make a fresh deposit.

#### **Q:** What happens to the ETH balance if I consolidate and my validator has `0x02` credentials and the total balance goes above 2048 ETH?
The balance will continue to collect at the validator until the next partial withdrawal is triggered. The validator will however contain a maximum effective balance of 2048 ETH, the remaining balance will be considered ineffective in the beacon chain. The portion over 2048ETH will be withdrawn when partial withdrawal sweep comes.
