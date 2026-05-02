# ECDSA简介

现代密码学如何重新定义我们所有数字交互的信任都不为过——从通过加密保护银行帐户登录到通过数字证书验证您喜爱的应用程序的真实性。

公钥密码学是支持这些交互的关键概念。它由两个密钥对组成：

**公钥**：广泛分发并由任何人使用来验证实体的身份。
**私钥**：保密且只有所有者知道，用于加密和签名消息。

**椭圆曲线加密 (ECC)** 是一种特定类型的公钥加密，它使用椭圆曲线的数学来创建更小、更高效的密钥。这在以太坊等资源受限的环境中尤其有用。在以太坊中，**椭圆曲线数字签名算法 (ECDSA)** 有助于验证提交的交易的合法性。

让我们考虑一个现实场景，以了解 ECDSA 的实际工作原理。

爱丽丝是一位勤奋的女商人，她被绑架并被囚禁在一个偏远的岛屿上。绑架者索要 100 万美元的巨额赎金才能释放她。由于沟通方式有限，他们为她提供了一张明信片，指示她的同事鲍勃转移资金。

爱丽丝考虑写下赎金金额并像签署支票一样在明信片上签名。然而，这种方法存在很大的风险：绑匪可以轻松伪造明信片，夸大金额，并欺骗鲍勃给他们寄更多的钱。

Alice 需要一种强大的方法来允许：

1. 鲍勃验证转账已获得她的授权，并且
2. 确保明信片的信息未被篡改。

本练习的目标是为 Alice 设计一种方法来创建只有她自己知道的**密钥🔑**。该密钥对于她证明自己的身份并确保鲍勃收到的消息的真实性至关重要。

一如既往，数学可以拯救你。通过巧妙地利用**椭圆曲线**，我们来探讨一下Alice如何生成**秘钥🔑**。

## 椭圆曲线

椭圆曲线是一条曲线**由方程式**描述：

$$
y^2 = x^3 + ax+b
$$

这样$4a^3 + 27b^2 \ne 0$就保证了曲线是非奇异的。
上面的方程就是长方程的 **Weierstrass Paradigm**：

$$
y^2 + a_1 xy + a_3 y = x^3 + a_2 x^2 + a_4 x + a_6
$$

示例：

<img src="images/elliptic-curves/examples.gif" width="500"/>

观察椭圆曲线关于 x 轴对称。

