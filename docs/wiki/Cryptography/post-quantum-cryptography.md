# Post-Quantum Cryptography

经典密码学利用某些数学问题的固有难度来保护信息。素因式分解、离散对数、图同构、最短向量问题等问题群都属于数学研究领域，称为 [“隐藏子群问题 (HSP)”](https://en.wikipedia.org/wiki/Hidden_subgroup_problem)。

从本质上讲，这些问题使得在不知道“秘密”(私有) 密钥的情况下确定大组内秘密子组的结构 (大小、元素) 在计算上变得困难。公钥加密算法为了保证其安全性而采用这种单向“陷门函数”。

[RSA's](<https://en.wikipedia.org/wiki/RSA_(cryptosystem)>) 安全性依赖于**大质数的因式分解**。相比之下，[ECDSA's](/wiki/Cryptography/ecdsa.md) 安全性基于椭圆曲线 **离散对数问题**。随着密钥大小的增加，解决这些隐藏子群问题中的任何一个都变得更加困难，使得经典计算机在计算上无法破解它们。这一根本性的困难保护了加密数据。

然而，情况正在发生变化。

量子计算机利用量子力学原理，提供新颖的计算方法。与经典算法相比，某些量子算法可以以指数效率解决这些经典密码问题。这种新发现的功能对使用经典密码学加密的数据的安全性构成了重大威胁。如果大规模量子计算机建成，它们将能够破解目前使用的许多公钥密码学。

用于整数分解的 [Shor 算法](https://ieeexplore.ieee.org/document/365700) 是量子计算最著名的应用。它以小于 $O (n^3)$ 的时间复杂度分解 n 位整数，这是对最佳经典算法的显着改进。

这就是 Post-Quantum Cryptography 领域的用武之地。它的目标是开发新的算法，即使在强大的量子计算机存在的情况下也能保持安全。

## 时间轴

根据对 [“2020 年量子威胁时间线报告”](https://globalriskinstitute.org/publication/quantum-threat-timeline-report-2020/) 所做的调查，大多数专家认为，到 2030 年，公钥密码学面临的威胁将低于 5%。然而，预计到 2050 年，风险将大幅增加到 50% 左右。

目前，[最先进的量子计算机](https://en.wikipedia.org/wiki/List_of_quantum_processors) 拥有<2000 个物理量子位。一小时内破解 Bitcoin 的加密 (理想时间窗口)[需要大约 3.17 亿个物理量子位](https://pubs.aip.org/avs/aqs/article/4/1/013801/2835275/The-impact-of-hardware-specifications-on-reaching)。

量子研究稳步进展；一位调查受访者指出：

> 情况并非总是如此[..]，但我发现我的预测往往比实际发生的情况更为悲观。我认为这表明研究正在加速。

请注意，这些预测有些主观，可能无法反映真正的进展，而这些进展大多不向公众开放。高级威胁行为者可能比公众更早获得强大的量子计算，并使用 [回顾性解密](https://en.wikipedia.org/wiki/Harvest_now%2C_decrypt_later) 等策略。

### 2025

2025 年 2 月，微软宣布 [单芯片上百万个量子位](https://news.microsoft.com/source/features/innovation/microsofts-majorana-1-chip-carves-new-path-for-quantum-computing/)。 [视频解说](https://www.youtube.com/watch?v=jwnez8HdN7E)。 

## Ethereum 的后量子风险

Ethereum 帐户由两层加密系统保护。私钥用于通过 [椭圆曲线乘法](/wiki/Cryptography/ecdsa.md) 生成公钥。该公钥使用 [keccak256](/wiki/Cryptography/keccak256.md) 进行哈希处理，以得出 Ethereum 地址。

直接的后量子威胁是逆转椭圆曲线乘法的能力，从而保护 ECDSA 从而暴露私钥。这使得所有外部账户 (EOA) 都容易受到量子攻击。假设将公钥映射到 Ethereum 地址的哈希函数仍然安全，提取其私钥仍然具有挑战性，但仍然容易受到攻击。

实际上，大多数用户的私钥本身就是使用 [BIP-32](https://github.com/bitcoin/bips/blob/b3701faef2bdb98a0d7ace4eedbeefa2da4c89ed/bip-0032.mediawiki) 进行一堆哈希计算的结果，它通过从主种子短语开始的一系列哈希生成每个地址。这使得泄露私钥的计算成本更高。

EthResearch 有一个在后量子紧急情况下进行 hard fork 的 [正在进行的提案](https://ethresear.ch/t/how-to-hard-fork-to-save-most-users-funds-in-a-quantum-emergency/18901)，关键行动是：

1. 恢复第一个区块之后的所有区块，显然正在发生大规模盗窃
2. 基于传统 EOA 的交易被禁用
3. 添加了新的交易类型，以允许来自智能合约钱包的交易 (例如 [RIP-7560](https://ethereum-magicians.org/t/rip-7560-native-account-abstraction/16664) 的一部分)，如果该类型尚不可用
4. 添加了新的交易类型或操作码，您可以通过它提供 STARK 证明，证明 (i) 私有原像 x，(ii) 哈希函数 ID `1 <= i < k` 来自 k 批准的列表哈希函数，以及 (iii) 公共地址 A，使得 `keccak (priv_to_pub (hashes[i](x)))[12:] = A`。 STARK 还接受该帐户的新验证码哈希作为公共输入。如果证明通过，您的帐户代码将切换为新的验证代码，从那时起您将能够将其用作智能合约钱包。

然而，这种方法并不完美。一些用户仍然会损失资金，因为并非所有来自攻击的区块都会被恢复。这是因为要可靠地检测网络上的量子攻击非常困难，如 [domothy 亮点](https://ethresear.ch/t/how-to-hard-fork-to-save-most-users-funds-in-a-quantum-emergency/18901/14)：

> 想象一下一个大型交易所钱包被量子计算机耗尽的情况。每个人都会自然地认为这是交易所一端的某种安全故障。或者，如果依赖离散日志假设的智能钱包被耗尽，首先想到的就是智能合约错误/漏洞。或者，支持量子的攻击者完全避开引人注目的目标，并慢慢地从各种大型 EOA 中窃取资金，而我们甚至不知道发生了量子攻击。

此外，支持 [EIP-4844](/wiki/research/scaling/core-changes/eip-4844.md) 的 KZG 承诺方案也需要升级以防止欺诈性提交。

## 研究

Post-Quantum Cryptography 是一个活跃的研究领域。一些组织正在致力于新的后量子算法的原型设计、开发和标准化。

## NIST Post-Quantum Cryptography

[NIST 后量子密码标准化](https://csrc.nist.gov/projects/post-quantum-cryptography) 进行了一项为期多年的国际竞赛，以评估和标准化抗量子密码算法。 2024 年 8 月，NIST 发布了第一套最终确定的 **PQC 标准**，作为联邦信息处理标准 (FIPS)：

### 发布的标准 (2024 年 8 月)

**关键封装机制：**

- **ML-KEM** ([FIPS 203](https://doi.org/10.6028/NIST.FIPS.203)) 源自 CRYSTALS-Kyber。 **密钥封装机制 (KEM)**：一组三种算法 (KeyGen、Encaps、Decaps)，用于通过公共通道建立共享密钥。基于 **有错误的模块学习 (MLWE)** 问题。

| 参数设置 | 安全实力 | 安全类别 |
|---|---|---|
| ML-KEM-512 | 128 位 | 1 |
| ML-KEM-768 | 192 位 | 3 |
| ML-KEM-1024 | 256 位 | 5 |

**数字签名算法：**

- **ML-DSA** ([FIPS 204](https://doi.org/10.6028/NIST.FIPS.204)) 源自 CRYSTALS-Dilithium。基于格的数字签名算法。

| 参数设置 | 安全实力 | 安全类别 |
|---|---|---|
| ML-DSA-44 | 128 位 | 2 |
| ML-DSA-65 | 192 位 | 3 |
| ML-DSA-87 | 256 位 | 5 |

- **SLH-DSA** ([FIPS 205](https://doi.org/10.6028/NIST.FIPS.205)) 源自 SPHINCS+。 NIST 的无状态哈希基于数字签名标准。

  它由三个经过充分研究的组件构成：
  - **WOTS+**(Winternitz 一次性签名 Plus)，一次性签名原语
  - **XMSS**(扩展 Merkle 签名方案)，基于 WOTS+ 构建的多时间方案
  - **FORS**(随机子集森林)，签名消息摘要的几个时间方案

  与 ML-DSA 不同，SLH-DSA 不需要**数论硬度假设**。安全性仅取决于标准哈希函数属性 (原像抵抗和相关属性)，使其能够抵抗量子攻击，而无需 Shor 算法可以利用的任何代数结构。

  每个安全级别提供两种变体：
  - `s` = 签名较小，签名速度较慢
  - `f` = 签名越大，签名速度越快

| 参数设置 | 安全类别 | 签名尺寸 |
|----------------------------------------|-------------------|----------------|
| SLH-DSA-SHA2-128s / SLH-DSA-SHAKE-128s | 1 | 7,856 字节 |
| SLH-DSA-SHA2-128f / SLH-DSA-SHAKE-128f | 1 | 17,088 字节 |
| SLH-DSA-SHA2-192s / SLH-DSA-SHAKE-192s | 3 | 16,224 字节 |
| SLH-DSA-SHA2-192f / SLH-DSA-SHAKE-192f | 3 | 35,664 字节 |
| SLH-DSA-SHA2-256s / SLH-DSA-SHAKE-256s | 5 | 29,792 字节 |
| SLH-DSA-SHA2-256f / SLH-DSA-SHAKE-256f | 5 | 49,856 字节 |

SHA2 和 SHAKE 变体仅在内部哈希函数实例化方面有所不同 (SHA-2 系列与 FIPS 202 中的 SHAKE)，而不是在安全级别或签名结构方面。

- **FN-DSA**(即将作为 [FIPS 206](https://csrc.nist.gov/presentations/2025/fips-206-fn-dsa-falcon)) 派生自 FALCON。全名：**NTRU 基于格的数字签名算法的快速傅立叶变换**。 哈希-Then-Sign Paradigm 中基于格的方案，签名生成接近从随机消息哈希派生的目标的格点，使用 **FFT** 和 **LDL 树**进行离散高斯采样。这使得 FN-DSA 的签名和公钥明显小于 ML-DSA，使其适合带宽受限的环境，例如具有严格大小限制的证书链或协议。
  
NIST 的 [“NIST Post-Quantum Cryptography 标准化进程第四轮状态报告”](https://nvlpubs.nist.gov/nistpubs/ir/2025/NIST.IR.8545.pdf)(2025 年 3 月) 总结了正在进行的第四轮。


### Post-Quantum Cryptography 联盟

[Post-Quantum Cryptography 联盟 (PQCA)](https://pqca.org/)，是 [linux 基金会](https://www.linuxfoundation.org/press/announcing-the-post-quantum-cryptography-alliance-pqca) 发起的一项开放协作计划，旨在推动 Post-Quantum Cryptography 的进步和采用。

该倡议下的 [开放量子安全 (OQS)](https://openquantumsafe.org/) 项目是一个开源项目，旨在支持向抗量子密码学的过渡。

### 加密货币论坛研究小组

互联网工程任务组内的 [加密货币论坛研究小组](https://datatracker.ietf.org/rg/cfrg/about/) 已经标准化了基于哈希的有状态签名方案 [“XMSS：扩展 Merkle 签名方案。”](https://datatracker.ietf.org/doc/rfc8391/)

## 生产用途

以下试点项目和研究计划正在探索 PQC 在生产中的使用：

- [Anchor Vault](https://chromewebstore.google.com/detail/omifklijimcjhfiojhodcnfihkljeali) 是一个 chrome 插件，允许使用 Lamport 的签名添加抗量子证明，以保护 ERC 令牌。
- Signal 已在密钥协商协议的生产中实施了 [“后量子扩展 Diffie-Hellman”](https://signal.org/docs/specifications/pqxdh/#introduction)。
- Chromium 开始支持 [“Hybrid Kyber KEM”](https://blog.chromium.org/2023/08/protecting-chrome-traffic-with-hybrid.html) 以保护传输中的数据。
- Apple 已实施 [PQ3](https://security.apple.com/blog/imessage-pq3/) 来保护 iMessage 免受量子攻击造成的密钥泄露。

## 资源

- 📝 Daniel J. Bernstein 等人，[“Post-Quantum Cryptography 简介”](https://pqcrypto.org/www.springer.com/cda/content/document/cda_downloaddocument/9783540887010-c1.pdf)
- 📝 维基百科，[“量子算法。”](https://en.wikipedia.org/wiki/Quantum_algorithm)
- 📝 P.W. Shor，[“量子计算算法：离散对数和因式分解。”](https://ieeexplore.ieee.org/document/365700)
- 📝 NIST，[“Post-Quantum Cryptography。”](https://csrc.nist.gov/projects/post-quantum-cryptography)
- 📝 ETHResearch，[“如何在量子紧急情况下通过 hard fork 来保存大多数用户的资金。”](https://ethresear.ch/t/how-to-hard-fork-to-save-most-users-funds-in-a-quantum-emergency/18901)
- 📝 ETHResearch，[“ETHResearch：后量子”](https://ethresear.ch/tag/post-quantum)
- 📝 Vitalik Buterin，[“STARKs，第一部分：使用多项式进行证明。”](https://vitalik.eth.limo/general/2017/11/09/starks_part_1.html)
- 📝 维基百科，[“兰波特的签名。”](https://en.wikipedia.org/wiki/Lamport_signature)
