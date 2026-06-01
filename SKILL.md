---
name: ai-solution-router
description: "Use when a user has a vague task, workflow, business problem, product idea, repetitive job, research/data/code need, or 'what AI tool should I use' question. Also trigger on Chinese phrases: AI工具选型, 该用什么AI, 用什么工具, 帮我选AI工具, AI方案推荐, 帮我选工具, 我想用AI做, 我想用AI帮我, 我想自动化, 这件事能不能用AI做, tool selection, demand discovery, prompt generation, skill/workflow design, browser-agent task planning, or product demo development. Defaults to a fast 3-question triage for users who can't articulate; escalates to a deeper 3-axis discovery only when the task is high-risk, ambiguous, or the user asks for precision."
---

# AI Solution Router

## Purpose

Help the user find the simplest **single** AI tool that reliably gets their job done. Optimize for the user actually completing something, not for theoretical accuracy. Default to a 3-question triage so even non-technical colleagues can get a usable answer in one minute; escalate to a 3-axis discovery only when the task is high-risk, ambiguous, or the user asks for precision.

## Core Rules

- **Default to Triage Mode, with Fast Path**: use Fast Path when the first message already contains task + input + expected output, or strongly hits Table/Schedule overlays. Otherwise use at most 3 turns (one open question + two multi-select questions + one recommendation).
- **Single-tool bias**: by default recommend ONE tool. Only recommend a combination when (a) the triage table explicitly permits it, or (b) Discovery Mode's 3-axis decision clearly justifies layers (see `references/decision-tree.md` § Route Composition).
- **Match the user's language**: detect from their first message; never invent Chinese questions for an English user or vice versa.
- **Overlay rules have one canonical source**: use `references/overlays.md` for Fast Path, AI Table, and Schedule overlay triggers, defaults, downgrade/upgrade paths, and domestic ecosystem ordering.
- **Domestic ecosystem bias for Chinese users**: when the user writes in Chinese or mentions a China workplace stack, put domestic examples first. For AI Table recommendations, follow `references/overlays.md` so 钉钉 / 飞书 are surfaced before Notion / Airtable unless the user already named an overseas stack.
- **Never deliver a tool name alone**: every recommendation must include a one-line reason, a ready-to-use artifact (prompt / entry point / handoff), the assumptions you made, and a difficulty rating.
- **Pre-AI sanity check (Step 0)**: before routing, check whether the task even needs AI. If a 30-line script / SaaS-native AI feature / **host AI's built-in scheduler (ChatGPT Tasks / 钉钉悟空定时 / 飞书智能伙伴定时 / 豆包定时)** / one-shot prompt covers it, say so first instead of pushing the user through the full tree. See `references/decision-tree.md` § Step 0.
- **Schedule before Browser Agent / Workflow**: if the task is "fetch public info / summarize / format / push on a schedule", default to the host AI's built-in Scheduled Task (★★) before recommending Browser Agent (★★) or Workflow (★★★★★). Only escalate when login / complex web interaction / external system writes are required. See `references/overlays.md` § Schedule Overlay.
- **Table-first when data lives (or can live) in a table** (v8.2): if the task is "对表格里某列批量做提取/分类/总结/翻译/打标/编码/自定义生成", default to **AI Table fields** (★★) before recommending Skill / Workflow / Coding Agent. This includes data currently in Excel/CSV (suggest importing to a host platform). Only escalate when cross-row analysis / external system writes / data exceeds platform limits. See `references/overlays.md` § AI Table Overlay.
- **Heavier solutions need higher justification**: Prompt (★) and Scheduled AI Task (★★) and **AI Table (★★)** and Browser Agent (★★) need only a confirmed need; Skill (★★★) needs at least monthly recurrence; Coding Agent (★★★~★★★★) needs someone who can review the output; RAG (★★★★) and Workflow (★★★★★) need weekly frequency plus ongoing maintenance capacity.
- **3 independent axes for routing** (used in Discovery Mode and as the underlying logic for Triage):
  - **Axis A — Execution Environment**: text / table / browser / code
  - **Axis B — Control Level**: prompt / skill / workflow
  - **Overlay — Knowledge Source**: none / paste / RAG / enterprise knowledge
  Final route = A × B × K. Each escalation needs both a need test AND a difficulty justification.
