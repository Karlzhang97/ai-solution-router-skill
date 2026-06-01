# Decision Tree (Deep Mode)

> **Use only in Discovery Mode.** The default path is `triage.md`.
> For Fast Path, AI Table, and Schedule overlay rules, use `overlays.md` as the canonical source.
> Tool product names below are 2026-Q2 examples and may change; users may name newer products and you should accept them.

## Core Principle: Minimum Sufficient Solution

Start from the simplest route. Escalate only when there is a hard, testable reason AND the implementation difficulty is justified. Heavier solutions need higher justification; lighter solutions need only a confirmed need.

### Difficulty Ordering (star ratings)

```text
Prompt(★) < Scheduled AI Task(★★) ≈ AI Table(★★) ≈ Browser Agent(★★) < Skill(★★★) < Coding Agent(★★★~★★★★) < RAG(★★★★) < Workflow(★★★★★)
```

Always state the star rating in the recommendation. If a lower-star route can do the job, prefer it.

**8 primary routes** (since v8.2):
1. AI Chat (Prompt) ★
2. **Scheduled AI Task** ★★ (v8.1) — ChatGPT Tasks / 钉钉悟空定时 / 飞书智能伙伴定时 / 豆包定时
3. **AI Table** ★★ (v8.2) — 飞书多维表格 AI 字段捷径 / 钉钉 AI 表格 / Notion AI Autofill / Airtable AI fields
4. Browser Agent ★★
5. Skill ★★★
6. Coding Agent ★★★~★★★★
7. RAG / Enterprise Knowledge ★★★★
8. Workflow / Workflow with AI Nodes ★★★★★

## Step 0: Does this actually need AI? (or need a heavy AI)

Before classifying, check:

- Can a deterministic script, formula, or shortcut do it in under 30 lines? → recommend the script, not AI.
- Is the capability already inside the user's existing SaaS (Notion AI / Linear AI / 飞书智能伙伴 / 钉钉 AI / Cursor AI 等)? → recommend the built-in feature first.
- **Is this a "定时跑一件简单事" task?** Check if the host AI has a built-in scheduler that can do it without a custom Browser Agent or Workflow:
  - ChatGPT **Tasks**
  - 钉钉悟空 **定时任务 / 触发器**
  - 飞书智能伙伴 **定时执行**
  - 豆包 / Kimi / 智谱 **定时**
  - Claude Projects（手动触发）+ Computer Use
  If YES + the task is just "fetch public info / summarize / format / push", → recommend the built-in scheduled task. Do NOT push the user to Browser Agent or Workflow platform. See `references/output-templates.md` § Scheduled AI Task Sub-template.
- **Is this a "对表格里某列批量做提取/分类/总结/翻译/打标" task?** Check if the user's data lives in (or can live in) a table that has AI fields:
  - 飞书 **多维表格 AI 字段捷径**（分类 / 信息提取 / 总结 / 翻译 / 自定义 AI 自动填充）
  - 钉钉 **AI 表格** (AI 字段提取 / AI 智能公式 / AI 智能分析 / AI 仪表盘 / 智能问数)
  - **Notion AI Autofill** (Basic / Custom Agent)
  - **Airtable AI fields** (Categorize / Summarize / Extract)
  - 腾讯文档 / WPS **AI 智能列**
  If YES + the task is "per-row text → per-row structured output" → recommend the AI Table feature. Do NOT push to Chat 数据分析 / Skill / Workflow / Coding Agent for these per-row tasks. See `references/output-templates.md` § AI Table Sub-template.
- Is the bottleneck really 数据 / 权限 / 流程, not 智能? → fix the bottleneck; AI won't save it.
- Is the user a one-time guest of this task? → a one-shot prompt is enough; don't build infrastructure.

If any of these is true, **stop here** with a "no-AI / built-in-AI / scheduled-task / AI-table / one-shot prompt" recommendation. Don't push the user into the full tree.

## Model Structure: Three Independent Decisions

Three decisions, evaluated in **parallel** (not sequentially), then composed:

```text
Decision A: Execution Environment — "What capability does the AI need?"
Decision B: Control Level         — "How much should the AI be constrained?"
Overlay:    Knowledge Source      — "What knowledge does the AI need?"

Final route = Environment × Control × Knowledge
```

Each decision has a **need test** AND a **difficulty gate**.

## Decision A: Execution Environment

Determines WHERE the work physically happens. Hard technical constraint with no subjective judgment.

Four environments since v8.2: **text / table / browser / code**.

### Boundary Questions

**Text ↔ Table** (added v8.2)

