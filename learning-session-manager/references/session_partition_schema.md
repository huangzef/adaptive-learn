---
version: 1.0
owned_by: learning-session-manager
used_by:
  - goal-clarifier
  - goal-decomposer
  - mentor-guide
  - socratic-tutor
  - project-tracker
  - learning-session-manager
storage_location: learning_sessions/{session_id}.md
current_skill: mentor-guide / socratic-tutor / project-tracker（当前活跃的子技能）
current_phase: INIT / CLARIFICATION / DECOMPOSITION / TEACHING / PROJECT / COMPLETED
---

# 分区格式标准 (Session Partition Schema)

会话文件的各分区格式规范。本文件由 `learning-session-manager` 维护，所有写入方和读取方应遵循本schema。

**会话文件位置**：`learning_sessions/{session_id}.md`

## 分区一览

| 分区名称 | 写入方 (Writes) | 读取方 (Reads) | 内容格式 |
|:---------|:----------------|:----------------|:---------|
| `## 用户背景` | goal-clarifier | mentor-guide, socratic-tutor, learning-session-manager | 目标 / 背景 / 兴趣 / 澄清后的需求 / 预期 / 时间 / 偏好 |
| `## 学习契约` | goal-clarifier | mentor-guide, socratic-tutor, learning-session-manager | 承诺人 / 时间承诺 / 交付物承诺 / 签署日期 / 状态 |
| `## 学习路径图` | goal-decomposer | mentor-guide, socratic-tutor, project-tracker, learning-session-manager | checkbox list，里程碑格式：[ ] 里程碑N / [x] 里程碑N |
| `## 诊断信息` | mentor-guide | mentor-guide, learning-session-manager | 先前经验 / 学习目标 / 学习节奏（见 diagnostic_schema.md） |
| `## 思考档案` | socratic-tutor | socratic-tutor | 教学过程中的问答记录和反思 |
| `## 学习画像` | learning-session-manager | mentor-guide | 掌握度追踪表 / 学习风格推断（见 learner_profile_schema.md） |
| `## 项目信息` | mentor-guide | project-tracker, goal-clarifier | 项目目标 / 项目类型 / 项目子任务列表 |
| `## 结课报告` | project-tracker | learning-session-manager | 叙事化结课报告：致用户信 / 成长轨迹 / 项目成果 / 思维方式升级 / 未来之路；同时将项目成果追加写入学习者画像 `../learning_profiles/{user_id}.md` |
| `## 教学复盘` | mentor-guide | (内部使用) | 学习过程分析 / 导师策略评估 / 用户画像更新 / 最终引导 |

## 内容格式详解

### `## 用户背景`

由 goal-clarifier 通过结构化提问生成。

```
## 用户背景

### 澄清后的需求
- 目标：[用户想达成的学习目标]
- 背景：[用户的起始水平/经验]
- 兴趣：[偏好的学习方向]
- 预期：[学习完成后期望的成果]
- 时间：[可用学习时间]
- 偏好：[学习方式偏好]
```

### `## 学习契约`

由 goal-clarifier 在用户确认后写入。

```
## 学习契约

### 承诺人
[用户名]

### 时间承诺
[截止日期] - [承诺的学习时长]

### 交付物承诺
[承诺完成的里程碑或产出物]

### 签署日期
YYYY-MM-DD

### 状态
active / completed
```

### `## 学习路径图`

由 goal-decomposer 生成，checkbox list 格式供用户勾选。

**Checkbox 状态标准：**
- `[ ]` — 未完成
- `[x]` — 已完成
- `[s]` — 已跳过（需在 `## 教学复盘` 中记录跳过原因）

```
## 学习路径图

### 里程碑 1：[里程碑名称]
- [ ] 任务 1.1
- [ ] 任务 1.2

### 里程碑 2：[里程碑名称]
- [ ] 任务 2.1
- [x] 任务 2.1（已完成）

### 里程碑 3：[里程碑名称]
- [s] 任务 3.1（已跳过：太简单，已掌握）
```

### `## 诊断信息`

由 mentor-guide 在 TEACHING 阶段开始时写入，记录用户的学习起点和偏好。

具体格式见 [diagnostic_schema.md](./diagnostic_schema.md)。

```
## 诊断信息

### 先前经验
[用户对主题的熟悉程度]

### 学习目标
[深入原理 / 动手实现 / 两者都要]

### 学习节奏
[快 / 慢]

> 记录时间：YYYY-MM-DD
```

### `## 思考档案`

由 socratic-tutor 在教学过程中持续写入，记录问答和反思。

```
## 思考档案

### 关键问答
**Q**: [问题]
**A**: [回答/引导]

### 反思
[教学策略评估 / 用户理解程度记录]
```

### `## 学习画像`

由 learning-session-manager 在每次教学后更新，记录掌握度和风格。

具体格式见 [learner_profile_schema.md](./learner_profile_schema.md)。

### `## 项目信息`

由 mentor-guide 在 TEACHING 阶段结束时写入，供 project-tracker 在 PROJECT 阶段读取。

```
## 项目信息

### 项目目标
[具体项目目标]

### 项目类型
编程类 / 调研类 / 设计类 / 理论类 / 混合类

### 项目子任务
1. [子任务 1] — 状态：进行中/已完成/待处理
2. [子任务 2] — 状态：待处理
```

### `## 结课报告`

由 project-tracker 在所有子任务完成后生成叙事化报告。写入完成后，同时将项目成果追加到学习者画像 `../learning_profiles/{user_id}.md` 的 `## 项目记录` 分区。

```
## 结课报告

### 致 [用户名] 的一封信
[个性化结课寄语]

### 你的成长轨迹
[学习过程回顾]

### 项目成果
[完成的项目内容]

### 思维方式升级
[从项目中获得的方法论提升]

### 未来之路
[延伸学习方向建议]
```

### `## 教学复盘`

由 mentor-guide 在每次教学后自动生成，内部使用。

```
## 教学复盘

### 学习过程分析
[用户理解难点 / 重点突破]

### 导师策略评估
[使用的教学策略效果评估]

### 用户画像更新
[更新到学习画像的观察]

### 最终引导
[对下一步学习的建议]
```

## 读写依赖图

```
goal-clarifier  ──writes──> ## 用户背景
goal-clarifier  ──writes──> ## 学习契约
goal-decomposer ──writes──> ## 学习路径图
socratic-tutor   ──writes──> ## 思考档案
learning-session-manager ──writes──> ## 学习画像
mentor-guide     ──writes──> ## 项目信息, ## 教学复盘
project-tracker  ──writes──> ## 结课报告
```

**警告**：各技能只能写入自己负责的分区。如需跨区写入，请先确认目标技能是否提供写入接口，否则可能覆盖他人数据。
