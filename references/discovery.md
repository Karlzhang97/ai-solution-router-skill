# Discovery (Deep Mode)

> **Only use this file in Discovery Mode**. The default flow is Triage in `triage.md`.
> Enter Discovery only when one of these is true:
> - Triage's High-Risk Keyword Auto-detect fired.
> - The user explicitly asked for more precise routing.
> - Q1 was too vague after one follow-up.
> - Q3 selected "不太确定".
> - Triage table cell was ambiguous and confidence is below 60%.

## Goal

Reach **95% routing confidence** on a task that genuinely needs it. Use the weighted scoring rubric below so confidence is a number you compute, not a feeling. Then make three independent decisions (environment × control × knowledge).

## Opening Question (when entering from cold start)

```text
你先用一个具体例子讲讲：这件事现在通常怎么发生，从你拿到什么输入开始，到你希望得到什么结果结束？
```

If entering from Triage, do **NOT** restart — carry Q1/Q2/Q3 answers forward and continue from the first unfilled checklist item.

## Fast Track

If the user's first message already covers **3 of 4 key dimensions**, skip the full checklist and go to routing. Mark missing details as assumptions.

The 4 key dimensions:

- **Input**: what the user starts with.
- **Output**: what counts as success.
- **Frequency**: how often the job happens.
- **Workspace**: where the work physically happens (chat / docs / browser / code / multi-system).

## Confidence Checklist (12 items, weighted)

### Critical 5 — each must score ≥ 1.0 before any routing

1. What exact job is the user trying to finish?
2. What input does the user start with?
3. What output counts as success?
4. Where does the work mostly happen? (chat / docs / browser / code / multi-system)
5. Does the task depend on internal company knowledge, policies, customer records, or permission-controlled content?

### Supporting 7 — total must score ≥ 5.5

6. How often does the job happen?
7. How much time, attention, or frustration does it currently cost?
8. Is the current path fixed, semi-fixed, or exploratory?
9. Are there SOPs, templates, examples, or past cases?
10. Does the task require login/session context, mouse clicks, web research, data analysis, or code changes?
11. What risks matter: accuracy, privacy, compliance, cost, external dependencies, user confirmation?
12. **Who will end up using the artifact?** (just me / my team / non-technical staff / external customer / system) — this is the **Operator** dimension and changes Skill ↔ Workflow ↔ Bot/Product decisions.

## Confidence Scoring Rubric

For each of the 12 items, assign a score:

| 回答情况 | 分数 |
| --- | --- |
| 用户明确回答 | 1.0 |
| 用户回答但仍有模糊 | 0.5 |
| 未问 / 用户说不知道 | 0.0 |

Then apply the gate:

| 关键 5 项 | 其余 7 项总分 | 结论 |
| --- | --- | --- |
| 全部 = 1.0 | ≥ 5.5 | **95%+，可路由**；剩余空缺标【假设】 |
| 全部 = 1.0 | 3.5 – 5.4 | **80%，可路由但必须标"基于 N 条假设"** |
| 4/5 = 1.0 | — | 必须再问那个缺失的关键项 |
| ≤ 3/5 critical | — | 不能路由；不管其余多高也要继续问 |

## Stuck Rules

- 同一个关键问题问 2 次仍模糊 → 给出"两种典型可能"让用户二选一，而不是无限追问。
- 用户主动说"先给建议、别问了" → 进入 Express Override：用已有信息按 triage 查表，剩下全标【假设】，输出第一行必须写"基于 N 条假设"。
- 用户连续 3 轮答非所问 → 暂停追问，复述"我现在理解的是：……。你纠正一下哪里不对？"

## Follow-Up Question Pool

Use this as a pool, not a script. Pick the question that fills the highest-weight gap. Questions are tagged by which routing decision they feed.

### Critical 1-3 (feeds task framing)

- "做出这件事的成品长什么样？你能给我一个理想结果的样例吗？"
- "你现在每次开始这件事，手里拿到的第一份资料是什么？"
- "完成后，谁会看 / 用这个结果？他们用它做什么？"

### Critical 4 — Environment (feeds Decision A)

