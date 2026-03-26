# Paper Writing Skills

科学论文写作多智能体 Skill 集 - 基于 WorkBuddy 的生产+验证双轨设计

## 概述

本仓库包含一套用于科学论文写作的 WorkBuddy Skill 集合，采用"生产+验证"双轨设计，覆盖论文写作的完整生命周期。

## Skill 清单（MVP v1.0）

### 协调层
- `paper-coordinator` - 总协调器，进度管理，阶段推荐

### 生产层
- `literature-reviewer` - 文献检索与综述
- `paper-writer` - 统一分章节写作引擎
- `abstract-writer` - 摘要与标题生成
- `paper-polisher` - 语言润色与格式规范

### 验证层
- `verify-content` - 内容准确性与逻辑检查
- `verify-abstract` - 摘要与全文一致性检查

## 安装

1. 克隆本仓库到本地
2. 将 `skills/` 下的文件夹复制到 `~/.workbuddy/skills/` 目录
3. 重新加载 WorkBuddy，即可使用

## 使用

1. 加载 `paper-coordinator` 开始
2. 按照协调器的推荐，依次使用各 Skill
3. 每个阶段完成后，使用对应的验证 Skill 检查

## 设计文档

详见 [DESIGN_DOC.md](./DESIGN_DOC.md)