以太坊使用名为 [secp256k1](http://www.secg.org/sec2-v2.pdf) 的标准曲线，参数为 $a=0$ 和 $b=7$；这是曲线：
$$y^2=x^3+7$$

<img src="images/elliptic-curves/secp256k1.png" width="500"/>

## 组和字段

### 集团
在数学中，**GROUP**是一个集合$G$，包含至少两个元素，在通常称为**加法**的二元运算下封闭($+$)。当操作的结果也是集合的成员时，集合在操作下是封闭的。 

实数集 $\mathbb{R}$ 是一个常见的群示例，因为两个实数的算术加法是闭集的。

$$
 3 \in \mathbb{R},  5 \in \mathbb{R} \\
 3 + 5 = 8 \in \mathbb{R}
$$

## 领域
类似地，**FIELD**是一个集合$F$，包含至少两个元素，在通常称为**加法**($+$)和**乘法**($\times$)的两个二元运算下封闭。 

换句话说，A **FIELD** 在加法和乘法下都是一个**GROUP**。

椭圆曲线很有趣，因为曲线上的点形成一个组，即两个点“相加”的结果保留在曲线上。这种几何加法与算术加法不同，涉及通过选定点(**P** 和 **Q**)绘制一条线，并反映 x 轴上所得的曲线交点 (**R'**)，以得出它们的总和 (**R**)。

<br />
<img src="images/elliptic-curves/addition.gif" width="500"/>

点 (**P**) 也可以添加到自身 ($P+P$)，在这种情况下，直线将成为反映总和 (**2P**) 的 **P** 的切线。

<br />
<img src="images/elliptic-curves/scalar-multiplication.png" width="500"/>

重复点加法称为**标量乘法**：

$$
nP = \underbrace{P + P + \cdots + P}_{n\ \text{times}}
$$

## 离散对数问题

让我们利用标量乘法来生成**密钥🔑**。这个键由 $K$ 表示，表示基点 $G$ 与其自身相加的次数，产生结果公共点 $P$：

$$
P = K*G
$$

给定 $P$ 和 $G$，可以通过有效地反转乘法来导出密钥 $K$，类似于**对数问题**。

我们需要确保标量乘法不会泄露我们的**密钥🔑**。换句话说，标量乘法一方面应该是“容易的”，另一方面应该是“不可追踪的”。

时钟的类比有助于说明所需的单向性质。想象一项任务从中午 12 点开始到 3 点结束。仅知道最终时间 (3) 就无法在没有其他信息的情况下确定确切的持续时间。这是因为**模算术**引入了“环绕”效应。该任务可能需要 3 小时、15 小时，甚至 27 小时，所有这些都导致最终时间模 12 相同。

<br />
<img src="images/elliptic-curves/clock.gif" width="500"/>

对于**质数模数**，这尤其困难，被称为**离散对数问题**。

## 有限域上的椭圆曲线

到目前为止，我们隐式地假设有理场 ($\mathbb{R}$) 上的椭圆曲线。通过离散对数问题确保**密钥🔑**安全性需要在由**素数模数**定义的有限域上过渡到椭圆曲线。这本质上是通过使用特定素数执行模约简来将曲线上的点限制为有限集。

为了便于讨论，我们将考虑在具有素数模数 **997** 的 **任意有限域** 上定义的 **secp256k1** 曲线：

$$
y^2 = x^3 + 7 \pmod {997}
$$

<img src="images/elliptic-curves/finite-field.png" width="500"/>

虽然有限域中曲线的几何表示与连续曲线相比可能显得抽象，但其对称性保持不变。此外，标量乘法仍然是封闭的，尽管考虑到模数性质，“切线”现在“环绕”。

<br />
<img src="images/elliptic-curves/finite-scalar-multiplication.gif" width="500"/>

## 生成密钥对

Alice 最终可以在有限域上使用椭圆曲线生成密钥对。

让我们在 [Sage.](https://www.sagemath.org/) 中素数模 997 的有限域上定义椭圆曲线。

```python
sage: E = EllipticCurve(GF(997),[0,7])
Elliptic Curve defined by y^2 = x^3 + 7 over Finite Field of size 997
```

通过选择曲线上的任意点来定义生成点 $G$。

```python
sage: G = E.random_point()
(174 : 487 : 1)
```

椭圆曲线上的标量乘法定义了一个阶数为 $n$** 的循环**子群。这意味着重复添加子组 $n$ 次中的任何点都会得到无穷远点 ($O$)，该点充当单位元。

$$
nP  = O
$$

```python
sage: n = E.order()
1057
# Illustrating that n*G (or any point) equals O, represented by (0 : 1 : 0).
sage: n*G
(0 : 1 : 0)
```

密钥对包括：

1. **密钥🔑**($K$)：从子组 $n$ 的顺序中选择的随机整数。确保只有 Alice 可以生成有效的签名。

Alice 随机选择 **42** 作为**密钥🔑**。

```python
sage: K = 42
```

2. **公钥**($P$)：曲线上的一个点，**私钥🔑**($K$)与生成点($G$)标量相乘的结果。允许任何人验证 Alice 的签名。

```python
sage: P = K*G
(858 : 832 : 1)
```

我们已经建立了Alice的密钥对$=[P, K] = [(858, 832), 42]$。

## ECDSA 在行动

ECDSA 是数字签名算法 (DSA) 的变体。它使用加密哈希基于消息的“指纹”创建签名。

为了使 ECDSA 工作，Alice 和 Bob 必须建立一组通用的域参数。此示例的域参数为：

|参数|价值|
| ------------------------------------- | --------------- |
| 椭圆曲线方程。          | $y^2 = x^3 + 7$ |
|有限域的素数模。 | 997 | 997
|发电机点，$G$。             | (174, 487) |
|子群的阶，$n$。       | 1057 | 1057

重要的是，Bob 确信公钥 $P = (858, 832)$ 实际上属于 Alice。

### 签约

Alice 打算通过以下步骤签署消息 **“发送 100 万美元”**：

1. 计算加密哈希 **$m$**。

```python
sage: m = hash("Send $1 million")
-7930066429007744594
```

2. 对于每个签名，都会生成一个随机的**临时密钥对 [$eK$，$eP$]**，以减轻暴露她的**秘密密钥🔑**的[攻击](https://youtu.be/DUGGJpn2_zY?si=4FZ3ZlQZTG9-eah9&t=2117)。

```python
# Randomly selected ephemeral secret key.
sage: eK = 10
# Ephemeral public key.
sage: eP = eK*G
(215 : 295 : 1)
```

临时密钥对 $=[eK, eP] = [10, (215, 295)]$。

3. 计算签名组件 **$s$**：

$$ s = k^{−1} (e + rK ) \pmod n$$

其中 $r$ 是临时公钥 **(eP)** 的 x 坐标，即 **215**。请注意，签名使用 Alice 的**秘密密钥 🔑 ($K$)** 和临时密钥对 **[$eK$, $eP$]**。

```python
# x-coordinate of the ephemeral public key.
sage: r = int(eP[0])
215
# Signature component, s.
sage: s = mod(eK**-1 * (m + r*K), n)
160
```

元组 $(r,s) =  (215, 160)$ 是 **签名对**。

然后，Alice 将消息和签名写入明信片。

<img src="images/elliptic-curves/postcard.jpg" width="500"/>

### 验证

Bob 通过从签名对 **$(r,s)$**、消息和 Alice 的公钥 **$P$** 独立计算**完全相同的临时公钥**来验证签名：

1. 计算加密哈希 **$m$**。

```python
sage: m = hash("Send $1 million")
-7930066429007744594
```

2. 计算临时公钥 **$R$**，并将其与 **$r$** 进行比较：

$$R =  (es^{−1} \pmod n)*G + (rs^{−1} \pmod n)*P$$

```python
sage: R = int(mod(m*s^-1,n)) * G  + int(mod(r*s^-1,n)) * P
(215 : 295 : 1)
# Compare the x-coordinate of the ephemeral public key.
sage: R[0] == r
True # Signature is valid ✅
```

如果Alice的捕获者要修改消息，就会改变加密的哈希，从而由于与原始签名不匹配而导致验证失败。

```python
sage: m = hash("Send $5 million")
7183426991750327432 # Hash is different!
sage: R = int(mod(m*s^-1,n)) * G  + int(mod(r*s^-1,n)) * P
(892 : 284 : 1)
# Compare the x-coordinate of the ephemeral public key.
sage: R[0] == r
False # Signature is invalid ❌
```

对签名的验证可以向 Bob 保证消息的真实性，使他能够转移资金并营救 Alice。 椭圆曲线拯救了一切！

## 总结

就像 Alice 一样，[以太坊上的每个帐户都使用 ECDSA 来签名交易](https://web.archive.org/web/20240229045603/https://lsongnotes.wordpress.com/2018/01/14/signing-an-ethereum-transaction-the-hard-way/)。然而，以太坊中的 ECC 涉及额外的安全考虑。虽然核心原理保持不变，但我们使用安全的哈希函数，如 keccak256 和更大的素数字段，拥有 78 位数字：$2^{256}-2^{32}-977$。



本次讨论是对椭圆曲线密码学的初步处理。为了获得细致入微的理解，请考虑以下资源。

最后：**永远不要推出自己的加密货币！**使用受信任的库和协议来保护您的数据和交易。

> ℹ️注意  
> ECDSA 面临着量子计算机的潜在淘汰——了解[后量子密码学如何应对这一挑战。](/wiki/Cryptography/post-quantum-cryptography.md)

## 进一步阅读

**椭圆曲线密码学**

- 📝 高效密码学组标准(SECG)，[“SEC 1：椭圆曲线密码学。”](http://www.secg.org/sec1-v2.pdf)
- 📝 高效密码学组标准 (SECG)，[“SEC 2：推荐的椭圆曲线域参数。”](http://www.secg.org/sec2-v2.pdf)
- 📘 Alfred J. Menezes、Paul C. van Oorschot 和 Scott A. Vanstone，[应用密码学手册](https://cacr.uwaterloo.ca/hac/)
- 🎥 Fullstack Academy，[“通过 Diffie-Hellman 密钥交换了解 ECC。”](https://www.youtube.com/watch?v=gAtBM06xwaw)
- 📝 Andrea Corbellini，[“椭圆曲线密码学：温和的介绍。”](https://andrea.corbellini.name/2015/05/17/elliptic-curve-cryptography-a-gentle-introduction/)
- 📝 威廉·斯坦因，[“椭圆曲线。”](https://wstein.org/simuw06/ch6.pdf)
- 📝 可汗学院，[“模块化算术”。](https://www.khanacademy.org/computing/computer-science/cryptography/modarithmetic/a/what-is-modular-arithmetic)
- 🎥 可汗学院，[“离散对数问题。”](https://www.youtube.com/watch?v=SL7J8hPKEWY)

**椭圆曲线的数学**

- 📘 Joseph H. Silverman，[“椭圆曲线的算术。”](https://books.google.co.in/books?id=6y_SmPc9fh4C&redir_esc=y)
- 📝 Joseph H. Silverman，[“椭圆曲线理论简介”](https://www.math.brown.edu/johsilve/Presentations/WyomingEllipticCurve.pdf)
- 📘 Neal Koblitz，[“数论和密码学课程。”](https://link.springer.com/book/10.1007/978-1-4419-8592-7)
- 📝 Ben Lynn，[“斯坦福加密货币：椭圆曲线。”](https://crypto.stanford.edu/pbc/notes/elliptic/)
- 📝 Rareskills.io，[“椭圆曲线积分添加。”](https://www.rareskills.io/post/elliptic-curve-addition)
- 📝 约翰·D·库克，[“有限域。”](https://www.johndcook.com/blog/finite-fields/)

**有用的工具**

- 🎥 Tommy Occhipinti，[“圣人中的椭圆曲线。”](https://www.youtube.com/watch?v=-fRWR_QKzuI)
- 🎥 Desmos，[“Desmos 图形计算器简介。”](https://www.youtube.com/watch?v=RKbZ3RoA-x4)
- 🧮 Andrea Corbellini，[“交互式椭圆曲线加法和乘法。”](https://andrea.corbellini.name/ecc/interactive/reals-add.html)

## 制作人员

- 感谢 Michael Driscoll 在 [动画椭圆曲线.](https://github.com/syncsynchalt/animated-curves) 上所做的工作
