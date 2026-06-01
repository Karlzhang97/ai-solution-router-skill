# Triage Mode

Use this as the **default** entry point. Only fall back to `discovery.md` and `decision-tree.md` when escalation triggers fire.

## Goal

Give the user a usable, single-tool recommendation in **at most 3 turns**. Optimize for the user actually doing something with the answer, not for theoretical accuracy.

## Interaction Flow

### Fast Path — one-turn recommendation when enough is known

Before asking Q1, read `references/overlays.md` and check Fast Path.

Use Fast Path when the first user message already contains task + input + expected output, strongly hits AI Table / Schedule overlay, or explicitly asks for a direct recommendation. Give the quick recommendation immediately, mark missing details as `【假设】`, and end with the optional refinement invitation from `references/overlays.md`.

Do not use Fast Path when High-Risk Keyword Auto-Escalate fires.

### Turn 1 — Q1 (open question)

Ask in the user's language. Chinese template:

```text
请用一两句话告诉我：
- 你想完成什么任务？
- 给 AI 什么输入？
- 期望它输出什么？

举个例子也行，比如"我每周要看 20 篇行业新闻，挑 3 篇写摘要发到周报里"。
```

English variant:

```text
In one or two sentences, tell me:
- What task do you want done?
- What input will you give the AI?
- What output do you expect?
```

Wait for the answer before asking Q2 + Q3.

### Turn 2 — Q2 + Q3 (multi-select, ask together)

Use a single structured multi-question form if the host UI supports it. Otherwise ask both in one message.

**Q2 — 你多久做一次这件事？** (single-select)

- A. 一次性 / 一两次就够
- B. 偶尔（一周不到一次）
- C. 高频（每周或每天反复做）

**Q3 — 这件事主要长什么样？** (single-select, pick the closest)

- a. 我主要想让 AI 帮我"写"东西（写邮件 / 写文案 / 写报告 / 翻译 / 改稿）
- b. 我手里有文档 / 表格 / 数据，要 AI 帮我"整理"（分类 / 提取 / 总结）
- c. 我希望 AI 在网页上"替我做"（在系统里填表 / 跨多个网页收集信息 / 自动操作）
- d. 我希望 AI 处理一批"程序能搞的事"（写脚本 / 跑数据管道 / 做内部小工具）
- e. 我希望它"自动跑一整件事"（从触发到结果中间我不管，可能涉及多个系统/审批）
- f. **不太确定 / 上面都不太像**

如果用户选了 **f. 不太确定**，不要硬猜——升级到 Discovery Mode，按 `discovery.md` 流程问一两个 follow-up。

### Turn 3 — Recommendation

1. Run **Table Pre-Check** on Q1 using `references/overlays.md` — if hit AND task is row-level AI processing → recommend AI Table directly, skip the routing table.
2. Run **Schedule Pre-Check** on Q1 using `references/overlays.md` — if hit AND task is simple "fetch / summarize / push" → recommend Scheduled AI Task directly, skip the routing table.
3. Run **Knowledge Keyword Auto-detect** on Q1 (see below).
4. Run **High-Risk Keyword Auto-detect** on Q1 — if any hit, escalate to Discovery Mode instead of giving a Triage answer.
5. Look up the **Triage Routing Table** — note that 频率 C 高频 has separate rows for **手动触发** vs **定时推送**, and **b. 表格** has separate rows for **AI Table 适用** vs **跨行复杂分析**.
6. Output using the **Newbie Express Output** template from `output-templates.md` (use **AI Table Sub-template** if Step 1 fired, **Scheduled AI Task Sub-template** if Step 2 fired).

## Table Pre-Check (查表前最先做，v8.2)

Use `references/overlays.md` § AI Table Overlay as the canonical trigger/default/upgrade source. Do not duplicate platform ordering here.

**强信号**（命中任一立刻进 Table 路径）：

- 用户明确说出了协作平台名 + 表格类词："飞书多维表格 / 钉钉智能表格 / 钉钉 AI 表格 / Notion 数据库 / Airtable / 腾讯文档 / WPS 表格"
- 用户的描述含"**对某列 / 对每一行 / 对每条 / 批量 / 一列**" + "**分类 / 打标 / 提取 / 总结 / 翻译 / 评分 / 编码**"
- 用户输入是"Excel / 表格 / CSV"且想"按行用 AI 处理"

**弱信号**（建议追问"你的数据现在在哪？"）：

- 数据量描述像表格（"500 条 / 1000 行 / 几百个客户 / 几十个 SKU"）
- 输入 IO 描述里出现"一份表 / 一个清单 / 一份名单"

如果强信号命中 → **直接推 AI Table 路径** ★★。平台排序按 `references/overlays.md` 执行。Do not use the regular table. See `output-templates.md` § AI Table Sub-template.

