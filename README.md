# FMCG Market Research AI Agent

> 基于 Coze + Doubao 搭建的 FMCG 市场调研 AI Workflow  
> 将品类市场、竞争格局、消费者需求等 8 个研究维度并行化，输出结构化市场调研报告

---

## 🧩 Business Problem

在做 Campaign Brief 之前，通常需要先完成以下研究：

- 品类市场规模与趋势
- 竞争格局与代表品牌
- 消费者需求与使用场景
- 社媒内容趋势与内容生态

这些信息分散在大量网页、行业报告、电商平台和社媒内容中，**人工搜索、整理、交叉验证成本较高**，且容易出现统计口径混用、来源质量参差不齐、推断被写成事实等问题。

---

## 💡 Solution：8 模块并行 Research Workflow

将市场研究拆解为 8 个独立研究模块，**并行联网检索 → 跨模块证据综合 → 结构化报告输出**。

```
参数输入（产品 / 品类 / 市场 / 目标消费者）
        ↓
┌─────────────────────────────────────┐
│     8 个并行 Research Modules        │
│  品类市场 │ 竞争格局 │ 代表品牌      │
│  使用场景 │ 消费者需求 │ 目标消费者  │
│  社媒趋势 │ 内容生态               │
└─────────────────────────────────────┘
        ↓
  跨模块洞察综合（Synthesis Layer）
        ↓
  结构化市场调研报告（Report Layer）
        ↓
         输出
```

> ⚠️ 本 Workflow 只负责 **Market Research**，不自动生成 Campaign Brief、达人策略或投放方案。它是后续营销判断的前期 Research Input。

---

## 🗂️ Workflow 架构

### 输入参数（Start 节点）

| 参数 | 说明 | 案例值 |
|------|------|--------|
| `product_keyword` | 产品关键词 | 家乐黑椒汁 |
| `product_category` | 产品品类 | 复合调味汁 |
| `market_country` | 目标市场 | 中国 |
| `target_consumer` | 目标消费者 | 20-35岁城市上班族 |

### 8 个并行研究模块

| 层级 | 模块 | 核心研究问题 |
|------|------|------------|
| 市场层 | `category_market_analysis` | 品类定义、规模、趋势、增长驱动 |
| 市场层 | `competitive_landscape_analysis` | 竞品定位、价格、卖点、差异化方向 |
| 产品/场景层 | `leading_brand_research` | 代表品牌 SKU、卖点、内容表达 |
| 产品/场景层 | `usage_scenario_analysis` | Consumer Job、Pain Point、使用情境 |
| 消费者层 | `consumer_needs_analysis` | 需求、痛点、决策因素、Trade-offs |
| 消费者层 | `target_consumer_research` | 目标人群生活方式、饮食行为、信息渠道 |
| 内容层 | `social_trend_analysis` | 社媒公开内容主题、消费者语言、新兴趋势 |
| 内容层 | `content_ecosystem_analysis` | 平台内容结构、Creator 类型、传播路径 |

> 消费者需求模块与目标消费者模块**刻意拆分**：品类消费者的需求不能直接等同于目标人群的需求，拆分后才能在 Synthesis 层做准确的人群匹配验证。

### Synthesis Layer（第 9 节点）

- 不联网，只基于 8 个模块输出做交叉验证
- 识别跨模块共识与冲突
- 提炼 Consumer Insight、Opportunity Hypotheses
- 标注 Evidence Strength / Evidence Status / Research Gaps

### Report Layer（第 10 节点）

- 不重新搜索，只基于 Synthesis 结果写报告
- 输出结构化 Markdown 市场调研报告

---

## 🔍 AI 可靠性设计：Evidence Validation 机制

这是本项目的核心设计亮点。

初版运行后发现 AI 市场研究容易出现：

- 来源误判（把转载文章当作原始报告）
- 统计口径混用（用复合调味品整体规模代表黑椒汁细分规模）
- 跨模块数字冲突
- 推断被写成确定事实
- 宽口径人群数据被直接套用到目标消费者

为此设计了以下机制：

### Source Tier 来源分级

