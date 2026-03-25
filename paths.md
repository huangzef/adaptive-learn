# 路径配置

> 版本：1.0.0
> 更新日期：2026-03-24

本文档定义所有技能文件使用的统一路径规范。

---

## 项目根目录

所有路径相对于**项目根目录**（即本文件所在目录）计算。

```
{PROJECT_ROOT}/
├── learning_sessions/     # 学习会话目录
├── learning_profiles/     # 学习者画像目录
├── tutor/                 # AI导师系统目录
├── knowledge_graphs/      # 知识图谱目录
├── goal-clarifier/        # 目标澄清技能
├── goal-decomposer/       # 目标拆解技能
├── mentor-guide/          # 导师引导技能
├── project-tracker/       # 项目追踪技能
├── socratic-tutor/        # 苏格拉底导师技能
└── paths.md               # 本配置文件
```

---

## 标准路径定义

### 会话文件路径

| 用途 | 路径模板 | 示例 |
|:-----|:---------|:-----|
| 会话文件 | `learning_sessions/learning_session_{id}.md` | `learning_sessions/learning_session_001.md` |
| 会话进度 | `learning_sessions/{session_id}/progress.md` | `learning_sessions/001/progress.md` |

### 学习者画像路径

| 用途 | 路径模板 | 示例 |
|:-----|:---------|:-----|
| 学习者画像 | `learning_profiles/{user_id}.md` | `learning_profiles/user_001.md` |

### AI导师系统路径

| 用途 | 路径模板 | 示例 |
|:-----|:---------|:-----|
| 教师角色 | `tutor/characters/{char_name}.md` | `tutor/characters/teacher_a.md` |
| 系统设定 | `tutor/system.md` | `tutor/system.md` |
| 群聊记录 | `tutor/group_chat.md` | `tutor/group_chat.md` |
| 教师日记 | `tutor/diary.md` | `tutor/diary.md` |
| 项目目标 | `tutor/project_goal.md` | `tutor/project_goal.md` |

### 知识图谱路径

| 用途 | 路径模板 | 示例 |
|:-----|:---------|:-----|
| 领域知识图谱 | `knowledge_graphs/{domain}.md` | `knowledge_graphs/machine_learning.md` |

---

## 技能内部路径

当技能文件需要引用其他路径时，使用以下相对路径：

### 从技能目录引用（如 goal-clarifier/SKILL.md）

```
# 引用会话文件
../learning_sessions/learning_session_{id}.md

# 引用学习者画像
../learning_profiles/{user_id}.md

# 引用其他技能的参考文件
../mentor-guide/references/diagnostic_schema.md
```

### 从会话文件引用（如 learning_sessions/xxx.md）

```
# 引用学习者画像
../learning_profiles/{user_id}.md

# 引用知识图谱
../knowledge_graphs/{domain}.md
```

---

## 路径使用规范

### ✅ 正确做法

1. **使用相对路径**：所有路径使用相对于项目根目录或当前文件的相对路径
2. **使用占位符**：动态部分使用 `{变量名}` 格式，如 `{session_id}`、`{user_id}`
3. **统一分隔符**：使用 `/` 作为路径分隔符（Windows 环境也兼容）

### ❌ 错误做法

1. **硬编码绝对路径**：`D:/skills-creator/...`、`C:/Users/...`
2. **重复路径前缀**：`D:/skills-creator/D:/skills-creator/...`
3. **混用分隔符**：`D:\skills-creator\...`

---

## 迁移指南

如果项目需要迁移到新位置，只需：

1. 保持目录结构不变
2. 所有相对路径自动生效
3. 无需修改任何 SKILL.md 文件

---

## 变量说明

| 变量名 | 说明 | 格式示例 |
|:-------|:-----|:---------|
| `{id}` | 会话ID | `001`、`20260324` |
| `{session_id}` | 会话ID（同上） | `001` |
| `{user_id}` | 用户ID | `user_001` |
| `{char_name}` | 角色名称 | `march7`、`keqing` |
| `{domain}` | 知识领域 | `machine_learning`、`python` |
