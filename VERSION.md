# 版本管理规范

> 版本：1.0.0
> 更新日期：2026-03-24

本文档定义所有技能和 schema 文件的版本管理规范。

---

## 版本号格式

采用语义化版本号：`MAJOR.MINOR.PATCH`

| 部分 | 含义 | 变更时机 |
|:-----|:-----|:---------|
| MAJOR | 主版本 | 不兼容的架构变更、核心流程重设计 |
| MINOR | 次版本 | 新增功能、参数、分区，但保持向后兼容 |
| PATCH | 补丁版本 | 文档修正、错误修复、格式调整 |

---

## 当前版本状态

### 技能文件版本

| 技能 | 版本 | 最后更新 |
|:-----|:-----|:---------|
| learning-session-manager | 1.0.0 | 2026-03-24 |
| goal-clarifier | 1.0.0 | 2026-03-24 |
| goal-decomposer | 1.0.0 | 2026-03-24 |
| mentor-guide | 1.0.0 | 2026-03-24 |
| socratic-tutor | 1.0.0 | 2026-03-24 |
| project-tracker | 1.0.0 | 2026-03-24 |

### Schema 文件版本

| Schema | 版本 | 依赖技能 | 最后更新 |
|:-------|:-----|:---------|:---------|
| session_partition_schema | 1.0.0 | learning-session-manager | 2026-03-24 |
| session_recovery_schema | 1.0.0 | learning-session-manager | 2026-03-24 |
| learner_profile_schema | 1.0.0 | learning-session-manager | 2026-03-24 |
| continuous_scoring_engine | 1.0.0 | learning-session-manager | 2026-03-24 |
| strategy_selection | 1.0.0 | learning-session-manager | 2026-03-24 |
| diagnostic_schema | 1.0.0 | mentor-guide | 2026-03-24 |
| milestone_skip_schema | 1.0.0 | mentor-guide | 2026-03-24 |
| project_schema | 1.0.0 | project-tracker | 2026-03-24 |

---

## 版本声明格式

### SKILL.md 头部声明

```markdown
---
name: skill-name
version: 1.0.0
schema_dependencies:
  - schema_name: 1.0.0
description: |
  技能描述
---
```

### Schema 文件头部声明

```markdown
# Schema 名称

> 版本：1.0.0
> 依赖：learning-session-manager >= 1.0.0
> 更新日期：YYYY-MM-DD
```

---

## 兼容性规则

### 向后兼容（MINOR 版本变更）

以下变更**不**需要升级主版本号：
- 新增可选参数
- 新增分区（非必需）
- 新增策略选项
- 文档格式调整

### 破坏性变更（MAJOR 版本变更）

以下变更需要升级主版本号：
- 删除必需参数
- 重命名核心字段
- 修改核心流程
- 更改分区名称

---

## 版本更新流程

1. **修改文件**：更新文件内容
2. **更新版本号**：在文件头部更新版本号
3. **更新依赖声明**：如有依赖变更，更新 `schema_dependencies`
4. **更新本文件**：在"当前版本状态"表格中更新记录
5. **记录变更日志**：在下方"变更日志"中记录

---

## 变更日志

### v1.0.0 (2026-03-24)

**初始版本发布**

- 创建全局路径配置 `paths.md`
- 创建版本管理规范 `VERSION.md`
- 统一所有技能和 schema 的版本号
- 修复路径重复拼接问题
- 消除公式重复定义
- 添加状态推断优先级规则
- 添加领域映射表
- 完善各技能的参数说明

---

## 依赖关系图

```
learning-session-manager (1.0.0)
├── session_partition_schema (1.0.0)
├── session_recovery_schema (1.0.0)
├── learner_profile_schema (1.0.0)
├── continuous_scoring_engine (1.0.0)
└── strategy_selection (1.0.0)

mentor-guide (1.0.0)
├── diagnostic_schema (1.0.0)
└── milestone_skip_schema (1.0.0)

project-tracker (1.0.0)
└── project_schema (1.0.0)

goal-clarifier (1.0.0)
└── (依赖 learning-session-manager)

goal-decomposer (1.0.0)
└── (依赖 learning-session-manager, strategy_selection)

socratic-tutor (1.0.0)
└── (依赖 learning-session-manager)
```