| Tier | 来源类型 |
|------|---------|
| Tier 1 | 政府、行业协会、上市公司披露、品牌官方资料 |
| Tier 2 | Euromonitor、NielsenIQ、Kantar、艾瑞、艾媒等市场研究机构 |
| Tier 3 | 主流商业媒体对相关研究的报道 |
| Tier 4 | 普通行业媒体、社媒内容、电商页面（用于定性观察）|

> Source Tier 按**实际访问页面**判断，不是按文章内引用了谁判断。

### Evidence Strength（跨模块一致性）

| 强度 | 定义 |
|------|------|
| High | 至少 3 个独立模块方向一致，并有事实或行业趋势支持 |
| Medium | 至少 2 个模块方向一致，但含推断或缺少专项数据 |
| Low | 主要来自单一模块或少量公开观察，需进一步验证 |

### Evidence Status（结论性质）

`已验证事实` / `行业趋势` / `分析推断` / `待验证假设`

> **High Evidence Strength ≠ Verified Fact**  
> 多个模块一致认为"工作日晚餐适合目标人群"，Evidence Strength 可以为 High，但 Evidence Status 仍为**分析推断**，不能写成"已被验证的核心使用场景"。

---

## 📋 案例验证：家乐黑椒汁 × 20-35岁城市上班族

详见 [`docs/case-study.md`](docs/case-study.md)

**输入：**
- 产品：家乐黑椒汁
- 品类：复合调味汁
- 市场：中国
- 目标消费者：20-35岁城市上班族

**运行结果：**
- 运行时长：约 6 分 34 秒
- Token 消耗：159,033
- 完成端到端运行，成功输出完整 8 章节市场调研报告

**报告预览：** [`outputs/knorr-market-research-sample.md`](outputs/knorr-market-research-sample.md)

---

## 🔄 迭代过程

| 问题 | 解决方案 |
|------|---------|
| Synthesis 节点一次输入 8 份长报告，出现 3 分钟执行超时 | 压缩输出结构、控制 JSON 长度、改用更轻量模型 |
| AI 把"复合调味品整体规模"直接用作"黑椒汁细分规模" | 加入 Category Scope Validation，强制区分统计口径 |
| 推断结论被写成确定事实 | 加入 Evidence Status 标注机制 |
| 宽口径人群数据被套用到目标消费者 | 拆分消费者需求与目标消费者两个独立模块 |
| 来源引用层级误判 | 加入 Source Tier 规则，按实际访问页面判断 |

---

## 📁 项目结构

```
fmcg-market-research-ai-agent/
│
├── README.md
├── assets/
│   ├── workflow-overview.png       # Workflow 全局截图
│   ├── workflow-detail.png         # 节点细节截图
│   └── report-preview.png          # 报告输出预览截图
│
├── docs/
│   ├── case-study.md               # 家乐黑椒汁案例详解
│   └── methodology.md              # 研究方法论说明
│
├── prompts/
│   └── prompt-framework.md         # Prompt 设计框架说明
│
└── outputs/
    └── knorr-market-research-sample.md  # 案例报告 Sample
```

---

## 🛠️ 技术栈

| 工具 | 用途 |
|------|------|
| Coze（扣子） | Workflow 搭建与节点管理 |
| Doubao 2.0 Pro | 8 个研究模块主力模型 |
| Doubao 2.0 Lite | Synthesis 节点（优化执行稳定性） |
| 联网问答 / Web Search | 各模块实时联网检索 |
| Prompt Engineering | 差异化模块 Prompt 设计 |
| JSON 结构化输出 | 模块间数据传递 |
| Markdown | 最终报告格式 |

---

## 💼 业务价值

- 将分散的市场信息系统化整合，降低人工搜索和交叉验证成本
- 通过 Evidence Validation 机制减少 AI 幻觉和推断事实化问题
- 输出带有来源分级和证据强度标注的结构化报告，支持后续策略判断
- Workflow 可迁移至其他 FMCG 产品，修改输入参数即可复用

---

## 🔗 Demo

Coze Workflow 链接（需登录）：[knorr_social_market_research](https://www.coze.cn/work_flow?workflow_id=7683513796270391296&space_id=7683495159329603634)

---

## 📌 声明

本项目为个人学习与求职作品，所有研究结论均标注证据强度与证据状态。案例中基于 AI Agent 联网检索的市场数据仅供参考，不构成商业建议。
