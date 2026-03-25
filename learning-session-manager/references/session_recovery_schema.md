---
version: 1.0
owned_by: learning-session-manager
used_by:
  - learning-session-manager
  - mentor-guide
  - socratic-tutor
  - project-tracker
---

# 会话恢复程序 (Session Recovery Schema)

本文件定义 learning-session-manager 技能在会话文件损坏或数据不完整时的检测、恢复和通知流程。

**相关文件**：`references/session_partition_schema.md`（分区格式标准）

---

## 恢复流程图

```
读取会话文件
     │
     ▼
文件是否存在？
 ├── 否 → 创建新会话，进入 INIT
 │
 ▼
文件可读？（非空、编码正确）
 ├── 否 → 尝试尾部追加模式；仍失败则创建新会话
 │
 ▼
frontmatter YAML 可解析？
 ├── 否 → 进入"严重损坏恢复模式"
 │
 ▼
frontmatter 必需字段完整？
 ├── 否 → 填补默认值（见"默认值填补规则"）
 │
 ▼
所有必需分区存在？
 ├── 否 → 从 schema 模板生成缺失分区
 │
 ▼
文件体内容完整？
 ├── 否 → 追加数据异常提示
 │
 ▼
恢复完成，添加 recovery_note，通知用户
```

---

## 第一步：文件存在性检查

### 检查项
1. 使用文件系统 API 检查 `../learning_sessions/` 目录下是否存在会话文件
2. 如果存在多个会话文件，按修改时间取最新的一个
3. 如果不存在任何会话文件 → 正常创建新会话

### 处理

```
if no session file found:
    create_new_session()
    return
```

---

## 第二步：文件可读性检查

### 检查项
1. 尝试打开文件（读模式）
2. 检查文件是否为空（内容长度 > 0）
3. 检查文件编码是否可解码（UTF-8）

### 处理

```
if file empty or unreadable:
    # 尝试以追加模式重新创建（保留学过的内容）
    if can_open_in_append_mode():
        # 文件可写但内容为空，重新初始化
        initialize_empty_session(file)
    else:
        # 文件完全损坏，废弃并新建
        create_new_session()
```

---

## 第三步：frontmatter 解析

### 正常情况
文件头部包含完整的 YAML frontmatter 块：

```markdown
---
session_id: learning_session_20260323_143052_agent_skills
status: CLARIFICATION
created_at: 2026-03-23 14:30:52
topic: "Agent Skills"
---
```

### 损坏情况分类

#### 情况 A：frontmatter 块缺失或破损
文件头部没有 `---` 分隔符，或只有单侧分隔符。

#### 情况 B：YAML 语法错误
```
---                       ← 正常
session_id: abc
status: [CORRUPTED        ← 缺少闭合括号
  some: [1, 2
---
```

#### 情况 C：frontmatter 块被截断
```
---
session_id: abc
status: DECOMP
```
（文件写入时突然中断，无尾部 `---`）

### 处理规则

**情况 A & B & C**：进入"严重损坏恢复模式"

1. 保留文件体内容（`##` 分区标题之后的所有内容）
2. 丢弃损坏的 frontmatter
3. 尝试从文件体推断状态：
   - `## 项目信息` 存在且有子任务 → 推断为 `PROJECT`
   - `## 学习路径图` 存在且有内容 → 推断为 `TEACHING`
   - `## 学习路径图` 存在但为空 → 推断为 `DECOMPOSITION`
   - `## 用户背景` 不存在或为空 → 推断为 `CLARIFICATION`
   - 默认 → 推断为 `INIT`
4. 从文件名提取 session_id（`learning_session_{id}.md` 中的 `{id}` 部分）
5. 无法提取时，生成新 session_id：`learning_session_{timestamp}_recovered`
6. 生成新的 frontmatter 并写入文件头部

---

## 第四步：必需字段验证

### 必需字段列表
| 字段名 | 类型 | 默认值 |
|:-------|:-----|:-------|
| `session_id` | string | 从文件名推断或新生成 |
| `status` | enum | `INIT` |
| `created_at` | string | 当前时间戳 |
| `topic` | string | `"未命名会话"` |

### 默认值填补规则

