---
name: literature-reviewer
version: "1.0"
description: |
  科学论文文献检索与综述撰写专家。当用户需要搜索相关文献、撰写文献综述、
  识别研究空白、整理文献笔记时使用此 Skill。
  触发词：文献综述、文献检索、研究空白、文献整理、literature review。
read_when:
  - 用户需要撰写文献综述或文献回顾
  - 用户需要整理和分析多篇文献
  - 用户需要识别研究领域的研究空白
  - 用户提供了多篇文献摘要需要归类分析
---

# Literature Reviewer — 文献检索与综述撰写

本 Skill 负责文献检索策略生成、文献综述撰写和研究空白识别。不执行实际的网络文献检索（用户自行检索后将结果提供），而是对用户提供的文献材料进行分析和综合。

## 工作流程

### 模式一：文献综述撰写

当用户提供多篇文献材料时：

1. 读取 `paper/metadata.json` 获取论文类型和研究方向
2. 读取 `paper/literature.md`（如已存在，追加而非覆盖）
3. 接收用户提供的文献材料（摘要、笔记、PDF 文本等），支持以下输入方式：
   - 直接粘贴文献摘要
   - 提供本地文件路径（PDF/txt/md）
   - 提供文献管理软件导出的格式
4. 对文献进行分析和归类：
   - 按主题/方法/结论分组
   - 识别各文献之间的共识与分歧
   - 提炼关键发现和趋势
5. 识别研究空白（Research Gap）：
   - 明确标注"哪些问题已有较多研究"
   - 明确标注"哪些问题研究不足或存在争议"
   - 明确标注"哪些问题尚未被研究"
6. 根据论文类型选择综述结构：
   - **英文期刊论文**：简短文献回顾，嵌入 Introduction 中（~500-1000词）
   - **中文期刊论文**：简短文献回顾（~1000-2000字）
   - **中文学位论文**：独立文献综述章节（~5000-15000字）
7. 撰写文献综述草稿，输出到 `paper/literature.md`

### 模式二：检索策略生成

当用户需要检索文献但尚未检索时：

1. 根据研究方向生成多组检索词（中英文各 3-5 组）
2. 推荐适合的数据库：
   - 综合类：Web of Science, Scopus, Google Scholar
   - 医学类：PubMed, Embase, Cochrane Library
   - 工程类：IEEE Xplore, Engineering Village
   - 中文：CNKI, 万方, 维普
3. 提供检索式构建建议（布尔逻辑、字段限定等）
4. 将检索策略保存到 `paper/literature.md` 的头部

### 模式三：文献笔记整理

当用户提供了零散的文献笔记时：

1. 将用户的文献笔记结构化，统一格式
2. 为每篇文献提取：作者、年份、研究目的、方法、主要发现、局限性
3. 按主题归类，写入 `paper/literature.md`

## 文献综述结构模板

### 英文期刊论文版（嵌入 Introduction）

```
[大背景：1-2段，介绍研究领域的重要性和整体状况]

[小背景：1-2段，聚焦到具体研究问题，介绍相关研究进展]

[研究空白：1段，明确指出当前研究中尚未解决的问题]
   "While previous studies have demonstrated X, little is known about Y..."
   "Despite the growing interest in Z, the mechanism underlying... remains unclear."

[本文目的：1-2句，过渡到本研究的目的]
   "To address this gap, the present study aims to..."
```

### 中文学位论文版（独立章节）

```
1. [主题一]研究现状
   1.1 [子主题]的国内外研究进展
   1.2 [子主题]的主要方法和结论

2. [主题二]研究现状
   2.1 ...

3. 现有研究的不足与本研究的切入点
   3.1 已有研究的主要贡献
   3.2 存在的问题与争议
   3.3 本研究拟解决的科学问题
```

## 语言规范

### 英文写作约束

- 避免简单罗列文献（"Smith (2020) found X. Jones (2021) found Y. Lee (2022) found Z."）
- 采用主题式综合（将多篇文献按主题归类讨论）
- 使用准确的转述动词：demonstrate, suggest, indicate, argue, contend, propose
- 区分强因果（demonstrate, show）和弱关联（suggest, indicate, may）
- 引用时标注局限性：将"X has been shown to..."改为更精确的表述

### 中文写作约束

- 使用"已有研究表明"而非"众所周知"
- 使用"部分学者认为"而非"大家普遍认为"
- 避免过度概括：具体说明哪些研究支持某个结论
- 使用学术书面语，避免口语化表达
- 区分"证明了"（强因果）和"提示了/暗示了"（弱关联）

## 输入输出

- 输入：文献材料（粘贴文本/文件路径/文献笔记）
- 输出：文献综述草稿 → `paper/literature.md`

## 文件读写约定

| 操作 | 文件 |
|------|------|
| 读取 | `paper/metadata.json`, `paper/literature.md`（如存在） |
| 写入 | `paper/literature.md` |
| 更新 | `paper/progress.md`（标记文献调研阶段完成） |

## 注意事项

- 如果 `paper/metadata.json` 不存在，提示用户先加载 `paper-coordinator` 初始化项目
- 文献综述中所有引用必须有对应的文献来源，不得编造引用
- 如用户提供的文献材料不足以支撑综述，明确告知缺少什么
- 每篇文献的引用至少应包含作者和年份信息
- 研究空白的识别必须基于提供的文献内容，不得凭空推断
