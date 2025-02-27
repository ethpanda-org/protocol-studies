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


Unix起源于简化[MULTICS]（https://en.wikipedia.org/wiki/Multics）复杂性的努力，MULTICS是20世纪60年代一个雄心勃勃的大型操作系统项目。由于MULTICS变得难以控制，一个包括AT&T贝尔实验室的[Ken Thompson]（https://en.wikipedia.org/wiki/Ken_Thompson）和[Dennis Ritchie]（https://en.wikipedia.org/wiki/Dennis_Ritchie）在内的小团队试图创建Unix——一个更模块化、更简单和可组合的替代方案：

> “在某种程度上，我意识到我距离操作系统只有三周的时间。我需要一个编辑器、汇编器和内核覆盖层——就叫它操作系统吧。一周，一周，一周，我们就有了Unix。”\
> — [_Ken Thompson 在一场采访中提到_](https://www.youtube.com/watch?v=EY6q5dv_B-o)

1972年，丹尼斯还写了《影响力》 [C 语言 ](<https://en.wikipedia.org/wiki/C_(programming_language)>).

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

> 🎦 观看: [ Unix纪录片。](https://www.youtube.com/watch?v=tc4ROCJYbm0)

Unix遗产展示了一小群人可以通过软件对世界产生的深远影响。

## 我们能保守秘密吗？

自人类文明诞生以来，秘密传递信息的需要一直是人类的追求。从商人隐藏商业秘密到间谍和传递关键信息的军方，密码学发挥了至关重要的作用。早期的方法通常使用相同的密钥进行加密和解密，这使得安全的密钥分发成为一个噩梦：

> “对于一个没有军事通信经验的人来说，制造、登记、分发和取消密钥的问题可能显得微不足道，但在战时，交通流量甚至会让信号工作人员感到吃惊。” \
> — [David Kahn在 **the codebreakers** 中写道](https://en.wikipedia.org/wiki/The_Codebreakers)

如果密钥落入敌人手中，消息就会变得脆弱。在第二次世界大战中，数学家艾伦·图灵（https://en.wikipedia.org/wiki/Alan_Turing）和他的团队破解了一个复杂的德国密码——[Enigma machine](https://en.wikipedia.org/wiki/Enigma_machine)，这一点显而易见。他们的成功极大地改变了战争的结果。

！[ 艾伦·图灵的雕像和谜机。]（img/overview/ Alan - Turing .jpg）
**艾伦·图灵和英格玛机的雕像。**

你如何在素未谋面的人之间安全地远距离交换密钥？批评者认为密码学注定要依赖信任：

> “几乎没有人会相信，发明一种能妨碍调查的秘密写作方法不是一件容易的事。然而，我们可以断言，人类的聪明才智无法编造出一种人类聪明才智无法解决的密码。”\
> — Edgar Allan Poe

从1974年到1978年，Poe 的一系列发明证明他错了。

1974年，[Ralph Merkle]（https://en.wikipedia.org/wiki/Ralph_Merkle）设计了[Merkle’s Puzzles]（https://en.wikipedia.org/wiki/Merkle%27s_Puzzles）——一种初始方法，允许双方通过交换消息就共享秘密达成一致，即使他们事先没有共同的秘密。

两年后，1976年，默克尔的工作启发了[Whitfield Diffie]（https://en.wikipedia.org/wiki/Whitfield_Diffie）和[Martin Hellman]（https://en.wikipedia.org/wiki/Martin_Hellman）发表了他们具有历史意义的论文[ “密码学的新方向” ](https://ee.stanford.edu/~hellman/publications/24.pdf)，介绍了[Diffie - Hellman 密钥交换算法]（https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange）。这种方法在数学上明显比默克尔的谜题更鲁棒——催生了不可靠的加密。

![Whitfield和Martin发表了《密码学新方向》](img/overview/new-direction-in-cryptography.jpg)
**Whitfield和Martin发表了《密码学的新方向》。**

在接下来的1977年，计算机科学家们 [Ronald Rivest](http://amturing.acm.org/award_winners/rivest_1403005.cfm), [Adi Shamir](http://amturing.acm.org/award_winners/shamir_2327856.cfm)， 和 [Leonard Adleman](http://amturing.acm.org/award_winners/adleman_7308544.cfm) 开发了[RSA密码系统](<https://en.wikipedia.org/wiki/RSA_(cryptosystem)>) - 在一篇题为[" a Method for getting Digital Signatures and public key Cryptosystems"](https://people.csail.mit.edu/rivest/Rsapaper.pdf)的论文中，首次实现了公钥密码体制。里维斯特把这篇论文的副本寄给了数学家马丁·加德纳。马丁被深深打动了，他打破了通常几个月前就计划好专栏的规矩，很快就写好了，准备在[1977年8月发表](https://web.archive.org/web/20230728001717/http://simson.net/ref/1977/Gardner_RSA.pdf)。《科学美国人》杂志：
![Len, Adi,Ron 在 CRYPTO '82，以及 Martin Gardner 现在著名的文章](img/overview/rsa-in-scientific-american.jpg)
**Len, Adi, Ron 在 CRYPTO '82，和 [现在很出名的文章](https://web.archive.org/web/20230728001717/http://simson.net/ref/1977/Gardner_RSA.pdf) 由 Martin Gardner 发表在《科学美国人》**

在文章中，Gardner提供了一个RSA-129密码，并向第一个解决它的人提供100美元：
![MIT 的 RSA 挑战](img/overview/rsa-challenge.jpg)
**MIT 的 RSA 挑战**

1994年，一群计算机科学家和志愿者 [破解密码](https://en.wikipedia.org/wiki/The_Magic_Words_are_Squeamish_Ossifrage) 把钱捐给了 [自由软件基金会。](https://www.fsf.org/) 这项工作强调了一个关键点：密码学的完美安全性是一种错觉。像RSA这样的加密方法正在不断发展，特别是在预测[量子计算机](/wiki/Cryptography/post-quantum-cryptography.md)的时候。

Nevertheless, modern RSA encryption (1024 to 4096 bits) created a secure pathway on the information superhighway, enabling banks and credit card companies to protect financial transactions. This fostered trust and facilitated the growth of e-commerce and online banking.

![Inventors of modern cryptography](img/overview/inventors-of-modern-cryptography.jpg)
**Inventors of modern cryptography: Adi Shamir, Ron Rivest, Len Adleman, Ralph Merkle, Martin Hellman, and Whit Diffie at Crypto 2000 [(courtesy of Eli B.)](https://www.ralphmerkle.com/merkleDir/KobayashiAward.html)**

In 1997, the British government declassified similar [research](https://cryptocellar.org/cesg/possnse.pdf) from 1970.

## Free as in freedom

Amidst a blizzard of advancing hardware and operating systems in the 1950s through 60s, early software was often primitive and required modification and software source code was no secret; in fact sharing source code was the norm. This fostered a hobbyist ["hacking culture"](https://en.wikipedia.org/wiki/Hacker_culture) that promoted exploration and exchange of knowledge. Anyone could inspect, modify, and provide feedback to the source code. Computer magazines would even feature printed [type-in programs](https://en.wikipedia.org/wiki/Type-in_program), that encouraged users to write software by hand:

![Type-in program from compute magazine](img/overview/type-in-program.jpg)
**A type-in program to backup data, Compute! magazine [Source: commodore.ca.](https://www.commodore.ca/gallery/magazines/compute/Compute-004.pdf)**

As software sizes grew and the cost of storage declined, software began to be distributed on tapes, often bundled with computer hardware by manufacturers like IBM. This practice came to a halt due to the 1969 [US vs. IBM antitrust lawsuit](https://www.justice.gov/atr/case-document/united-states-memorandum-1969-case), which argued that users were compelled to purchase hardware to use the bundled software. Although the lawsuit was later dropped, it backfired – companies seized the opportunity to start charging separately for software. Software became a commodity.

Unix was another casualty of this trend. Initially distributed at no cost to government and academic researchers, by the early 1980s, AT&T ceased free distribution and started charging for system patches as Unix became more widespread. Due to the challenges of switching to alternative architectures, many researchers opted to pay for commercial licenses.

To boost revenues, a general trend emerged where companies ceased distributing source code. Some companies went out of their way to prevent software distribution. In an infamous [open letter](https://en.wikipedia.org/wiki/An_Open_Letter_to_Hobbyists), [Bill Gates](https://en.wikipedia.org/wiki/Bill_Gates) asked hobbyists to stop sharing BASIC source code:

> "Why is this? As the majority of hobbyists must be aware, most of you steal your software. Hardware must be paid for, but software is something to share. Who cares if the people who worked on it get paid?"\
> — Bill Gates, [An Open Letter to Hobbyists.](https://en.wikipedia.org/wiki/An_Open_Letter_to_Hobbyists)

Amidst the growing debate over software ownership, [Richard Stallman](https://en.wikipedia.org/wiki/Richard_Stallman), a research assistant at MIT's AI laboratory, found himself in a personal battle. He was frustrated by his inability to modify the source code of his newly installed Xerox printers. He believed such restriction to be "a crime against humanity:"

> "If you cook, you probably exchange recipes and share them with your friends, which they are free to change as they wish. Imagine a world, where you can't change your recipe because somebody went out of their way to set it up so that its impossible to change. And if you try to share the recipe with your friends, they would call you a **pirate** and put you in prison."\
> — Richard stallman, in a [documentary](https://www.youtube.com/watch?v=XMm0HsmOTFI)

In a 1983 [email](https://groups.google.com/g/net.unix-wizards/c/8twfRPM79u0), he declared his ambition to work on a free alternative to Unix called [GNU:](https://www.gnu.org/)

![GNU announcement](img/overview/gnu-announcement.jpg)
**Richard Stallman, and his [email announcement](https://groups.google.com/g/net.unix-wizards/c/8twfRPM79u0) of the GNU project.**

GNU is an in-your-face take on Unix and [recursively](https://en.wikipedia.org/wiki/Recursion) stands for “GNU's Not Unix". He decided to make the operating system compatible with Unix because the overall design was already proven and portable, and compatibility would make it easy for Unix users to switch from Unix to GNU.

As Richard explains, free software goes beyond just the cost aspect:

> "Free software", I should explain, refers to freedom, not price. It's unfortunate that the word "free", in english, is ambiguous - it has a
> number of different meanings. One of them means "zero price", but another meaning is "freedom".
> So think of "free speech", not "free beer".

> 🎦 WATCH: [Richard Stallman talks about Free Software and it's impact on the society.](https://www.youtube.com/watch?v=Ag1AKIl_2GM)

GNU started in January 1984. As part of this work, Richard wrote the [GNU General Public License](https://en.wikipedia.org/wiki/GNU_General_Public_License) (GPL). By 1990, GNU had either found or written all the major components for the operating system except one — the kernel.

Coincidentally, [Linus Torvalds](https://en.wikipedia.org/wiki/Linus_Torvalds), a computer science student, was developing a kernel called Linux:

![Linux announcement](img/overview/linux-announcement.jpg)
**Linus Torvalds, and his [email announcement](https://groups.google.com/g/comp.os.minix/c/dlNtH7RRrGA/m/SwRavCzVE7gJ) of Linux.**

The first responses arrived within hours, several hundred joined the development over the course of next year. Linux was released under the GPL license, which completed GNU/Linux operating system.

During this course, Linux practically laid the blueprint for software development based on social consensus:

> "Very early in 1992, suddenly, I didn't know everybody anymore. It was no longer me and couple of friends. It was me and hundreds of people. That was a big step."\
> — Linus Torvalds

These diverse range of contributors, from individual enthusiasts to major corporations, collaborated to improve the kernel, fix bugs, and implement new features. It practically laid the blue print for what would later shape the open-source software movement.

The open-source movement diverges from the free software movement, focusing more on the practical benefits of accessible source code. This approach offered a balance between community-driven innovation and commercial viability which led to widespread business adoption. Free and open-source software (FOSS) is an inclusive umbrella term for free software and open-source software.

The GNU/Linux stands as a testament to the idea that software should empower, not restrict, its users.

> 🎦 WATCH: [Revolution OS: A documentary about GNU/Linux.](https://www.youtube.com/watch?v=k0RYQVkQmWU)

## Cypherpunks write code

Since the end of World War II, governments a enjoyed stranglehold on advancements in cryptography, and guarded it accordingly. In the US,
encryption technology was controlled under the [Munitions List](https://en.wikipedia.org/wiki/United_States_Munitions_List). This meant that the [National Security Agency](https://en.wikipedia.org/wiki/National_Security_Agency) had a keen interest in cryptographic advancements.

When the NSA received a copy the RSA paper from MIT, they attempted to classify the research but eventually allowed publication:

![NSA cryptology debate](img/overview/nsa-cryptology-debate.jpg)
**NSA's response from a [2009 request under the Freedom of Information Act.](https://cryptome.org/2021/04/Joseph-Meyer-IEEE-1977.pdf)**

NSA's approach opened itself up for considerable public criticism when a personal letter from Joseph Mayer, an NSA employee, written to IEEE noting cryptology publications required government approval was published.

This marked the genesis of [Crypto Wars](https://en.wikipedia.org/wiki/Crypto_Wars) between the government and cryptography advocates.

![NSA cryptology debate](img/overview/nsa-crypto-wars.jpg)
**A Science magazine [publication](https://www.science.org/doi/10.1126/science.197.4311.1345) about the cryptology debate.**

The government's attempts to undermine cryptography were viewed as a means of surveilling public communications.

![NSA cryptology debate](img/overview/wire-tap-surveillance.jpg)
**A Science magazine [publication](https://www.science.org/doi/10.1126/science.199.4330.750) about wire tapping, and a related Banksy street art in England.**

Research on cryptography as a means to secure communication continued to evolve through the 1980s.

In 1985, cryptographer [David Chaum](https://en.wikipedia.org/wiki/David_Chaum) published his breakthrough paper [“Security without Identification: Transaction Systems to Make Big Brother Obsolete,”](https://dl.acm.org/doi/pdf/10.1145/4372.4373) in which he described schemes for transactions that provide security and privacy. He also presented the radical idea of “a digital pseudonym” for individuals using cryptography.

![David Chaum](img/overview/david-chaum.jpg)
**David Chaum, and his paper.**

Crypto adoption for the general public was propelled by [Pretty Good Privacy](https://en.wikipedia.org/wiki/Pretty_Good_Privacy) (PGP), an an encryption program developed by Phil Zimmermann in 1991. PGP allowed individuals to secure their communications and data with strong encryption.

The Crypto Wars continued in 1993 when Zimmermann became the subject of a criminal investigation by the US Customs Service for allegedly violating export restrictions on cryptographic software.

In an iconic move, he published the entire source code of PGP in a [hardback book](https://philzimmermann.com/EN/essays/BookPreface.html) arguing that the export of books is protected by the [First Amendment](https://en.wikipedia.org/wiki/First_Amendment_to_the_United_States_Constitution). These books were exported from the USA in accordance with US Export Regulations, and the pages were then scanned and OCR-ed to make the source available in electronic form. In a show of support, some activists printed source code on t-shirts.

The case was dropped in 1996.

> "I used to feel like I was a flea on the back of a T-Rex. Now I feel I might be a small yapping poodle on the back of a T-Rex."\
> — Phil Zimmermann

![Phil Zimmermann ](img/overview/phil-zimmermann.jpg)
**Phil Zimmermann, a t-shirt sporting the RSA source code, and a European volunteer scanning the PGP book; more than [70 people from all over Europe worked for over 1000 hours to make the PGP release possible outside US.](https://www.pgpi.didisoft.com/pgpi/project/scanning/)**

In early 1992, the same week was PGP 2.0 released, three Bay Area engineers— [Eric Hughes](<https://en.wikipedia.org/wiki/Eric_Hughes_(cypherpunk)>), [Timothy C. May](https://en.wikipedia.org/wiki/Timothy_C._May), and [John Gilmore](<https://en.wikipedia.org/wiki/John_Gilmore_(activist)>) — got together to start a mailing list named [Cypherpunk](https://mailing-list-archive.cryptoanarchy.wiki/) (cipher + [cyberpunk](https://en.wikipedia.org/wiki/Cyberpunk)).

Cypherpunk evolved into a defining movement, with over [700 activists and rebels, including Zimmerman](https://mailing-list-archive.cryptoanarchy.wiki/authors/notable/) ready to fight back with code:

> "Cypherpunks write code. We know that someone has to write software to defend privacy, and we're going to write it.
> [...]
> Cypherpunks are therefore devoted to cryptography. Cypherpunks wish to learn
> about it, to teach it, to implement it, and to make more of it. Cypherpunks know that
> cryptographic protocols make social structures. Cypherpunks know how to attack a
> system and how to defend it. Cypherpunks know just how hard it is to make good cryptosystems."\
> — Eric Hughes

![Phil Zimmermann ](img/overview/cypherpunks-write-code.jpg)
**Tim, Eric, and John (top). Eric's cypherpunk [email](https://mailing-list-archive.cryptoanarchy.wiki/archive/1992/09/fdf9c19e77ec3f1a9bbc6bc19266d565b89d19dbd0ad369f5a2e800af3fc9558/) (bottom). [The Cypherpunk's Manifesto](https://www.activism.net/cypherpunk/manifesto.html) (right).**

During a 1994 conference, Tim [described](https://web.archive.org/web/20240415133242/http://www.kreps.org/hackers/overheads/11cyphernervs.pdf) Cypherpunks' core beliefs:

> There is nothing official (not much is), but there is an emergent, coherent set of
> beliefs which most list members seem to hold:
>
> - that the government should not be able to snoop into our affairs
> - that protection of conversations and exchanges is a basic right
> - that these rights may need to be secured through _technology_ rather than
>   through law
> - that the power of technology often creates new political realities (hence the list
>   mantra: "Cypherpunks write code")

In his 1988 ["Crypto Anarchist Manifesto,"](https://groups.csail.mit.edu/mac/classes/6.805/articles/crypto/cypherpunks/may-crypto-manifesto.html) Tim introduced the political philosophy of "Crypto anarchism," which opposes all forms of authority and recognizes no laws except those described by cryptography and enforced by code.

![Crypto Anarchist Manifesto](img/overview/crypto-anarchy.jpg)
**Anarchism, and Tim May's Crypto Anarchist Manifesto.**

The manifesto envisioned anonymous digital transactions as a cornerstone of individual liberty.

The missing piece: **A cryptonative-native [digital currency.](https://en.wikipedia.org/wiki/Digital_currency)**

> 🎦 WATCH: [Tim reflects on 30 years of crypto anarchy.](https://www.youtube.com/watch?v=TdmpAy1hI8g)

## Search for the missing piece

Throughout the '90s cryptopunks made several attempts at creating a digital currency.

In 1990, David Chaum introduced [DigiCash](https://en.wikipedia.org/wiki/DigiCash) providing the first glimpse of an anonymous digital economy. However, it relied on existing financial infrastructure and was largely centralized. Ultimately, DigiCash filed for bankruptcy in 1998.

![DigiCash Homepage](img/overview/digicash.jpg)
**DigiCash Homepage.**

E-gold emerged later in 1996 backed by physical gold held in reserve. At its peak, e-gold had [3.5 million registered accounts](https://web.archive.org/web/20061109161419/http://www.e-gold.com/stats.html) and facilitated transactions worth billions of dollars annually. However, in 2009, transfers were suspended due to legal issues.

Later schemes focused on moving away from collateral such as gold instead scarcity was digitally controlled. In 1998, [Wei Dai](https://en.wikipedia.org/wiki/Wei_Dai) proposed [B-money](https://web.archive.org/web/20220303184029/http://www.weidai.com/bmoney.txt) powered by a cryptographic function to create money. In 2005, [Nick Szabo](https://en.wikipedia.org/wiki/Nick_Szabo) designed [BitGold](https://web.archive.org/web/20240329075756/https://unenumerated.blogspot.com/2005/12/bit-gold.html) but was never implemented. Neither successfully garnered mainstream adoption but their designs influenced what would eventually make digital currency a reality - Bitcoin.

![Wei Dai and Nick Szabo](img/overview/wei-dai-nick-szabo.jpg)
**Wei Dai and Nick Szabo.**

## Bitcoin

The 2008 financial crisis revived interest in digital currency experiments and especially brought BitGold back into the conversation.

A solution to the open problem of how to achieving consensus without a leader was introduced in a 2008 paper titled ["Bitcoin: A Peer-to-Peer Electronic Cash System"](https://bitcoin.org/bitcoin.pdf) by the pseudonymous author [Satoshi Nakamoto](https://en.wikipedia.org/wiki/Satoshi_Nakamoto). Bitcoin established itself as a distributed ledger system where data is cryptographically linked in chronological blocks. It also became the first decentralized digital currency, operating without underlying collateral, and eliminating the need for trusted third-party intermediaries like banks.

![A statue dedicated to Satoshi, and Bitcoin announcement post.](img/overview/satoshi-and-bitcoin.jpg)
**A statue dedicated to Satoshi, and Bitcoin announcement post.**

Bitcoin is also the largest socio-economic experiment the world has ever seen:

> "When Satoshi Nakamoto first set the Bitcoin blockchain into motion in January 2009, he was
> simultaneously introducing two radical and untested concepts. The first is the 'bitcoin', a decentralized
> peer-to-peer online currency that maintains a value without any backing, intrinsic value or central issuer. So
> far, the 'bitcoin' as a currency unit has taken up the bulk of the public attention.
>
> However, there is also another, equally important, part to Satoshi's grand experiment: the concept of a proof of
> work-based blockchain to allow for public agreement on the order of transactions."\
> — Vitalik Buterin

[Several](https://web.archive.org/web/20230404234458/https://www.etoro.com/wp-content/uploads/2022/03/Colored-Coins-white-paper-Digital-Assets.pdf) [attempts](https://en.wikipedia.org/wiki/Namecoin) were made to build applications on top of Bitcoin's network to leverage the newly created digital currency. However, for this purpose Bitcoin's network proved primitive, and the applications were built using complex and not very scalable workarounds.

Ethereum emerged as a solution to address these challenges.

## The Ethereum world computer

In 2012, [Vitalik Buterin](https://en.wikipedia.org/wiki/Vitalik_Buterin) and Mihai Alisie founded [Bitcoin Magazine](https://en.wikipedia.org/wiki/Bitcoin_Magazine) - the first serious publication dedicated to digital currencies. Vitalik soon discovered the limitations of Bitcoin and [proposed a platform](https://web.archive.org/web/20150627031414/http://vbuterin.com/ultimatescripting.html) that would support generalized financial applications.

In 2014, with the help of [Gavin Wood](https://en.wikipedia.org/wiki/Gavin_Wood), the [design of Ethereum was formalized](https://ethereum.github.io/yellowpaper/paper.pdf).

![Vitalik, Jeff, and Gavin working on Ethereum.](img/overview/ethereum-launch.jpg)
**Vitalik, Jeff, and Gavin working on Ethereum.**

On July 30, 2015, Ethereum [went live](https://etherscan.io/block/1) as a platform aimed at building tools for a self-sovereign economy using digital currency.

As of the time of writing, Ethereum has a market capitalization of **$400 billion.**

> 📄 READ: [Vitalik's post about the origin of Ethereum.](https://vitalik.eth.limo/general/2017/09/14/prehistory.html)

> 🎦 WATCH: [Mario Havel talks about the Ethereum philosophy.](https://streameth.org/ethereum_protocol_fellowship/watch?session=65d77e4f437a5c85775fef9d)

> 📄 READ: [Evolution of Ethereum.](/wiki/protocol/history.md)

## Resources

- 📄 Computer History Museum, ["The history of Computer Networking"](https://www.computerhistory.org/timeline/networking-the-web/)
- 📄 Wikipedia, ["ARPANET"](https://en.wikipedia.org/wiki/ARPANET)
- 📘 Brian K., ["Unix: A History and a Memoir"](https://www.amazon.com/dp/1695978552)
- 📄 CryptoCouple, ["A History of The World’s Most Famous Cryptographic Couple"](https://cryptocouple.com/)
- 📄 Steven E., ["The Day Cryptography Changed Forever"](https://medium.com/swlh/the-day-cryptography-changed-forever-1b6aefe8bda7)
- 📄 GNU, ["Overview of the GNU System"](https://www.gnu.org/gnu/gnu-history.en.html)
- 📄 Steven V., ["A look back at 40 Years of GNU and the Free Software Foundation"](https://www.zdnet.com/article/40-years-of-gnu-and-the-free-software-foundation/)
- 📄 David C., [“Security without Identification: Transaction Systems to Make Big Brother Obsolete”](https://dl.acm.org/doi/pdf/10.1145/4372.4373)
- 📄 Steven L., ["Wired: Crypto Rebels"](https://web.archive.org/web/20160310165713/https://archive.wired.com/wired/archive/1.02/crypto.rebels_pr.html)
- 📄 Arvind N., ["What Happened to the Crypto Dream?"](https://www.cs.princeton.edu/~arvindn/publications/crypto-dream-part1.pdf)
- 📄 Satoshi N., ["Bitcoin: A Peer-to-Peer Electronic Cash System"](https://bitcoin.org/bitcoin.pdf)
- 📄 Harry K. et al, ["An empirical study of Namecoin and lessons for decentralized namespace design"](https://www.cs.princeton.edu/~arvindn/publications/namespaces.pdf)
- 📄 Nick S,, ["Formalizing and Securing Relationships on Public Networks"](https://web.archive.org/web/20040228033758/http://www.firstmonday.dk/ISSUES/issue2_9/szabo/index.html)
- 📄 Nick S., ["The Idea of Smart Contracts"](https://web.archive.org/web/20040222163648/https://szabo.best.vwh.net/idea.html)
- 📄 Vitalik B., ["Ethereum Whitepaper"](https://ethereum.org/content/whitepaper/whitepaper-pdf/Ethereum_Whitepaper_-_Buterin_2014.pdf)
- 📄 Vitalik B., ["Ethereum at Bitcoin Miami 2014"](https://www.youtube.com/watch?v=l9dpjN3Mwps)
- 🎥 Gavin Wood, ["Ethereum for Dummies"](https://www.youtube.com/watch?v=U_LK0t_qaPo)
