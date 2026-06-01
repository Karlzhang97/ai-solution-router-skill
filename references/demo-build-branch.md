# Demo Build Branch

> **Optional extended module.** The router's main job ends at the **Coding Agent Handoff Prompt**. Only load this file when the chosen route is Coding Agent AND the user explicitly asks to continue from handoff into full product development inside the same conversation.
>
> If you've just delivered the handoff prompt and the user hasn't asked to continue, **stop**. Do not auto-load this file.

When you do load it, there are **two sub-routes** — pick before doing anything else:

- **A. In-Repo Task** — modifying an existing codebase (bug, feature, refactor, tests, migration). Light path.
- **B. Greenfield Demo** — turning a vague need or a PRD into a runnable new product/demo. Heavy path with PRD/Mock/ACCEPTANCE.

This branch is standalone: do not assume another skill is installed.

## Sub-Route Decision

Ask one question if it is not obvious from the user's previous answers:

```text
你现在的目标是：
A. 在已有项目里改一处（加功能 / 修 bug / 写测试 / 迁移）—— 我用轻路径，不写 PRD。
B. 从 0 做一个新 demo / 内部工具 / 原型 —— 我用重路径，含 PRD / Mock / 验收。
```

If user clearly says "已有项目" / "我现在的代码库" / "改 bug" / "加测试" → **A**.
If user describes a product idea, target user, or hands over a transcript / PRD → **B**.

---

## A. In-Repo Task (Light Path)

### When to use

- 已有代码库（GitHub repo / 本地工程 / 公司单仓 / monorepo 子项目）。
- 目标是局部改动，而不是新搭一个产品。
- 不需要写 PRD、不需要 mock 外部服务、不需要 ACCEPTANCE 文档。

### Non-Negotiable Rules

- Inspect the repo first (file tree + relevant files) before proposing changes.
- Follow existing project conventions (language style, test framework, lint rules, file layout).
- Keep the change minimal — list the affected files **before** writing code.
- Run the project's tests / build / typecheck after the change.
- Document any assumptions in the PR / commit message, not buried in code.

### Workflow

1. **Inspect**: ls the repo, read the closest 3-5 files to the change, identify the test command and CI config.
2. **Plan**: list affected files, list expected diff in 2-3 bullet points, ask once if anything is unclear.
3. **Implement**: minimal diff.
4. **Verify**: run tests / build; paste the output.
5. **Hand off**: write a 1-page change summary (what changed, why, how to verify, what was not done).

### Coding Agent Handoff Prompt (In-Repo)

```text
你在一个已有项目里工作。

目标：
[一句话目标]

约束：
- 先列出预计影响的文件，再写代码。
- 遵守项目现有的代码风格、测试框架、目录约定。
- 改动最小化，不引入不必要的依赖。
- 完成后跑：[测试命令 / 构建命令 / typecheck 命令]，把输出贴给我。
- 任何假设写在 PR 描述里，不要藏在代码注释里。

交付物：
- 最小可运行的 diff
- 1 页变更摘要：改了什么 / 为什么 / 如何验证 / 哪些未做
```

### What NOT to do in A

- Do not generate `产品PRD.md` / `ACCEPTANCE.md` / `MOCK_SERVICES.md` / `产品方案文档.md`.
- Do not propose a stack switch.
- Do not refactor unrelated files "while I'm here".
- Do not run `npm init` / `pip init` — you are inside an existing project.

---

## B. Greenfield Demo (Heavy Path)

### When to use

- 用户有想法但没代码 / 没产品。
- 从访谈 / PRD / 业务问题出发。
- 需要 demo 给老板、客户或团队看，证明 "AI 能做"。
- 需要 mock 数据、mock 服务、可以本地跑通的 demo。

### Non-Negotiable Rules

