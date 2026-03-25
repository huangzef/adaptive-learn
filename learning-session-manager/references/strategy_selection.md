---
version: 1.0
owned_by: learning-session-manager
used_by:
  - learning-session-manager
  - mentor-guide
  - goal-decomposer
scope: strategy decision module
---

# 学习策略选择器 (Strategy Selection Module)

根据多因素评分自动选择最优学习策略，当策略差距不足15分时自动生成混合策略。

## 四种学习策略

| 策略 | 核心特征 | 最佳场景 |
|------|----------|----------|
| 线性递进 | 循序渐进 | 零基础 |
| 螺旋上升 | 同一主题多次深入 | 需要深度理解 |
| 项目驱动 | 以真实项目为主线 | 有明确应用目标 |
| 理论优先 | 先建立框架认知 | 系统性学习 |

## 多因素评分公式

```
S_linear  = 综合能力指数×0.3 + 目标清晰度×0.3 + 时间资源×0.2 + (100-基础能力)×0.2
S_spiral  = 综合能力指数×0.3 + 目标清晰度×0.2 + 基础能力×0.3 + 学习动机×0.2
S_project = 目标清晰度×0.35 + 学习动机×0.25 + 时间资源×0.2 + (100-综合能力指数)×0.2
S_theory  = 综合能力指数×0.3 + 基础能力×0.3 + 目标清晰度×0.25 + 时间资源×0.15
```

各因素取值范围均为 0-100。

## 用户偏好处理

- 用户明确选择某策略时：该策略得分 +20 bonus
- bonus 仅在用户主动指定时生效，不适用于系统推荐场景

## 策略差距与混合策略生成

```
gap = S_A - S_B  (S_A >= S_B)

if gap < 15:
    blend_ratio = (S_A - S_B + 15) / 30
    # blend_ratio ∈ [0.5, 1.0]
    # 混合策略 = blend_ratio × Strategy_A + (1 - blend_ratio) × Strategy_B
else:
    直接选择 Strategy_A
```

## 领域权重适配 (8 domains)

| 领域 | 线性递进 | 螺旋上升 | 项目驱动 | 理论优先 |
|------|:--------:|:--------:|:--------:|:--------:|
| 技术/编程 | 1.2 | 1.0 | 1.3 | 0.9 |
| 语言学习 | 1.3 | 1.1 | 0.8 | 0.9 |
| 艺术创意 | 0.9 | 1.2 | 1.1 | 1.0 |
| 体育运动 | 1.1 | 1.0 | 1.2 | 0.7 |
| 职业技能 | 1.0 | 1.0 | 1.4 | 1.0 |
| 学术知识 | 1.1 | 1.1 | 0.9 | 1.3 |
| 生活技能 | 1.2 | 0.9 | 1.1 | 0.8 |
| 考证备考 | 1.3 | 0.9 | 0.8 | 1.2 |

权重乘以对应策略的原始得分后再比较。

## 领域映射表

当用户输入的学习主题不在上述8个标准领域中时，使用以下映射规则：

| 用户输入示例 | 映射领域 | 映射理由 |
|:-------------|:---------|:---------|
| 烹饪、烘焙、料理 | 生活技能 | 日常生活实用技能 |
| 摄影、视频剪辑、后期制作 | 艺术创意 | 创作类技能 |
| 健身、瑜伽、减肥 | 体育运动 | 身体运动类 |
| 投资、理财、股票 | 职业技能 | 实用职业技能 |
| 写作、文案、内容创作 | 艺术创意 | 创作类技能 |
| 心理学、哲学、历史 | 学术知识 | 理论学科 |
| 驾驶、游泳、急救 | 生活技能 | 生存技能 |
| 设计、UI/UX、平面设计 | 艺术创意 | 创作类技能 |
| 数据分析、数据科学 | 技术/编程 | 技术工具类 |
| 产品管理、项目管理 | 职业技能 | 管理技能 |
| 英语、日语、法语 | 语言学习 | 语言类 |
| CPA、CFA、PMP | 考证备考 | 资格认证 |
| 机器学习、深度学习 | 技术/编程 | 技术领域 |
| 沟通技巧、演讲、谈判 | 职业技能 | 软技能 |

**映射优先级**：
1. 精确匹配 → 直接使用
2. 关键词匹配 → 按上表映射
3. 无匹配 → 默认使用"职业技能"权重

## 路径生成适配

| 策略 | 推荐路径类型 |
|------|-------------|
| 线性递进 | unit → module → course（逐层递进） |
| 螺旋上升 | topic → revisit → deepen → master（循环深入） |
| 项目驱动 | project → milestone → deliverable → reflection（项目贯穿） |
| 理论优先 | concept → framework → application → synthesis（框架先行） |
| 混合模式 | 根据 blended strategies 动态组合上述路径元素 |