```text
"输入是一段段散文本？还是一张/几张表，需要给某些列批量加 AI 处理？"
```

- Free-form text / one input at a time → Text environment
- Tabular data with rows × columns + want AI to fill one or more columns → **Table** environment

**Text ↔ Browser Agent**

```text
"任务所需的信息和操作，能通过粘贴文本给 AI 完成？还是 AI 必须打开网页点击/填写/抓取？"
```

- Can paste → Text environment
- Must interact with web pages → Browser Agent

**Text ↔ Coding Agent**

```text
"交付物是对话里的一段文字？还是必须生成文件/跑脚本/构建可运行的东西？"
```

- Conversation text is enough → Text environment
- Must produce files/code/runnable artifacts → Coding Agent

**Table ↔ Coding Agent** (added v8.2)

```text
"数据量在协作平台表格的承载范围内吗？需要跨行/跨表的复杂分析吗？"
```

- 在飞书多维表格 50 万行 / 钉钉 / Notion / Airtable 限额以内 + 主要是单行 → AI 处理 → **Table**
- 超过表格限额 / 需要跨行复杂统计 / 需要写脚本 → **Coding Agent**

### Difficulty Gates

| Environment | Difficulty | Gate question | YES → | NO → |
| --- | --- | --- | --- | --- |
| Text | ★ | None. Default starting point. | — | — |
| **Table** (v8.2) | **★★** | "用户的数据已经在 / 能放进支持 AI 字段的协作表格里吗？"（飞书多维 / 钉钉 AI 表格 / Notion 数据库 / Airtable）| **AI Table** | data is purely a CSV file → downgrade to Chat 数据分析 or Coding Agent |
| Browser Agent | ★★ | None. Low cost, use when the need is confirmed. | — | — |
| Coding Agent | ★★★~★★★★ | "有人能看懂 AI 生成的代码并验收吗？" | Coding Agent | downgrade to Text + manual operation, note the capability gap |

### Multi-Environment Tasks

When both boundary questions return YES (need both browser AND code):

- **Separable by handoff artifact**: split into sub-tasks. Sub-task A produces a file/data that sub-task B consumes. Route each sub-task independently.
- **Inseparable** (two environments alternate with no clean split): route to Coding Agent (most modern coding agents include browser capabilities) or to a Workflow platform for orchestration.

## Decision B: Control Level

Determines HOW MUCH the AI is constrained. Applies to all environments — text, browser, and coding.

### Three Levels

| Level | Core trait | Who executes |
| --- | --- | --- |
| Prompt | One-off instruction, use and discard | AI alone |
| Skill | Reusable rule package, AI has judgment room | **AI alone** (reads rules, executes autonomously) |
| Workflow | Platform orchestration, multiple actors | Platform coordinates **AI + humans + external systems** |

**The boundary between Skill and Workflow is NOT "whether steps are fixed"** — Skills can have fixed steps too. The boundary is **"whether the AI is the sole executor"**.

### Boundary: Prompt ↔ Skill

| Test | Question | YES → | NO → |
| --- | --- | --- | --- |
| Need | "下次做同样的事，需要重新跟 AI 解释规则/标准/上下文吗？" | check difficulty | **Prompt** |
| Difficulty | "这件事 ≥ 每月做一次，且有明确的规则/案例可以固化？" | **Skill** | Prompt + save a template doc for manual pasting |

### Boundary: Skill ↔ Workflow

The largest difficulty gap (★★★ → ★★★★★), so the gate is strictest.

**Need tests** — hit ANY ONE to enter Workflow territory:

| # | Question | YES → | NO → |
| --- | --- | --- | --- |
| 1 | "过程中需要读写外部系统吗？"（写数据库 / 调 API / 发通知 / 触发其他工具） | continue | Skill |
| 2 | "跳过某步的后果比'输出质量差'更严重吗？"（合规 / 数据不一致 / 钱花错 / 人没被通知到） | continue | Skill |
| 3 | "流程中有必须等人审批 / 确认才能继续的环节吗？" | continue | Skill |
| 4 | **"最终使用者是非技术员工或外部客户？"** (Operator dimension) | continue | Skill |

**Difficulty tests** — must pass ALL to confirm Workflow:

| # | Question | YES → | NO → |
| --- | --- | --- | --- |
| 1 | "任务频率 ≥ 每周？" | continue | Skill + humans handle approvals/integrations manually |
| 2 | "有人能搭建和持续维护这个工作流？" | **Workflow** | Skill + humans handle approvals/integrations manually |

