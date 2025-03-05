# ECDSA 简介

现代密码学重新定义了我们所有数字交互的信任，这一点怎么强调都不为过——从使用加密技术保护银行账户登录安全，到通过数字证书验证你喜爱的应用程序的真实性。

公钥密码学是实现这些交互的核心概念。它由一对密钥组成：

**公钥**：广泛分发，任何人都可以用其来验证某个实体的身份。
**私钥**：保密，仅为所有者所知，用于加密和签署消息。

**椭圆曲线密码学 (ECC)** 是公钥密码学的一种特定类型，它使用椭圆曲线数学来创建更小、更高效私钥。这在以太坊等资源受限的环境中特别有用。在以太坊中，**椭圆曲线数字签名算法 (ECDSA)** 被用于验证提交交易的合法性。

让我们通过一个现实场景来理解 ECDSA 的工作方式。

Alice 是一位精明的女商人，她被绑架并囚禁在一个偏远的岛屿上。绑匪要求支付 100 万美元赎金才能释放她。由于沟通方式有限，他们只给她提供了一张明信片，让她指示助手 Bob 转账。

Alice 考虑直接在明信片上写下赎金金额并签名，就像填写支票一样。但这种方式存在极大风险：绑匪可以轻易伪造明信片，篡改金额，欺骗 Bob 支付更多的钱。

Alice 需要一种可靠的方法，以确保：

1. Bob 能够验证转账确实是由她授权的，并且
2. 确保明信片上的消息没有被篡改。

本练习的目标是创建一种方法，让 Alice 创建一个只有她自己知道的 **私钥 🔑**。此私钥对于帮助她向 Bob 证明自己的身份并确保消息的真实性至关重要。

数学，如往常一样，提供了解决方案。通过巧妙运用 **椭圆曲线**，我们来探索 Alice 如何生成 **私钥 🔑**。

## 椭圆曲线

椭圆曲线是 **由以下方程描述的** 曲线：

$$
y^2 = x^3 + ax+b
$$

其中需要满足 $4a^3 + 27b^2 \ne 0$，以确保曲线是非奇异性的。
上面的方程是以下长方程的 **Weierstrass 规范形式**：

$$
y^2 + a_1 xy + a_3 y = x^3 + a_2 x^2 + a_4 x + a_6
$$

示例:

<img src="images/elliptic-curves/examples.gif" width="500"/>

观察椭圆曲线关于 x 轴对称。

