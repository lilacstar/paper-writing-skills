# 科学论文写作多智能体 Skill 集 — 完整设计文档

> 版本：v1.0 | 日期：2026-03-26 | 状态：MVP 实施中

---

## 一、项目背景与目标

### 1.1 项目背景

科学论文写作是一个多阶段、高复杂度的过程，涉及选题、文献调研、研究设计、数据分析、论文撰写、审校润色、投稿准备和同行评审应对等环节。本项目的目标是将科学论文写作的完整流程封装为一套 WorkBuddy Skill 集合。

### 1.2 核心目标

1. **覆盖完整写作流程**
2. **生产+验证双轨制**
3. **共享工作区**
4. **单一入口**
5. **跨设备同步**

## 二、方案演进历史

- **方案一：Skill 集合** → 选为 MVP
- **方案二：Team 模式** → 后续升级
- **方案三：Skill + Automation** → 后续扩展

## 三、完整 Skill 清单（15个）

### 协调层
- `paper-coordinator` — 总入口

### 生产层（9个）
- `topic-explorer`, `literature-reviewer`, `study-designer`, `data-analyst`
- `paper-writer`, `abstract-writer`, `paper-polisher`
- `journal-matcher`, `reviewer-responder`

### 验证层（5个）
- `verify-literature`, `verify-design`, `verify-content`, `verify-abstract`, `verify-response`

## 四、共享工作区设计

见 GitHub 仓库中各 Skill 的 SKILL.md 文件。

## 五、验证 Skill 统一行为约束

1. 默认存疑
2. 禁止讨好
3. 只做规范性检查
4. 问题导向输出
5. 回退判断

## 六、MVP 版本（7个 Skill）

- paper-coordinator
- literature-reviewer
- paper-writer
- abstract-writer
- paper-polisher
- verify-content
- verify-abstract

## 七、迭代策略

SKILL.md 即代码，改了即生效，Git 保版本。