Any need test YES + both difficulty tests YES → **Workflow**.
Otherwise → **Skill + manual fallback** for system interactions.

### Hybrid: Workflow with AI Nodes

When the main flow is Workflow-like but 1-3 steps need an LLM call (classify / extract / summarize / generate), recommend **Workflow with AI Nodes**:

- Platforms: Dify, 扣子 (Coze), n8n + AI node, Make AI module, 飞书 aPaaS + AI, 钉钉悟空 workflow.
- Output: node map + which nodes are LLM nodes + prompt schema per LLM node + fallback when LLM returns garbage.

## Knowledge Overlay

Evaluated independently from environment and control. The result is layered on top of the main route.

### Boundary: No Layer ↔ RAG

```text
"AI 完成任务需要参考的内部资料，能一次性粘贴进对话吗？"
```

- Can paste (< a few pages) → **No knowledge layer needed**.
- Cannot paste (too many / too scattered / growing / permission-controlled) → check difficulty.

### Difficulty Gates for RAG (★★★★)

| # | Question | YES → | NO → |
| --- | --- | --- | --- |
| 1 | "查询频率 ≥ 每周？" | continue | manually select key excerpts to paste |
| 2 | "文档会持续更新，不是一次性的？" | continue | one-time summary document |
| 3 | "有人 / 有平台能维护知识库索引？" | **RAG** | Skill + manually updated summary docs |

All three YES → add RAG layer.
Any NO → downgrade to the lighter alternative shown.

For full stack-aware platform guidance (DingTalk / Lark / WeCom / Notion / Confluence / Slack / M365 / Google Workspace / 自建), see `references/enterprise-knowledge-rag.md`.

## Schedule Overlay (cross-cutting)

Canonical trigger/default/upgrade rules live in `references/overlays.md` § Schedule Overlay. This section keeps Discovery-level boundary and composition notes.

Evaluated **independently** from environment, control, and knowledge. Often downgrades Workflow / Browser Agent / Coding Agent to a much lighter "Scheduled AI Task" inside the host AI.

This overlay was added in v8.1 after observing that the router systematically over-recommended Browser Agent / Workflow for tasks that the host AI's built-in scheduler can handle.

### Boundary: Manual ↔ Scheduled

```text
"这事是用的时候手动触发，还是希望定时 / 事件自动跑？"
```

- 手动触发 → no overlay, use main route.
- 定时 / 事件触发 → check difficulty gate below.

### Difficulty Gate

| 任务特征 | 推荐承载 | 难度 |
| --- | --- | --- |
| 只需 LLM + web search + 格式化 + **公开数据** | **AI 工具内置 Scheduled Task** (ChatGPT Tasks / 钉钉悟空定时 / 飞书智能伙伴定时 / 豆包定时 / Kimi 定时) | ★★ |
| 需要 **登录 / 复杂网页交互** + 定时 | Scheduled Browser Agent 或 RPA + 定时 | ★★★★ |
| 需要 **写外部系统 / 多步审批** + 定时 | Workflow Platform with Schedule | ★★★★★ |

If the built-in scheduler can carry the task, **always prefer it**. Don't push the user to Browser Agent or Workflow just because the task is recurring. The full sub-template for this route is in `references/output-templates.md` § Scheduled AI Task Sub-template.

### When to escalate from Scheduled Task

| Signal | Upgrade to |
| --- | --- |
| 定时任务总跑挂 / 某些源开始要登录 | Scheduled Browser Agent |
| 输出每次都要人审才能发 / 涉及多步审批 | Workflow Platform |
| 需要把结果写入数据库 / 触发外部 API | Workflow Platform |
| 数据量超过 host AI 的上下文 | Coding Agent + 定时 cron |

## Table Overlay (cross-cutting, added v8.2)

Canonical trigger/default/upgrade rules live in `references/overlays.md` § AI Table Overlay. This section keeps Discovery-level boundary and composition notes.

Evaluated **independently** from the main route. Most often **replaces** the main route entirely when data lives in (or can live in) a table with AI fields. Listed as an overlay (not just an Axis A option) because it can also **layer onto** Workflow / Scheduled tasks (e.g. 钉钉 AI 表格 + 定时触发 + 群推送).

### Boundary: Free Text ↔ Table-Native Data

```text
"用户的数据是行 × 列的结构（即使现在还在 Excel / Word / 群聊里），并且每行需要相似的 AI 处理吗？"
```

- 是 → Table Overlay 介入
- 否（自由文本 / 单次任务）→ no overlay

### Difficulty Gate

