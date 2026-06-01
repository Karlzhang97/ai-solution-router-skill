# Output Templates

> Headings below default to Chinese. **Always match the user's language**; translate the headings if the user writes in English.

There are three output styles. Pick by mode and confidence.

| Mode | Template to use |
| --- | --- |
| Triage Mode (default) | **Newbie Express Output** |
| Triage / Discovery, **AI Table** Pre-Check fired | **AI Table Sub-template** wrapped in Newbie Express |
| Triage / Discovery, **Scheduled AI Task** Pre-Check fired | **Scheduled AI Task Sub-template** wrapped in Newbie Express |
| Discovery Mode, High confidence + single tool | **Lightweight Recommendation** |
| Discovery Mode, Medium / Low confidence OR combination route | **Full Recommendation** |

---

## Newbie Express Output (Triage default)

Use this for every Triage Mode recommendation. Keep it under one screen.

````markdown
## 推荐工具
**[业务话术名称]**（内部技术名：[Internal Name]）
工具示例：[1-2 个具体产品]
难度：[★ ~ ★★★★★]

## 一句话理由
[为什么是它，比最接近的备选好在哪。]

## 直接拿去用
**怎么用**：
1. 打开 [工具网址 / App / 客户端]
2. 把下面这段话整段粘贴进去
3. 看它的回复，不对就告诉我

```text
[一段可粘贴的 prompt / 一个工具入口 / 一段可丢给浏览器代理的任务描述 / 一段可贴给编程助手的 handoff prompt]
```

## 知识层提示（仅在 Knowledge auto-detect 命中时出现）
- [建议优先用你公司 X 平台自带的知识搜索，能解决就别上 RAG。]

## 我做了哪些假设（如果不对告诉我）
- 【假设】[假设 1]
- 【假设】[假设 2]

## 如果你想升级
- 想精准推荐 → 告诉我"再问几个"，我进入详细模式。
- 想固化复用 → 告诉我"做成 Skill"。
- 想给团队 / 非技术员工用 → 告诉我"要部署给团队用"。

## 什么情况下我会改建议
[1 句话：最可能让推荐翻车的一个因素是什么。]
````

### Rules for Newbie Express

