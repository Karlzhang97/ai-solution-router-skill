# Enterprise Knowledge and RAG

Use this reference when the user's task depends on internal company knowledge, policies, SOPs, documents, cases, meeting notes, customer records, or permission-controlled content.

> Product names below are 2026-Q2 examples. Always anchor on **the user's existing collaboration stack**; do not push them off-platform unless the existing one genuinely cannot serve.

## Core Rule

Do not treat internal enterprise knowledge as ordinary chat context unless the user only needs a one-off answer from pasted text.

If the task needs **repeated retrieval, citations, permissions, or many internal documents**, explicitly consider a knowledge layer — but apply the difficulty gates before recommending it.

## Data-Form Decision (added v8.2 — read first)

Internal "knowledge" comes in different shapes. **Different shapes have different best routes — do not default everything to RAG.**

| 数据形态 | 例子 | 优先方案 | 难度 |
| --- | --- | --- | --- |
| **结构化表格里的字段** | CRM 客户记录、订单、招聘漏斗、用户评论、SKU 列表 | **AI Table 字段**（飞书多维 AI 字段 / 钉钉 AI 表格 / Notion Autofill / Airtable AI fields）| ★★ |
| **半结构化文档** | 产品手册、SOP、政策、案例库 | **协作平台自带知识搜索 (Tier 1)** | ★ |
| **多源 + 复杂权限 + 频繁问答** | 跨多个知识库的客服 / 销售支持 Agent | **平台 RAG + Agent (Tier 2)** | ★★★ |
| **跨多源 + 自定义评测 + 私有部署** | 法务 / 金融严合规、内部研发知识库 | **自定义 RAG (Tier 3)** | ★★★★★ |

### Anti-Pattern Reminders

- "**500 条客户评论自动分类**" → **AI Table**（不是 RAG）。评论已经是表格里的行，加一列 AI 字段就行。
- "**1000 个候选人简历自动打标**" → **AI Table**（不是 RAG）。简历数据进飞书多维或钉钉 AI 表格，配 AI 字段。
- "**销售群里的反馈整理成表格**" → **AI Table**（先用 IM 机器人 / 表单把数据收进表格，再用 AI 字段处理）。
- "**对一列产品描述批量生成 SEO 摘要**" → **AI Table 自定义 AI 自动填充**。

只有当**数据真的是文档**（PDF / Word / 长文本）且需要 **检索 + 引用 + 权限**，才进入下面的 Tier 1-3。

## Three Tiers (lightest first)

Always recommend the lightest tier that meets the need. Only escalate when there is a hard reason.

### Tier 1: Enterprise-Native Knowledge Search

Use when:

- The user only needs to find answers in internal documents.
- No multi-step execution is needed.
- The company already uses a collaboration suite with built-in AI search.

Recommendation pattern:

```text
Primary route: enterprise knowledge search (built into the user's existing collaboration platform)
Output: a query prompt + 5-10 validation questions with expected source docs.
```

### Tier 2: Knowledge Base / RAG + Workflow or Agent

Use when:

- Internal knowledge must drive drafting, classification, approval routing, customer response, SOP execution, or agent decisions.
- The user wants a bot / workflow / agent that repeatedly uses company knowledge.
- The output needs source references or permission-aware retrieval.

Recommendation pattern:

```text
Primary route: workflow / agent platform with knowledge base integration
Knowledge layer: that platform's knowledge base / RAG
Reusable logic: skill or prompt template
```

### Tier 3: Custom RAG + Coding Agent

Use when:

- Sources are scattered across many systems (CRM + wiki + drive + ticket system + ...).
- Permissions, freshness, retrieval quality, or evaluation need custom design.
- The desired output is a product, dashboard, tool, API, or local demo.

Recommendation pattern:

```text
Primary route: AI coding agent
Knowledge layer: custom RAG pipeline (LlamaIndex / LangChain / custom)
Reusable logic: skill / prompt templates
Output: route to demo-build-branch.md with RAG requirements baked in.
```

## Difficulty Gates (aligned with decision-tree.md)

Before recommending Tier 2 or Tier 3, check:

| # | Gate | YES → continue | NO → downgrade to |
| --- | --- | --- | --- |
| 1 | Query frequency ≥ weekly? | continue | Tier 1, or paste excerpts manually |
| 2 | Documents update regularly? | continue | one-time summary document |
| 3 | Someone can maintain the knowledge index? | proceed with RAG | Tier 1 + manually curated excerpts |

All three YES → Tier 2 or Tier 3 is justified.
Any NO → downgrade to the lighter alternative shown.

## Enterprise Suite Routing Table (stack-aware)

Always anchor on the user's existing collaboration platform first. Only suggest off-platform tools when the existing one genuinely cannot serve.