如果弱信号命中且 Q3 选了 b（表格 / 数据整理）→ 追问一句："你的表格目前在哪里？（钉钉 / 飞书 / Notion / Airtable / Excel / 其他）" 然后：

- 在钉钉/飞书/Notion/Airtable → **AI Table** ★★
- 在 Excel/CSV → 看任务类型：若每行独立处理 → 推"导入飞书多维 / 钉钉 AI 表格再用 AI 字段"或 "Chat 数据分析"；若复杂统计 → Chat 数据分析 / Coding Agent

For "do not use AI Table" and upgrade paths, use `references/overlays.md` § AI Table Overlay.

## Schedule Pre-Check (查表前先做)

Use `references/overlays.md` § Schedule Overlay as the canonical trigger/default/upgrade source.

If hit AND task is "fetch public info / summarize / format / push" type → **优先推 AI 工具内置 Scheduled Task** ★★。Do not use the regular table for these.

See `output-templates.md` § Scheduled AI Task Sub-template for the artifact.

## Triage Routing Table

Single tool unless the row explicitly says "组合". Tools named are 2026-Q2 examples, not exclusive choices. Update freely as the market changes.

| Q2 频率 | Q3 场景 | 推荐路线（单工具） | 工具示例 | 难度 | 升级条件（这时改推括号里的） |
| --- | --- | --- | --- | --- | --- |
| A. 一次性 | a-e 任意 | **AI 聊天** | ChatGPT / Claude / Gemini / 豆包 / Kimi | ★ | 输入超过 50KB → Chat + 上传文件；强机密 → 公司内部 AI |
| B. 偶尔 | a. 写 | **AI 聊天 + 收藏 prompt** | 同上 | ★ | 用户说"想固化下来" → Skill |
| B. 偶尔 | b. 表格（**每行独立 AI 处理**） | **AI 表格字段** | 飞书多维 AI 字段捷径 / 钉钉 AI 表格 / Notion AI Autofill / Airtable AI fields | ★★ | 跨行复杂统计 → AI 仪表盘 / Chat 数据分析；写库/审批 → Workflow |
| B. 偶尔 | b. 表格（**整表/跨行统计分析**） | **AI 聊天的数据分析功能** | ChatGPT 数据分析 / Claude / 豆包 | ★~★★ | 数据量大 / 字段复杂 → Coding Agent |
| B. 偶尔 | c. 网页操作 | **网页操作 AI 代理** | ChatGPT Agent / Comet / Manus / 浏览器 AI 插件 | ★★ | 需登录敏感账号 → 人工兜底 |
| B. 偶尔 | d. 程序 / 数据 | **AI 编程助手** | Cursor / Codex / Claude Code / Windsurf | ★★★~★★★★ | — |
| B. 偶尔 | e. 自动跑一整件事 | **轻量自动化平台** | Dify / 扣子 / n8n / Make / Zapier | ★★★ | — |
| C. 高频（**手动触发**）| a. 写 | **Skill（一键 AI 模板）** | Codex / Claude Code / Cursor 的 Skill；钉钉悟空 / GPT 的可安装 skill | ★★★ | 给全公司非技术员工用 → 升 Bot/Workflow |
| C. 高频（**定时推送**）| a. 写 | **AI 工具内置定时任务** | ChatGPT Tasks / 钉钉悟空定时 / 飞书智能伙伴定时 | ★★ | 推送多人 / 写入文档 → 升 Workflow |
| C. 高频（**手动触发**）| b. 表格（**每行 AI 处理**）| **AI 表格字段（默认开启自动更新）** | 飞书多维 AI 字段捷径 / 钉钉 AI 表格 / Notion AI Autofill / Airtable AI fields | ★★ | 写库/审批 → 升 Workflow + AI 表格节点 |
| C. 高频（**手动触发**）| b. 表格（**跨行复杂分析**）| **Skill 或 带 AI 节点的自动化流程** | Skill；或 Dify/扣子里加 LLM 节点 | ★★★ | 输出要写库 / 触发审批 → 升 Workflow |
| C. 高频（**定时推送**）| b. 表格（**每行 AI 处理 + 推结果**）| **AI 表格 + 平台原生定时 / 工作流** | 飞书多维 + 工作流 / 钉钉 AI 表格 + AI 助理定时 | ★★★ | 数据源在外部 → 升 Workflow with HTTP + AI 节点 |
| C. 高频 | c. 网页操作（**公开数据 + 可定时推送**）| **AI 工具内置定时任务** | ChatGPT Tasks（含 web）/ 钉钉悟空定时 / 飞书智能伙伴定时 / 豆包定时 | ★★ | 某些源需登录 → 升 Browser Agent + 定时 |
| C. 高频 | c. 网页操作（**需登录 / 复杂交互**）| **网页操作 AI 代理 + 任务模板** | ChatGPT Agent / Comet / Manus | ★★★ | 步骤完全固定 → 写真正的爬虫脚本 |
| C. 高频 | d. 程序 / 数据 | **AI 编程助手 + Skill** | Cursor / Codex / Claude Code + Skill | ★★★~★★★★ | 要给非技术同事用 → 包装成本地工具 + UI |
| C. 高频 | e. 自动跑一整件事 | **组合：自动化平台 + 知识库** | Dify / 扣子 / 钉钉悟空 / 飞书智能伙伴 + 内部知识库 | ★★★★★ | — |