- Only **one** tool in "推荐工具" (the triage table allows one combination case — flag it explicitly when used).
- "直接拿去用" must include both the 3-step "怎么用" instructions AND the literal artifact (prompt / handoff / etc.).
- Use **business-friendly language** in the route name (see Triage's 业务话术对照表); put the internal technical name in parentheses for users who want to look it up.
- Assumptions must be listed, prefixed with `【假设】`.
- Fast Path outputs must end with the optional refinement invitation from `references/overlays.md`.
- Never include sections like "竞争分析" / "技术选型" / "风险矩阵" in Express mode — that belongs in Detailed Output.

---

## Lightweight Recommendation (Discovery, High confidence, single tool)

Use when discovery is done, route is obvious, and a single tool wins.

```markdown
# AI工具选型建议

## 需求理解
- 任务：
- 输入 → 输出：
- 频率：
- 置信度：__%

## 推荐路线（三轴）
- 环境 A：文本 / AI Table / Browser Agent / Coding Agent
- 控制 B：Prompt / Skill / Workflow
- 知识 K：无 / RAG / 企业知识
- 综合难度：[★ ~ ★★★★★]
- 一句话理由：

## 实现难度与前提
- 难度：[星级]
- 前提条件（需要的能力 / 资源）：

## 具体执行方案
（放对应路线的产物——见下面 Sub-template）

## 什么时候该换路线
- 升级信号：
- 降级信号：
```

---

## Full Recommendation (Discovery, Medium / Low confidence or combination)

Use when confidence is medium / low, route is a combination, or trade-offs need explanation.

```markdown
# AI工具选型建议

## 1. 需求理解
- 当前任务：
- 输入：
- 期望输出：
- 频率 / 耗时：
- 当前痛点：
- 使用者（Operator）：
- 置信度：__%（按 discovery.md 的加权评分得出）

## 2. 三轴判断
- **环境 A**：文本 / AI Table / Browser Agent / Coding Agent → 依据 + 难度门槛是否过：
- **控制 B**：Prompt / Skill / Workflow → 依据 + 难度门槛是否过：
- **知识 K**：无 / RAG / 企业知识 → 依据 + 难度门槛是否过：

## 3. 推荐路线
最终路线 = 环境 × 控制 × 知识 = [完整路线名]
综合难度：[★ ~ ★★★★★]

## 4. 实现难度评估
- 用户能力匹配：
- 如难度不匹配的降级方案：

## 5. 为什么不是其他路线
| 候选路线 | 不推荐原因 |
| --- | --- |

## 6. 具体执行方案
（放对应路线的产物——见下面 Sub-template）

## 7. 验证方法（30 分钟内可完成的 smoke test）
- 最小输入示例：
- 期望输出示例：
- 通过标准（具体可判断）：
- 不通过时的下一步：

## 8. 什么时候该换路线
- 升级信号：
- 降级信号：

## 9. 风险与人审边界
- 不可逆动作清单：
- 必须人审的节点：
- 失败兜底：

## 10. 仍需确认的假设
| 假设 | 影响 | 如何验证 |
```

---

## Combination Output (Discovery, multi-layer routes)

Use only when the deep tree confirms one tool is not enough.

```markdown
## 推荐工具组合
| 层级 | 推荐 | 作用 | 为什么需要 |
| --- | --- | --- | --- |
| 主工具 |  | 用户主要在哪里完成工作 |  |
| 知识层 |  | 内部知识检索 / RAG / 引用 |  |
| 执行层 |  | 点击网页 / 跑代码 / 执行工作流 |  |
| 可复用逻辑层 |  | Prompt / Skill / SOP 规则 |  |
| 人审层 |  | 谁在什么节点确认什么 |  |

## 组合边界
- 单工具不够的原因（引用 decision-tree.md 哪条 trigger）：
- 不建议继续增加工具的原因：
- 最小可验证版本：
```

---

# Sub-templates by Route

## AI Table Sub-template (added v8.2)

For "对表格里某列批量做提取/分类/总结/翻译/打标/编码/自定义生成" tasks. Triggered by Triage's Table Pre-Check, Discovery's Axis A = Table, or Discovery's Table Overlay. Platform ordering and upgrade/downgrade rules are canonical in `references/overlays.md`.

````markdown
## 推荐工具类型
AI 表格字段。

**平台排序**：按 `references/overlays.md` § AI Table Overlay 执行。中文用户或未说明海外工具时，先推荐 **钉钉 AI 表格** / **飞书多维表格 AI 字段捷径**；用户明确说“钉钉”时，推荐工具和操作步骤都以钉钉为主。

示例：
- 钉钉 **AI 表格** / AI 字段提取 / AI 智能公式 / AI 仪表盘 / 智能问数（通义/法睿/Kimi/ChatGLM4/DeepSeek-V3 等模型可选）
- 飞书 **多维表格 AI 字段捷径**（分类 / 信息提取 / 总结 / 翻译 / 自定义 AI 自动填充；接 DeepSeek/OpenAI/硅基流动 QwQ 等）
- **Notion AI Autofill**：Basic（Summary / Translate / Key Info / Multi-select 分类 / 自定义 prompt，Business/Enterprise 包含）+ Custom Agent（多步推理 + web/workspace 搜索，用 credits）
- **Airtable AI fields**：Categorize / Summarize / Extract / 自然语言生成公式（所有付费计划包含）
- 腾讯文档 / WPS **AI 智能列**（国内 Office 生态）

## 为什么不是 Chat / Skill / Workflow / Coding
- **不是 AI Chat**：Chat 是 1 次手动；这里要批量 × 每行自动跑。
- **不是 Skill**：Skill 是可复用 prompt，要在 IDE / Chat 里手动调用；AI Table 是数据库式字段，**输出直接落进结构化数据**。
- **不是 Workflow**：Workflow 是节点编排（节点 IO + 决策 + 调用），过重。AI Table 是声明式字段，零节点。
- **不是 Coding Agent**：零代码、5-15 分钟上手，非技术员工就能做完。

## 怎么用（按你用的平台二选一/三选一）

### 飞书多维表格
1. 把数据导入飞书多维表格（支持 Excel / CSV 直接粘贴或导入）。
2. 在数据列右侧 **+** 新增字段 → **探索字段捷径** → 搜并选择 **分类 / 信息提取 / 总结 / 翻译 / 自定义 AI 自动填充**。
3. 配置：
   - 选源字段
   - 填规则 / 分类选项 / 提取字段名 + 类型
   - 加 2-3 个 **参考示例**（飞书亲测最有效的提升准确率方式）
   - 默认开 **自动更新**（新增行自动跑）
4. 点 **"生成前 5 行"** 预览 → 不对就改规则；对了再 "应用到全部"。
5. 想用第三方模型（DeepSeek / QwQ-32B 等）→ 在 **字段捷径中心** 搜 "硅基流动 / DeepSeek"，关联 API Key 即可。

### 钉钉 AI 表格
1. 数据已在钉钉多维表格或智能表单。如果在 Excel，5 分钟内可粘贴/CSV 导入。
2. 字段类型选 **AI 字段提取**（在 AI 助理工作流里配置）：
   - 选模型（通义千问 plus 是默认首选，响应快）
   - 待提取的文本 = 引用某列
   - 要提取的字段 = 列名 + 参数类型（文本/数字/是否）+ 描述
3. 想要自动分析/打标 → 直接用 **AI 智能分析** 或 **AI 智能公式**。
4. 想要把 AI 输出对接到群消息/审批 → 用 **AI 助理工作流**：读取数据 → 调用 AI → 推送钉钉。

### Notion
1. 选数据库，新增 property → 类型选 **AI Autofill**。
2. Basic Autofill 出口：
   - **Summary** → 总结当前页 / 行内容
   - **Translate** → 翻译
   - **Key info** → 抽取关键字段
   - **Custom autofill** → 自定义 prompt
3. Multi-select 类型可选 **AI Autofill** 让 AI 自动打标 / 自动生成新选项。
4. 触发方式：手动 / 新建页面 / 编辑后 / 定时。
5. 如果要联网或跨页查询 → 切到 **Custom Agent Autofill**（消耗 credits）。

### Airtable
1. 在表里 + 新字段 → 选 **AI** 字段。
2. 用预设模板（Meeting transcript / UX Research / Feedback summary）或自定义 prompt。
3. 推荐保留 **"enable the best models"** 选项做摘要类任务。
4. 自动化更新通过 Automations 配置；新增记录会自动跑。

## 字段类型 / 用途速查
| 想做什么 | 建议字段类型 |
| --- | --- |
| 把文本分到固定几个类别 | **分类** / **多选** AI 字段 |
| 从文本里抽取特定字段（金额 / 地点 / 姓名 / 联系方式） | **信息提取** |
| 长文本变短摘要 | **总结** |
| 多语言互译 | **翻译** |
| 复杂、个性化逻辑（"根据描述自动起标题 / 写回复"） | **自定义 AI 自动填充** |
| 数字排名 / 简单计算 | **公式类捷径**（不用 AI） |
| 跨行汇总 / 仪表盘 / 自然语言查询 | **AI 仪表盘 / 智能问数**（不在 AI 字段里）|

## 准备事项
- 数据先进表格：有没有 Excel / CSV 可以一键导入？（4 个主流平台都支持）
- 列设计：源字段是什么类型？AI 字段输出是什么类型？
- 参考示例：准备 2-3 个 "input → 期望 output" 的样例，准确率提升非常明显。
- 模型选择：飞书 / 钉钉允许选第三方模型；Notion / Airtable 用平台内置。

## 升级 / 降级信号
- 需要 **跨行 / 跨表分析**（"这个月哪个销售业绩最好"） → 升 AI 仪表盘 / 智能问数 / Chat 数据分析
- 需要 **结果自动写入外部系统 / 触发审批 / 群发** → 升 Workflow + AI 表格节点（飞书 aPaaS / 钉钉悟空 / Notion Automations）
- **数据量超平台限额**（Airtable Free 1000 / Team 5 万 / Business 12.5 万；飞书多维 50 万行） → 升 Coding Agent + 数据库
- **每行都要联网查信息** → Notion Custom Agent Autofill (with web search) 或 飞书 AI 字段 + 联网模型
- **效果不稳定 / 输出 schema 老变** → 加更多参考示例；改用更强模型（飞书可切 DeepSeek-R1）；最后再考虑写 Skill
- **跑了几次没人用** → 删字段 / 暂停自动更新，降回 Chat
````

## Scheduled AI Task Sub-template

For tasks where the host AI's built-in scheduler (ChatGPT Tasks / 钉钉悟空定时 / 飞书智能伙伴定时 / 豆包定时 / Kimi 定时) carries the whole job. Triggered by Triage's Schedule Pre-Check or Discovery's Schedule Overlay.

````markdown
## 推荐工具类型
AI 工具内置定时任务。
示例：ChatGPT **Tasks** / 钉钉悟空 **定时任务** / 飞书 **智能伙伴定时执行** / 豆包 **定时** / Kimi **定时** / 智谱 **定时**。

## 为什么不是 Browser Agent 或 Workflow
- 任务是「定时跑 + 公开数据 + 摘要 / 格式化 / 推送」，host AI 内置调度器够用。
- Browser Agent 适合「登录 + 复杂网页交互」，对这类任务过重。
- Workflow 平台适合「写外部系统 / 多步审批」，对这类任务过重。

## 怎么用（以 ChatGPT Tasks 为例，其他平台同理）
1. 打开 ChatGPT，确认账号支持 Tasks（左下角名字 → Settings → Tasks 已启用）。
2. 在主对话框跟它说："**帮我创建一个定时任务**：[任务描述 + 频率 + 时间]"，或者直接复制下方的「定时任务 Prompt」整段粘贴。
3. 它会生成任务卡，确认时间和内容即可保存。
4. 之后失败 / 完成会以通知或对话提示告诉你；可随时调时间 / 暂停 / 删除。

## 定时任务 Prompt
```text
请创建一个定时任务：

频率：每[天/周/月]在 [HH:MM]（时区：[Asia/Shanghai]）
执行内容：
1. 从 [数据源：网址 / 搜索词 / RSS / 公开 API] 获取 [类型数据]
2. 按 [筛选 / 排序 / 评分标准] 处理
3. 输出为 [摘要 / 列表 / 表格 / 邮件草稿] 格式
4. 推送方式：[在 ChatGPT 对话里展示 / 发邮件 / 我自己复制走]

约束：
- 只用公开数据，不访问需要登录的站点。
- 失败时告诉我原因，不要静默跳过。
- 如果某次源不可达，跳过那一项继续，不要整个任务挂掉。
- 输出语言：[中文 / English]。
```

## 准备事项
- 数据源是否真的公开可访问（无登录、无地区限制、无 robots 屏蔽）？
- 推送渠道：是接受在对话里展示，还是必须发邮件 / IM？
- 时区：写清楚，host AI 默认时区不一定是你的本地时区。

## 升级信号 / 降级信号
- 某些源开始要登录 / 经常跑失败 → 升级到 **Scheduled Browser Agent**（Browser Agent + 定时触发）
- 需要把结果写入数据库 / Issue / CRM / 触发审批 → 升级到 **Workflow Platform with Schedule**
- 输出需要给团队多人看 / 多渠道推送 → 升级到 **Workflow Platform** 或 **Bot**
- 数据量超过 host AI 单次上下文（一次需要处理几百篇/几 MB 文本） → 升级到 **Coding Agent + cron**
- 跑了几次没人再看 → 暂停或删掉，降回手动 Chat 即可
````

## AI Chat Sub-template

````markdown
## 推荐工具类型
通用 AI 聊天。示例：ChatGPT / Claude / Gemini / 豆包 / Kimi 等。

## 怎么用
1. 打开任一支持的 AI 聊天工具
2. 把下面这段话整段粘贴进去（含中括号里的内容）
3. 看输出，不满意就告诉它怎么改

## 可直接使用的 Prompt
```text
你是一名[角色]。我要完成[任务]。

背景：
[业务背景]

输入：
[粘贴或描述输入]

请输出：
1. [输出项 1]
2. [输出项 2]
3. [输出项 3]

要求：
- [格式要求]
- [判断标准]
- [语言 / 风格要求]
- 如果信息不足，先问我最多 3 个关键问题。
```

## 升级信号 / 降级信号
- 同样的上下文复制粘贴 ≥ 3 次 → 升级为 Skill
- 需要 AI 操作网页 → 升级为 Browser Agent
- 交付物需要是文件 / 代码 → 升级为 Coding Agent
- 生成的脚本只用了一次就没再用 → 降级为 Chat（保持现状即可）
````

## Skill Sub-template

```markdown
## 推荐工具类型
制作 Skill（Codex / Claude Code / Cursor / 钉钉悟空 / GPT 等通用）。

## 下一步加载
读取 `references/skill-creation-guide.md`，再产出 skill brief 或完整 skill 文件夹。

## Skill 草案
- 名称：
- 触发场景：
- 输入：
- 输出：
- 必读上下文：
- 工作流程：
- 质量检查：
- 示例请求（2-3 个）：

## 是否需要文件化
若用户确认，创建：
- `SKILL.md`
- `references/[domain].md`（如需要）
- `agents/openai.yaml`（如在 Codex 使用）
- 打包为 `[skill-name].zip`（如需要分享）

## 升级信号 / 降级信号
- 出现审批 / 系统集成 / 跳步出事 → 升级为 Workflow
- 使用者从"我"变成"非技术同事 / 外部客户" → 升级为 Bot / Workflow
- 规则每次都在变，没有稳定模式 → 降级为 Prompt
```

## Workflow Sub-template

```markdown
## 推荐工具类型
工作流 / Bot / 自动化平台。
示例平台：Dify、扣子、钉钉机器人 / AI 助手、飞书智能伙伴 / aPaaS、n8n、Make、Zapier、企业内部工作流平台。

## 节点设计
| 节点 | 作用 | 输入 | 输出 | 执行者（AI / 人 / 系统）| 失败处理 |

## 决策规则
| 条件 | 去向 | 说明 |

## 人工确认点
| 节点 | 用户确认什么 | 不确认会怎样 |

## 测试用例
| 用例 | 输入 | 预期输出 |

## 升级信号 / 降级信号
- 实际没有审批 / 集成需求，只是"有步骤" → 降级为 Skill + 人工兜底
```

## Workflow with AI Nodes Sub-template

```markdown
## 推荐工具类型
带 AI 节点的工作流。示例：Dify / 扣子 / n8n + LLM node / Make AI module / 飞书 aPaaS + AI。

## 整体流程
（mermaid 或 ASCII 节点图）

## LLM 节点详情（每个 LLM 节点都填一份）
- 节点名：
- 上游输入字段：
- 提示词草稿：
- 期望输出 JSON schema：
- LLM 返回非法时的兜底：

## 非 LLM 节点
| 节点 | 类型（HTTP/DB/审批/通知）| 配置要点 |

## 验证用例
| 输入 | 预期路径 | 预期输出 |

## 升级信号 / 降级信号
- LLM 节点输出始终一致、规则可硬编码 → 降级为纯 Workflow（去 AI 节点）
- LLM 节点之间需要复杂上下文传递 → 升级为 AI Coding Agent 自建编排
```

## Enterprise Knowledge / RAG Sub-template

```markdown
## 推荐工具类型
知识库 / RAG 方案。

## 适用原因
（说明为什么内部知识不是简单粘贴上下文能解决）

## 知识场景判断
- 简单查阅 / 问答：优先用你公司协作平台自带的知识搜索（钉钉 / 飞书 / 企微 / Notion / Confluence / Slack / M365 / Google Workspace）。
- 知识驱动工作流 / Agent：用平台知识库 + 工作流 / Agent 能力。
- 定制应用 / 复杂权限 / 多源知识：用自定义 RAG + AI 编程工具搭建。

## 知识源清单
| 知识源 | 位置 | 权限要求 | 是否需要引用来源 |

## 验证问题集
| 问题 | 期望引用来源 | 合格标准 |

## 风险与边界
- 权限隔离：
- 知识过期：
- 引用准确性：
- 人工确认点：

## 升级信号 / 降级信号
- 实际只用几份文档且不再增长 → 降级为粘贴上下文
- 跨多源 + 复杂权限 + 需要评测 → 升级为自定义 RAG
```

## Browser Agent Sub-template

````markdown
## 推荐工具类型
网页操作 AI 代理。
示例：ChatGPT Agent / Claude Computer Use / Manus / Comet / 浏览器 AI 插件。

## 适用原因
（说明为什么需要浏览器环境，而不是粘贴文本或代码工具）

## 怎么用
1. 打开你选的浏览器代理工具
2. 把下面这段任务描述粘贴进去
3. 观察它执行，遇到登录 / 验证码 / 高风险动作时让它停下来等你

## 执行 Prompt
```text
你是浏览器执行代理。目标是：[目标]。

你可以：
- 打开网页：
- 搜索：
- 点击和填写：
- 提取信息：

约束：
- 不要提交 / 付款 / 删除 / 群发，除非我明确确认。
- 遇到登录、验证码、权限或高风险操作时暂停。
- 每完成一个阶段汇报当前结果和下一步。

输出格式：
[表格 / 清单 / 摘要 / 文件]
```

## 准备事项
- 需要登录的网站：
- 需要提供的数据：
- 需要人工确认的动作：

## 升级信号 / 降级信号
- 抓取后需要复杂数据处理 / 生成文件 → 加 Coding Agent
- 每周重复且规则固定 → 加 Skill 打包规则
- 一次性需求做完即弃 → 不必再升级
````

## Coding Agent Sub-template（两个子路线）

### A. In-Repo Task（已有代码库改动）

````markdown
## 推荐工具类型
AI 编程助手 + 现有代码库直接改动。示例：Cursor / Codex / Claude Code / Windsurf。

## 怎么用
1. 在你的项目里打开 AI 编程助手
2. 把下面这段 handoff prompt 粘贴进去
3. 它会先列出预计影响文件，让它确认后再写代码
4. 完成后跑验收命令，把输出贴回来

## 任务 Handoff Prompt
```text
你在一个已有项目里工作。

目标：
[一句话目标 + 验收命令]

要求：
- 遵守现有项目的代码规范和目录约定
- 改动最小化，先列影响文件再写代码
- 完成后跑 [测试命令 / 构建命令]，把输出贴给我
- 任何假设写在 PR 描述里
```

## 涉及文件清单（预估）
- [预估要修改的文件]

## 验收命令
```
[npm test / pytest / build 等]
```

## 升级信号 / 降级信号
- 改动越来越大、跨多模块 → 升级到 Greenfield Demo 路径
- 单次改完不再需要 → 不必升级
````

### B. Greenfield Demo（新产品 demo）

读取 `references/demo-build-branch.md` → Greenfield 子路线。**默认 router 推完 handoff prompt 就停**，只有用户明确说"继续做完产品 demo"才进入 demo-build。

```text
推荐路线：AI 编程助手 + Greenfield Demo
默认产出：Handoff Prompt（见下方）
是否继续做完整产品 demo？需要的话告诉我"继续做"，我会切到 demo-build 流程产出 PRD / 技术文档 / Mock / 验收文档。
```

#### Greenfield Handoff Prompt（默认产出，停在这）

```text
你是实现代理。请把以下需求做成一个本地可运行的最小 demo。

需求：
[一句话目标 + 关键功能 1-3 条]

约束：
- 优先本地可跑，外部依赖默认 Mock
- 不要在未验证前声称完成
- 完成后跑测试或手动验收路径
- 用现有项目约定（如果没有，用最简单的 stack）

交付物：
- 可运行代码
- README（安装 / 运行 / demo 步骤）
- 样例数据
- 验收清单
```

#### 如果用户说"继续做"

切到 `references/demo-build-branch.md`，按 Greenfield 子路完整跑：访谈分析 → PRD → 技术文档 → Mock → 实现 → 验收。
