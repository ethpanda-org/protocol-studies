# Boneh-Lynn-Shacham (BLS) 签名

### TLDR;

- 权益证明（PoS）协议使用数字签名来识别其参与者并使其负责。
  - Beacon 链中的验证者（以太坊）使用 BLS 签名来参与共识、签署区块、发布证明等。
- BLS 签名可以进行聚合，使其在大规模验证时高效。
- 签名聚合使 Beacon 链能够扩展到数十万个验证者。
- 执行层中的正常以太坊交易签名仍然使用 ECDSA。然而，账户抽象钱包也可以使用 BLS。

BLS 是一种具有聚合特性的数字签名方案。给定一组签名（_signature_1_，...，_signature_n_），任何人都可以生成一个聚合签名。聚合也可以对私钥和公钥进行操作。此外，BLS 签名方案是确定性的、不可篡改的且高效的。它的简洁性和密码学特性使其在许多需要最小存储空间或带宽的用例中非常有用。本页将介绍 BLS 签名的基本概念和数学原理，并进一步探讨 BLS 在以太坊中的应用。

## BLS 是如何工作的？

BLS 签名的核心概念是通过椭圆曲线的配对进行双线性映射。一个关键组件是配对函数 $e$，定义在两个源自椭圆曲线的群之间：

$$e: G_1 \times G_2 \rightarrow G_T$$

该函数计算高效，必须满足双线性性质：

- 对于所有 $P, Q$ 属于 $G_1$ 和 $a$ 属于整数，双线性定义为：
  $$e(aP, Q) = e(P, Q)^a$$
  $$e(P, aQ) = e(P, Q)^a$$

- 此外，它必须对加法分配：
  $$e(P + Q, R) = e(P, R) \times e(Q, R)$$
  $$e(P, Q + R) = e(P, Q) \times e(P, R)$$

这些性质使得加密机制能够执行如签名聚合等关键功能，这对于区块链应用和密码学共识至关重要。

#### 为什么选择 BLS 而不是 Schnorr 或 ECDSA 作为数字签名？

传统的 ECDSA 签名（如在比特币或以太坊交易中常见）严重依赖于随机数生成的非对称性，并且需要单独验证每个涉及的公钥，这可能会增加计算负担。在专利到期后，Schnorr 签名成为了一个替代方案，虽然它允许某种形式的聚合，但仍然缺乏像 BLS 所带来的完全效率。

BLS 签名采用双线性配对，能够有效防止某些密码学攻击，并生成较短的签名。与 Schnorr 不同，BLS 不依赖于随机数生成来保障签名的安全性，使其天生更加防范与随机性相关的漏洞。

_请注意：虽然 BLS 签名本身不需要为每次签名操作生成随机的非对称值（使其成为确定性签名），但 BLS 私钥生成的初步步骤仍然依赖于安全的随机数生成。与 ECDSA 不同，后者在每个签名中至关重要的非对称值需要保证其随机性以避免漏洞，BLS 避免了这种类型的非对称值生成。但私钥生成中的随机性仍然至关重要。此初始随机性确保私钥的安全和不可预测性，这是密码系统整体安全性的基础。_

#### BLS 签名生成与验证的示例：

<figure class="diagram" style="width:80%">

![BLS 签名生成与验证的示意图](../../images/elliptic-curves/bls-alice.png)

<figcaption>

_图示帮助理解 BLS 签名的工作原理_

</figcaption>
</figure>

假设 Alice 创建一个 BLS 签名。她从她的私钥 $a$ 开始，使用椭圆曲线上的生成点 $G$ 计算她的公钥 $P$：

$$P = aG$$

她对消息进行哈希处理并将此哈希映射到曲线上的一个点 $H(M)$。她的签名 $S$ 就是：

$$S = a \times H(M)$$

然后使用配对函数验证签名：

$$e(G, S) = e(P, H(M))$$

这可以证明为：
$$e(G,S)=e(G,a×H(m))=e(a×G,H(m))=e(P,H(M))$$
其中 $G$ 是椭圆曲线上的生成点。

该等式证明了签名确实是由持有私钥的公钥对应者创建的。

#### 示例

对于使用 BLS12-381 曲线的 BLS 签名，示例值如下所示：



```
Message: "Hello"
Secret Key: 26daf744780a51072aa8de191259bf7ff080b8457512cfd0eedfb4f8c71b131d
Public Key: bfdab807246849b76b7bdf5229619b9ccb33713633644a48b7ab3a7e67af7c1ae9d597a1c0fac6f61e63c1278b26c2f527be3d58bce95451b36f0c692ee90e1f
Signature: dee15784b458419b4b8bbdbb13838da13c27dccab6ef50f0dcb4ff7352048c0b
```