- Ask one question at a time until reaching 95% confidence.
- Produce Chinese-facing artifacts unless the user asks otherwise.
- Mark inferred details as `【假设】`.
- Prefer local runnable demos over elegant but fragile architecture.
- Mock every external dependency unless the user has already verified access.
- Include sample data whenever uploads, imports, or seeded records are required.
- Verify before claiming completion, or state exactly what was not verified.

### Entry Routing

| User input | Start point |
| --- | --- |
| Vague product idea or business problem | Discovery |
| Transcript / interview notes | Requirement analysis |
| Product solution doc | PRD |
| PRD | Technical delivery docs |
| Technical docs | Implementation |
| Existing codebase with goal | 走 **A. In-Repo Task** 不是 B |

If the entry is unclear, ask one question:

```text
你现在手里最完整的材料是哪一种：一句话想法、访谈记录、产品方案、PRD、技术文档？
（如果你已经有代码项目，我会走另一条更轻的 In-Repo 路径。）
```

### Workflow

1. **Discovery**
   - One question at a time.
   - 95% confidence on goal, user, current workflow, MVP boundary, data, dependencies, risks, demo success.
   - If user gave a transcript, skip basic discovery and analyze the transcript first.

2. **Requirement Analysis**
   - Read `references/product-demo-templates.md` § "Requirement Analysis Templates".
   - Produce:
     - `01_访谈分析报告.md`
     - `02_追问补全分析报告.md`

3. **Product Documents**
   - Read `references/product-demo-templates.md` § "Product Plan and PRD Templates".
   - Produce:
     - `产品方案文档.md`
     - `产品PRD.md`

4. **Technical Delivery Docs**
   - Read `references/product-demo-templates.md` § "Technical Delivery Templates".
   - Produce:
     - `TECH_DESIGN.md`
     - `MOCK_SERVICES.md`
     - `FUTURE_INTEGRATION.md`
     - `ACCEPTANCE.md`
     - `README.md`
     - `DEMO_DATA/`

5. **Implementation**
   - Build the shortest complete demo path.
   - Use existing project conventions first.
   - If greenfield, choose a low-setup stack.
   - Mock all external dependencies by default.
   - Include a 3-5 minute demo script.

6. **Verification**
   - Run install / build / test when available.
   - Start the app if possible.
   - Complete the demo path using sample data.
   - Confirm no external account / key is required in mock mode.
   - State any unverified gaps.

### Default Artifact Structure

```text
project/
  docs/
    01_访谈分析报告.md
    02_追问补全分析报告.md
    产品方案文档.md
    产品PRD.md
    TECH_DESIGN.md
    MOCK_SERVICES.md
    FUTURE_INTEGRATION.md
    ACCEPTANCE.md
  DEMO_DATA/
    README.md
    sample_*.csv/xlsx/pdf/json
  README.md
  .env.example
  src/
```

Adapt paths to existing repo conventions when relevant.

### Coding Agent Handoff Prompt (Greenfield, full)

```text
你处于实现模式。请把已澄清的业务需求和产品文档变成一个本地可运行的 demo。

优先级：
1. mock 模式下 demo 本地能跑。
2. 实现最短完整用户路径（样例数据 / 输入 → 最终输出）。
3. 所有外部依赖默认 mock。
4. 架构保持简单、与已有项目一致。
5. 更新 README / 样例数据 / 验收文档，使其与真实运行路径一致。
6. 在声称完成前先跑验证。

交付物：
- 可运行代码
- README（含安装 / 运行 / demo 步骤）
- DEMO_DATA（真实感的样例数据）
- Mock service / mock adapter 文档
- 验收清单和 demo 脚本
```

## Stop-at-Handoff Hard Rule

Even inside this branch, the router defaults to:

1. Producing the handoff prompt (In-Repo or Greenfield).
2. Asking the user if they want to continue further.
3. Stopping unless they say yes.

Do not chain into PRD / tech doc generation just because the user is in this branch. Wait for explicit go-ahead at each major artifact (PRD, tech doc, implementation).
