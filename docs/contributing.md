# 贡献给协议 Wiki

协议 Wiki 是一个开放的协作项目。无论您是否是 [学习小组](/eps/intro.md) 的一部分，我们都欢迎您的贡献！帮助我们构建有关以太坊核心研发的文档并提高学习资源的可用性。

*我们的目标不是重写其他现有的以太坊文档*，而是为有抱负的核心开发人员和研究人员创建一个有凝聚力的技术资源集合。

创建贡献时，请考虑它是否在其他地方不以其他形式存在。使用它并在文本中引用它，但不要复制其内容。专注于将其添加到一个整体中，并将​​其与相关主题联系起来。 

根据您一路上对协议的了解、您收集的经验以及遗漏的导致您放慢速度的部分来撰写贡献。将其视为对您对等节点的解释，反思您自己作为核心开发人员/研究人员的旅程。

## 贡献

在开始编辑之前，请阅读行为准则、遵循指南并熟悉整个 wiki 结构。 

wiki 源代码托管在 Github 仓库中，地址为 [github.com/eth-protocol-fellows/protocol-studies](https://github.com/eth-protocol-fellows/protocol-studies)。 Github 仓库是主要的协调和托管平台，但 wiki 源代码也镜像在 [radicle](https://app.radicle.xyz/nodes/seed.radicle.garden/rad:zkV49UANVb2w2g5eE4Le197Wuasz) 和 [独立集中主机](https://git.ethquokkaops.io/eth-protocol-fellows/protocol-studies) 上。 

> 该 wiki 由 `wiki-pages` 分支提供服务，该分支定期从 `main` 更新。贡献时，打开 PR 到与更改相关的分支，或打开 `main` 以获得较小的快速修复。来自其他分支的 PR 在合并到 `main` 之前会经过审核，收集的更新然后会被推送到更新 `wiki-pages`。

探索现有问题或针对缺失内容提出新问题，建议改进现有内容或 wiki 前端功能。如果您发现缺失或未完成的内容，请随时打开 PR。首先，检查现有的 PR 或分支，以确保您的工作不是多余的。 

我们的目的不是重建其他现有的 wiki。如果相同的内容在其他地方得到了很好的解释，只需链接它并提供额外的上下文即可。 

### 什么(不)属于这里

本 wiki 的范围仅限于以太坊核心协议基础设施的技术资源。这意味着它的规范、实现、测试、研究或相关工具。 

它**不**涵盖链上协议/dapps、layer 2s/Rollup 或不依赖于核心基础设施的任何其他工具。换句话说，这些内容将由 EIP 涵盖，而不是 ERC 涵盖。

### 结构与协作

该 wiki 应该涵盖以太坊核心协议及其开发的所有重要部分。协议架构和相关主题反映在 wiki 格式中。整个 wiki 位于 `/docs/wiki` 下，`/docs/_sidebar.md` 定义了主要文档结构。 
高层区域被抽象为包含所有子主题的目录。将您的贡献集中于 wiki 本身。 `eps` 目录中的周页面用于每周演示信息，而不是资源的主要位置。 

对于贡献者，我们建议关注相应文档中包含的特定主题。最好拥有一个主题并解决所有细节。创建一个新文档并将主题添加到侧边栏(如果尚不存在)。加入 [discord 服务器](https://discord.gg/8RPnPGEQtJ)，让其他人知道您在群组频道中正在做什么，并与其他贡献者协作撰写相关主题。如果您与多人合作处理重要的内容，您可以在仓库中拥有一个专用分支，以便于协调。 

## 编辑维基

wiki 是常规 Markdown 文件的集合，可以使用 github 界面或您最喜欢的 Markdown 编辑器直接编辑。在做出承诺和开放 PR 时，请提供评论来解释您的贡献。

网络文档版本由 [Docsify](https://docsify.js.org/) 生成。了解其 [config](https://docsify.js.org/#/configuration) 和 [features](https://docsify.js.org/#/plugins) 选项。配置和网页设计也欢迎改进或建议。 

要对配置进行更广泛的编辑或更改，请使用 Docsify 在本地预览网络。你只需要 git 和节点.js。 

安装 docsify，克隆仓库并将其托管在本地。 

```
npm i docsify-cli -g
git clone https://github.com/eth-protocol-fellows/protocol-studies.git
cd protocol-studies
docsify serve ./docs
```

## 修复本地拼写错误
aspell 拼写错误检查器在每个 PR 上运行并检查所有文件，建议在提交拉取请求以解决更改中标记的单词之前在本地运行它。

要在本地检查拼写错误：
1. 为您的平台安装 [aspell](https://www.gnu.org/software/aspell/)。
2. 导航到项目根目录并运行脚本：
```
bash check_typos.sh
```

要修复拼写错误：
1. 打开相关文件并修复任何发现的拼写错误。
2. 更新单词列表：如果标记的单词实际上是项目特定术语，请将其添加到项目根目录中的 `wordlist.txt` 中。
   每个单词应单独列出。
 * 记住：
    * 添加新单词时，NOT 内部或周围必须有任何空格或特殊字符。
    * \`wordlist\` NOT 区分大小写。
    * 使用反引号引用代码变量，以免 \`wordlist\` 膨胀。
    * 使用 HTML 标签 <name> 包围专有名称。 <name>约翰·杜e</name>


## 风格指南

Wiki 页面遵循标准 Markdown，可以通过 Github 或 Docsify 呈现。有关使用它的详细信息，请参阅 Markdown [指南](https://www.markdownguide.org/)。 

本 wiki 的受众是技术人员，内容应反映这一点。有很多技术和文档写作指南可供您学习，例如您可以查看[本讲座](https://www.youtube.com/watch?v=vtIzMaLkCaM) 来开始。

 以下是编写此 wiki 时应遵循的主要准则：

- 以客观、清晰和解释性的语气写作
- 避免不必要的简化，描述技术现实
- 避免使用太长和复杂的句子或段落
    - 使用简洁明了的陈述 
    - 使用块引用、项目符号或图像分解文本 
- 始终链接您的资源并验证它们
- 对于需要枚举的主题使用要点或表格 
- 突出显示关键字以支持扫描和浏览文章
- 提供可视化以更好地解释主题
- 添加图片时，将其添加到图片的目录中，并使用其相对路径
- 使用首字母缩略词或技术术语时，请务必先介绍它 
- 以太坊变化很快，写的内容尽可能面向未来 
- 不要使用法学硕士来生成文本
    - 我们不接受完全由 AI 生成的文本，但我们建议使用它来修复语法或措辞
- 考虑创建记录技术步骤的教程和实践指南
- 在顶部添加推荐阅读，指向您依赖的主题
- 对于数学符号，您可以使用 Katex
- 您可以使用美人鱼图进行可视化

目标是产生一个可信的中性文本，该文本正式、结构良好，并保持清晰的思想进展。内容应该是纯技术性的，不应该浪费空间来介绍高水平/众所周知的概念。介绍性主题是必要的，可以使用比较、历史轶事和具体例子来使复杂的概念更容易理解。


### 内容标准化

维基百科使用美式英语而非英式拼写。所有页面的术语、大小写和命名法都应匹配。使用 [以太坊.org 指南](https://ethereum.org/contributing/style-guide/content-standardization) 作为参考。 

鼓励使用图像和可视化。如果您使用第三方创建的图像，请确保其许可证允许并提供原始图像的链接。要创建您自己的可视化效果，我们建议使用 [excalidraw.com](https://github.com/excalidraw/excalidraw)。 

请随意在合适的地方使用[表情符号](https://docsify.js.org/#/emoji?id=emoji)或[图标](https://icongr.am/fontawesome)，例如在块引用中。 

### 链接资源

添加外部链接时，您可以直接在正文中使用，也可以在页面底部的“资源”部分中使用。

链接资源时使用描述性名称，例如 [inevitableeth.com](https://inevitableeth.com/)，而不是 [this wiki](https://inevitableeth.com/) 等通用短语。

不要让文本中的太多资源让读者不知所措。

链接此 wiki 中的页面时，请使用相对路径，如果它引用页面中的特定主题，请使用标题 ID 的链接。 

对于其他重要链接，请在页面底部添加包含资源列表的部分。资源应该有一个名称或简短描述，并带有指向其存档镜像的链接和替代链接。我们强烈建议添加指向 archive.org 的最新快照的链接。资源部分中的链接示例： 

[JSON-RPC API 参考](https://ethereum.org/en/developers/docs/apis/json-rpc)，[已存档](https://web.archive.org/web/20240117035335/https://ethereum.org/en/developers/docs/apis/json-rpc)

### 页内通知

我们在页面顶部使用块引用通知，为读者提供有关页面内容的适当上下文。 

#### 积极研究

未来可能更新的 Wiki 页面，即涵盖活跃研究主题的页面需要在页面顶部添加通知：

```
* Warning message artifact using the following format:
> [!WARNING]
> This document covers an active area of research, may be outdated at time of reading and subject to future updates, as the design space around evolves.
```

#### 路线图跟踪器

为了帮助用户浏览研究主题，我们使用以下格式的路线图跟踪器：
 ``` 
| Upgrade |    URGE     |   Track   |               Topic               |                                     Cross-references                                      |
|:-------:|:-----------:|:---------:|:---------------------------------:|:-----------------------------------------------------------------------------------------:|
|  ePBS   | the Scourge | MEV track | Endgame block production pipeline | intersection with: [ET](/wiki/research/ET.md), [PEPC](), [IL]() |
```
理想情况下，引用指向本地 wiki 页面。

#### 页面不完整

内容最少、需要更多工作才能涵盖主题的页面需要包含一条通知： 

```
> :warning: This article is a [stub](https://en.wikipedia.org/wiki/Wikipedia:Stub), help the wiki by [contributing](/contributing.md) and expanding it.
```

## 还有别的吗？

此页面也向贡献者开放！对 github 仓库中的风格和指南提出改进建议。 