- "这件事主要在哪儿发生：聊天框、文档/表格、网页后台、浏览器搜索、代码项目，还是多个系统之间？"
- "需要 AI 真正点击网页、登录系统、改文件或跑代码吗？还是只让它给文字建议？"
- "交付物是对话里的一段文字？还是必须生成文件 / 跑脚本 / 构建可运行的东西？"

### Critical 5 — Knowledge (feeds Knowledge Overlay)

- "这件事需要引用公司内部文档、制度、历史案例、客户资料或知识库吗？"
- "这些知识现在放在哪里：钉钉 / 飞书 / 企微 / Notion / Confluence / CRM / 网盘 / 散文件？"
- "需要参考的资料能一次性粘贴进对话吗？还是太多 / 太散 / 会持续增长？"
- "输出需要标明引用来源吗？是否有权限隔离要求？"

### Control — feeds Decision B

- "这件事有没有固定步骤、模板、SOP 或以前做过的优秀案例？"
- "下次再做，你需要重新跟 AI 解释一遍规则吗？还是规则可以固化下来？"
- "如果 AI 跳过某步、不通知某人、不写入某系统，会怎样？只是质量差一点，还是会出事？"
- "过程中有没有必须等人审批 / 确认才能继续的环节？"

### Capability and Difficulty — feeds difficulty gates

- "你们团队有没有能搭建 / 维护工作流平台（如 Dify / 扣子 / n8n）的人？"
- "如果推荐用 AI 编程工具生成代码，有人能看懂输出并验收吗？"
- "如果推荐搭建知识库 / RAG，有人能持续维护文档索引吗？"
- "这件事如果要持续跑下去，谁负责维护？"

### Risk and Boundary

- "AI 输出之后，你希望它只给建议，还是直接执行？哪些地方必须你确认？"
- "这个任务如果做错，代价是什么？只是返工，还是会影响客户、合规或业务决策？"
- "**最后用这个产物的是你自己、你的小团队、全公司非技术员工，还是外部客户？**"

## Stop Conditions

Proceed to routing (read `decision-tree.md`) when:

- Critical 5 are all at 1.0.
- Supporting 7 total ≥ 5.5.
- You can make all three independent decisions (environment, control, knowledge) with confidence.
- You can name at least two alternative routes and explain why each loses.
- You have assessed the user's implementation capability for the likely route.
- You can produce a concrete next artifact (prompt / skill spec / workflow node map / browser-agent prompt / coding-agent handoff), not just a tool name.

If the user has said "先给判断" and you fall short, ask **one** final highest-weight question, then deliver a provisional recommendation with assumptions clearly labeled.

## Edge Cases

Four named scenarios. Handle exactly as described; do not improvise.

### Edge 1 — User changes requirements mid-discovery

If the user introduces a fundamentally different task during discovery, **restart the confidence checklist for the new task**. Do not carry over assumptions from the old task. Briefly acknowledge:

```text
理解，我们换到新需求重新判断。前面的假设清空。
```

### Edge 2 — User rejects the route at confirmation

1. Ask **which axis** they disagree with: A (environment), B (control), or Knowledge.
2. Ask one targeted question about that axis.
3. Re-evaluate **only the disputed axis**, keep the other two.
4. Re-present the updated route.

Do NOT restart full discovery unless the objection reveals the underlying task was misunderstood. Phrase:

```text
你觉得问题出在：
A. 环境判断（我说"在网页操作"，你觉得其实在别处）
B. 控制判断（我说"做 Skill"，你觉得太轻 / 太重）
C. 知识层（我说"不需要"，你觉得需要 / 反之）
```

### Edge 3 — Insufficient information after 5 questions

Produce a provisional recommendation. Mark all uncertain items as assumptions with their impact. State:

```text
以下是基于当前信息的初步判断，标注了 X 处假设。
确认或纠正后我可以更新。
```

### Edge 4 — User states a tool preference upfront

If the user names a specific tool (e.g. "我想用 Dify 搭个工作流", "我想用 Cursor 做这个"), do not ignore their preference:

1. Complete discovery as normal.
2. At route confirmation, present **both**: the skill's recommendation AND the user's preferred tool.
3. If they differ, explain the trade-off and let the user decide.

Phrase:

```text
你倾向用 [X]，按我的判断更合适的是 [Y]。
区别在 [关键差异 1-2 句]。
你想用哪个？或者我说说为什么我推 [Y]？
```
