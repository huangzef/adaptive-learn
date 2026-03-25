---
version: 1.0
owned_by: mentor-guide
---

# Pre-Assessment Diagnostic Schema

此格式用于记录 mentor-guide Phase 1 的诊断问题回答，供后续 phase 或下次会话参考。

## 存储位置

会话文件的 `## 诊断信息` 分区（由 mentor-guide 在 TEACHING 阶段自动写入）。

## 字段定义

```yaml
diagnostic:
  prior_experience:  # Q1: 先前经验
    # 用户对主题的熟悉程度
    # 选项: 完全没有 / 看过一些 / 用过但不太熟
    value: ""

  learning_goal:  # Q2: 学习目标
    # 用户最希望达成的学习方向
    # 选项: 深入原理 / 动手实现 / 两者都要
    value: ""

  pace_preference:  # Q3: 学习节奏
    # 用户偏好的教学节奏
    # 选项: 快 / 慢
    value: ""

  recorded_at: ""  # ISO 8601 时间戳，记录时间
```

## 说明

| 字段 | 说明 |
|:-----|:-----|
| `prior_experience` | Q1 回答。完全没有=从基础层开始；看过一些/用过但不太熟=从中间层开始；很熟=直接进深层 |
| `learning_goal` | Q2 回答。深入原理=概念驱动；动手实现=实践驱动；两者都要=平衡双线并进 |
| `pace_preference` | Q3 回答。快=减少类比直击要点；慢=多给示例循序渐进 |

## 使用场景

- **自适应教学**：Phase 2 路线图生成时根据 `prior_experience` 调整里程碑数量和深度
- **风格选择**：`learning_goal` 影响提问与示范的比例
- **节奏控制**：`pace_preference` 决定每个里程碑的展开程度
- **跨会话恢复**：下次启动 mentor-guide 时读取此信息，快速定位用户水平

## 示例

```yaml
diagnostic:
  prior_experience:
    value: "看过一些"
  learning_goal:
    value: "两者都要"
  pace_preference:
    value: "慢"
  recorded_at: "2026-03-24T10:30:00+08:00"
```

---

**Usage note**: 此格式由 mentor-guide 在 TEACHING 阶段写入，供后续 phase 或下次会话参考。
