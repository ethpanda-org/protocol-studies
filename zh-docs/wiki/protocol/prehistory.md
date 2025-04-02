# 以太坊的历史背景

> “英雄之所以成为英雄，是因为他们的行为具有英雄气概，而非因为他们取得了胜利或失败。” \
> — Nicholas Taleb

本文探讨了以太坊的发展脉络，赞颂了那些凭借勇气、创造力以及纯粹的叛逆精神对其产生深远影响的英雄们。

以太坊起源于早期互联网的开放精神，其设计理念深受 Unix 系统“专注于一件事并将其做好”这一理念的影响。由 GNU/Linux 所体现的自由开源运动的兴起，再次确认了软件领域的开放标准。与此同时，公钥加密技术的重大突破以及“密码朋克”（cypherpunks）对其的倡导，为像比特币这样安全、透明且去中心化的系统奠定了基础，而比特币最终启发了以太坊构建一个无国界、自主主权的数字经济平台的愿景。

> “倘若你审视一下那些在比特币领域早期阶段有所涉足的人士，他们过往的经历（如果有的话）大多源自开源软件领域——比如 Linux、Mozilla 以及 cypherpunk 邮件列表。”\
> — _Vitalik Buterin, 以太坊的联合创始人。_

## 信息高速公路

从1969年作为一个冷战项目（[ARPANET](https://en.wikipedia.org/wiki/ARPANET)）的微小开始，互联网已经发展成为一种前所未有的全球现象。

> “互联网的普及速度超过了之前的所有其他技术。在5000万人收听收音机之前，收音机已经存在了38年；电视花了13年才达到这个标准。第一个个人电脑套件问世16年后，有5000万人在使用它。一旦它向公众开放，互联网在四年后就跨越了这条线。”\
> — [新兴的数字经济，（1998年7月）。](https://www.commerce.gov/sites/default/files/migrated/reports/emergingdig_0.pdf)

![1989年至2021年的互联网连线地图。](img/overview/information-superhighway.gif)
**1989年至2021年的互联网连线地图。 [来源：《纽约时报》](https://www.nytimes.com/interactive/2019/03/10/technology/internet-cables-oceans.html)**

它最初只是少数几个机构的研究工具，现在连接了全球数十亿人，打破了地理边界，促进了曾经不可思议的人类互动。


> “国界只是信息高速公路上的减速带。”\
> — Timothy May, Cypherpunk.

## Unix和贝尔实验室


Unix起源于简化[MULTICS](https://en.wikipedia.org/wiki/Multics)复杂性的努力，MULTICS是20世纪60年代一个雄心勃勃的大型操作系统项目。由于MULTICS变得难以控制，一个包括AT&T贝尔实验室的[Ken Thompson](https://en.wikipedia.org/wiki/Ken_Thompson)和[Dennis Ritchie](https://en.wikipedia.org/wiki/Dennis_Ritchie)在内的小团队试图创建Unix——一个更模块化、更简单和可组合的替代方案：

> “在某种程度上，我意识到我距离操作系统只有三周的时间。我需要一个编辑器、汇编器和内核覆盖层——就叫它操作系统吧。一周，一周，一周，我们就有了Unix。”\
> — [_Ken Thompson 在一场采访中提到_](https://www.youtube.com/watch?v=EY6q5dv_B-o)

1972年，丹尼斯还写出了颇具影响力的 [C 语言 ](<https://en.wikipedia.org/wiki/C_(programming_language)>).

![Ken Thompson 和 Dennis Ritchie](img/overview/ken-thompson-dennis-ritchie.jpg)
**Ken Thompson 和 Dennis Ritchie.**

贝尔实验室是本世纪最具决定性的技术构件无与伦比的孵化器：

> “你不可能去商店买到贝尔实验室的创新产品，但它深藏在其他东西里面；这是通信基础设施不可或缺的平台创新。” \
> — Jon G., 创意工厂

> 🎦 观看: [Jon talk about innovations at Bell Labs.](https://www.youtube.com/watch?v=OJsKgiGGzzs)

在很多方面， [Ethereum functions](https://ethereum.foundation/infinitegarden) 就像一个开放的贝尔实验室。
Unix引入了一些概念，比如分层文件系统、shell作为命令行界面、可以组合起来执行复杂任务的单一用途实用程序。
这些基本原则奠定了后来被称为UNIX哲学的基础——在软件设计中支持简单、灵活和可重用性。

今天，UNIX及其衍生物继续支撑着现代计算的大部分，影响着从Linux和macOS这样的操作系统到永恒的软件开发原则的一切。

> 🎦 观看: [Unix纪录片。](https://www.youtube.com/watch?v=tc4ROCJYbm0)

Unix遗产展示了一小群人可以通过软件对世界产生的深远影响。

## 我们能保守秘密吗？

自人类文明诞生以来，秘密传递信息的需要一直是人类的追求。从商人隐藏商业秘密到间谍和传递关键信息的军方，密码学发挥了至关重要的作用。早期的方法通常使用相同的密钥进行加密和解密，这使得安全的密钥分发成为一个噩梦：

> “对于一个没有军事通信经验的人来说，制造、登记、分发和取消密钥的问题可能显得微不足道，但在战时，交通流量甚至会让信号工作人员感到吃惊。” \
> — [David Kahn在 **the codebreakers** 中写道](https://en.wikipedia.org/wiki/The_Codebreakers)

如果密钥落入敌人手中，消息就会变得脆弱。在第二次世界大战中，数学家 [艾伦·图灵](https://en.wikipedia.org/wiki/Alan_Turing) 和他的团队破解了一个复杂的德国密码——[Enigma machine](https://en.wikipedia.org/wiki/Enigma_machine)，这一点显而易见。他们的成功极大地改变了战争的结果。

![艾伦·图灵的雕像和谜机](img/overview/alan-turing.jpg)
**艾伦·图灵和英格玛机的雕像。**

你如何在素未谋面的人之间安全地远距离交换密钥？批评者认为密码学注定要依赖信任：

> “几乎没有人会相信，发明一种能妨碍调查的秘密写作方法不是一件容易的事。然而，我们可以断言，人类的聪明才智无法编造出一种人类聪明才智无法解决的密码。”\
> — Edgar Allan Poe

从1974年到1978年，Poe 的一系列发明证明他错了。

1974年，[Ralph Merkle](https://en.wikipedia.org/wiki/Ralph_Merkle) 设计了[Merkle’s Puzzles](https://en.wikipedia.org/wiki/Merkle%27s_Puzzles)——一种初始方法，允许双方通过交换消息就共享秘密达成一致，即使他们事先没有共同的秘密。

两年后，1976年，默克尔的工作启发了 [Whitfield Diffie](https://en.wikipedia.org/wiki/Whitfield_Diffie) 和 [Martin Hellman](https://en.wikipedia.org/wiki/Martin_Hellman) 发表了他们具有历史意义的论文[ “密码学的新方向” ](https://ee.stanford.edu/~hellman/publications/24.pdf)，介绍了[Diffie - Hellman 密钥交换算法](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange)。这种方法在数学上明显比默克尔的谜题更鲁棒——催生了不可靠的加密。

![Whitfield和Martin发表了《密码学新方向》](img/overview/new-direction-in-cryptography.jpg)
**Whitfield和Martin发表了《密码学的新方向》。**

在接下来的1977年，计算机科学家们 [Ronald Rivest](http://amturing.acm.org/award_winners/rivest_1403005.cfm), [Adi Shamir](http://amturing.acm.org/award_winners/shamir_2327856.cfm)， 和 [Leonard Adleman](http://amturing.acm.org/award_winners/adleman_7308544.cfm) 开发了[RSA密码系统](<https://en.wikipedia.org/wiki/RSA_(cryptosystem)>) - 在一篇题为[" a Method for getting Digital Signatures and public key Cryptosystems"](https://people.csail.mit.edu/rivest/Rsapaper.pdf)的论文中，首次实现了公钥密码体制。里维斯特把这篇论文的副本寄给了数学家马丁·加德纳。马丁被深深打动了，他打破了通常几个月前就计划好专栏的规矩，很快就写好了，准备在[1977年8月发表](https://web.archive.org/web/20230728001717/http://simson.net/ref/1977/Gardner_RSA.pdf)。《科学美国人》杂志：
![Len, Adi,Ron 在 CRYPTO '82，以及 Martin Gardner 现在著名的文章](img/overview/rsa-in-scientific-american.jpg)
**Len, Adi, Ron 在 CRYPTO '82，和 [现在很出名的文章](https://web.archive.org/web/20230728001717/http://simson.net/ref/1977/Gardner_RSA.pdf) 由 Martin Gardner 发表在《科学美国人》**

在文章中，Gardner提供了一个RSA-129密码，并向第一个解决它的人提供100美元：
![MIT 的 RSA 挑战](img/overview/rsa-challenge.jpg)
**MIT 的 RSA 挑战**

1994年，一群计算机科学家和志愿者 [破解密码](https://en.wikipedia.org/wiki/The_Magic_Words_are_Squeamish_Ossifrage) 把钱捐给了 [自由软件基金会。](https://www.fsf.org/) 这项工作强调了一个关键点：密码学的完美安全性是一种错觉。像RSA这样的加密方法正在不断发展，特别是在预测[量子计算机](/wiki/Cryptography/post-quantum-cryptography.md)的时候。

尽管如此，现代的 RSA 加密（1024 至 4096 位）在信息高速公路上建立了一条安全通道，使银行和信用卡公司能够保护金融交易。这促进了信任，并推动了电子商务和网上银行的发展。

![现代密码学的发明者](img/overview/inventors-of-modern-cryptography.jpg)  
**现代密码学的发明者：Adi Shamir、Ron Rivest、Len Adleman、Ralph Merkle、Martin Hellman 和 Whit Diffie 在 Crypto 2000 大会上的合影 [(图片来源：Eli B.)](https://www.ralphmerkle.com/merkleDir/KobayashiAward.html)**

1997 年，英国政府解密了来自 1970 年的类似[研究](https://cryptocellar.org/cesg/possnse.pdf)。

## 自由，就像在自由中的自由

在 20 世纪 50 至 60 年代，硬件和操作系统飞速发展，早期的软件通常较为原始，需要修改，而软件源代码并非秘密。事实上，共享源代码是当时的常态。这催生了一种业余爱好者的[“黑客文化”](https://en.wikipedia.org/wiki/Hacker_culture)，鼓励探索与知识交流。任何人都可以查看、修改并对源代码提供反馈。计算机杂志甚至会刊登[“手写输入程序”](https://en.wikipedia.org/wiki/Type-in_program)，鼓励用户手动编写软件：

![计算机杂志上的手写输入程序](img/overview/type-in-program.jpg)  
**用于数据备份的手写输入程序，《Compute!》杂志 [来源：commodore.ca](https://www.commodore.ca/gallery/magazines/compute/Compute-004.pdf)**

随着软件规模的增长和存储成本的下降，软件开始通过磁带分发，并常常由 IBM 等制造商与计算机硬件捆绑销售。然而，这一做法在 1969 年因[美国对 IBM 的反垄断诉讼](https://www.justice.gov/atr/case-document/united-states-memorandum-1969-case)而终止。诉讼认为，用户被迫购买硬件才能使用捆绑软件。尽管该诉讼最终被撤销，但却带来了意想不到的后果——公司们抓住机会开始单独为软件收费。软件从此成为了一种商品。

Unix 也成为了这一趋势的牺牲品。最初，它被免费分发给政府和学术研究人员，但到了 20 世纪 80 年代初，随着 Unix 的普及，AT&T 停止了免费分发，并开始对系统补丁收费。由于切换到其他架构的难度较大，许多研究人员选择支付商业许可证费用。

为了提高收入，公司普遍停止分发源代码。有些公司甚至竭尽全力阻止软件的自由传播。在一封臭名昭著的[公开信](https://en.wikipedia.org/wiki/An_Open_Letter_to_Hobbyists)中，[比尔·盖茨](https://en.wikipedia.org/wiki/Bill_Gates) 呼吁业余爱好者停止共享 BASIC 源代码：

> “为什么会这样？大多数业余爱好者都应该清楚，你们大多数人都在偷软件。硬件必须付费，但软件是一种可以共享的东西。谁在乎那些为此付出努力的人是否能得到报酬？”\
> ——比尔·盖茨，[〈致业余爱好者的公开信〉](https://en.wikipedia.org/wiki/An_Open_Letter_to_Hobbyists)

在围绕软件所有权的激烈争论中，[理查德·斯托曼](https://en.wikipedia.org/wiki/Richard_Stallman)——麻省理工学院人工智能实验室的一名研究助理——陷入了一场个人斗争。他对自己无法修改新安装的 Xerox 打印机的源代码感到沮丧，并认为这种限制是“对人类的犯罪”：

> “如果你做饭，你可能会交换食谱，并与朋友分享，他们可以自由修改。但想象一个世界，你无法修改自己的食谱，因为有人刻意让它变得无法更改。如果你尝试与朋友分享这个食谱，他们会称你为**海盗**，甚至把你送进监狱。”\
> ——理查德·斯托曼，在[纪录片](https://www.youtube.com/watch?v=XMm0HsmOTFI)中

在 1983 年的一封[电子邮件](https://groups.google.com/g/net.unix-wizards/c/8twfRPM79u0)中，他宣布了自己的目标——开发一个 Unix 的自由替代方案：[GNU](https://www.gnu.org/)：

![GNU 公告](img/overview/gnu-announcement.jpg)  
**理查德·斯托曼及其关于 GNU 项目的[电子邮件公告](https://groups.google.com/g/net.unix-wizards/c/8twfRPM79u0)。**


GNU 是对 Unix 的直接回应，且[递归](https://en.wikipedia.org/wiki/Recursion)代表着“GNU 不是 Unix”。他决定使操作系统与 Unix 兼容，因为 Unix 的整体设计已经经过验证且具有可移植性，兼容性将使 Unix 用户能够轻松地从 Unix 切换到 GNU。

正如理查德所解释的，自由软件不仅仅是价格方面的考量：

> “自由软件”，我需要解释的是，它指的是自由，而不是价格。遗憾的是，英语中的“free”一词含义模糊——它有许多不同的意思。其中一个意思是“零价格”，另一个意思是“自由”。  
> 所以，想一想“言论自由”，而不是“免费啤酒”。

> 🎦 观看：[理查德·斯托曼谈自由软件及其对社会的影响。](https://www.youtube.com/watch?v=Ag1AKIl_2GM)

GNU 于 1984 年 1 月启动。作为这项工作的组成部分，理查德编写了[GNU 通用公共许可证](https://en.wikipedia.org/wiki/GNU_General_Public_License)（GPL）。到 1990 年，GNU 已经找到了或编写了操作系统的所有主要组件，除了一个——内核。

巧合的是，[林纳斯·托瓦兹](https://en.wikipedia.org/wiki/Linus_Torvalds)，一名计算机科学学生，正在开发一个名为 Linux 的内核：

![Linux 公告](img/overview/linux-announcement.jpg)  
**林纳斯·托瓦兹及其[Linux 公告电子邮件](https://groups.google.com/g/comp.os.minix/c/dlNtH7RRrGA/m/SwRavCzVE7gJ)。**

第一批回应在几小时内到达，几百人加入了接下来一年的开发。Linux 以 GPL 许可证发布，完成了 GNU/Linux 操作系统。

在这个过程中，Linux 实际上为基于社会共识的软件开发奠定了蓝图：

> “在 1992 年初，很快，我突然发现我不再认识每一个人了。那不再是我和几个朋友，而是我和成百上千的人。这是一个巨大的进步。”\
> ——林纳斯·托瓦兹

这群来自各个领域的贡献者，从个人爱好者到大公司，合作改进内核，修复漏洞，并实现新特性。它实际上为后来塑造开源软件运动奠定了基础。

开源运动与自由软件运动有所不同，更加注重可访问源代码的实际利益。这种方法在社区驱动的创新和商业可行性之间提供了平衡，推动了商业的广泛采纳。自由和开源软件（FOSS）是自由软件和开源软件的统称。

GNU/Linux 作为一种证明，展示了软件应该赋予用户力量，而不是限制用户的理念。

> 🎦 观看：[Revolution OS：关于 GNU/Linux 的纪录片。](https://www.youtube.com/watch?v=k0RYQVkQmWU)


## Cypherpunks 写代码

自第二次世界大战结束以来，政府在加密学领域一直享有垄断地位，并对此进行严格管控。在美国，加密技术受[军火清单](https://en.wikipedia.org/wiki/United_States_Munitions_List)的控制。这意味着[国家安全局](https://en.wikipedia.org/wiki/National_Security_Agency)对加密学的进展高度关注。

当国家安全局收到 MIT 的 RSA 论文时，他们试图将其归类为机密，但最终允许了论文的发布：

![NSA 加密学辩论](img/overview/nsa-cryptology-debate.jpg)  
**国家安全局根据[2009年《信息自由法》](https://cryptome.org/2021/04/Joseph-Meyer-IEEE-1977.pdf)请求的回应。**

国家安全局的做法在公众中引发了广泛批评，当时国家安全局员工 Joseph Mayer 写给 IEEE 的一封私人信件披露了加密出版物需要政府批准的情况。

这标志着政府与加密学倡导者之间[加密战争](https://en.wikipedia.org/wiki/Crypto_Wars)的开端。

![NSA 加密学辩论](img/overview/nsa-crypto-wars.jpg)  
**《科学》杂志关于加密学辩论的[报道](https://www.science.org/doi/10.1126/science.197.4311.1345)。**

政府试图削弱加密技术的行为被视为对公共通信进行监控的手段。

![NSA 监听监控](img/overview/wire-tap-surveillance.jpg)  
**《科学》杂志关于窃听和英国相关街头艺术 Banksy 的[报道](https://www.science.org/doi/10.1126/science.199.4330.750)。**

在 1980 年代，加密学作为保障通信安全的手段继续发展。

1985 年，加密学家[David Chaum](https://en.wikipedia.org/wiki/David_Chaum) 发表了突破性论文[《无身份认证的安全：使老大哥过时的交易系统》](https://dl.acm.org/doi/pdf/10.1145/4372.4373)，他在其中描述了提供安全性和隐私的交易方案，并提出了一个激进的想法——“数字化化名”供个人使用加密技术。

![David Chaum](img/overview/david-chaum.jpg)  
**David Chaum 和他的论文。**

加密技术的普及得益于[Pretty Good Privacy](https://en.wikipedia.org/wiki/Pretty_Good_Privacy)（PGP），这是由 Phil Zimmermann 于 1991 年开发的加密程序。PGP 使个人能够使用强加密保护他们的通信和数据。

1993 年，加密战争继续升级，当时 Zimmermann 成为美国海关局因涉嫌违反加密软件出口限制而进行刑事调查的对象。

在一次标志性的举动中，他将整个 PGP 源代码发布在一本[硬皮书](https://philzimmermann.com/EN/essays/BookPreface.html)中，辩称书籍的出口受[美国宪法第一修正案](https://en.wikipedia.org/wiki/First_Amendment_to_the_United_States_Constitution)保护。这些书籍根据美国出口法规从美国出口，随后将页面扫描并使用 OCR 技术将源代码以电子形式发布。一些活动家为表示支持，还将源代码打印在 T 恤上。

此案于 1996 年撤销。

> “我曾经觉得自己像是寄生在霸王龙背上的跳蚤。现在我觉得自己可能是跳蚤背上的一只小啸小狗。”\
> ——Phil Zimmermann

![Phil Zimmermann](img/overview/phil-zimmermann.jpg)  
**Phil Zimmermann，一件印有 RSA 源代码的 T 恤，以及一位欧洲志愿者扫描 PGP 书籍；来自欧洲的[70多人，花费超过1000小时使 PGP 发布得以在美国以外地区实现](https://www.pgpi.didisoft.com/pgpi/project/scanning/)。**

1992 年初，在 PGP 2.0 发布的同一周，三位湾区工程师——[Eric Hughes](<https://en.wikipedia.org/wiki/Eric_Hughes_(cypherpunk)>), [Timothy C. May](https://en.wikipedia.org/wiki/Timothy_C._May), 和 [John Gilmore](<https://en.wikipedia.org/wiki/John_Gilmore_(activist)>)——聚集在一起，创建了一个名为[Cypherpunk](https://mailing-list-archive.cryptoanarchy.wiki/)（密码+ [赛博朋克](https://en.wikipedia.org/wiki/Cyberpunk)）的邮件列表。

Cypherpunk 发展成为一个具有深远影响的运动，吸引了超过[700名活动家和反叛者，包括 Zimmerman](https://mailing-list-archive.cryptoanarchy.wiki/authors/notable/)，准备通过代码反击：

> “Cypherpunks 写代码。我们知道必须有人编写软件来捍卫隐私，我们就是那个人。  
> [...]  
> Cypherpunks 因此致力于加密学。Cypherpunks 希望学习它、教授它、实施它，并创造更多的加密学。Cypherpunks 知道加密协议创造社会结构。Cypherpunks 知道如何攻击一个系统，也知道如何防御它。Cypherpunks 知道制作出好的加密系统是多么困难。”\
> ——Eric Hughes

![Phil Zimmermann](img/overview/cypherpunks-write-code.jpg)  
**Tim, Eric, 和 John（顶部）。Eric 的 Cypherpunk [电子邮件](https://mailing-list-archive.cryptoanarchy.wiki/archive/1992/09/fdf9c19e77ec3f1a9bbc6bc19266d565b89d19dbd0ad369f5a2e800af3fc9558/)（底部）。[Cypherpunk 宣言](https://www.activism.net/cypherpunk/manifesto.html)（右侧）。**

1994 年的一次会议上，Tim [描述](https://web.archive.org/web/20240415133242/http://www.kreps.org/hackers/overheads/11cyphernervs.pdf)了 Cypherpunks 的核心信念：

> “没有什么是官方的（不多的），但有一套渐渐形成的连贯信念，似乎大多数邮件列表成员都认同：
>
> - 政府不应能够窥探我们的事务
> - 保护谈话和交流是基本权利
> - 这些权利可能需要通过 _技术_ 而不是法律来保障
> - 技术的力量常常创造出新的政治现实（因此，列表的口号是：“Cypherpunks 写代码”）”

在他 1988 年的[《加密无政府主义者宣言》](https://groups.csail.mit.edu/mac/classes/6.805/articles/crypto/cypherpunks/may-crypto-manifesto.html)中，Tim 提出了“加密无政府主义”的政治哲学，反对一切形式的权威，并承认只有通过加密学和代码执行的法律才有效。

![加密无政府主义宣言](img/overview/crypto-anarchy.jpg)  
**无政府主义和 Tim May 的《加密无政府主义者宣言》。**

该宣言设想了匿名数字交易作为个人自由的基石。

缺失的部分：**一种加密原生的[数字货币](https://en.wikipedia.org/wiki/Digital_currency)。**

> 🎦 观看：[Tim 反思 30 年的加密无政府主义。](https://www.youtube.com/watch?v=TdmpAy1hI8g)


## 寻找缺失的部分

在 90 年代，加密朋克多次尝试创建数字货币。

1990 年，David Chaum 推出了[DigiCash](https://en.wikipedia.org/wiki/DigiCash)，为匿名数字经济提供了首次展示。然而，它依赖现有的金融基础设施，并且在很大程度上是集中化的。最终，DigiCash 于 1998 年申请破产。

![DigiCash 首页](img/overview/digicash.jpg)  
**DigiCash 首页。**

1996 年，E-gold 出现，背后由储备金实物黄金支持。在其巅峰时期，E-gold 拥有[350 万个注册账户](https://web.archive.org/web/20061109161419/http://www.e-gold.com/stats.html)，每年促进了数十亿美元的交易。然而，由于法律问题，2009 年交易被暂停。

后来的一些方案则致力于不再依赖黄金等担保物，而是通过数字控制稀缺性。1998 年，[Wei Dai](https://en.wikipedia.org/wiki/Wei_Dai) 提出了由加密函数驱动的[B-money](https://web.archive.org/web/20220303184029/http://www.weidai.com/bmoney.txt)，以创造货币。2005 年，[Nick Szabo](https://en.wikipedia.org/wiki/Nick_Szabo) 设计了[BitGold](https://web.archive.org/web/20240329075756/https://unenumerated.blogspot.com/2005/12/bit-gold.html)，但未被实施。虽然这些方案未能成功获得主流采用，但它们的设计影响了后来数字货币成为现实的关键——比特币。

![Wei Dai 和 Nick Szabo](img/overview/wei-dai-nick-szabo.jpg)  
**Wei Dai 和 Nick Szabo。**

## 比特币

2008 年的金融危机重新点燃了对数字货币实验的兴趣，尤其是让 BitGold 再次成为话题。

2008 年，化名作者[Satoshi Nakamoto](https://en.wikipedia.org/wiki/Satoshi_Nakamoto) 在论文《[比特币：一种点对点电子现金系统](https://bitcoin.org/bitcoin.pdf)》中提出了解决如何在没有领导者的情况下达成共识的问题。比特币确立了自己作为分布式账本系统的地位，其中数据以加密的方式按时间顺序链接在区块中。它还成为了第一个去中心化的数字货币，不依赖任何实物担保，并消除了银行等受信中介的需求。

![Satoshi 的雕像和比特币公告](img/overview/satoshi-and-bitcoin.jpg)  
**Satoshi 的雕像和比特币公告帖子。**

比特币也是世界上最大规模的社会经济实验：

> “当 Satoshi Nakamoto 在 2009 年 1 月首次启动比特币区块链时，他同时引入了两个激进且未经验证的概念。第一个是 ‘比特币’，一种去中心化的点对点在线货币，它在没有任何担保、内在价值或中央发行者的情况下维持其价值。到目前为止，‘比特币’作为货币单位已经占据了公众的主要关注。  
> 然而，还有另一个同样重要的部分，Satoshi 的宏大实验：基于工作量证明的区块链概念，允许公众就交易顺序达成共识。”\
> ——Vitalik Buterin

[几次](https://web.archive.org/web/20230404234458/https://www.etoro.com/wp-content/uploads/2022/03/Colored-Coins-white-paper-Digital-Assets.pdf) [尝试](https://en.wikipedia.org/wiki/Namecoin) 已经建立在比特币网络之上的应用，以利用新创建的数字货币。然而，出于这个目的，比特币的网络证明过于原始，应用程序是通过复杂且不太可扩展的解决方法构建的。

以太坊作为一种解决方案，旨在解决这些挑战。


## 以太坊世界计算机

2012 年，[Vitalik Buterin](https://en.wikipedia.org/wiki/Vitalik_Buterin) 和 Mihai Alisie 创办了[比特币杂志](https://en.wikipedia.org/wiki/Bitcoin_Magazine)——第一本专注于数字货币的严肃出版物。Vitalik 很快发现比特币的局限性，并[提出了一个平台](https://web.archive.org/web/20150627031414/http://vbuterin.com/ultimatescripting.html)，该平台支持通用的金融应用。

2014 年，在 [Gavin Wood](https://en.wikipedia.org/wiki/Gavin_Wood) 的帮助下，[以太坊的设计得以正式化](https://ethereum.github.io/yellowpaper/paper.pdf)。

![Vitalik、Jeff 和 Gavin 一起工作于以太坊](img/overview/ethereum-launch.jpg)  
**Vitalik、Jeff 和 Gavin 一起工作于以太坊。**

2015 年 7 月 30 日，以太坊[上线](https://etherscan.io/block/1)，成为一个旨在构建自我主权经济工具的平台，使用数字货币。

截至撰写本文时，以太坊的市值为 **4000 亿美元**。

> 📄 阅读：[Vitalik 关于以太坊起源的文章。](https://vitalik.eth.limo/general/2017/09/14/prehistory.html)

> 🎦 观看：[Mario Havel 讨论以太坊的哲学。](https://streameth.org/ethereum_protocol_fellowship/watch?session=65d77e4f437a5c85775fef9d)

> 📄 阅读：[以太坊的演变。](/wiki/protocol/history.md)


## 资源

- 📄 维基百科, ["ARPANET"](https://en.wikipedia.org/wiki/ARPANET)
- 📘 Brian K., ["Unix: A History and a Memoir"](https://www.amazon.com/dp/1695978552)
- 📄 CryptoCouple, ["世界上最著名的加密夫妇的历史"](https://cryptocouple.com/)
- 📄 Steven E., ["加密学改变历史的那一天"](https://medium.com/swlh/the-day-cryptography-changed-forever-1b6aefe8bda7)
- 📄 GNU, ["GNU 系统概述"](https://www.gnu.org/gnu/gnu-history.en.html)
- 📄 Steven V., ["回顾 GNU 和自由软件基金会 40 年"](https://www.zdnet.com/article/40-years-of-gnu-and-the-free-software-foundation/)
- 📄 David C., [“无身份认证的安全：使老大哥过时的交易系统”](https://dl.acm.org/doi/pdf/10.1145/4372.4373)
- 📄 Steven L., ["Wired: 加密反叛者"](https://web.archive.org/web/20160310165713/https://archive.wired.com/wired/archive/1.02/crypto.rebels_pr.html)
- 📄 Arvind N., ["加密梦想的去向？"](https://www.cs.princeton.edu/~arvindn/publications/crypto-dream-part1.pdf)
- 📄 Satoshi N., ["比特币：一种点对点电子现金系统"](https://bitcoin.org/bitcoin.pdf)
- 📄 Harry K. 等, ["Namecoin 的实证研究及去中心化命名空间设计的教训"](https://www.cs.princeton.edu/~arvindn/publications/namespaces.pdf)
- 📄 Nick S., ["在公共网络上形式化和保护关系"](https://web.archive.org/web/20040228033758/http://www.firstmonday.dk/ISSUES/issue2_9/szabo/index.html)
- 📄 Nick S., ["智能合约的理念"](https://web.archive.org/web/20040222163648/https://szabo.best.vwh.net/idea.html)
- 📄 Vitalik B., ["以太坊白皮书"](https://ethereum.org/content/whitepaper/whitepaper-pdf/Ethereum_Whitepaper_-_Buterin_2014.pdf)
- 📄 Vitalik B., ["2014 年比特币迈阿密大会上的以太坊"](https://www.youtube.com/watch?v=l9dpjN3Mwps)
- 🎥 Gavin Wood, ["以太坊入门"](https://www.youtube.com/watch?v=U_LK0t_qaPo)