| 字段缺失 | 填补方式 |
|:---------|:---------|
| `session_id` | 从文件名 `learning_session_{id}.md` 提取 `{id}` 部分；无文件名则生成 `learning_session_{timestamp}_recovered` |
| `status` | 设为 `INIT` |
| `created_at` | 从文件体内容中最早的时间戳提取；无则设为当前时间 |
| `topic` | 设为 `"未命名会话"` |

### 处理

```
if frontmatter parsed successfully:
    if session_id missing:
        session_id = infer_from_filename()
    if status missing:
        status = "INIT"
    if created_at missing:
        created_at = infer_from_file_content() or current_timestamp()
    if topic missing:
        topic = "未命名会话"
```

---

## 第五步：必需分区验证

### 必需分区列表

| 分区标题 | 最低内容要求 |
|:---------|:------------|
| `## 用户背景` | 存在标题行 |
| `## 学习路径图` | 存在标题行 |
| `## 项目信息` | 存在标题行 |
| `## 结课报告` | 存在标题行 |

**参考**：`references/session_partition_schema.md` 第 22-33 行（分区一览表）

### 检查方式
使用正则或字符串匹配搜索文件中是否包含完整的 `## 分区名称` 行。

### 缺失分区处理

| 缺失分区 | 生成模板 |
|:---------|:---------|
| `## 用户背景` | 从 session_partition_schema.md 复制"用户背景"模板（第 37-51 行） |
| `## 学习路径图` | 从 session_partition_schema.md 复制"学习路径图"模板（第 76-90 行） |
| `## 项目信息` | 从 session_partition_schema.md 复制"项目信息"模板（第 113-129 行） |
| `## 结课报告` | 从 session_partition_schema.md 复制"结课报告"模板（第 131-152 行） |

### 处理

```
required_partitions = [
    "## 用户背景",
    "## 学习路径图",
    "## 项目信息",
    "## 结课报告",
]

for partition in required_partitions:
    if partition not in file_body:
        template = get_partition_template(partition)
        insert_partition(partition, template)
        recovery_log.append(f"分区 [{partition}] 已从 schema 模板重建")
```

### 分区插入位置
按以下顺序插入到文件中（跳过已存在的分区）：

```
文件头部（frontmatter）
---
# 学习会话：[topic]

## 用户背景        ← 位置 1
## 学习路径图      ← 位置 2
## 思考档案        ← 位置 3（可选）
## 学习画像        ← 位置 4（可选）
## 项目信息        ← 位置 5
## 结课报告        ← 位置 6
## 教学复盘        ← 位置 7（可选）
```

---

## 第六步：数据异常标记

### 检测条件
分区存在但内容格式与 schema 不符。例如：
- `## 学习路径图` 内容不是 checkbox list 格式
- `## 学习契约` 存在但缺少必需子字段

### 处理
不修改原始内容，仅在分区开头追加提示行：

```markdown
## 学习路径图

[data_warning]: 格式异常，已尽力保留原始内容，如有疑问请核对 schema 模板。
[原始内容保持不变]
```

---

## 第七步：写入恢复记录

### recovery_note 格式

在 frontmatter 中追加一条恢复记录：

```markdown
---
session_id: learning_session_20260323_143052_agent_skills
status: DECOMPOSITION
created_at: 2026-03-23 14:30:52
topic: "Agent Skills"
recovery_note: "2026-03-24 10:15:32 | 缺失分区 [项目信息] 已从 schema 重建；frontmatter 解析失败，已推断状态为 DECOMPOSITION"
---
```

### recovery_note 字段说明
- 格式：`{恢复时间} | {处理摘要}`
- 摘要中列出：缺失字段填补情况、缺失分区重建情况、状态推断依据
- 如无任何异常，不写入此字段

### recovery_note 使用规范

> **重要**：`recovery_note` 字段**仅由 learning-session-manager 的恢复流程写入**。

| 使用方 | 是否可写 | 是否可读 | 说明 |
|:-------|:---------|:---------|:-----|
| learning-session-manager | ✅ | ✅ | 恢复流程写入，会话开始时读取 |
| goal-clarifier | ❌ | ✅ | 可读取了解恢复历史 |
| goal-decomposer | ❌ | ✅ | 可读取了解恢复历史 |
| mentor-guide | ❌ | ✅ | 可读取了解恢复历史 |
| socratic-tutor | ❌ | ✅ | 可读取了解恢复历史 |
| project-tracker | ❌ | ✅ | 可读取了解恢复历史 |