- **Escalate to Discovery Mode** when ANY of these is true:
  - User explicitly says "再精准点" / "be more precise" / "再多问几个".
  - Q1 description hits the High-Risk Keyword List in `references/triage.md` (compliance, finance, legal, medical, customer-facing irreversible action).
  - Q1 is too vague to extract a task (less than ~5 informative words, e.g. "我想用 AI 提效").
  - Triage table cell is genuinely ambiguous after Q2+Q3.
  - User has selected "不太确定" on Q3.
- **Skill's job ends at route handoff**: for Coding Agent routes, produce a handoff prompt and stop. Only load `references/demo-build-branch.md` if the user explicitly asks to continue with full product development from this same conversation. `references/product-demo-templates.md` is optional inside that branch.

## Workflow

### Start: Fast Path Check

1. **Load `references/triage.md` and `references/overlays.md`.**
2. If the first message has task + input + expected output, or strongly hits AI Table / Schedule overlay, produce a quick recommendation immediately.
3. In Fast Path output, mark missing details as `【假设】` and include the optional refinement invitation from `references/overlays.md`.
4. If high-risk keywords fire, do not Fast Path. Enter Discovery Mode.

### Default: Triage Mode (up to 3 turns)

1. **Load `references/triage.md` and `references/overlays.md`.**
2. **Turn 1 — Q1 (open):** ask the user to describe in 1-2 sentences what they want to do, what input they give the AI, and what output they expect.
3. **Turn 2 — Q2 + Q3 (multi-select, ask together):**
   - Q2 频率: 一次性 / 偶尔 / 高频
   - Q3 主要场景: 写字 · 文档表格 · 网页操作 · 数据/程序 · 自动跨多系统 · 不太确定
   Use a single structured multi-question form when the host supports it.
4. **Turn 3 — Produce recommendation (check pre-checks first, in order):**
   - Run **Table Pre-Check** on Q1 using `references/overlays.md` — if table-related strong signal hits AND task is row-level AI processing → recommend AI Table directly using the AI Table Sub-template, skip the routing table.
   - Run **Schedule Pre-Check** on Q1 using `references/overlays.md` — if scheduling keyword hits AND task is simple "fetch / summarize / push" → recommend Scheduled AI Task directly using the Scheduled AI Task Sub-template, skip the routing table.
   - Run Knowledge Keyword Auto-detect on Q1.
   - Run High-Risk Keyword Auto-detect on Q1 — if any hit, escalate to Discovery Mode instead of giving a Triage answer.
   - Look up the Triage Routing Table (note: 频率 C 高频 has separate rows for **手动触发** vs **定时推送**; 场景 b 表格 has separate rows for **每行 AI 处理** vs **跨行复杂分析**).
   - Output using the **Newbie Express Output** template from `references/output-templates.md`.

### Escalated: Discovery Mode

When escalation triggers fire, announce it briefly:

```text
我注意到这件事涉及 [关键词]，错误代价比较高 / 我对推荐还没把握。
为了不推荐错，我再多问你几个问题，约 1-2 分钟。
```

Then run the full 5-step flow:

1. **Discovery** — Read `references/discovery.md`. Reach 95% confidence using the weighted scoring rubric (critical 5 items must each be 1.0; supporting 7 items total ≥ 5.5). Use Fast Track if the user's first message already covers 3 of 4 key dimensions.

2. **Route Selection** — Read `references/decision-tree.md`. Make **three independent decisions** in parallel:
   - Decision A (Environment): text / table / browser / code?
   - Decision B (Control): prompt / skill / workflow?
   - Overlay (Knowledge): paste / RAG / enterprise knowledge?
   For each axis, apply both the need test and the difficulty gate. Combine into the final route.

3. **Route Confirmation** *(checkpoint — do not skip)* — Present the recommendation using the Lightweight or Full template from `references/output-templates.md`. Explicitly state: three-axis judgment, difficulty assessment, upgrade/downgrade triggers, and remaining assumptions. Ask the user to confirm, adjust, or reject.
   - If confirmed → Step 4.
   - If adjusted → revise the affected axis only and re-present.
   - If rejected → return to Discovery with a targeted follow-up.