## 选择流程 (Decision Tree)

```
Step 1: 收集用户输入
        - 综合能力指数 (0-100)
        - 基础能力 (0-100)
        - 目标清晰度 (0-100)
        - 学习动机 (0-100)
        - 时间资源 (0-100)
        - 学习领域 (8选1)
        - 用户偏好 (可选)

Step 2: 计算四项原始得分
        - S_linear_raw
        - S_spiral_raw
        - S_project_raw
        - S_theory_raw

Step 3: 应用领域权重
        - S_linear  = S_linear_raw  × weight_linear[domain]
        - S_spiral  = S_spiral_raw  × weight_spiral[domain]
        - S_project = S_project_raw × weight_project[domain]
        - S_theory  = S_theory_raw  × weight_theory[domain]

Step 4: 应用用户偏好（如有）
        - 如果用户明确选择某策略，该策略 +20

Step 5: 排序选择 Top 2 策略
        - Strategy_A = highest score
        - Strategy_B = second highest

Step 6: 计算策略差距
        - gap = S_A - S_B

Step 7: 决策分支
        ├─ gap >= 15 → 输出 Strategy_A
        └─ gap < 15  → 生成混合策略

Step 8: 生成混合策略（当 gap < 15 时）
        - blend_ratio = (S_A - S_B + 15) / 30
        - 输出格式: "混合: X% Strategy_A + Y% Strategy_B"

Step 9: 生成对应路径规划
        - 根据最终策略类型选择推荐路径模板

Step 10: 输出完整决策结果
```

## 输出格式

```json
{
  "selected_strategies": ["spiral", "linear"],
  "primary_strategy": "spiral",
  "gap": 12.5,
  "blend_ratio": {
    "spiral": 0.75,
    "linear": 0.25
  },
  "final_strategy": "blended: 75% spiral + 25% linear",
  "path_type": "topic → revisit → deepen → master (adapted with linear elements)",
  "scores": {
    "linear": 68.5,
    "spiral": 76.2,
    "project": 54.3,
    "theory": 61.8
  },
  "domain": "技术/编程",
  "reasoning": "spiral和linear得分差距12.5<15，触发混合策略生成"
}
```

## 混合策略计算示例

**输入参数：**
- 综合能力指数: 65
- 基础能力: 45
- 目标清晰度: 70
- 学习动机: 60
- 时间资源: 50
- 学习领域: 技术/编程
- 用户偏好: 无

**Step 1-2: 计算原始得分**

```
S_linear  = 65×0.3 + 70×0.3 + 50×0.2 + (100-45)×0.2
          = 19.5 + 21 + 10 + 11 = 61.5

S_spiral  = 65×0.3 + 70×0.2 + 45×0.3 + 60×0.2
          = 19.5 + 14 + 13.5 + 12 = 59.0

S_project = 70×0.35 + 60×0.25 + 50×0.2 + (100-65)×0.2
          = 24.5 + 15 + 10 + 7 = 56.5

S_theory  = 65×0.3 + 45×0.3 + 70×0.25 + 50×0.15
          = 19.5 + 13.5 + 17.5 + 7.5 = 58.0
```

**Step 3: 应用领域权重 (技术/编程)**

```
S_linear  = 61.5 × 1.2 = 73.8
S_spiral  = 59.0 × 1.0 = 59.0
S_project = 56.5 × 1.3 = 73.45
S_theory  = 58.0 × 0.9 = 52.2
```

**Step 4-5: 排序**

```
Top 1: S_linear  = 73.8
Top 2: S_project = 73.45
```

**Step 6: 计算差距**

```
gap = 73.8 - 73.45 = 0.35 < 15
```

**Step 7-8: 生成混合策略**

```
blend_ratio_linear  = (73.8 - 73.45 + 15) / 30 = 15.35 / 30 ≈ 0.512
blend_ratio_project = 1 - 0.512 = 0.488
```

**最终输出:**

```json
{
  "selected_strategies": ["linear", "project"],
  "primary_strategy": "linear",
  "gap": 0.35,
  "blend_ratio": {
    "linear": 0.512,
    "project": 0.488
  },
  "final_strategy": "blended: 51.2% linear + 48.8% project",
  "path_type": "unit → module → course (adapted with project milestones)",
  "scores": {
    "linear": 73.8,
    "spiral": 59.0,
    "project": 73.45,
    "theory": 52.2
  },
  "domain": "技术/编程",
  "reasoning": "linear和project得分差距0.35<15，触发混合策略生成"
}
```
