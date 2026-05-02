# Boneh-Lynn-Shacham (BLS) 签名

### TLDR；

- 权益证明协议使用数字签名来识别参与者并追究他们的责任。
  - 验证者中的信标链(以太坊)使用BLS 签名参与共识，签署区块，发布证明等。
- BLS 签名可以聚合在一起，使它们能够高效地进行大规模验证。
- 签名聚合允许信标链扩展到数十万个验证者。
- 执行层上的正常以太坊交易签名仍使用 ECDSA。然而，帐户抽象钱包也可以使用 BLS。

BLS 是具有聚合属性的数字签名方案。给定一组签名 (_signature_1_, ..., _signature_n_) 任何人都可以生成聚合的签名。聚合也可以在密钥和公钥上完成。此外，BLS 签名方案是确定性的、不可延展的且高效的。其简单性和加密属性使其可用于各种用例，特别是在需要最小存储空间或带宽时。本页将介绍 BLS 签名背后的一般思想和数学，并在以太坊的上下文中进一步介绍 BLS。

## BLS 如何工作？

BLS 签名的核心是通过椭圆曲线上的配对进行双线性映射的概念。一个关键组件是配对函数 $e$，它在源自椭圆曲线的两个组之间定义：

$$e: G_1 \times G_2 \rightarrow G_T$$

该函数是可有效计算的并且必须满足双线性属性：

- 对于整数 $G_1$ 中的所有 $P,Q$ 和 $a$，双线性定义为：
  $$e(aP, Q) = e(P, Q)^a$$
  $$e(P, aQ) = e(P, Q)^a$$

- 此外，它必须通过加法进行分配：
  $$e(P + Q, R) = e(P, R) \times e(Q, R)$$
  $$e(P, Q + R) = e(P, Q) \times e(P, R)$$

这些属性启用了签名聚合等功能所需的加密机制，这是区块链应用程序和加密共识中的关键功能。

#### 为什么 BLS 优于 Schnorr 和 ECDSA 数字签名？

传统的 ECDSA 签名(如比特币中常用的以太坊交易)严重依赖于随机数生成的随机性，并且需要单独验证所有涉及的公钥，这可能需要大量计算。专利到期后，Schnorr 签名成为一种替代方案，它允许进行一些聚合，但仍然缺乏 BLS 所获得的全部效率。

BLS 签名采用双线性配对，针对某些加密攻击提供强大的保护，并生成更短的签名。与 Schnorr 不同，BLS 不依赖随机数生成来保护签名，这使其本质上更安全，可以抵御与随机性相关的漏洞。

_请注意：虽然 BLS 签名本身不需要为每个签名操作提供随机数(使其具有确定性)，但在 BLS 中生成私钥的初始步骤仍然依赖于安全随机数生成。与 ECDSA 不同，每个签名中的随机数对于维护安全性至关重要(并且该随机数的随机性对于防止漏洞至关重要)，BLS 避免了在签名过程中对此类随机数的需要。然而，生成私钥的随机性仍然至关重要。这种初始随机性确保私钥是安全且不可预测的，这对于密码系统的整体安全性至关重要。_

#### BLS 签名生成和验证示例：

<figure class="diagram" style="width:80%">

![Diagram showing key pair generation and verification for BLS](../../images/elliptic-curves/bls-alice.png)

<figcaption>

_视觉辅助了解 BLS 签名的工作原理_

</figcaption>
</figure>

考虑 Alice 创建一个 BLS 签名。她从她的私钥 $a$ 开始，并使用椭圆曲线上的生成点 $G$ 计算她的公钥 $P$：

$$P = aG$$

她哈希她的消息并将此哈希映射到曲线上的点 $H(M)$。那么她的签名 $S$ 就是：

$$S = a \times H(M)$$

使用配对函数验证签名：

$$e(G, S) = e(P, H(M))$$

这可以证明为：
$$e(G,S)=e(G,a×H(m))=e(a×G,H(m))=e(P,H(M))$$
其中 $G$ 是椭圆曲线上的生成点。

这个等式证明签名确实是由$P$对应的私钥的持有者创建的。

#### 示例

对于 BLS 签名使用像 BLS12-381 这样的曲线示例值将如下所示：

```
Message: "Hello"
Secret Key: 26daf744780a51072aa8de191259bf7ff080b8457512cfd0eedfb4f8c71b131d
Public Key: bfdab807246849b76b7bdf5229619b9ccb33713633644a48b7ab3a7e67af7c1ae9d597a1c0fac6f61e63c1278b26c2f527be3d58bce95451b36f0c692ee90e1f
Signature: dee15784b458419b4b8bbdbb13838da13c27dccab6ef50f0dcb4ff7352048c0b
```

对于使用像 secp256k1 这样的曲线的 ECDSA，有一个 $R$ 和一个 $S$ 值，这会产生一个更长的签名，其示例值如下所示：

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

### BLS 中的聚合：

BLS 的一个主要优点是能够将多个签名聚合成一个紧凑的签名。这在涉及多个交易或签名者的场景中特别有用，大大减少了验证所需的区块链空间和计算能力。例如，如果有 100 个交易，其中每个签名由 $S_i$ 表示，并且每个都与 $P_i$ 的公钥(和消息 $M_i$)相关联，而不是存储 100 个单独的签名， BLS 允许将它们合并为一个：