**禁止行为**：
- 子技能不得修改或追加 `recovery_note` 内容
- 子技能不得依赖 `recovery_note` 进行业务逻辑判断
- `recovery_note` 仅用于恢复审计和用户通知

---

## 用户通知策略

### 情况一：完全正常
不显示任何提示，直接进入会话。

### 情况二：自动修复（不影响使用）
> "会话文件已加载。你之前的学习进度已完整保留，我们继续吧。"

### 情况三：部分数据丢失
> "会话文件曾出现写入中断，部分内容已自动恢复。如果发现某些里程碑的细节不完整，请告诉我，我可以帮你补充。"

### 情况四：严重损坏，无法推断状态
> "会话文件损坏较为严重，已创建一个新的会话环境。不过，你之前的学习文件仍然保存在 `../learning_sessions/` 目录中。如需恢复历史内容，可以查看原文件并手动补充。"

### 情况五：新建会话（无历史文件）
正常引导进入 INIT 阶段。

---

## 恢复优先级

如恢复过程中出现多个冲突决策，按以下优先级处理：

1. **保留用户数据优先** — 任何恢复操作都不主动删除用户已写入的内容
2. **frontmatter 可推断时优先使用** — 优先从 frontmatter 读取状态，而不是从分区内容推断
3. **schema 模板兜底** — 分区内容无法解读时，用 schema 模板重建，保证后续写入不会因格式错误而失败
4. **状态保守原则** — 无法判断当前状态时，默认设为较低阶段（INIT），让用户重新确认进度

---

## 状态推断优先级规则

当 frontmatter 缺失或损坏时，按以下优先级推断会话状态：

### 推断优先级（从高到低）

| 优先级 | 推断依据 | 推断状态 | 说明 |
|:-------|:---------|:---------|:-----|
| 1 | `## 结课报告` 存在且有完整内容 | `COMPLETED` | 项目已完成，会话结束 |
| 2 | `## 项目信息` 存在且有子任务记录 | `PROJECT` | 正在进行项目实践 |
| 3 | `## 学习路径图` 存在且有已完成的里程碑 | `TEACHING` | 正在学习阶段 |
| 4 | `## 学习路径图` 存在但无完成记录 | `DECOMPOSITION` | 路径图已生成，等待开始学习 |
| 5 | `## 用户背景` 存在且有澄清后的需求 | `CLARIFICATION` | 目标已澄清，等待路径生成 |
| 6 | `## 用户背景` 存在但无需求内容 | `INIT` | 刚创建会话 |
| 7 | 默认兜底 | `INIT` | 无法推断任何信息 |

### 冲突解决规则

当多个条件同时满足时：

```
if 多个分区都有内容:
    取优先级最高的状态
    
示例:
- ## 学习路径图 有内容（优先级3）
- ## 项目信息 有内容（优先级2）
→ 推断为 PROJECT（优先级更高）
```

### 推断流程图

```
开始推断
    │
    ▼
## 结课报告 有完整内容？
├── 是 → COMPLETED
│
    ▼
## 项目信息 有子任务记录？
├── 是 → PROJECT
│
    ▼
## 学习路径图 有已完成的里程碑？
├── 是 → TEACHING
│
    ▼
## 学习路径图 存在但无完成记录？
├── 是 → DECOMPOSITION
│
    ▼
## 用户背景 有澄清后的需求？
├── 是 → CLARIFICATION
│
    ▼
## 用户背景 存在但无内容？
├── 是 → INIT
│
    ▼
默认 → INIT
```

### 与对话内容推断的协调

如果同时存在：
- 文件分区推断结果
- 对话内容推断结果（如用户说"继续上次的学习"）

**处理规则**：
1. 文件分区推断优先（更可靠）
2. 对话内容仅作为辅助确认
3. 两者冲突时，以文件分区推断为准，但向用户确认

**示例**：
```
文件推断: TEACHING
对话推断: PROJECT（用户说"继续做项目"）

处理:
1. 以 TEACHING 为准
2. 向用户确认："检测到你上次正在学习阶段，是否继续学习？还是直接进入项目？"
```