以太坊使用被称为 [secp256k1](http://www.secg.org/sec2-v2.pdf) 的标准曲线，其参数为 $a=0$，$b=7$；即曲线：
$$y^2=x^3+7$$

<img src="images/elliptic-curves/secp256k1.png" width="500"/>

## 群和域

### 群
在数学中，**群** 是一个集合 $G$，包含至少两个元素，并且在通常记作 **加法**（$+$）的二元运算下封闭。当一个集合的运算结果仍然属于该集合时，意味着该集合在某种运算下封闭。

实数集合 $\mathbb{R}$ 是一个常见的群示例，因为两个实数的算术加法是封闭的。

$$
 3 \in \mathbb{R},  5 \in \mathbb{R} \\
 3 + 5 = 8 \in \mathbb{R}
$$

## 域
与 **群** 类似，**域** 是一个集合 $F$，包含至少两个元素，并且在通常记作 **加法**（$+$）和 **乘法**($\times$) 的二元运算下封闭。

换句话说，**域** 在加法和乘法下都是 **群**。

椭圆曲线之所以有趣，是因为曲线上的点构成一个群，即两个点 "相加" 的结果仍然在曲线上。这种几何加法不同于算术加法，它涉及画一条穿过选定点（**P** 和 **Q**）的直线，并将该直线与曲线的交点（**R'**）沿 x 轴反射，得到它们的和（**R**）。

<br />
<img src="images/elliptic-curves/addition.gif" width="500"/>

一个点（**P**）也可以与自身相加（$P+P$），此时直线变为 **P** 点的切线，并沿 x 轴反射得到其和（**2P**）。

<br />
<img src="images/elliptic-curves/scalar-multiplication.png" width="500"/>

重复的点加法被称为 **标量乘法**：

$$
nP = \underbrace{P + P + \cdots + P}_{n\ \text{times}}
$$

## 离散对数问题

让我们利用标量乘法来生成 **私钥 🔑**。此私钥记作 $K$，表示一个基点 $G$ 与自身相加的次数，最终得到一个公钥点 $P$：

$$
P = K*G
$$

已知 $P$ 和 $G$，可以通过有效地逆向乘法推导出私钥 $K$，类似于 **对数问题**。

我们需要确保标量乘法不会泄露我们的 **私钥 🔑**。换句话说，标量乘法应该在一个方向上 "容易" 计算，而在反方向上 "不可追踪"。

一个时钟的类比可以很好地说明这种所需的单向性质。想象一个任务从中午 12 点开始，结束时是 3 点。如果只知道最终时间（3 点），但没有其他信息，就无法确定确切的时间跨度。这是因为 **模运算** 引入了 "循环" 效果。任务可能持续了 3 小时、15 小时，甚至 27 小时，所有这些时间在模 12 后都会得到相同的结果。

<br />
<img src="images/elliptic-curves/clock.gif" width="500"/>

在 **素数模数下**，这个问题尤其困难，被称为 **离散对数问题**。

## 有限域上的椭圆曲线

到目前为止，我们默认椭圆曲线是在有理数域（$\mathbb{R}$）上定义的。为了确保 **私钥 🔑** 的安全性并利用离散对数问题，我们需要转向由 **素数模数** 在有限域上定义的椭圆曲线。这本质上是使用特定素数进行模运算来限制曲线上的点，使其成为一个有限集合。

在本讨论中，我们将考虑在使用素数模数 **997** 的 **任意有限域** 上定义的 **secp256k1** 椭圆曲线：

$$
y^2 = x^3 + 7 \pmod {997}
$$

<img src="images/elliptic-curves/finite-field.png" width="500"/>

在有限域中曲线的几何表示可能比连续曲线更加抽象，但其对称性依然保持不变。此外，标量乘法仍然封闭，尽管 "切线" 现在会由于模数的特性而 "循环"。

<br />
<img src="images/elliptic-curves/finite-scalar-multiplication.gif" width="500"/>

## 生成密钥对

Alice 最终可以使用有限域上的椭圆曲线来生成密钥对。

我们在 [Sage](https://www.sagemath.org/) 中定义一个素数模数为 997 的有限域上的椭圆曲线。

```python
sage: E = EllipticCurve(GF(997),[0,7])
Elliptic Curve defined by y^2 = x^3 + 7 over Finite Field of size 997
```

通过在曲线上选择一个任意点来定义生成点 $G$。

```python
sage: G = E.random_point()
(174 : 487 : 1)
```

椭圆曲线上的标量乘法运算定义了一个 **阶为 $n$ 的循环子群**。这意味着在子群内重复相加任意一点 $n$ 次的结果都会得出无穷远点 ($O$)，即单位元。

$$
nP  = O
$$

```python
sage: n = E.order()
1057
# 说明 n*G（或任何子群内的点）等于 O（无穷远点），其表示为 (0 : 1 : 0)。
sage: n*G
(0 : 1 : 0)
```

一个密钥对由以下部分组成：

1. **私钥 🔑**($K$)：从子群的阶 $n$ 中随机选择的整数，确保只有 Alice 能够生成有效的签名。

Alice 随机选择 **42** 作为 **私钥 🔑**.

```python
sage: K = 42
```

2. **公钥** ($P$)：椭圆曲线上的一个点，由 **私钥 🔑($K$)** 与生成点 ($G$) 进行标量乘法计算得出。公钥允许任何人验证 Alice 的签名。

```python
sage: P = K*G
(858 : 832 : 1)
```

由此，我们得出 Alice 的密钥对 $=[P, K] = [(858, 832), 42]$。

## ECDSA 实际应用

ECDSA 是数字签名算法（DSA）的一种变体。它基于消息的 "指纹" 使用加密哈希创建签名。

为了使 ECDSA 正确运作，Alice 和 Bob 需要建立一组共同的域参数。在本示例中，这些参数为：

| 参数                             | 值           |
| ------------------------------------- | --------------- |
| 椭圆曲线方程          | $y^2 = x^3 + 7$ |
| 有限域的素数模数 | 997             |
| 生成点 $G$.             | (174, 487)      |
| 子群的阶 $n$.       | 1057            |

重要的是，Bob 确信公钥 $P = (858, 832)$ 确实属于 Alice。

### 签名

Alice 打算对消息 **"Send $1 million" (发送 100 万美元)** 进行签名，步骤如下：

1. 计算加密哈希 **$m$**。

```python
sage: m = hash("Send $1 million")
-7930066429007744594
```

2. 对于每个签名，都会生成一个随机的 **临时密钥对 [$eK$, $eP$]**，以防止暴露她 **私钥 🔑** 的 [攻击](https://youtu.be/DUGGJpn2_zY?si=4FZ3ZlQZTG9-eah9&t=2117)。

```python
# 随机选择的临时私钥。
sage: eK = 10
# 临时公钥。
sage: eP = eK*G
(215 : 295 : 1)
```

临时密钥对 $=[eK, eP] = [10, (215, 295)]$.

3. 计算签名组件 **$s$**:

$$ s = k^{−1} (e + rK ) \pmod n$$

其中，$r$ 是临时公钥 **(eP)** 的 x 坐标，即 **215**。请注意，签名的计算同时使用了 Alice 的 **私钥 🔑 ($K$)** 和临时密钥对 **[$eK$, $eP$]**。

```python
# 提取临时公钥的 x 坐标。
sage: r = int(eP[0])
215
# 签名组件 s。
sage: s = mod(eK**-1 * (m + r*K), n)
160
```

最终得出 **签名对** 为元组 $(r,s) =  (215, 160)$。

之后，Alice 将消息和签名写入明信片。

<img src="images/elliptic-curves/postcard.jpg" width="500"/>

### 验证

Bob 通过签名对 **$(r,s)$**、消息，以及 Alice 的公钥 **$P$**，独立计算出 **完全相同的临时公钥** 以验证签名：

1. 计算加密哈希 **$m$**。

```python
sage: m = hash("Send $1 million")
-7930066429007744594
```

2. 计算临时公钥 **$R$** 并于 **$r$** 进行比较：

$$R =  (es^{−1} \pmod n)*G + (rs^{−1} \pmod n)*P$$

```python
sage: R = int(mod(m*s^-1,n)) * G  + int(mod(r*s^-1,n)) * P
(215 : 295 : 1)
# 与临时公钥的 x 坐标对比。
sage: R[0] == r
True # 签名有效 ✅
```

如果 Alice 的劫持者试图篡改消息，这将改变加密哈希，从而导致与原始签名不匹配而验证失败。

```python
sage: m = hash("Send $5 million")
7183426991750327432 # 哈希值不同!
sage: R = int(mod(m*s^-1,n)) * G  + int(mod(r*s^-1,n)) * P
(892 : 284 : 1)
# 与临时公钥的 x 坐标对比。
sage: R[0] == r
False # 签名无效 ❌
```

签名的验证使 Bob 能够确保消息的真实性，从而安全地转账并拯救 Alice。椭圆曲线拯救了一切！

## 总结

与 Alice 一样，以太坊的每个账户都 [使用 ECDSA 来签署交易](https://web.archive.org/web/20240229045603/https://lsongnotes.wordpress.com/2018/01/14/signing-an-ethereum-transaction-the-hard-way/)。然而，以太坊中的 ECC 涉及额外的安全考量。虽然核心原理相同，但以太坊使用更安全的哈希函数（如 keccak256）和更大的素数域，高达 78 位数：$2^{256}-2^{32}-977$。



本讨论仅为椭圆曲线密码学的基础介绍。想要深入了解，请参考下方资源。

最后：**永远不要自己手动加密！** 请使用经过审查的库和协议来保护你的数据和交易。

> ℹ️ 注意  
> ECDSA 可能会被量子计算机淘汰——了解 [后量子密码学如何应对这一挑战](/wiki/Cryptography/post-quantum-cryptography.md)。

## 进一步阅读

**椭圆曲线密码学**

- 📝 高效密码学标准组织 (SECG), ["SEC 1: 椭圆曲线密码学"](http://www.secg.org/sec1-v2.pdf)。
- 📝 效密码学标准组织 (SECG), ["SEC 2: 推荐的椭圆曲线域参数"](http://www.secg.org/sec2-v2.pdf)。
- 📘 Alfred J. Menezes, Paul C. van Oorschot and Scott A. Vanstone, [Handbook of Applied Cryptography](https://cacr.uwaterloo.ca/hac/)
- 🎥 Fullstack Academy, ["通过 Diffie-Hellman 密钥交换理解 ECC"](https://www.youtube.com/watch?v=gAtBM06xwaw)。
- 📝 Andrea Corbellini, ["椭圆曲线密码学：温和介绍"](https://andrea.corbellini.name/2015/05/17/elliptic-curve-cryptography-a-gentle-introduction/)。
- 📝 William A. Stein, ["椭圆曲线"](https://wstein.org/simuw06/ch6.pdf)。
- 📝 Khan Academy, ["模运算"](https://www.khanacademy.org/computing/computer-science/cryptography/modarithmetic/a/what-is-modular-arithmetic)。
- 🎥 Khan Academy, ["离散对数问题"](https://www.youtube.com/watch?v=SL7J8hPKEWY)。

**椭圆曲线数学**

- 📘 Joseph H. Silverman, ["椭圆曲线的算术"](https://books.google.co.in/books?id=6y_SmPc9fh4C&redir_esc=y)。
- 📝 Joseph H. Silverman, ["椭圆曲线理论介绍"](https://www.math.brown.edu/johsilve/Presentations/WyomingEllipticCurve.pdf)。
- 📘 Neal Koblitz, ["数论与密码学课程"](https://link.springer.com/book/10.1007/978-1-4419-8592-7)。
- 📝 Ben Lynn, ["斯坦福密码学：椭圆曲线"](https://crypto.stanford.edu/pbc/notes/elliptic/)。
- 📝 Rareskills.io, ["椭圆曲线点加法"](https://www.rareskills.io/post/elliptic-curve-addition)。
- 📝 John D. Cook, ["有限域"](https://www.johndcook.com/blog/finite-fields/)。

**实用工具**

- 🎥 Tommy Occhipinti, ["Sage 中的椭圆曲线"](https://www.youtube.com/watch?v=-fRWR_QKzuI)。
- 🎥 Desmos, ["Desmos 图形计算器介绍"](https://www.youtube.com/watch?v=RKbZ3RoA-x4)。
- 🧮 Andrea Corbellini, ["交互式椭圆曲线加法与乘法"](https://andrea.corbellini.name/ecc/interactive/reals-add.html)。

## 致谢

- 感谢 Michael Driscoll 对 [椭圆曲线动画](https://github.com/syncsynchalt/animated-curves) 的贡献。