$$S = S_1 + S_2 + \ldots + S_{100}$$

然后可以通过(使用乘法运算)进行验证：
$$e(G,S)=e(P_1,H(M_1))⋅e(P_2,H(M_2))⋅…⋅e(P_{100},H(M_{100}))$$

对该聚合签名的验证将涉及公钥和消息哈希的相应聚合，从而维护所有单独签名的完整性和不可否认性。

## BLS 签名在 以太坊

对于区块链，数字签名通常利用椭圆曲线组。 以太坊主要采用[ECDSA](/wiki/Cryptography/ecdsa.md)签名使用[secp256k1](https://www.secg.org/sec2-v2.pdf)曲线，而信标链协议采用BLS 签名基于 [BLS12-381](https://hackmd.io/@benjaminion/bls12-381) 曲线。与 ECDSA 不同，BLS 签名利用了某些椭圆曲线的独特功能，称为“[配对](https://medium.com/@VitalikButerin/exploring-elliptic-curve-pairings-c73c1864e627)”。这允许聚合多个签名，提高共识协议的效率。虽然 ECDSA 签名的处理速度[快得多](https://datatracker.ietf.org/doc/html/draft-irtf-cfrg-bls-signature-04#section-1.1)，但 BLS 签名的聚合能力为区块链可扩展性和共识效率提供了显着的优势。

创建和验证 BLS 签名的过程很简单，涉及一系列可以通过图表、描述和数学原理进行解释的步骤，尽管理解数学细节对于实际应用来说并不是必需的。

BLS 数字签名进程中有 **4** 个组成数据块。

- **秘密密钥：** 协议中的每个参与者，特别是验证者，都拥有一个秘密密钥，也称为私钥。该密钥对于签署消息和维护验证者在网络内的操作的机密性至关重要。
- **公钥：** 使用加密方法直接从秘密密钥导出，虽然公钥与秘密密钥相关联，但如果不进行大量计算，就无法对其进行逆向工程。它作为协议中验证者的公共身份，可供所有参与者访问。
- **消息：** 在以太坊中，消息由字节字符串组成，其结构和用途将在上下文中进一步详细探讨。首先，将这些消息理解为区块链协议中处理的基本数据单元。
- **签名：** 签名是消息与密钥组合的加密过程的结果。该签名唯一标识消息是由密钥持有者编写的。通过使用相应的公钥验证签名，可以确认该消息源自特定的验证者并且在签名后未被更改。

在数学术语中，我们使用 BLS12-381 椭圆曲线的 2 个子组：在基字段 $F_q$ 上定义的 $G_1$，以及在字段扩展 $F_{q^2}$ 上定义的 $G_2$。两个子群的阶均为 $r$，一个 77 位素数。 $G_1$ 的(任意选择的)生成器是 $g_1$，$G_2$ 的生成器是 $g_2$。

1. 秘密密钥 $sk$ 是 $1$ 和 $r$ 之间的一个数字(从技术上讲，该范围包括 $1$，但不包括 $r$。但是，$sk$ 的值非常小将是非常不安全的)。
2. 公钥 $pk$ 是 $[sk]g_1$，其中方括号表示椭圆曲线群点的标量乘法。因此，公钥是 $G_1$ 组的成员。
3. 消息 $m$ 是一个字节序列。在签名过程中，这将被映射到作为 $G_2$ 组成员的某个点 $H(m)$。
4. 签名、$\sigma$ 也是 $G_2$ 组的成员，即 $[sk]H(m)$。

<figure class="diagram" style="margin-left:5%; width:80%">

![Diagram showing how we will depict the various components in the diagrams below.](../../images/elliptic-curves/bls-key.svg)

<figcaption>

_钥匙中的钥匙。这就是我们在下图中描述各个组件的方式。同一物体的变体的孵化方式不同。密钥在数学上是一个标量；公钥是_$G_1$_group成员；消息根映射到_$G_2$_group成员；和签名是 $G_2$ 组成员._

</figcaption>
</figure>

### 密钥对

密钥对是一个秘密密钥及其公钥。这些无可辩驳地将每个验证者与其行为联系在一起。

每个验证者至少配备 1 个主要“签名密钥”，用于日常操作，例如创建区块、制作证明等。根据其提款凭证，验证者还可能有一个辅助“提款密钥”，该密钥离线存储以增加安全性。

密钥应在 $[1,r)$ 范围内随机生成，遵循 [EIP-2333](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2333.md) 标准，该标准建议使用草案 IRTF 中的 [`KeyGen`](https://datatracker.ietf.org/doc/html/draft-irtf-cfrg-bls-signature-04#section-2.3) 方法BLS 签名标准。尽管不强制遵守此方法，但通常不鼓励偏离此方法。大多数质押者使用以太坊基金会中的 [`eth2.0-deposit-cli`](https://github.com/ethereum/eth2.0-deposit-cli) 工具生成密钥。为了安全起见，密钥对通常存储在受密码保护的 [EIP-2335](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-2335.md) 密钥库文件中。

密钥 $sk$ 是一个 32 字节无符号整数。公钥 $pk$ 表示为 $G_1$ 曲线上的一个点，并以压缩格式序列化为协议内的 48 字节字符串。

<a id="img_bls_setup"></a>

<figure class="diagram" style="margin-left:20%; width:50%">

![Diagram of the generation of the public key.](../../images/elliptic-curves/bls-setup.svg)

<figcaption>

_A 验证者随机生成其密钥。然后从中派生出它的公钥。_

</figcaption>
</figure>

### 登录信标链

在以太坊的 信标链中，唯一获得签名的消息是对象的 [哈希树根](https://eth2book.info/capella/part2/building_blocks/merkleization/)。这些根称为“签名根”，是 32 字节的字符串。 [`compute_signing_root()`](/wiki/CL/functions#compute_signing_root) 函数将哈希树根与特定“域”集成，增强了安全性。

<!-- define and link context (domain separation and forks) in CL-->

签名根映射到 $G_2$ 组内的椭圆曲线点。此映射 $H(m)$(其中 $m$ 是签名根)将哈希转换为适合加密操作的格式。 [哈希-to-Curve 标准草案](https://datatracker.ietf.org/doc/draft-irtf-cfrg-hash-to-curve/) 中概述了这个复杂的过程，但通常开发人员依靠加密库来有效管理此步骤。

#### 签名创建：

实际签名涉及将 $G_2$ 点 $H(m)$ 乘以签名者的密钥 $sk$：

$$
\sigma = [sk]H(m)
$$

由此生成的签名 $\sigma$ 也是 $G_2$ 组的一部分，并且通常被压缩为 96 字节字符串以供实际使用。

<figure class="diagram" style="margin-left:10%; width:65%">

![Diagram of signing a message.](../../images/elliptic-curves/bls-signing.svg)

<figcaption>

_验证者使用他们的密钥对消息进行签名，生成唯一的数字签名_

</figcaption>

</figure>

### 正在验证签名

要验证签名，需要相应验证者的公钥。该密钥在信标状态中很容易获得，可以通过验证者的索引访问，从而确保密钥检索直接且可靠。

简化验证：将消息、公钥和签名输入到验证过程中。如果签名是真实的(与公钥和消息匹配)，则它被接受；否则，它会因潜在的损坏、密钥使用不正确或消息篡改而被拒绝。

正式而言，此验证使用椭圆曲线配对。对于 BLS12-381 曲线，配对采用 $G_1$ 中的点 $P$ 和 $G_2$ 中的点 $Q$，从而得到 $G_T$ 组中的一个点：

$$
e: G_1 \times G_2 \rightarrow G_T
$$

配对表示为 $e(P, Q)$，对于验证签名和公钥之间的对应关系至关重要：

$$
e(g_1, \sigma) = e(pk, H(m))
$$

这使用 [pairings](/wiki/Cryptography/bls#how-bls-works) 的基本属性检查使用密钥 $sk$ 签名的消息是否与观察到的签名 $\sigma$ 匹配。

<figure class="diagram" style="margin-left:10%; width:80%">

![Diagram of verifying a signature.](../../images/elliptic-curves/bls-verifying.svg)

<figcaption>

_验证使用验证者的公钥和原始消息来确认签名的真实性。_

</figcaption>

</figure>

**验证输出**：如果签名与公钥和消息一致，则该过程返回 `True`，确认其有效性。如果不是，则返回 `False`，表明签名的完整性或来源存在问题。

## 资源和参考资料

- [BLS 和密钥配对](https://asecuritysite.com/encryption/js_bls)
- [BLS 签名和密钥配对概念](https://www.youtube.com/watch?v=cVgJBdM5E2M)
- [BLS 由 Vitalik Buterin 和 Justin Drake 聚合](https://www.youtube.com/watch?v=DpV0Hh9YajU)
- [实用签名聚合，作者：Justin Drake](https://ethresear.ch/t/pragmatic-signature-aggregation-with-bls/2105?u=benjaminion)
- [从 Eth2 手册构建区块](https://eth2book.info/capella/part2/building_blocks/signatures/)
- [正式IETF标准草案](https://www.ietf.org/archive/id/draft-irtf-cfrg-bls-signature-05.html)
- [配对友好曲线](https://datatracker.ietf.org/doc/html/draft-irtf-cfrg-pairing-friendly-curves-10)
- [哈希为椭圆曲线](https://datatracker.ietf.org/doc/html/draft-irtf-cfrg-hash-to-curve-09)
- [ERC2333](https://github.com/ethereum/ercs/blob/master/ERCS/erc-2333.md) 提供了一种基于熵种子导出 BLS12-381 密钥的树层次结构的方法。
- [ERC2334](https://github.com/ethereum/ercs/blob/master/ERCS/erc-2334.md) 定义了一个确定性帐户层次结构，用于指定密钥的用途。
- [ERC2335](https://github.com/ethereum/ercs/blob/master/ERCS/erc-2335.md) 指定用于存储和交换 BLS12-381 密钥的标准密钥库格式。
