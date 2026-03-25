# 错误码规范

> 版本：1.0.0
> 更新日期：2026-03-24

本文档定义所有技能使用的统一错误码规范。

---

## 错误码格式

错误码采用 6 位数字格式：`XXYYZZ`

| 部分 | 范围 | 含义 |
|:-----|:-----|:-----|
| XX | 01-99 | 技能模块编号 |
| YY | 01-99 | 错误类型编号 |
| ZZ | 01-99 | 具体错误编号 |

---

## 技能模块编号

| 编号 | 技能模块 |
|:-----|:---------|
| 01 | learning-session-manager |
| 02 | goal-clarifier |
| 03 | goal-decomposer |
| 04 | mentor-guide |
| 05 | socratic-tutor |
| 06 | project-tracker |
| 99 | 通用错误 |

---

## 错误类型编号

| 编号 | 错误类型 | 说明 |
|:-----|:---------|:-----|
| 01 | 文件错误 | 文件不存在、读取失败、写入失败 |
| 02 | 格式错误 | 解析失败、格式不正确 |
| 03 | 参数错误 | 参数缺失、参数无效、参数越界 |
| 04 | 状态错误 | 状态不一致、状态转换无效 |
| 05 | 依赖错误 | 依赖文件缺失、依赖版本不兼容 |
| 06 | 用户输入错误 | 用户输入无效、用户取消操作 |
| 99 | 未知错误 | 未预期的错误 |

---

## 错误码列表

### 01 - learning-session-manager

| 错误码 | 错误名称 | 说明 | 处理建议 |
|:-------|:---------|:-----|:---------|
| 010101 | SESSION_FILE_NOT_FOUND | 会话文件不存在 | 创建新会话 |
| 010102 | SESSION_FILE_READ_ERROR | 会话文件读取失败 | 检查文件权限 |
| 010103 | SESSION_FILE_WRITE_ERROR | 会话文件写入失败 | 检查磁盘空间 |
| 010201 | SESSION_PARSE_ERROR | 会话文件解析失败 | 尝试恢复或重建 |
| 010301 | MISSING_REQUIRED_FIELD | 缺少必需字段 | 使用默认值填充 |
| 010302 | INVALID_STATUS_VALUE | 状态值无效 | 重置为 INIT |
| 010401 | INVALID_STATE_TRANSITION | 状态转换无效 | 检查当前状态 |
| 010402 | STATE_INCONSISTENCY | 状态不一致 | 执行恢复流程 |
| 010501 | SCHEMA_FILE_NOT_FOUND | Schema 文件缺失 | 检查安装完整性 |

### 02 - goal-clarifier

| 错误码 | 错误名称 | 说明 | 处理建议 |
|:-------|:---------|:-----|:---------|
| 020101 | PROFILE_FILE_NOT_FOUND | 学习者画像不存在 | 创建新画像 |
| 020301 | CLARIFICATION_INCOMPLETE | 澄清流程未完成 | 继续澄清 |
| 020601 | USER_CANCELLED_CLARIFICATION | 用户取消澄清 | 保存当前进度 |

### 03 - goal-decomposer

| 错误码 | 错误名称 | 说明 | 处理建议 |
|:-------|:---------|:-----|:---------|
| 030101 | KNOWLEDGE_GRAPH_NOT_FOUND | 知识图谱不存在 | 使用默认模板 |
| 030301 | INVALID_GRANULARITY_VALUE | 颗粒度参数无效 | 使用默认值 60 |
| 030501 | STRATEGY_SELECTION_FAILED | 策略选择失败 | 使用线性策略 |

### 04 - mentor-guide

| 错误码 | 错误名称 | 说明 | 处理建议 |
|:-------|:---------|:-----|:---------|
| 040101 | SESSION_FILE_NOT_FOUND | 会话文件不存在 | 调用 learning-session-manager |
| 040301 | HEAT_METER_OUT_OF_RANGE | 热力计值越界 | 重置为 50 |
| 040401 | MILESTONE_NOT_FOUND | 里程碑不存在 | 检查路径图 |

### 05 - socratic-tutor

| 错误码 | 错误名称 | 说明 | 处理建议 |
|:-------|:---------|:-----|:---------|
| 050101 | TUTOR_CHARACTER_NOT_FOUND | 教师角色不存在 | 使用默认角色 |
| 050102 | PROGRESS_FILE_NOT_FOUND | 进度文件不存在 | 创建新进度文件 |
| 050301 | GOAL_CLARITY_UPDATE_FAILED | 目标清晰度更新失败 | 保持原值 |

### 06 - project-tracker

| 错误码 | 错误名称 | 说明 | 处理建议 |
|:-------|:---------|:-----|:---------|
| 060101 | PROJECT_INFO_NOT_FOUND | 项目信息不存在 | 检查前置阶段 |
| 060301 | INVALID_MILESTONE_SIZE | 里程碑大小无效 | 使用默认值 5 |
| 060401 | PROJECT_ALREADY_COMPLETED | 项目已完成 | 生成结课报告 |

### 99 - 通用错误

| 错误码 | 错误名称 | 说明 | 处理建议 |
|:-------|:---------|:-----|:---------|
| 990101 | FILE_NOT_FOUND | 文件不存在 | 检查路径 |
| 990201 | PARSE_ERROR | 解析失败 | 检查格式 |
| 990301 | INVALID_PARAMETER | 参数无效 | 检查参数 |
| 999901 | UNKNOWN_ERROR | 未知错误 | 记录日志并通知用户 |

---

## 错误处理流程

```
错误发生
    │
    ▼
记录错误码和上下文
    │
    ▼
查找处理建议
    │
    ├── 找到 → 执行处理建议
    │           │
    │           ▼
    │       恢复成功？── 是 → 继续执行
    │           │
    │           否
    │           │
    │           ▼
    └── 未找到 → 通知用户并提供错误码
```

---

## 错误消息格式

### 用户可见消息

```
❌ 操作失败

错误码：010101
说明：会话文件不存在

建议：将为您创建新的学习会话。
```

### 日志记录格式

```json
{
  "timestamp": "2026-03-24T10:30:00Z",
  "error_code": "010101",
  "error_name": "SESSION_FILE_NOT_FOUND",
  "skill": "learning-session-manager",
  "context": {
    "session_id": "learning_session_001",
    "file_path": "../learning_sessions/learning_session_001.md"
  },
  "resolution": "created_new_session"
}
```

---

## 错误恢复策略

| 错误类型 | 恢复策略 |
|:---------|:---------|
| 文件不存在 | 创建新文件或使用默认值 |
| 文件读取失败 | 尝试恢复或使用缓存 |
| 格式错误 | 尝试修复或重建 |
| 参数无效 | 使用默认值 |
| 状态不一致 | 执行恢复流程 |
| 依赖缺失 | 提示用户或降级处理 |
| 用户取消 | 保存当前进度 |

---

## 使用示例

### 在技能中抛出错误

```markdown
**错误处理**：
如果会话文件不存在，抛出错误码 010101：
> ❌ 错误码 010101：SESSION_FILE_NOT_FOUND
> 说明：会话文件 `../learning_sessions/learning_session_001.md` 不存在
> 建议：将创建新的学习会话

然后执行创建新会话流程。
```

### 在技能中捕获错误

```markdown
**错误捕获**：
当收到错误码 010101 时：
1. 不显示错误消息给用户
2. 自动创建新会话文件
3. 继续执行后续流程
```