| 用户主要协作平台 | Tier 1：简单查询 | Tier 2：知识驱动 Agent / 工作流 | 备注 |
| --- | --- | --- | --- |
| 钉钉 (DingTalk) | 钉钉企业知识搜索 / 智能搜索 | 钉钉悟空 + 钉钉知识库 + AI 助理 | 中大型企业，IM-first |
| 飞书 / Lark | 飞书智能伙伴 / Wiki AI 搜索 | 飞书智能伙伴 + aPaaS + 飞书知识库 | 文档生态强，aPaaS 适合做流程 |
| 企业微信 / WeCom | 微盘 + 企微 AI 助手 | 腾讯混元智能体 / 自建知识助手 + 企微回调 | 客户侧触达强；企业内沉淀偏弱 |
| Notion | Notion AI Q&A | Notion AI + 自定义 RAG / Zapier / Make | 文档主仓库就在 Notion 时最自然 |
| Confluence / Atlassian | Atlassian Intelligence / Rovo | Rovo Agents / 自定义 RAG | 工程团队为主 |
| Slack | Slack AI Search | Slack AI + 自定义 Bot / 自定义 RAG | 沟通为主，知识沉淀通常在别处 |
| Microsoft 365 | Microsoft Search / Copilot 检索 | Microsoft 365 Copilot + Power Automate + SharePoint | Office 生态绑得深 |
| Google Workspace | Gemini for Workspace Search | Gemini for Workspace + AppSheet | 海外 SaaS-first 团队 |
| 自建 / 混合 / 散落多处 | 内部搜索（Elastic、自研）| 自定义 RAG + 工作流平台（Dify / 扣子 / n8n）| 需自建评测；最贵但最灵活 |

### 选择原则

1. **场景 1（只查询）**：永远先用平台自带，能用就别上 RAG。
2. **场景 2（驱动执行）**：仍优先平台自带 Agent 能力；只有当能力明显不够（缺工作流引擎 / 缺自定义提示词 / 缺多源融合）才上自定义。
3. **场景 3（定制 App）**：用户应已经回答"为什么不能在现有平台做"，否则不要轻易推荐自建 RAG。

## Platform Neutrality (meta-rule, do not violate)

When recommending specific platforms, follow these rules:

- Use the platform the user already mentioned. If they said "we use DingTalk", use DingTalk examples. If they said "we use Feishu", use Feishu examples.
- If the user has not stated a platform, ask which one they use OR list 2-3 examples across ecosystems rather than defaulting to one.
- Do not embed platform-specific sales language (e.g. pre-written marketing phrases for any vendor).
- If a new collaboration platform appears that's not in the table above, treat it as legitimate and add a row mentally; don't refuse to route.

## Knowledge Requirements Checklist

Before recommending the final architecture, identify:

- **知识源**：钉钉文档、飞书 wiki、Notion、SharePoint、网盘、CRM、工单系统、SOP PDF、电子表格、数据库。
- **检索范围**：全公司 / 部门 / 项目 / 角色 / 用户自带文件。
- **权限规则**：谁能看见什么；是否需要继承原系统权限。
- **引用要求**：标题、URL、段落、时间戳、责任人。
- **新鲜度**：实时 / 每日同步 / 手工上传 / 归档。
- **质量验证**：5-10 个代表性问题 + 期望来源文档。
- **失败兜底**：知识缺失 / 过期 / 冲突时的策略。

## Output Template

```markdown
## 企业知识 / RAG 判断
- 是否涉及内部知识：是 / 否
- 知识用途：查阅 / 问答 / 生成 / 决策 / 执行 / 审批
- 用户主要协作平台：[钉钉 / 飞书 / 企微 / Notion / Confluence / Slack / M365 / Google Workspace / 自建]
- 推荐 Tier：1 / 2 / 3
- 难度门槛：三关是否全过（写出哪关没过及降级方案）
- 推荐主工具：

## 推荐方案
（说明为什么选这个 Tier；如需组合，按层列出：主工具 / 知识层 / 执行层 / 复用层 / 人审层）

## 知识源与权限
| 知识源 | 位置 | 权限 | 更新频率 | 引用要求 |

## 最小验证集
| 测试问题 | 期望来源 | 合格标准 |
```

## Common Mistakes to Avoid

- 在用户还没说"现有平台不够"时就推自定义 RAG。
- 把"客户记录"和"公开文档"放进同一个知识库而不做权限隔离。
- 推荐组合方案但没有写"如何评测"——RAG 不评测 = 不可用。
- 假设用户用钉钉（或飞书 / Notion）——必须先问用户的实际栈。
- 在没有人能维护索引时仍推 RAG——会变成烂尾工程。