对于使用 secp256k1 曲线的 ECDSA，存在 $R$ 和 $S$ 值，生成的签名更长，示例值如下所示：


```
Message: "Hello"
Private Key: 2aabe11b7f965e8b16f525127efa01833f12ccd84daf9748373b66838520cdb7
Public Key (EC Point):
    x: 39516301f4c81c21fbbc97ada61dee8d764c155449967fa6b02655a4664d1562
    y: d9aa170e4ec9da8239bd0c151bf3e23c285ebe5187cee544a3ab0f9650af1aa6
Signature:
    R: 905eceb65a8de60f092ffb6002c829454e8e16f3d83fa7dcd52f8eb21e55332b
    S: 8f22e3d95beb05517a1590b1c5af4b2eaabf8e231a799300fffa08208d8f4625
```

### BLS 签名的聚合特性

BLS 的一大优势在于能够将多个签名聚合成一个紧凑的签名。这在涉及多个交易或签名者的场景中特别有用，大大减少了区块链存储空间和验证所需的计算量。例如，假设有 100 笔交易，每笔交易的签名为 $S_i$，其对应的公钥为 $P_i$（以及消息 $M_i$），BLS 允许将它们合并为一个单一的签名，而不是存储 100 个独立的签名：

$$S = S_1 + S_2 + \ldots + S_{100}$$

然后，该聚合签名可以通过以下方式验证（使用乘法运算）：
$$e(G,S)=e(P_1,H(M_1))⋅e(P_2,H(M_2))⋅…⋅e(P_{100},H(M_{100}))$$

验证该聚合签名时，需要相应地聚合公钥和消息哈希，同时保持所有独立签名的完整性和不可抵赖性。

## BLS 签名在以太坊中的应用

