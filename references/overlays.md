# Routing Overlays

Use this file as the canonical source for cross-cutting routing rules that can override or layer onto the normal Triage / Discovery route. Other files should summarize these rules and point here instead of duplicating the full logic.

## Fast Path + Optional Refinement

Use Fast Path when the first user message is already actionable enough to recommend without Q1/Q2/Q3.

Fast Path triggers when any of these are true:
- The message already states the task, input, and expected output.
- The message strongly hits AI Table Overlay.
- The message strongly hits Schedule Overlay and does not require login, private system access, external writes, or risky actions.
- The user says "先给方案", "直接推荐", "别问了", or similar.

Fast Path output rules:
- Give one quick recommendation immediately using `references/output-templates.md`.
- Mark missing details as `【假设】`.
- Include this optional refinement invitation at the end:
  ```text
  如果你愿意补充频率 / 当前工具 / 是否给团队用，我可以再帮你判断要不要升级路线。
  ```
- Do not use Fast Path when high-risk keywords fire. Enter Discovery instead.

## AI Table Overlay

Use AI Table when data is or can become rows x columns, and each row needs similar AI processing.

Strong signals:
- The user names a table/database platform plus a table-like object: 钉钉智能表格 / 钉钉 AI 表格 / 飞书多维表格 / Notion 数据库 / Airtable / 腾讯文档 / WPS 表格.
- The user says "对某列 / 对每一行 / 对每条 / 批量 / 一列" plus "分类 / 打标 / 提取 / 总结 / 翻译 / 评分 / 编码 / 生成".
- The input is Excel / CSV / 表格 and the task is row-level AI processing.

Default recommendation:
- Recommend **AI Table 字段** (★★) before Chat / Skill / Workflow / Coding Agent.
- Chinese or China-workplace users: surface **钉钉 AI 表格** and **飞书多维表格 AI 字段捷径** before Notion / Airtable.
- If the user says 钉钉, make 钉钉 the primary example.
- If the user says 飞书, make 飞书 the primary example.
- If the user already uses Notion / Airtable, use that platform as primary and mention domestic options only as alternatives when relevant.

Do not recommend AI Table as the primary route when:
- The main task is cross-row / cross-table statistics or dashboard analysis.
- The result must write to external systems, trigger approval, send group messages, or mutate records outside the table.
- Data volume exceeds likely platform limits.
- The task requires complex code, custom parsing, or multi-step orchestration across systems.

Upgrade paths:
- Cross-row / dashboard / natural-language query -> AI 仪表盘 / 智能问数 / Chat 数据分析.
- External trigger -> table -> AI processing -> push/approval -> Workflow + AI Table node.
- Huge data or complex statistics -> Coding Agent + database.

## Schedule Overlay

Use Scheduled AI Task when the user wants a recurring task that can be fully handled by the host AI scheduler.

Strong signals:
- 每天 / 每周 / 每月 / 定时 / 自动推送 / 提醒我 / on schedule / daily / weekly.
- The task is "fetch public information / summarize / format / push".

Default recommendation:
- Recommend **AI 工具内置定时任务** (★★): ChatGPT Tasks / 钉钉悟空定时任务 / 飞书智能伙伴定时执行 / 豆包定时 / Kimi 定时.
- Prefer the scheduler inside the tool the user already named.

Do not recommend Scheduled AI Task as the primary route when:
- The task requires login, private systems, browser interaction, captcha, or session state.
- The task writes to external systems, triggers approval, sends mass messages, deletes, pays, or mutates records.
- The task needs large-scale processing beyond a host AI context.

Upgrade paths:
- Login / complex browser interaction -> Scheduled Browser Agent or Workflow.
- External writes / approvals / group push -> Workflow Platform with Schedule.
- Large recurring data jobs -> Coding Agent + cron.