| 任务特征 | 推荐承载 | 难度 |
| --- | --- | --- |
| 单行处理（提取 / 分类 / 总结 / 翻译 / 打标 / 编码 / 自定义生成） | **AI Table 字段**（platform ordering follows `overlays.md`） | ★★ |
| 单行处理 + 需要联网查信息 | **Notion Custom Agent Autofill** (with web search) 或 飞书 AI 字段 + 联网模型 | ★★★ |
| 跨行 / 跨表分析（"这个月哪个销售业绩最好"） | **AI 仪表盘 / 智能问数**（钉钉 / 飞书）or Chat 数据分析 | ★★ |
| 自动从外部触发 → 写入表格 → AI 处理 → 推送 | **Workflow + AI Table 节点**（飞书 aPaaS / 钉钉悟空 + 智能表格） | ★★★★ |
| 数据量超平台限额 / 复杂统计 | Coding Agent + 数据库 | ★★★★ |

### Anti-Pattern Reminders

- "客户评论批量分类" → **AI Table**（不是 Skill，不是 Chat 一条条粘，不是 RAG）
- "招聘候选人简历自动打标" → **AI Table**（数据在飞书多维 / 钉钉 AI 表格里）
- "对一列产品描述生成 SEO 摘要" → **AI Table**（不是 Workflow）
- "从用户反馈里提取 BUG/建议/咨询" → **AI Table 信息提取字段**（不是 Coding）

### Platform Matrix (2026-Q2, update freely)

| 平台 | AI 字段类型 | 触发 | 模型选择 | 数据量天花板 | 适合场景 |
| --- | --- | --- | --- | --- | --- |
| 飞书多维表格 | 分类 / 信息提取 / 总结 / 翻译 / 自定义 AI 自动填充 / 第三方模型字段 | 自动更新（默认开）/ 手动 / "生成前 5 行"预览 / 工作流 | 内置 + DeepSeek / OpenAI / 硅基流动 QwQ-32B 等 | 50 万行 | 客户之声、运营 UGC、电商评论 |
| 钉钉 AI 表格 | AI 字段提取 / AI 智能公式 / AI 智能分析 / AI 仪表盘 / 智能问数 | 实时 / 定时 / AI 助理工作流 | 通义千问 plus/max / 法睿 / 月之暗面 / ChatGLM4 / 零一万物 / DeepSeek-V3 | 大（企业级）| 销售周报、招聘、客户关怀、AI 质检 |
| Notion | Basic Autofill（Summary / Translate / Key Info / Select 分类 / 自定义）+ Custom Agent Autofill | 手动 / 创建 / 编辑 / 定时 | Notion 内置（2026 单行 < 3 秒）| 受 page/database 限制 | 会议笔记摘要、任务分级、知识库批量打标 |
| Airtable | AI fields（Categorize / Summarize / Extract / 自然语言生成公式） | 手动 / Automations | "enable the best models" | Team 5 万 / Business 12.5 万 / Enterprise 50 万行 | UX 研究、Meeting transcript、Feedback summary |
| 腾讯文档 / WPS | AI 智能列 | 手动为主 | 内置 | 较小 | 国内中小团队、Office 生态 |

## Route Composition Table

The three decisions produce three independent results. Cross them to get the final route. The 11 most common compositions:

| Environment | Control | Knowledge | Final Route | Combined ★ |
| --- | --- | --- | --- | --- |
| Text | Prompt | None | Chat + Prompt | ★ |
| Text | Skill | None | Skill | ★★★ |
| Text | Skill | RAG | Skill + RAG | ★★★★ |
| Text | Workflow | Enterprise knowledge | Workflow + Enterprise Knowledge Base | ★★★★★ |
| **Table** | **Prompt** (field config) | **None** | **AI Table 字段** (单个/几个 AI 字段) | **★★** |
| **Table** | **Prompt** + Schedule | **None** | **AI Table + 定时 / 工作流触发** | **★★★** |
| **Table** | **Workflow** | **Knowledge** | **AI 表格 + 工作流 + 知识库**（钉钉悟空 / 飞书 aPaaS） | **★★★★** |
| Browser | Prompt | None | Browser Agent + Execution Prompt | ★★ |
| Browser | Skill | None | Skill + Browser Agent | ★★★ |
| Browser | Skill | RAG | Skill + Browser Agent + RAG | ★★★★ |
| Coding | Prompt | None | Coding Agent + Handoff Prompt | ★★★ |
| Coding | Skill | None | Skill + Coding Agent | ★★★ |
| Coding | Skill | RAG | Skill + Coding Agent + RAG | ★★★★ |
| Coding | Workflow | None | Workflow with Code Nodes | ★★★★★ |

