# AI Solution Router

> **三个问题之内，告诉你这件事最适合用哪一种 AI 工具。**

一个通用的 AI 选型 skill：你描述一件想做的事，它会推一个最简单、能跑通的 AI 工具，附上**直接可粘贴的 prompt** + 操作步骤 + 难度评估。

[![Format](https://img.shields.io/badge/format-skill-green.svg)](#安装)
[![License](https://img.shields.io/badge/license-MIT-lightgrey.svg)](#许可证)

---

## 它解决什么问题

公司里很多同事面对一件具体的工作，知道"应该能用 AI"，但卡在：

- 不知道 AI Chat、AI 表格、定时任务、自动化流程、AI 编程助手、知识库这些方案哪一个最合适
- 选了过重的方案（搭个 workflow 跑一句话能解决的事）
- 选了过轻的方案（每天手动复制粘贴 100 次）

这个 skill 就是来帮你**省掉这一步纠结**。

适合的人：

- 完全不懂技术、但被要求"用 AI 提效"的同事
- 工具选型负责人 / 内训师 / 团队 lead
- 写代码前先想清楚"这件事真的需要写代码吗"的开发者

---

## 快速体验

> **你**：我们用飞书多维表格，里面有 500 条客户评论，想让 AI 自动分成「投诉 / 咨询 / 表扬 / 建议」四类。

> **AI**：
>
> **推荐**：飞书多维表格的 **AI 字段捷径 → 分类**（难度 ★★）
>
> **怎么用**：
> 1. 在评论列右侧 **+** 新增字段 → **探索字段捷径** → 搜「分类」
> 2. 配置：源字段选「评论列」、添加 4 个类别、加 2-3 条示例
> 3. 点 **生成前 5 行** 预览 → 没问题就 **应用到全部**
>
> **不用搭 Skill、不用上 RAG、不用写代码。**

整个交互 **1 轮、30 秒内**。换成其他任务（写邮件 / 改 bug / 每周新闻摘要 / 整理客户工单……）它会推完全不同的方案，逻辑一致。

---

## 安装

### 最简单：直接让 AI 帮你装

打开你常用的 AI 工具（Cursor / Claude Code / Codex CLI / ChatGPT 都行），把下面这段整段发给它：

```text
帮我安装这个 skill：https://github.com/<owner>/ai-solution-router

要求：
- 拉到本工具默认的 skills 目录（Cursor 是 ~/.cursor/skills/，Codex 是 ~/.codex/skills/，Claude Code 是 ~/.claude/skills/，按你识别到的工具放）
- 装完后告诉我怎么调用
```

现在主流 AI 工具都能识别 GitHub URL，自己 clone + 放到正确位置 + 提示触发方式。不用手动复制文件夹。

### 一行命令（power user）

```bash
git clone https://github.com/<owner>/ai-solution-router.git ~/.cursor/skills/ai-solution-router
```

Codex / Claude 同理，把目标换成 `~/.codex/skills/` 或 `~/.claude/skills/`。Windows 用 `%USERPROFILE%\.cursor\skills\` 等价路径。

### 用 ChatGPT / 钉钉悟空 / 飞书智能伙伴 / 自建 Bot

这类托管平台没有"本地 skills 目录"，让 AI 帮你做：

```text
读取 https://github.com/<owner>/ai-solution-router 这个仓库：
1. 把 SKILL.md 全文贴进我这个 GPT / 智能体的系统提示词
2. 把 references/ 下面所有 md 上传到 Knowledge / 知识库
3. agents/openai.yaml 里的 default_prompt 设置成对话开场白
```

### 验证装好了

随便跑一个 prompt：

```text
帮我判断这件事最适合用哪种 AI 方案：我每天要把客户在微信群里发的反馈截图整理成表格。
```

如果它先**问 1-2 个问题再给推荐**（而不是直接甩答案），就说明 skill 触发成功了。

---

## 工作原理（一段话就够）

收到任务后，skill 会：

1. **快速判断**有没有现成的轻量方案能直接搞定（主流 AI 工具内置的 chat / 定时 / 表格 AI 字段；或者干脆不需要 AI）
2. 不能直接搞定时，**最多问 3 个问题**：任务是什么、做的频率多高、主要在哪里发生
3. 推**一个**最合适的工具 + 可粘贴的 prompt + 假设清单 + "什么时候该换另一个方案"

涉及高风险（合规 / 财务 / 法律 / 对外发布等）时会自动切到深度模式多问几个问题，避免推错。

完整的判断逻辑写在 `references/` 下面那几个 md 文件里，平时不用看。

---

## 文件结构

```
ai-solution-router/
├── SKILL.md                       # Skill 入口
├── README.md                      # 本文件
├── test-prompts.json              # 回归测试用例
├── agents/openai.yaml             # ChatGPT/Codex 的卡片元数据
└── references/                    # 按需加载的判断逻辑
    ├── triage.md                  # 默认 3 问流程
    ├── discovery.md               # 深度问答（高风险/模糊场景）
    ├── decision-tree.md           # 完整决策树
    ├── enterprise-knowledge-rag.md
    ├── output-templates.md
    ├── skill-creation-guide.md
    ├── demo-build-branch.md       # （可选）
    └── product-demo-templates.md  # （可选）
```

---

## 自定义

最常见的两种改动：

- **改工具示例**：`references/triage.md` 里的查表，把推荐工具改成你公司常用的（例如把 ChatGPT 换成豆包 / 智谱清言）。
- **改高风险关键词**：`references/triage.md` 的 High-Risk 清单，加你公司特别在意的合规点。

其他逻辑（路由、置信度、模板）几乎不需要动。

---

## 测试

`test-prompts.json` 里有一组覆盖典型场景的回归用例。任何对 `SKILL.md` 或 `references/` 的改动，把这些 prompt 依次喂给装好的 skill，对照 `expected_behavior` 看结果是否一致即可。

简单做法：用 Cursor 起一组新对话，每个粘一个 prompt，按 case 描述检查。

---

## 已知限制

- 推荐里的工具名是当前主流例子，会过时——这种情况直接改 `triage.md` 的工具示例那一列即可。
- 默认面向中文使用场景；英文 prompt 会自动切英文回答。
- 复杂 3+ 工具组合的判断仍较弱，可能过度或漏推。

---

## 贡献

欢迎 PR / Issue。特别欢迎：

- 你公司的真实需求 prompt + 你认为正确的路由（加进 `test-prompts.json`）
- 看到 router 推得离谱的 case（加进 decision-tree 的 Anti-Patterns）
- 你公司用的协作平台不在示例里（加进相关查表）

---

## 许可证

MIT License — 随便用、改、商用、再分发，保留版权声明即可。
