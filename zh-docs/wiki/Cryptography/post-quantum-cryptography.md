# 后量子密码学

经典密码学通过利用某些数学问题的固有难度来保护信息。这类问题包括素因子分解、离散对数、图同构、最短向量问题等，它们都属于数学研究领域中的[“隐藏子群问题 (HSP)”](https://en.wikipedia.org/wiki/Hidden_subgroup_problem)。

本质上，这些问题使得在未知“秘密”（私钥）的情况下，确定一个大群中的隐藏子群（大小、元素）的结构在计算上变得不可行。这种单向“陷门函数”被公钥密码算法用于保障其安全性。

[RSA](<https://en.wikipedia.org/wiki/RSA_(cryptosystem)>) 的安全性依赖于**大素数的因子分解**。相比之下，[ECDSA](/wiki/Cryptography/ecdsa.md) 的安全性基于椭圆曲线**离散对数问题**。解决这些隐藏子群问题的计算难度随着密钥长度的增加呈指数增长，使得经典计算机几乎无法破解。这一基本特性保障了加密数据的安全性。

然而，局势正在发生变化。

量子计算机利用量子力学原理提供了全新的计算方法。某些量子算法可以比经典算法**指数级**提高求解这些经典密码学问题的效率。这一新能力对当前使用的公钥密码学构成了重大威胁。如果大规模量子计算机得以建成，它们将能够破解目前广泛使用的许多公钥密码系统。

[Shor 算法](https://ieeexplore.ieee.org/document/365700) 是量子计算最著名的应用之一，它可以在时间复杂度小于 $O(n^3)$ 的情况下对 $n$ 位整数进行因子分解，这相比于最佳的经典算法而言是一个显著的提升。

这正是后量子密码学研究的核心目标：开发即使在强大量子计算机存在的情况下仍然安全的新型算法。

## 时间线

根据 ["Quantum Threat Timeline Report 2020"](https://globalriskinstitute.org/publication/quantum-threat-timeline-report-2020/) 的调查，大多数专家认为，在 2030 年之前，公钥密码学面临量子计算攻击的风险低于 5%。然而，到 2050 年，这一风险预计将大幅上升至约 50%。

目前，最[先进的量子计算机](https://en.wikipedia.org/wiki/List_of_quantum_processors) 仅拥有不到 2000 个物理量子比特。而要在一小时（理想时间窗口）内破解比特币的加密，[需要大约 3.17 亿个物理量子比特](https://pubs.aip.org/avs/aqs/article/4/1/013801/2835275/The-impact-of-hardware-specifications-on-reaching)。

量子研究正在稳步推进；某位调查受访者指出：

> 并非总是如此 [..] 但我发现，我的预测往往比实际进展更加悲观。我认为这表明研究正在加速进行。

需要注意的是，这些预测在一定程度上具有主观性，并可能无法准确反映实际进展，尤其是许多研究未对公众开放。某些高级威胁行为者可能会比公众更早获得强大量子计算能力，并采取诸如[“先收集，后解密”](https://en.wikipedia.org/wiki/Harvest_now%2C_decrypt_later) 的策略。

### 2025年

2025年2月，微软宣布在一块芯片上实现了**百万个量子比特**。这一成就标志着量子计算领域的重大突破。[视频解释与背景](https://www.youtube.com/watch?v=jwnez8HdN7E)。

**Majorana-1芯片**是微软在量子计算方面的一项重要进展，采用了**Majorana费米子**，它们被认为是自我反粒子。微软通过这一技术，希望克服目前量子计算中与量子比特稳定性和错误修正相关的挑战，从而使量子计算机能够更好地扩展。

如果你对这一进展的技术细节感兴趣，可以点击观看[视频](https://www.youtube.com/watch?v=jwnez8HdN7E)，了解更多背景信息和影响。



## 以太坊的后量子风险

以太坊账户由一个双层加密系统保护。私钥通过[椭圆曲线乘法](/wiki/Cryptography/ecdsa.md)生成公钥，该公钥再经过 [keccak256](/wiki/Cryptography/keccak256.md) 哈希计算，从而得出以太坊地址。

最直接的后量子威胁是量子计算机能够逆向计算椭圆曲线乘法，从而破解 ECDSA，进而暴露私钥。这使得所有外部拥有账户（EOA）都容易受到量子攻击。假设将公钥映射到以太坊地址的哈希函数仍然安全，提取私钥仍然具有挑战性，但依然存在风险。

实际上，大多数用户的私钥本身是通过 [BIP-32](https://github.com/bitcoin/bips/blob/b3701faef2bdb98a0d7ace4eedbeefa2da4c89ed/bip-0032.mediawiki) 进行多次哈希计算后生成的。BIP-32 从一个主种子（master seed phrase）派生出一系列地址。这进一步增加了揭示私钥的计算难度。

EthResearch 针对后量子紧急情况提出了[硬分叉提案](https://ethresear.ch/t/how-to-hard-fork-to-save-most-users-funds-in-a-quantum-emergency/18901)，主要措施包括：

1. 回滚至发生大规模盗窃的首个区块之前的状态。
2. 禁用基于 EOA 的传统交易。
3. 增加新的交易类型，使智能合约钱包（例如 [RIP-7560](https://ethereum-magicians.org/t/rip-7560-native-account-abstraction/16664)）可以执行交易（如果尚未支持）。
4. 添加新的交易类型或操作码（opcode），允许用户提供 STARK 证明，以证明以下内容：
   - (i) 知道一个私钥前像 $x$；
   - (ii) 该私钥是从一个获批哈希函数列表中的某个哈希函数 $1 \leq i < k$ 计算所得；
   - (iii) 该私钥对应的公钥地址 $A$，满足 `keccak(priv_to_pub(hashes[i](x)))[12:] = A`。
   
   STARK 证明的公共输入还需包含新账户验证代码的哈希值。如果证明通过，该账户的代码将被更新为新的验证逻辑，并从此作为智能合约钱包使用。

然而，这种方法并不完美。部分用户仍然会损失资金，因为攻击发生后，并非所有区块都会被回滚。此外，要准确识别网络上正在进行的量子攻击是极其困难的，正如 [domothy](https://ethresear.ch/t/how-to-hard-fork-to-save-most-users-funds-in-a-quantum-emergency/18901/14) 所指出的：

> 设想一个大型交易所的钱包被量子计算机盗取。每个人首先会认为是交易所自身的安全漏洞。又或者一个依赖离散对数假设的智能合约钱包被盗，开发者可能首先怀疑是智能合约漏洞。如果攻击者避免高调目标，而是慢慢从多个大型 EOA 账户盗取资金，我们甚至可能不会意识到量子攻击已经发生。

此外，支持 [EIP-4844](/wiki/research/scaling/core-changes/eip-4844.md) 的 KZG 承诺（commitment）方案也需要升级，以防止欺诈性提交。

## 研究进展

后量子密码学是一个活跃的研究领域。多个机构正在推动新型抗量子算法的原型开发、技术标准化及应用落地。

### NIST 后量子密码学

[NIST 后量子密码学标准化项目](https://csrc.nist.gov/projects/post-quantum-cryptography) 旨在通过竞赛式流程征集、评估并标准化一个或多个抗量子公钥密码算法。

### NIST 2022 年第三轮选定算法

#### I. 公钥加密与密钥交换算法

- [CRYSTALS-KYBER](https://pq-crystals.org/) —— Peter Schwabe 等人提出

#### II. 数字签名算法

- [CRYSTALS-DILITHIUM](https://pq-crystals.org/) —— Vadim Lyubashevsky 等人提出
- [FALCON](https://falcon-sign.info/) —— Thomas Prest 等人提出
- [SPHINCS+](https://falcon-sign.info/) —— Andreas Hulsing 等人提出

NIST 在[《2022 年状态报告》](https://tsapps.nist.gov/publication/get_pdf.cfm?pub_id=934458)中记录了标准化流程、评估标准及安全模型。

### 后量子密码学联盟（PQCA）

[后量子密码学联盟（PQCA）](https://pqca.org/) 由 [Linux 基金会](https://www.linuxfoundation.org/press/announcing-the-post-quantum-cryptography-alliance-pqca) 发起，旨在推动后量子密码技术的发展和应用。

在此框架下，[开放量子安全（OQS）](https://openquantumsafe.org/) 是一个开源项目，致力于支持抗量子密码学的过渡。

### 加密论坛研究组（CFRG）

[加密论坛研究组（CFRG）](https://datatracker.ietf.org/rg/cfrg/about/) 隶属于互联网工程任务组（IETF），已标准化了[状态哈希签名方案 "XMSS: eXtended Merkle Signature Scheme"](https://datatracker.ietf.org/doc/rfc8391/)。

## 生产环境中的应用

以下试点项目和研究计划正在探索后量子密码学在实际生产环境中的应用：

- [Anchor Vault](https://chromewebstore.google.com/detail/omifklijimcjhfiojhodcnfihkljeali) 是一个 Chrome 插件，允许使用 Lamport 签名为 ERC 代币提供抗量子证明。
- Signal 已在生产环境中实现[“后量子扩展 Diffie-Hellman”](https://signal.org/docs/specifications/pqxdh/#introduction) 作为密钥交换协议。
- Chromium 开始支持[“混合 Kyber KEM”](https://blog.chromium.org/2023/08/protecting-chrome-traffic-with-hybrid.html)，用于保护数据传输安全。
- Apple 推出了 [PQ3](https://security.apple.com/blog/imessage-pq3/)，以防止 iMessage 受到量子攻击引发的密钥泄露。

## 资源

- 📝 Daniel J. Bernstein 等人，[《后量子密码学导论》](https://pqcrypto.org/www.springer.com/cda/content/document/cda_downloaddocument/9783540887010-c1.pdf)
- 📝 维基百科，[《量子算法》](https://en.wikipedia.org/wiki/Quantum_algorithm)
- 📝 P.W. Shor，[《量子计算算法：离散对数与因子分解》](https://ieeexplore.ieee.org/document/365700)
- 📝 NIST，[《后量子密码学》](https://csrc.nist.gov/projects/post-quantum-cryptography)
- 📝 ETHResearch，[《如何通过硬分叉在量子紧急情况下保护大部分用户资金》](https://ethresear.ch/t/how-to-hard-fork-to-save-most-users-funds-in-a-quantum-emergency/18901)
- 📝 ETHResearch，[《ETHResearch：后量子密码学》](https://ethresear.ch/tag/post-quantum)