在区块链领域，数字签名通常利用椭圆曲线群。以太坊主要采用 [ECDSA](/wiki/Cryptography/ecdsa.md) 签名，并使用 [secp256k1](https://www.secg.org/sec2-v2.pdf) 曲线，而信标链协议则采用基于 [BLS12-381](https://hackmd.io/@benjaminion/bls12-381) 曲线的 BLS 签名。与 ECDSA 不同，BLS 签名利用了一种特殊的椭圆曲线特性，即 "[配对](https://medium.com/@VitalikButerin/exploring-elliptic-curve-pairings-c73c1864e627)"，这使得多个签名可以聚合，从而提高共识协议的效率。虽然 ECDSA 签名的处理速度[更快](https://datatracker.ietf.org/doc/html/draft-irtf-cfrg-bls-signature-04#section-1.1)，但 BLS 签名的聚合能力在区块链可扩展性和共识效率方面提供了显著的优势。

BLS 签名的创建和验证过程相对简单，涉及一系列可以通过图表、描述和数学原理解释的步骤，尽管理解这些数学细节并不是实际应用的必要条件。

## BLS 签名的四个组成部分

在 BLS 数字签名过程中，有 **4** 个核心数据组件：

- **私钥（Secret Key）**：协议中的每个参与者（特别是验证者）都拥有一个私钥，用于签署消息并确保自身操作的机密性。
- **公钥（Public Key）**：通过密码学方法直接从私钥派生，尽管与私钥相关联，但无法通过计算逆推出私钥。它作为验证者的公开身份，在网络中可供所有参与者访问。
- **消息（Message）**：在以太坊中，消息是字节字符串，具有特定结构和用途。最初可以将它们理解为区块链协议中处理的基本数据单元。
- **签名（Signature）**：签名是通过加密过程，将消息与私钥结合得到的结果。该签名唯一标识了持有私钥的实体创建了该消息，并且通过公钥验证签名，可以确认消息确实来自某个特定的验证者，并且未在签名后被篡改。

在数学上，我们使用 BLS12-381 椭圆曲线的两个子群：$G_1$ 定义在基域 $F_q$ 上，而 $G_2$ 定义在扩展域 $F_{q^2}$ 上。这两个子群的阶为 $r$，是一个 77 位的素数。$G_1$ 和 $G_2$ 的（任意选择的）生成元分别为 $g_1$ 和 $g_2$。

1. **私钥（Secret Key）$sk$**：是一个介于 $1$ 和 $r$ 之间的数字（严格来说，范围包括 $1$ 但不包括 $r$，但过小的 $sk$ 会导致安全性极低）。
2. **公钥（Public Key）$pk$**：计算方式为 $[sk]g_1$，其中方括号表示椭圆曲线群点的标量乘法。公钥因此是 $G_1$ 群的成员。
3. **消息（Message）$m$**：是一个字节序列。在签名过程中，该消息会被映射到 $G_2$ 群的某个点 $H(m)$。
4. **签名（Signature）$\sigma$**：也是 $G_2$ 群的成员，即 $[sk]H(m)$。

<figure class="diagram" style="margin-left:5%; width:80%">

![BLS 签名组件示意图](../../images/elliptic-curves/bls-key.svg)

<figcaption>

_密钥的关键表示方式。本图展示了以下图示中的不同组件如何表示。相同对象的不同变体使用不同的阴影填充。数学上，私钥是一个标量；公钥属于 $G_1$ 群；消息根被映射到 $G_2$ 群；签名也是 $G_2$ 群的成员。_

</figcaption>
</figure>

---

### 密钥对（Key Pairs）

密钥对由私钥和其对应的公钥组成。两者共同确保每个验证者与其操作之间的不可否认性。

每个验证者至少配备一个**主签名密钥**（Signing Key），用于执行常规操作，如创建区块、提交证明等。  
根据其提款凭证（Withdrawal Credentials），验证者可能还拥有一个**提款密钥**（Withdrawal Key），该密钥通常存储离线以增强安全性。

私钥应在范围 $[1,r)$ 内随机生成，并符合 [EIP-2333](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2333.md) 标准，该标准推荐使用 IRTF BLS 签名草案标准中的 [`KeyGen`](https://datatracker.ietf.org/doc/html/draft-irtf-cfrg-bls-signature-04#section-2.3) 方法。尽管遵守此方法并非强制性要求，但通常不建议偏离该标准。  
大多数质押者使用 [Ethereum Foundation 提供的 `eth2.0-deposit-cli`](https://github.com/ethereum/eth2.0-deposit-cli) 生成密钥。  
为了安全性，密钥对通常存储在受密码保护的 [EIP-2335](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2335.md) 密钥存储文件中。

- **私钥（$sk$）** 是一个 32 字节的无符号整数。
- **公钥（$pk$）** 表示为 $G_1$ 曲线上的一个点，在协议中以 **压缩格式** 作为 48 字节字符串存储。

<a id="img_bls_setup"></a>

<figure class="diagram" style="margin-left:20%; width:50%">

![公钥生成过程](../../images/elliptic-curves/bls-setup.svg)

<figcaption>

_验证者随机生成其私钥，然后派生出公钥。_

</figcaption>
</figure>

### 密钥对（Key pairs）

密钥对由私钥和其对应的公钥组成。两者共同确保每个验证者与其操作之间的不可否认性。

每个验证者至少配备一个主签名密钥，用于执行常规操作，如创建区块、提交证明等。根据其提款凭证（Withdrawal Credentials），验证者还可能拥有一个二级提款密钥（Withdrawal Key），该密钥通常存储离线以增强安全性。

私钥应在范围 $[1,r)$ 内随机生成，并符合 [EIP-2333](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2333.md) 标准，标准推荐使用 IRTF BLS 签名草案中的 [`KeyGen`](https://datatracker.ietf.org/doc/html/draft-irtf-cfrg-bls-signature-04#section-2.3) 方法。虽然遵守此方法并非强制要求，但偏离此方法通常不被推荐。大多数质押者使用 [Ethereum Foundation 提供的 `eth2.0-deposit-cli`](https://github.com/ethereum/eth2.0-deposit-cli) 工具生成密钥。为了安全起见，密钥对通常存储在受密码保护的 [EIP-2335](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2335.md) 密钥存储文件中。

- **私钥（$sk$）** 是一个 32 字节的无符号整数。
- **公钥（$pk$）** 是 $G_1$ 曲线上的一个点，并在协议中以 **压缩格式** 存储为 48 字节字符串。

<a id="img_bls_setup"></a>

<figure class="diagram" style="margin-left:20%; width:50%">

![公钥生成过程](../../images/elliptic-curves/bls-setup.svg)

<figcaption>

_验证者随机生成其私钥，然后派生出公钥。_

</figcaption>
</figure>

---

### 在 Beacon 链中的签名

在以太坊的信标链中，唯一需要签署的消息是对象的[哈希树根](https://eth2book.info/capella/part2/building_blocks/merkleization/)。这些根，称为“签名根”，是 32 字节的字符串。`[`compute_signing_root()`](/wiki/CL/functions#compute_signing_root) 函数将哈希树根与特定的“域”结合，增强了安全性。

<!-- 在 CL 中定义和链接上下文（域分离和分叉）-->

签名根会被映射到 $G_2$ 群中的一个椭圆曲线点。这个映射 $H(m)$，其中 $m$ 是签名根，将哈希转换为适合加密操作的格式。这个复杂的过程已在 [Hash-to-Curve 草案标准](https://datatracker.ietf.org/doc/draft-irtf-cfrg-hash-to-curve/)中进行了阐述，但通常开发者依赖加密库来高效处理这一步骤。

#### 签名创建：

实际的签名过程涉及将 $G_2$ 点 $H(m)$ 乘以签名者的私钥 $sk$：

$$
\sigma = [sk]H(m)
$$

由此生成的签名 $\sigma$ 也属于 $G_2$ 群，通常会被压缩为 96 字节的字符串以供实际使用。

<figure class="diagram" style="margin-left:10%; width:65%">

![签署消息示意图](../../images/elliptic-curves/bls-signing.svg)

<figcaption>

_验证者使用其私钥签署消息，从而生成唯一的数字签名_

</figcaption>

</figure>


### 验证签名

要验证一个签名，必须使用相应验证者的公钥。该公钥可以在信标状态中找到，通过验证者的索引即可访问，确保了公钥的获取既简单又可靠。

验证过程简化：将消息、公钥和签名输入验证过程。如果签名是有效的——即与公钥和消息匹配——则接受该签名；否则，由于潜在的篡改、不正确的密钥使用或消息篡改，签名将被拒绝。

形式化地，验证过程使用了椭圆曲线配对。对于 BLS12-381 曲线，配对将 $G_1$ 中的一个点 $P$ 和 $G_2$ 中的一个点 $Q$ 映射到 $G_T$ 群中的一个点：

$$
e: G_1 \times G_2 \rightarrow G_T
$$

配对表示为 $e(P, Q)$，它对于验证签名与公钥之间的对应关系至关重要：

$$
e(g_1, \sigma) = e(pk, H(m))
$$

这检查通过私钥 $sk$ 签署的消息是否与观察到的签名 $\sigma$ 匹配，使用的是 [配对](#how-bls-works) 的基本属性。

<figure class="diagram" style="margin-left:10%; width:80%">

![签名验证示意图](../../images/elliptic-curves/bls-verifying.svg)

<figcaption>

_验证过程使用验证者的公钥和原始消息来确认签名的真实性。_

</figcaption>

</figure>

**验证结果**：如果签名与公钥和消息匹配，过程返回 `True`，确认签名有效。如果不匹配，则返回 `False`，表示签名的完整性或来源存在问题。

## 资源与参考文献

- [BLS 和密钥配对](https://asecuritysite.com/encryption/js_bls)
- [BLS 签名和密钥配对概念](https://www.youtube.com/watch?v=cVgJBdM5E2M)
- [BLS 聚合 by Vitalik Buterin 和 Justin Drake](https://www.youtube.com/watch?v=DpV0Hh9YajU)
- [Justin Drake 的务实签名聚合](https://ethresear.ch/t/pragmatic-signature-aggregation-with-bls/2105?u=benjaminion)
- [Eth2 手册中的构建模块](https://eth2book.info/capella/part2/building_blocks/signatures/)
- [正式的 IETF 草案标准](https://www.ietf.org/archive/id/draft-irtf-cfrg-bls-signature-05.html)
- [配对友好的曲线](https://datatracker.ietf.org/doc/html/draft-irtf-cfrg-pairing-friendly-curves-10)
- [哈希到椭圆曲线](https://datatracker.ietf.org/doc/html/draft-irtf-cfrg-hash-to-curve-09)
- [ERC2333](https://github.com/ethereum/ercs/blob/master/ERCS/erc-2333.md) 提供了一种基于熵种子派生 BLS12-381 密钥树层次结构的方法。
- [ERC2334](https://github.com/ethereum/ercs/blob/master/ERCS/erc-2334.md) 定义了一个确定性的账户层次结构，用于指定密钥的用途。
- [ERC2335](https://github.com/ethereum/ercs/blob/master/ERCS/erc-2335.md) 规范了 BLS12-381 密钥的存储和交换标准密钥库格式。
