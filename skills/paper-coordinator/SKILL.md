---
name: paper-coordinator
version: "1.0"
description: |
  科学论文写作总协调器。本 Skill 应在论文写作项目的任何阶段首先加载，作为唯一的统一入口。
  它负责初始化共享工作区、管理写作进度、推荐下一步应使用的 Skill。
  当用户提到"开始写论文"、"论文进度"、"下一步该做什么"时触发。
read_when:
  - 用户开始一个新的论文写作项目
  - 用户询问论文写作进度
  - 用户不知道下一步该使用哪个 Skill
  - 用户需要合并各章节为完整论文
  - 用户需要检查验证报告并决定是否回退
---

# Paper Coordinator — 论文写作总协调器

本 Skill 是科学论文写作 Skill 集的唯一入口。不执行具体写作或验证任务，只负责项目管理、阶段推荐和文件合并。

## 核心职责

1. 首次使用时：收集论文信息，初始化共享工作区
2. 后续使用时：读取进度，推荐下一步 Skill，合并全文
3. 验证后：解读验证报告，推荐回退方案

## 工作流程

### 首次初始化

首次使用时，执行以下步骤：

1. 询问用户以下信息（一次性，后续从 metadata.json 读取）：
   - 论文类型：中文学位论文 / 英文期刊论文 / 中文期刊论文
   - 研究方向/主题（简短描述）
   - 是否已有素材（已有文献、已有草稿、已有数据等）
   - 如已有素材，记录文件路径

2. 在当前工作区下创建共享工作区目录 `paper/`，生成以下初始文件：

   **metadata.json**（根据用户回答填写）：
   ```json
   {
     "paperType": "chinese-thesis | english-journal | chinese-journal",
     "language": "zh | en",
     "researchTopic": "用户输入的研究方向",
     "targetJournal": "",
     "citationStyle": "APA | Vancouver | GB/T 7714",
     "currentPhase": "topic",
     "version": "v1",
     "createdAt": "日期",
     "lastModified": "日期"
   }
   ```

   **progress.md**（初始化为全部待处理）：
   ```markdown
   # 论文写作进度

   ## 阶段状态

   | 阶段 | 状态 | 最后更新 |
   |------|------|---------|
   | 文献调研 | 待处理 | - |
   | 研究设计 | 待处理 | - |
   | 数据分析 | 待处理 | - |
   | 论文写作 | 待处理 | - |
   | 摘要标题 | 待处理 | - |
   | 审校润色 | 待处理 | - |

   ## 验证记录

   <!-- 每次验证后在此追加记录 -->
   ```

   **outline.md**（空文件，后续由 paper-writer 或用户填充）：
   ```markdown
   # 论文大纲

   <!-- 由 paper-writer 或用户填充 -->
   ```

3. 如用户已有素材（文献文件、草稿等），读取内容并写入对应的共享工作区文件，跳过对应阶段

4. 根据当前阶段推荐用户加载的第一个 Skill：
   - 阶段"文献调研"：推荐 `literature-reviewer`
   - 阶段"论文写作"：推荐 `paper-writer`
   - 阶段"摘要标题"：推荐 `abstract-writer`
   - 阶段"审校润色"：推荐 `paper-polisher`

### 后续使用

每次加载时，执行以下步骤：

1. 读取 `paper/metadata.json` 和 `paper/progress.md`
2. 向用户展示当前进度摘要（哪些阶段已完成，哪些待处理）
3. 如果 `paper/review-report.md` 中有未处理的致命问题，优先展示回退建议
4. 推荐下一步应加载的 Skill

### 合并全文

当用户请求"合并全文"或"生成完整草稿"时：

1. 按以下顺序读取分章节文件，合并为 `paper/draft-full.md`：
   - `paper/draft-intro.md`（如果存在）
   - `paper/draft-methods.md`（如果存在）
   - `paper/draft-results.md`（如果存在）
   - `paper/draft-discussion.md`（如果存在）
   - `paper/draft-conclusion.md`（如果存在）
2. 如果 `paper/draft-full.md` 已存在，先备份为 `paper/draft-full-v{n}.md`（n 自增）
3. 更新 `paper/progress.md` 中的合并状态

## 阶段与 Skill 对应关系

| 当前阶段 | 推荐生产 Skill | 配套验证 Skill |
|---------|--------------|--------------|
| 文献调研 | `literature-reviewer` | — |
| 论文写作 | `paper-writer` | `verify-content` |
| 摘要标题 | `abstract-writer` | `verify-abstract` |
| 审校润色 | `paper-polisher` | — |

## 回退建议规则

读取 `paper/review-report.md` 时，如果发现致命问题，按以下规则给出回退建议：

- verify-content 报告致命问题 → 建议回退到"论文写作"阶段，使用 paper-writer 修改对应章节
- verify-abstract 报告致命问题 → 建议回退到"摘要标题"阶段使用 abstract-writer 重写，或回退到"论文写作"修改正文

## 输入输出

- 输入：用户的论文信息和当前状态
- 输出：进度报告 + 下一步 Skill 推荐 + 合并后的完整草稿（按需）

## 文件读写约定

| 操作 | 文件 |
|------|------|
| 读取 | `paper/metadata.json`, `paper/progress.md`, `paper/review-report.md` |
| 写入 | `paper/metadata.json`（初始化时）, `paper/progress.md`, `paper/draft-full.md`（合并时） |
| 创建 | `paper/` 目录及所有初始文件（首次使用时） |

## 注意事项

- 本 Skill 不执行任何写作、验证或润色任务，只做协调管理
- 推荐具体 Skill 时，简要说明该 Skill 的作用和使用方式
- 合并全文时保留各章节文件的独立性，不删除分章节文件
- 所有文件路径相对于当前工作区根目录