**Single-tool bias**: combinations emerge naturally from independent decisions, but if the final composition has more than 2 layers, double-check whether each layer is really justified. If in doubt, ship the simpler route first.

## Recommendation Confidence

| Label | Meaning | Threshold |
| --- | --- | --- |
| **High** (≥ 95%) | One route clearly wins. All boundary questions unambiguous. | critical 5/5 + supporting ≥ 5.5/7 |
| **Medium** (80-94%) | One route wins, but 1-2 details could flip the environment or control. | critical 5/5 + supporting 3.5-5.4 |
| **Low** (< 80%) | Ask another question before recommending. | critical < 5/5 |

Always state, in one line, **what fact would change the recommendation**.

## Upgrade and Downgrade Triggers

Recommendations are not final. Include these signals in the output so the user knows when to re-evaluate.

### Upgrade Signals

| From | To | Trigger |
| --- | --- | --- |
| Prompt | Skill | Same context copy-pasted ≥ 3 times; output quality varies too much across sessions |
| Skill | Workflow | Real approval / integration / notification needs emerge; skipping a step causes an incident |
| Skill | Bot / Workflow | Operator changes from "me" to "non-tech team" or "external customer" |
| Text route | Browser Agent | User frequently operates web pages manually then pastes results to AI |
| Text route | Coding Agent | Data exceeds paste capacity; deliverable must be runnable files |
| Paste context | RAG | User spends significant time finding and pasting excerpts every time; document corpus keeps growing |

### Downgrade Signals

| From | To | Trigger |
| --- | --- | --- |
| Workflow | Skill + manual | No actual approval / integration need; "having steps" was mistaken for needing enforcement |
| RAG | Paste context | Only a few documents are actually used; corpus is not growing |
| Coding Agent | Chat | Generated scripts were used once and never again |
| Skill | Prompt | Packaged rules change every time; no stable pattern to reuse |
| Combination (3+ layers) | Single tool | Each additional layer was added "just in case", not because it solved a tested problem |

## Anti-Patterns (do not do)

15 typical mis-routes. Use as a sanity check before delivering.

- 给"改写一段邮件"推荐 Workflow → 应该是 **AI Chat**。
- 给"读一份 PDF 总结"推荐 RAG → 一次性粘贴 / 上传就够，是 **AI Chat**。
- 给"小批量 CSV 处理"推荐 Coding Agent + Demo → 应该是 **AI Chat 的数据分析功能** 或 **单个脚本**。
- 给"内部知识查询"直接推荐 Custom RAG → 先用 **企业内置搜索** 验证不够再上 RAG。
- 给"浏览器自动化"推荐 Coding Agent → 优先 **Browser Agent**，除非需要持久化代码。
- 给"想固化下来反复用"的任务只给一个 prompt → 应该升级到 **Skill** 或 **Workflow**。
- 给"非技术员工 / 外部客户使用"推荐"装 Cursor 然后用我的 Skill" → 应该把 Skill 包装成 **Bot / Workflow**。
- 为了"显得专业"堆叠 4-5 个工具组合 → 单工具够用就别上层。
- 给"完全不懂技术的人"推 Coding Agent → 改推 **"找技术同事配合 + 临时用 AI Chat"**。
- 给"没有运维资源的小团队"推 RAG / Workflow → 改推 **"用现成 SaaS 自带 AI 功能"**（钉钉 / 飞书 / Notion / M365）。
- 给"只有一次性需求"推 Skill → 直接推 **AI Chat**，避免过度抽象。
- **(v8.1)** 给"每周固定时间跑一件公开数据汇总"推 Browser Agent / Workflow → 应该是 **AI 工具内置定时任务**（ChatGPT Tasks / 钉钉悟空定时 / 飞书智能伙伴定时）。
- **(v8.2)** 给"对一列数据批量提取/分类/总结/翻译/打标"推 Chat 数据分析（手动复制粘贴 100 次）→ 应该是 **AI Table 字段**（飞书多维 / 钉钉 AI 表格 / Notion AI Autofill / Airtable AI fields）。
- **(v8.2)** 给"飞书多维表格里 500 条客户评论分类"推 Custom RAG → 数据已经在表格里，应该是 **飞书多维 AI 字段捷径 → 分类**。
- **(v8.2)** 给"非技术员工要把 Excel 数据批量加 AI 处理"推 Coding Agent / Skill → 应该 **导入飞书多维 / 钉钉 AI 表格 / Notion / Airtable，配 AI 字段即可**。
