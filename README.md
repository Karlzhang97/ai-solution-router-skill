# AI Solution Router

> **三个问题之内，告诉你这件事最适合用哪一种 AI 工具。**
>
> A portable AI skill that routes any "我该用哪种 AI 工具" question to a concrete, single-tool recommendation — with a ready-to-paste prompt, a difficulty rating, and clear upgrade/downgrade signals.

[![Version](https://img.shields.io/badge/version-v8.2-blue.svg)](#版本演化)
[![Format](https://img.shields.io/badge/format-skill-green.svg)](#安装方式)
[![License](https://img.shields.io/badge/license-MIT-lightgrey.svg)](#许可证)

---

## 这是什么

AI Solution Router 是一个**通用 AI 选型 skill**。当你（或你的同事）面对一个具体业务任务，不知道该用 AI Chat、AI 表格字段、定时任务、自动化流程、浏览器代理、AI 编程助手还是公司知识库时，它会：

1. 用 **3 个问题**（1 个开放 + 2 个多选）做快速分诊。
2. 给出**一个**（默认不组合）最适合的 AI 工具推荐。
3. 附一段**直接可粘贴的 prompt** + 操作步骤 + 难度评估 + 升降级触发条件。
4. 涉及高风险任务（合规 / 财务 / 法律 / 对外）时自动升级到深度问答模式。

它不是一个新的 AI 工具，**而是一个"该用哪个工具"的决策助手**。

## 谁适合用

- **非技术同事**：被工作压力或老板要求"用 AI 提效"，但不知道从哪起步。
- **AI 工具培训师 / 内训师**：把这个 skill 装进公司知识库或培训材料，让同事自助找方案。
- **AI 工具选型负责人**：避免每次被同事问"我这个用啥工具"。
- **开发者 / 产品经理**：在开始写代码前，先确认这件事是不是真该 AI 化。

---

## 快速体验（示例对话）

> **你**：我每周要看 20 篇行业新闻，挑出 3 篇做摘要发到团队周报里。

> **AI**（识别到"每周" + 公开网页 + 摘要 + 推送 → 触发 Schedule Pre-Check，跳过常规三问，直接推轻方案）：
>
> **推荐工具**：AI 工具内置定时任务（内部技术名：Scheduled AI Task）
> **难度**：★★
> **工具示例**：ChatGPT **Tasks** / 钉钉悟空 **定时任务** / 飞书 **智能伙伴定时执行** / 豆包 **定时**
> **一句话理由**：你已经在用主流 AI 工具，它们自带的定时功能就能干这事——不需要单独开浏览器代理也不需要搭工作流。
>
> **怎么用**（以 ChatGPT Tasks 为例）：
> 1. 打开 ChatGPT，确认 Tasks 已启用（Settings → Tasks）
> 2. 把下面这段话整段粘进对话框
> 3. 它会生成任务卡，确认时间和内容即可保存
>
> ```text
> 帮我创建一个定时任务：
>
> 频率：每周一上午 9:00（Asia/Shanghai）
> 执行内容：
> 1. 从 [我的常用行业新闻源列表] 抓取最近 7 天文章
> 2. 按 [相关度 / 引用量 / 发布媒体权威性] 挑出 3 篇
> 3. 每篇 200 字中文摘要 + 原文链接
> 4. 推送方式：在 ChatGPT 对话里直接展示
>
> 约束：只用公开数据；某个源不可达就跳过那一项；输出中文。
> ```
>
> **假设**：你的新闻源都是公开网页（无登录墙）；周报你自己手动发，不涉及群发审批。
> **什么时候该换**：
> - 某些源开始要登录 → 升级到 Scheduled Browser Agent
> - 要把摘要写进文档 / 自动 @ 团队 → 升级到 Workflow Platform
> - 数据量大到 ChatGPT 一次跑不完 → 升级到 Coding Agent + cron

整个交互 **1 轮、不到 30 秒**。

> 💡 **如果场景不一样**：上面这个 case 触发了 v8.1 的 Schedule Pre-Check 快速通道。**当任务不是"定时跑简单事"时**，会走完整 Triage 三问（开放 + 频率 + 场景）然后查表推荐，可能落到 AI Chat / AI Table / Skill / Browser Agent / Coding Agent / Workflow 等 8 条路线之一。详见 [工作原理](#工作原理)。

### 另一个示例：表格场景（v8.2 新增）

> **你**：我们用飞书多维表格，里面有 500 条客户评论，我想让 AI 自动分成「投诉 / 咨询 / 表扬 / 建议」四类，方便分给不同同事跟进。

> **AI**（识别到「飞书多维表格」+「自动分成 N 类」+「500 条」→ Table Pre-Check 强命中，一轮直出）：
>
> **推荐工具**：AI 表格字段（内部技术名：AI Table Field）
> **难度**：★★
> **工具示例**：飞书多维表格 **AI 字段捷径 → 分类**
> **一句话理由**：你的数据已经在飞书多维表格里了，加一列「分类」AI 字段就能自动跑，5 分钟搞定，不用搭 Skill、不用上 RAG、不用写代码。
>
> **怎么用**：
> 1. 在评论列右侧 **+** 新增字段 → **探索字段捷径** → 搜「分类」
> 2. 配置：
>    - 源字段：评论列
>    - 添加分类：投诉 / 咨询 / 表扬 / 建议
>    - 参考示例（贴 2-3 条已知正确分类的例子）
>    - 保留默认「自动更新」（新增评论自动跑）
> 3. 点 **生成前 5 行** 预览 → 不对就改示例；对了再 **应用到全部**
>
> **什么时候该换**：
> - 类别经常变 → 改成「自定义 AI 自动填充」配可调 prompt
> - 要分类完自动 @ 对应同事 → 升级到工作流 + AI 表格节点
> - 评论数量超过 50 万行 → 升级到 Coding Agent + 数据库

整个交互 **1 轮、不到 30 秒**。

---

## 安装方式

这个 skill 兼容市面上所有主流"可装 skill"的 AI 工具：

### Cursor

```bash
# 把 SKILL.md 所在的文件夹整个拷到：
%USERPROFILE%\.cursor\skills\ai-solution-router\

# macOS / Linux:
~/.cursor/skills/ai-solution-router/
```

### Codex / OpenAI Codex CLI

```bash
# Windows:
%USERPROFILE%\.codex\skills\ai-solution-router\

# macOS / Linux:
~/.codex/skills/ai-solution-router/
```

### Claude Code

```bash
# Windows:
%USERPROFILE%\.claude\skills\ai-solution-router\

# macOS / Linux:
~/.claude/skills/ai-solution-router/
```

### ChatGPT（作为自定义 GPT）

1. 创建一个新的 Custom GPT。
2. 把 `SKILL.md` 全文粘贴进 Instructions。
3. 把 `references/` 下所有 `.md` 上传到 Knowledge 文件。
4. （可选）把 `agents/openai.yaml` 里的 `default_prompt` 放进 Conversation Starters。

### 钉钉悟空 / 飞书智能伙伴 / 自建 Bot

1. 新建一个智能体 / 应用。
2. 把 `SKILL.md` 内容粘贴进系统提示词（System Prompt）。
3. 把 `references/` 的全部内容作为知识库上传，让智能体能按需检索。
4. 把 `test-prompts.json` 里的 15 个 case 作为测试输入回归一次。

### 手动验证安装

跑这个 prompt：

```text
帮我判断这件事最适合用哪种 AI 方案：我每天要把客户在微信群里发的反馈截图整理成表格。
```

如果它先问你 1 个开放问题 + 2 个多选问题（频率 + 场景），而**不是立刻给一个推荐**，说明 skill 已经被正确触发。

---

## 工作原理

### 默认：Triage Mode（3 轮）

新人友好的快速分诊路径：

```
Turn 1 ─ 开放问 Q1：任务 / 输入 / 输出
Turn 2 ─ 多选问 Q2 + Q3：频率 + 主要场景
Turn 3 ─ 查 12 行 Triage 路由表 → 单工具推荐 + 可粘贴 prompt + 假设 + 升级触发
```

查表中除最后一行（C 高频 / 自动跨多系统）以外，**全部只推一个工具**。单工具偏置作为硬规则。

**v8.1 新增 Schedule Pre-Check**：在查表前先扫 Q1，如果包含"每天 / 每周 / 定时 / 自动跑"等关键词 + 任务是"获取公开信息 / 摘要 / 推送"类，**直接推主流 AI 工具的内置定时任务**（ChatGPT Tasks / 钉钉悟空定时 / 飞书智能伙伴定时 / 豆包定时 / Kimi 定时），不走 Browser Agent / Workflow 这类重方案。

**v8.2 新增 Table Pre-Check**：在查表前先扫 Q1，如果用户提到"飞书多维表格 / 钉钉 AI 表格 / Notion 数据库 / Airtable"或"对某列批量分类/提取/总结/翻译/打标"，**直接推主流协作平台的 AI 表格字段**（飞书 AI 字段捷径 / 钉钉 AI 表格 / Notion AI Autofill / Airtable AI fields），不走 Skill / Workflow / Coding Agent 这类过重方案。

### 升级：Discovery Mode（5 步）

仅在以下任一情况触发：

- 用户主动说"再精准点 / 多问几个"
- 命中 High-Risk 关键词（合规 / 财务 / 法律 / 医疗 / 对外发布等）
- Q1 描述过短 / Q3 选"不太确定" / Triage 查表落空

进入后走 5 步：

```
1. Discovery  ─ 加权置信度评分（关键 5 项必须 1.0 + 支持 7 项 ≥ 5.5）
2. Routing    ─ 3 个独立轴并行判断：环境 A × 控制 B × 知识 K
3. Confirmation ─ 路由确认 checkpoint，用户可调整 / 拒绝 / 接受
4. Output     ─ 生成产物（Newbie Express / Lightweight / Full）
5. Package    ─ 询问是否落成文件
```

### 3 个核心维度 + 2 个横切 Overlay

| 维度 | 问题 | 选项 | 难度范围 |
|---|---|---|---|
| **A. 环境** | 工作物理发生在哪？ | 文本 / **表格 (v8.2)** / 浏览器 / 代码 | ★ → ★★★★ |
| **B. 控制** | AI 要被约束到什么程度？ | Prompt / Skill / Workflow | ★ → ★★★★★ |
| **K. 知识** | AI 需要什么知识？ | 无 / 粘贴 / RAG / 企业知识 | — → ★★★★ |
| **Overlay: 调度 (v8.1)** | 是不是定时 / 事件触发？ | 主流 AI 工具内置调度器够用吗 | ★★ → ★★★★★ |
| **Overlay: 表格 (v8.2)** | 数据是不是行 × 列 + 每行相似 AI 处理？ | AI 表格字段够用吗 | ★★ → ★★★★ |

**最终路线 = A × B × K**，加上 Schedule / Table 两个 overlay 决定是否替换或叠加。每个维度都有"need test + difficulty gate"双关。

### 难度阶梯（控制过度推荐）

```text
Prompt(★) < Scheduled AI Task(★★) ≈ AI Table(★★) ≈ Browser Agent(★★) < Skill(★★★) < Coding Agent(★★★~★★★★) < RAG(★★★★) < Workflow(★★★★★)
```

更重的方案需要更强的理由：Prompt、Scheduled AI Task、AI Table、Browser Agent 只要确认需求；Skill 要求月频；Coding Agent 要求有人能验收代码；RAG 和 Workflow 要求周频 + 有人维护。

### 8 条主路线（v8.2 全图）

```
1. AI Chat (Prompt)        ★          通用对话，一次性任务
2. Scheduled AI Task       ★★  (v8.1) 主流 AI 工具内置定时任务
3. AI Table                ★★  (v8.2) 协作平台表格里的 AI 字段
4. Browser Agent           ★★         网页操作 AI 代理
5. Skill                   ★★★        可复用 AI 模板
6. Coding Agent            ★★★~★★★★   AI 编程助手
7. RAG / Enterprise        ★★★★       知识库 / 检索增强
8. Workflow                ★★★★★      自动化平台 + AI 节点
```

### Step 0：先问"需不需要 AI / 需不需要这么重"

在路由前先检查：

- 30 行脚本能不能做？ → 推荐脚本
- SaaS 自带 AI 能不能做？（Notion AI / 飞书智能伙伴 / 钉钉 AI）→ 推荐内置功能
- **是不是"定时跑简单事"？host AI 自带定时任务够用吗？**（ChatGPT Tasks / 钉钉悟空定时 / 飞书智能伙伴定时 / 豆包定时）→ 推荐内置定时任务
- **是不是"对表格里某列批量做提取/分类/总结"？数据能进协作表格吗？**（飞书多维 / 钉钉 AI 表格 / Notion / Airtable）→ 推荐 AI 表格字段
- 瓶颈是数据 / 权限 / 流程而不是智能？ → 先修流程
- 一次性需求？ → 一段 prompt 就够

任一为是 → 直接给出对应轻方案，**完全跳过更重的路线**。

---

## 文件结构

```
ai-solution-router/
├── SKILL.md                              # Skill 入口；核心规则 + 工作流编排
├── README.md                             # 本文件
├── test-prompts.json                     # 15 个回归测试用例
├── agents/
│   └── openai.yaml                       # Codex UI 卡片元数据
└── references/
    ├── triage.md                         # 默认入口：3 问查表 + Table/Schedule/Knowledge/HighRisk 4 个 Pre-Check
    ├── discovery.md                      # 升级模式：加权评分 + Edge Cases
    ├── decision-tree.md                  # 3 轴决策树（A 含 table）+ Schedule/Table 两个 Overlay + 难度门槛 + 15 条反例
    ├── enterprise-knowledge-rag.md       # 知识层：Data-Form 决策 + 9 平台 stack-aware 表
    ├── output-templates.md               # 产物模板：Newbie / Lightweight / Full + 8 路线 Sub-template
    ├── skill-creation-guide.md           # 推 Skill 时如何生成完整 skill 文件
    ├── demo-build-branch.md              # 推 Coding Agent 时的产品 demo 分支（optional）
    └── product-demo-templates.md         # demo 分支用的 PRD/Mock/验收模板（optional）
```

各文件的具体职责见 `SKILL.md` 末尾的 Reference Loading Guide。

---

## 设计原则

| 原则 | 解释 |
|---|---|
| **Single-Tool Bias** | 默认只推一个工具。组合方案只在 Triage 表 1 行 + Discovery 明确触发 5 条 combination trigger 时出现。 |
| **Minimum Sufficient Solution** | 永远先试最便宜的方案。Prompt 能做就不要 Skill；Skill 能做就不要 Workflow。 |
| **Capability Gates** | 推 Coding/RAG/Workflow 前都要问"有没有人维护"。没有 → 自动降级到轻方案。 |
| **Platform Neutrality** | 不偏心任何一家协作平台（钉钉/飞书/企微/Notion/...）。用户用什么就用什么；没说就列 2-3 个跨生态例子。 |
| **Stop-at-Handoff** | 推荐 Coding Agent 时只给 handoff prompt，**不自动跳进 PRD/Mock/ACCEPTANCE**。用户明确说"继续"才进 demo-build 分支。 |
| **Revisable Recommendations** | 每个推荐都附 Upgrade 和 Downgrade 触发信号，推荐被设计成"暂时正确、可演化"。 |
| **Human Review for High-Risk** | 命中合规 / 财务 / 法律 / 对外 / 不可逆操作时强制升级到 Discovery，输出必须含"人工审核"段。 |

---

## 自定义指南

### 改 Triage 路由表

`references/triage.md` 里有一张 12 行的查表（频率 × 场景）。如果你公司常用的工具不在示例里，直接改"工具示例"那一列即可。Update freely；其他逻辑不会受影响。

### 改企业知识层推荐

`references/enterprise-knowledge-rag.md` 里有一张 9 平台 stack-aware 表。如果你们公司用了表里没列的平台（如 Lark Suite 私有部署、ServiceNow），加一行即可。

### 改高风险关键词清单

`references/triage.md` 的 "High-Risk Keyword Auto-Escalate" 段。如果你们公司有特别的合规关注点（如"涉及未上市公司"、"涉及内部投资决策"），加进清单。

### 改难度阶梯

`references/decision-tree.md` 的 "Difficulty Ordering"。如果你们团队对某些工具特别熟（比如自建 RAG 像家常便饭），可以下调那个工具的星级。

---

## 测试

`test-prompts.json` 提供 15 个回归测试用例，覆盖：

| ID | Tag | 测什么 |
|---|---|---|
| 1 | triage / high-frequency | 标准 Triage 流程 |
| 2 | knowledge-keyword / stack-aware | 钉钉场景下不误推自定义 RAG |
| 3 | coding-agent / stop-at-handoff | 不自动跳进 demo-build 大流程 |
| 4 | chat-only / high-risk | "对客户"触发高风险升级 |
| 5 | step-0 / not-ai | Excel 公式能做的不推 AI |
| 6 | high-risk / compliance | 合同审查强制人审 |
| 7 | edge-case-4 / user-tool-preference | 用户指定 Dify 时不忽略偏好 |
| 8 | in-repo-coding / light-path | 改 bug 走 In-Repo 而不是 Greenfield |
| 9 | high-freq / scheduled-task | 周频 + 公开网页 → Scheduled AI Task |
| 10 | vague-q1 / ambiguity | "我想用 AI 提效"先问具体小事 |
| 11 | scheduled-task / fast-path | "每天 7 点推 5 条 AI 新闻" → 一轮直出 ChatGPT Tasks |
| 12 | scheduled-task / escalation-needed | 登录 ERP + 群发销售 → 不能用内置定时，升 Workflow |
| **13** | **ai-table / feishu** | **飞书多维 500 条评论分类 → 一轮直出 AI 字段捷径** |
| **14** | **ai-table / dingtalk** | **钉钉表格订单提取 3 个字段 → 钉钉 AI 字段提取** |
| **15** | **ai-table / anti-rag-misroute** | **Notion 200 条会议记录摘要 → 纠正用户"要 RAG"的错觉，推 Notion AI Autofill** |

### 怎么跑回归

任何对 `SKILL.md` 或 `references/` 的改动后，把 15 个 case 依次喂给装好的 skill，对照 `expected_behavior` 看是否一致。

简单方式：用 Cursor 起 15 个对话，每个粘一个 prompt，按 case 描述检查。

---

## 版本演化

这个 skill 是社区迭代出来的，几个里程碑版本：

| 版本 | 关键改动 |
|---|---|
| v4 | 初版：6 路线 + 单步决策 + 95% 置信度 + 钉钉示例偏心 |
| v5 | Triage 双模式 + Step 0（不一定要 AI）+ 9 平台 stack-aware 表 + In-Repo/Greenfield 子路 + 加权评分 + Anti-Patterns + 用户视角 yaml |
| v7 | 3 独立轴模型 + 难度阶梯（★~★★★★★）+ Difficulty Gate + Upgrade/Downgrade 触发表 + Route Confirmation 步骤 + Edge Cases 4 个 + Platform Neutrality 元规则 + test-prompts.json |
| v8 | = v7 推理骨架 + v5 用户体验外壳。Triage Mode 作为快速入口 + 3 轴模型作为底层逻辑 + 完整 Edge Cases + 10 个测试 case + 业务话术对照表 |
| v8.1 | 新增第 7 条路线 **Scheduled AI Task**：识别 ChatGPT Tasks / 钉钉悟空定时 / 飞书智能伙伴定时 / 豆包定时等内置调度器，作为 Browser Agent / Workflow 之前的轻量优选。Triage 加 Schedule Pre-Check + 高频行拆分手动/定时；decision-tree 加 Schedule Overlay；output-templates 加 Scheduled AI Task Sub-template；test cases 11、12 覆盖新路径与升级边界 |
| **v8.2 (当前)** | **新增第 8 条路线 AI Table**：覆盖飞书多维表格 AI 字段捷径 / 钉钉 AI 表格 / Notion AI Autofill / Airtable AI fields / 腾讯 WPS AI 智能列。Axis A 加 "table" 选项；Triage 加 Table Pre-Check + b 行拆分「每行 AI 处理」vs「跨行复杂分析」；decision-tree 加 Table Overlay + 平台矩阵；enterprise-knowledge-rag 加 Data-Form 决策（结构化数据 ≠ 文档 ≠ 知识库）+ Anti-Pattern 防止误推 RAG；output-templates 加 AI Table Sub-template（4 大平台各有操作指引）；test cases 13/14/15 覆盖飞书/钉钉/Notion 三大平台 + 纠正用户 RAG 错觉的反例 |

详细 diff 看 `CHANGELOG.md`（如果你想要的话，按 commit 自动生成即可）。

---

## 已知限制

- 推荐里的工具名是 2026-Q2 例子，会过时。Update freely。
- 评测集只有 15 个 case，覆盖典型路径但不可能穷尽。欢迎加 case。
- 默认面向中文使用场景；英文用户的多选题需要手动翻译（skill 里有规则但未提供英文模板）。
- 复杂组合（3+ 工具叠加）的判断仍较弱，可能过度推荐或漏推荐组合。

---

## 贡献

欢迎 PR / Issue。重点欢迎：

- **新的 test-prompts**：你公司的真实需求 prompt + 你认为正确的路由。
- **新平台的知识层推荐**：你公司用了 9 平台表里没的协作平台。
- **新的 Anti-Pattern**：你看到 router 推得离谱的 case。
- **业务话术翻译**：把更多技术名翻译成业务同事看得懂的话。
- **多语言**：英文 / 日文 / 其他语言的 Triage 多选题模板。

### 改动后请

1. 跑一遍 `test-prompts.json` 的 10 个 case 看有没有回归。
2. 如果加了新 case，更新 `test-prompts.json` 并标 tag。
3. 改了路由逻辑，更新对应 reference 文件，**不要重复定义**（每个规则只在一个地方写）。
4. 改了 SKILL.md 的核心规则，记得在 PR 描述里说明影响哪些 case。

---

## 许可证

MIT License — 随便用、改、商用、再分发，保留版权声明即可。

---

## 致谢

- v4 → v5 → v7 → v8 演化过程中的所有贡献者。
- Anthropic / OpenAI / Cursor / DingTalk / Feishu 等团队提供的 skill 机制让这种"可移植决策助手"成为可能。
- 所有在公司里被同事问"这事用啥 AI 工具"问到崩溃、最终决定写一个 router 的工具选型负责人 :)

---

## 联系 / 反馈

- 用着不对劲？开一个 Issue 把对话截图贴上来，最好附 case 编号。
- 想加新 case？直接 PR `test-prompts.json`。
- 商业合作 / 内训：（自己加联系方式）