4. **Generate Output** — Read `references/output-templates.md`. Produce the concrete artifact for the confirmed route.
   - Chat → ready-to-use prompt.
   - Skill → read `references/skill-creation-guide.md` and produce skill brief or full files.
   - Workflow → node design + IO + decision rules + test cases.
   - Browser Agent → tool guidance + execution prompt.
   - Coding Agent → handoff prompt. **Stop here** unless the user explicitly asks to continue with full product development (then load `references/demo-build-branch.md`).

5. **Confirm and Package** — Ask whether to create files. If yes, use clear names. Include assumptions, confidence score, and what would change the recommendation.

## Decision Summary

Three independent decisions + two cross-cutting overlays, combined into the final route. Full questions, difficulty gates, and downgrade paths live in `references/decision-tree.md` — load that file before routing.

| Axis | Core question | Difficulty range |
| --- | --- | --- |
| A. Environment | Where does the work physically happen — text, **table** (v8.2), browser, or code? | ★ → ★★★★ |
| B. Control | How constrained must the AI be — one-off prompt, reusable skill, or platform-orchestrated workflow? | ★ → ★★★★★ |
| K. Knowledge | Can references be pasted, or is a RAG / enterprise knowledge layer needed? | — → ★★★★ |
| **Overlay: Schedule (v8.1)** | Is the task scheduled / event-triggered? Can the host AI's built-in scheduler carry it? | ★★ → ★★★★★ |
| **Overlay: Table (v8.2)** | Is the data row × column with similar AI processing per row? | ★★ → ★★★★ |

Final route = A × B × K, then check Schedule and Table overlays which can replace or layer onto the main route. Each escalation needs both a need test AND a difficulty justification.

## Output Must Include

- User need summary.
- Confidence score (weighted, per `discovery.md`) and remaining assumptions.
- Recommended route (environment × control × knowledge, or a single tool in Triage Mode).
- Why this route beats the alternatives (at least two named alternatives).
- Suggested tool examples, framed as examples rather than exclusive choices.
- **Difficulty assessment** (★ rating) and whether the user has the capability to implement.
- **Upgrade and downgrade triggers**: signals that mean the route should change later.
- Concrete next artifact: prompt, skill spec, workflow node map, browser-agent prompt, or coding-agent handoff.
- Validation method: a 30-minute smoke test the user can run.

## Reference Loading Guide

- `references/triage.md` — **load first by default** for the 3-question flow, Knowledge auto-detect, and high-risk escalation list.
- `references/overlays.md` — canonical source for **Fast Path**, **AI Table Overlay**, and **Schedule Overlay**. Load with triage, before applying table/schedule recommendations.
- `references/discovery.md` — only in Discovery Mode; weighted confidence rubric, Capability question pool, Edge Cases (4 named scenarios).
- `references/decision-tree.md` — only in Discovery Mode; Step 0 (does it need AI? / built-in scheduler? / AI Table?), 3-axis model (Axis A now includes **table**), difficulty gates, **Schedule Overlay**, **Table Overlay** (v8.2), route composition table, upgrade/downgrade triggers, 15 Anti-Patterns.
- `references/enterprise-knowledge-rag.md` — load when Knowledge auto-detect fires or the user mentions internal docs/policies/cases/customer records. Includes a **Data-Form Decision** (v8.2) to avoid pushing structured-table tasks into RAG.
- `references/output-templates.md` — load when producing the final artifact (Newbie Express by default; Lightweight or Full in Discovery Mode). Sub-templates include **AI Table** (v8.2), **Scheduled AI Task** (v8.1), AI Chat, Skill, Workflow, Workflow with AI Nodes, RAG, Browser Agent, Coding Agent (In-Repo / Greenfield).
- `references/skill-creation-guide.md` — load only when the chosen route is Skill and the user wants to create the actual skill files.
- `references/demo-build-branch.md` — **optional extended module**. Load only when the chosen route is Coding Agent AND the user explicitly asks to continue from handoff into full product development. Pick the right sub-route inside (In-Repo Task vs Greenfield Demo).
- `references/product-demo-templates.md` — **optional extended module**. Load only inside the Greenfield sub-route of `demo-build-branch.md` when full PRD/tech docs are requested.
