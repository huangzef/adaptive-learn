# Knowledge Graphs

跨会话知识图谱，记录已掌握的知识点及其关联。

## 文件结构

- `{domain}.md` — 按领域组织的知识图谱

## 用途

- 跨会话追踪知识点连贯性
- 智能推荐学习路径（跳过已掌握内容）
- 关联相关领域知识

## 知识图谱文件格式

每个 `.md` 文件代表一个领域，遵循以下结构：

```markdown
# {领域名称}

## 知识点（Nodes）

- **知识点名称 A**
  - 定义/描述：...
  - 掌握状态：✅ 已掌握 / 🔶 部分理解 / ❌ 待学习
  - 来源：学习会话记录

- **知识点名称 B**
  - 定义/描述：...
  - 掌握状态：✅ 已掌握
  - 来源：学习会话记录

## 关系（Edges）

- 知识点 A → 依赖 → 知识点 B（"要先学 B 才能学 A"）
- 知识点 A → 关联 → 知识点 C（"A 和 C 有重叠或互补关系"）
- 知识点 B → 前置 → 知识点 D（"学会 B 后可以学 D"）
```

## 示例：machine_learning.md

```markdown
# Machine Learning

## 知识点（Nodes）

- **Supervised Learning**
  - 定义/描述：监督学习是机器学习的一种范式，通过标注数据训练模型
  - 掌握状态：✅ 已掌握
  - 来源：learning_session_20260310_ml_basics

- **Neural Networks**
  - 定义/描述：由多层神经元组成的计算模型，能够学习非线性模式
  - 掌握状态：🔶 部分理解
  - 来源：learning_session_20260312_nn_intro

- **Backpropagation**
  - 定义/描述：神经网络中用于更新权重的反向传播算法
  - 掌握状态：❌ 待学习
  - 来源：-

- **Deep Learning**
  - 定义/描述：使用多层神经网络的机器学习方法
  - 掌握状态：❌ 待学习
  - 来源：-

## 关系（Edges）

- Neural Networks → 包含 → Perceptron（感知机是单层神经网络）
- Supervised Learning → 依赖 → Neural Networks（监督学习可以用神经网络实现）
- Backpropagation → 依赖 → Neural Networks（反向传播用于训练神经网络）
- Deep Learning → 基于 → Neural Networks（深度学习是多层神经网络）
- Deep Learning → 属于 → Supervised Learning（深度学习主要应用于监督学习任务）
```

## 工作流程

当用户完成某个领域的学习后，系统会自动建议将新知识归档到对应的 `.md` 文件：
- 根据会话中掌握的知识点，自动填充到对应领域的知识图谱文件
- 自动推断新知识点与已有知识点的关系
- 供后续学习会话参考，实现跨会话知识连贯性

## 跨域知识推断

当向某个领域文件添加新知识点时，系统会扫描其他领域文件，推断跨域连接建议。

### 推断规则

系统使用以下三种规则进行跨域推断：

- **shared_dependencies（共享依赖）**：两个知识点依赖同一个前置知识
  - 例如：Neural Networks 和 Deep Learning 都依赖线性代数基础

- **similar_patterns（相似模式）**：两个知识点在结构或方法上相似
  - 例如：机器学习中的"梯度下降"与优化理论中的"最速下降法"

- **prerequisite_relations（前置关系）**：一个领域的知识点是另一领域知识点的预备知识
  - 例如：统计学基础 → 机器学习（统计学习理论）

### 推断触发时机

- 新知识点添加到任意领域 `.md` 文件时
- 用户请求关联建议时（对话中主动触发）

### 推断流程

```
1. 新增知识点写入目标领域 .md 文件
2. 系统读取其他所有领域 .md 文件
3. 对每个其他领域的知识点，按三条规则评分
4. 返回得分最高的几条跨域连接建议
5. 用户确认后将关系写入对应文件的 "关系（Edges）" 部分
```

### 示例：添加 Neural Networks 触发跨域建议

假设 machine_learning.md 中已存在 Deep Learning 知识点：

```markdown
# Machine Learning

## 知识点（Nodes）

- **Neural Networks**
  - 定义/描述：由多层神经元组成的计算模型，能够学习非线性模式
  - 掌握状态：🔶 部分理解
  - 来源：learning_session_20260312_nn_intro
```

**系统推断输出：**

```
[跨域建议] 发现 Neural Networks 与以下知识点存在潜在关联：

1. [similar_patterns] Deep Learning（machine_learning.md）
   - 理由：Deep Learning 基于 Neural Networks，两者构成层级关系
   - 建议关系：Deep Learning → 基于 → Neural Networks

是否将以上关系添加到知识图谱？ [Y/n]
```

用户确认后，machine_learning.md 的关系部分将更新为：

```markdown
## 关系（Edges）

- Deep Learning → 基于 → Neural Networks（深度学习是多层神经网络）
- Neural Networks → 包含 → Perceptron
- ...
```

### 跨域建议的显示格式

在知识图谱文件中，跨域连接用 `[跨域]` 标记注明来源领域：

```markdown
## 关系（Edges）

- Neural Networks → 关联 → [跨域] 数学基础（linear_algebra.md）
- Deep Learning → 属于 → [跨域] 人工智能（ai_overview.md）
```