**单工具偏置**：除了最后一行 (C, e) 明确允许组合，其他行都只推一个工具；备选只在脚注里提，不进主推荐。

**怎么判断"手动触发" vs "定时推送"**：看 Q1 描述里有没有"每天/每周/定时/自动/提醒我/推送"等词；没有就默认"手动触发"，有就走"定时推送"行。

**业务话术对照表**（输出时给用户看的话术 / 内部技术名）：

| 业务话术 | 内部技术名 |
| --- | --- |
| AI 聊天 | AI Chat |
| AI 模板（一键调用） / Skill | Skill |
| 自动化流程 / 自动跑一整件事 | Workflow |
| 自动化流程里塞一段 AI 智能判断 | Workflow with AI Nodes |
| 网页操作 AI 代理 | Browser Agent |
| AI 编程助手（要技术同事看一眼） | Coding Agent |
| 公司知识库问答 | RAG / Enterprise Knowledge |
| **AI 工具内置定时任务（v8.1）** | **Scheduled AI Task** |
| **表格里加一列 AI 字段（v8.2）** | **AI Table (AI Field)** |

## Knowledge Keyword Auto-detect

Scan Q1 for these words. If any hit, **append** a one-line knowledge suggestion to the recommendation, but do NOT change the primary route.

| 出现关键词 | 自动追加建议 |
| --- | --- |
| 公司 / 内部 / 制度 / 政策 / 规章 / SOP / 案例库 / 知识库 / 文档库 | "建议先用你公司协作平台（钉钉 / 飞书 / 企微 / Notion / Confluence）自带的知识搜索，能解决就别上 RAG。" |
| 客户 / CRM / 订单 / 合同 / 客户记录 / 工单 | "涉及客户数据，注意权限隔离；如果只是查询，优先用 CRM 自带搜索。" |
| 论文 / 资料 / 行业报告 / 竞品 | "外部资料 → AI 聊天上传文件即可；只有需要反复检索同一批资料才上传到知识库。" |

If two or more knowledge keywords appear AND 频率 = C (高频)，escalate to Discovery Mode——可能需要专门的 RAG 方案，Triage 表覆盖不够。

## High-Risk Keyword Auto-Escalate

If Q1 contains any of these, **do not** give a Triage answer. Switch to Discovery Mode immediately.

- 合规 / 合同审查 / 法务 / 法律意见
- 财务 / 报表 / 账目 / 对账 / 付款 / 转账
- 医疗 / 诊断 / 用药 / 健康建议
- 直接回复客户 / 对外发邮件 / 发布到外部平台 / 群发 / 全员通知
- 删除 / 覆盖 / 不可逆操作
- 涉及人事决策 / 招聘筛选 / 绩效 / 薪资 / 晋升
- 老板要看 / 领导审阅 / 提交给上级 / 对外发布

Escalation message template:

```text
我注意到这件事涉及 [关键词]，错误代价比较高。
为了不推荐错，我再多问你 3-5 个问题，约 1-2 分钟。
```

Then read `references/discovery.md` and follow the weighted confidence rubric.

## Ambiguity Handling

Light rules to handle before escalating——能省一轮就省一轮。

- **Q1 太短或太空**（例如 "我想用 AI 提效"）：再问一次，但只问一句——"能举一个具体小事吗？比如最近一周你最想让 AI 帮你做的一件事。" 不要立刻进 Discovery。
- **Q1 明确说"先给方案别问了"**（Express Override）：根据已有信息按表查；缺的全部标【假设】；不要再问。
- **Q3 选 f. 不太确定**：升级到 Discovery，按 `discovery.md` 问 1-2 个 follow-up。
- **查表落进空格**（极少见）：取最近邻格子，标在假设里，并降置信度。

## When to Voluntarily Suggest Escalation

Even without high-risk keywords, **offer** (don't force) escalation when:

- 用户在 Q1 同时提到 2 个及以上完全不同的任务（"我要做 A，也想 B"）。
- 用户说"团队里有不同需求" / "我们好几个人想用"。
- 推荐结果你心里把握 < 60%（例如 Q3 多个场景同等重要、Q1 描述抽象）。

Phrase:

```text
我可以现在就给一个推荐，也可以再多问你 3 个问题做更精准的判断——你想哪个？
```

让用户选，别替用户选。
