# 贡献到协议维基

协议维基是一个开放且协作的项目。无论您是否是[学习小组](/eps/intro.md)的一员，我们都欢迎您的贡献！帮助我们建立文档并提高以太坊核心研发的学习资源可用性。

*我们并不打算重写现有的以太坊文档*，而是创建一个连贯的技术资源集合，供有志于成为核心开发者和研究者的人使用。

在创建贡献时，请考虑是否在其他地方已存在相同内容。可以引用并参考它，但不要复制内容。重点是将其融入大局，将其与相关主题连接起来。

根据您在学习协议过程中学到的知识，您自己的经验，以及那些让您感到困惑的内容来撰写贡献。把它当作是向同行解释，反思您作为核心开发者/研究者的旅程。

## 贡献指南

在开始编辑之前，请阅读行为准则、相关指南，并熟悉整体维基结构。

维基源文件托管在Github仓库 [github.com/eth-protocol-fellows/protocol-studies](https://github.com/eth-protocol-fellows/protocol-studies) 上。Github仓库是主要的协作和托管平台，但维基源文件也在[radicle](https://app.radicle.xyz/nodes/seed.radicle.garden/rad:zkV49UANVb2w2g5eE4Le197Wuasz)和[独立的集中式主机](https://git.ethquokkaops.io/eth-protocol-fellows/protocol-studies)上镜像托管。

> 维基来自 `wiki-pages` 分支，定期从 `main` 分支更新。当贡献时，请对相关分支或小修复使用 `main` 提交PR。来自其他分支的PR在合并到 `main` 之前会被审核，并将收集的更新推送以更新 `wiki-pages`。

探索现有问题或开设新问题，建议改进现有内容或维基前端功能。如果您发现缺失或未完成的内容，可以随时打开PR。首先检查现有的PR或分支，确保您的工作不会重复。

我们并不打算重建其他现有的维基。如果相同内容已经在其他地方得到很好的阐述，只需链接到它并提供额外的上下文。

### 什么是（不）属于此处

此维基的范围限于以太坊核心协议基础设施的技术资源。包括其规范、实现、测试、研究或相关工具。

它**不**涵盖链上协议/dapps、第二层/rollups或任何与核心基础设施无关的工具。换句话说，内容应涵盖EIP，而不是ERC。

### 结构与协作

维基应涵盖以太坊核心协议及其开发的所有重要部分。协议架构及相关主题在维基格式中得到体现。整个维基存放在 `/docs/wiki` 下，`/docs/_sidebar.md` 定义了主要文档结构。
高层领域被抽象为目录，其中包含所有子主题。专注于对维基本身的贡献。`eps` 目录下的周页面主要用于每周演示信息，而非资源的主要存放地。

对于贡献者，建议专注于对应文档中的具体主题。最好能负责一个主题并详细处理所有细节。如果该主题尚未出现，可以创建新文档并将其添加到侧边栏。如果您在多个主题上进行合作，可以在仓库中创建专用分支以便更容易地协调。

## 编辑维基

维基是由常规Markdown文件组成的集合，可以直接通过Github界面或您喜欢的Markdown编辑器编辑。提交和打开PR时，请提供说明您贡献的注释。

Web文档版本由[Docsify](https://docsify.js.org/)生成。了解其[配置](https://docsify.js.org/#/configuration)和[功能](https://docsify.js.org/#/plugins)选项。配置和网页设计也可以进行改进或提供建议。

对于更广泛的编辑或配置更改，您可以使用Docsify在本地预览网页。只需安装git和node.js。

安装docsify，克隆仓库并在本地托管。



```
npm i docsify-cli -g
git clone https://github.com/eth-protocol-fellows/protocol-studies.git
cd protocol-studies
docsify serve ./docs
```

## 风格指南

维基页面遵循标准的Markdown格式，可通过Github或Docsify渲染。有关如何使用它的详细信息，请参考Markdown[指南](https://www.markdownguide.org/)。

本维基的受众是技术人员，因此内容应反映这一点。您可以学习许多关于技术和文档写作的指南，例如，您可以查看[这个讲座](https://www.youtube.com/watch?v=vtIzMaLkCaM)来入门。

以下是撰写此维基时要遵循的主要指南：

- 以客观、清晰和解释性的语气写作
- 避免不必要的简化，描述技术现实
- 避免使用过长和复杂的句子或段落
    - 使用简洁明了的陈述
    - 使用块引用、项目符号或图片来分解文本
- 总是链接您的资源并验证它们
- 对需要枚举的主题使用项目符号或表格
- 高亮关键词以支持扫描和浏览文章
- 提供可视化图形来更好地解释主题
- 在使用首字母缩写或技术术语时，确保先介绍它
- 以太坊发展迅速，写作时尽可能让内容具有未来性
- 不要使用LLM生成文本
    - 我们不接受完全由AI生成的文本，但推荐使用它来修正语法或措辞
- 考虑创建教程和实践指南，记录技术步骤
- 在顶部提供推荐阅读，指向您内容的依赖主题
- 对于数学符号，您可以使用Katex
- 可以使用mermaid图表进行可视化

目标是生成可信、正式、结构良好并保持清晰思路的文本。内容应完全是技术性的，不应浪费空间介绍高层次/已知概念。引言部分可以使用比较、历史轶事和具体例子来使复杂的概念更易于理解。

### 内容标准化

维基使用美式英语，而不是英式英语。术语、大小写和命名应在所有页面中保持一致。可以参考[以太坊.org指南](https://ethereum.org/contributing/style-guide/content-standardization)。

鼓励使用图片和可视化。如果您使用了第三方创建的图像，请确保其许可允许使用并提供原始链接。对于自己创建的可视化，建议使用[excalidraw.com](https://github.com/excalidraw/excalidraw)。

可以在适合的地方使用[emoji](https://docsify.js.org/#/emoji?id=emoji)或[图标](https://icongr.am/fontawesome)，例如在块引用中。

### 资源链接

添加外部链接时，您可以直接在文本中使用它，也可以放在页面底部的“资源”部分。

添加链接时，使用描述性的名称，如[inevitableeth.com](https://inevitableeth.com/)而不是诸如[这个维基](https://inevitableeth.com/)之类的通用短语。

不要让读者在文本中被过多的资源淹没。

当链接到本维基中的页面时，使用相对路径，并且如果它引用了页面中的特定主题，请使用指向标题ID的链接。

对于其他重要链接，在页面底部添加一个资源部分，其中列出资源。资源应有名称或简短描述，并提供链接和其存档镜像的备用链接。强烈建议添加指向archive.org上最新快照的链接。资源部分链接示例：

[JSON-RPC API参考](https://ethereum.org/en/developers/docs/apis/json-rpc)，[已存档](https://web.archive.org/web/20240117035335/https://ethereum.org/en/developers/docs/apis/json-rpc)

### 页面内通知

我们在页面顶部使用块引用通知，提供适当的上下文，告知读者页面内容的相关性。

#### 活跃的研究

维基页面如果涉及未来的更新，即涵盖活跃研究主题的页面，需在页面顶部添加通知：


```
* 警告消息使用以下格式：
> [!WARNING]
> 本文涵盖一个活跃的研究领域，可能在阅读时已过时，并且随着设计空间的发展，可能会进行未来更新。
```

#### 路线图追踪器

为了帮助用户浏览研究主题，我们使用了路线图跟踪，格式如下：
 ``` 
| 升级 |    URGE     |   跟踪   |               主题              |                                     交叉引用                                      |
|:-------:|:-----------:|:---------:|:---------------------------------:|:-----------------------------------------------------------------------------------------:|
|  ePBS   | the Scourge | MEV track |终极区块生产管道 | 和: [ET](/wiki/research/ET.md), [PEPC](), [IL]() |
```
理想情况下，参考指向本地维基页面。

#### 不完整页面

页面内容较少，需要更多工作来覆盖主题时，需要添加通知：



```
> :警告: 本文是一个[存根](https://en.wikipedia.org/wiki/Wikipedia:Stub), 请通过 [贡献](/contributing.md) 帮助维基扩展它。
```


## 其他？

此页面也开放给贡献者！建议改进我们的风格和指南，请在github仓库中提出建议。

