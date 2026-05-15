---
name: setup-matt-pocock-skills
description: 在 AGENTS.md/CLAUDE.md 与 `docs/agents/` 中设置 `## Agent skills` 区块，让工程类技能知道本仓库使用的问题跟踪器（issue tracker）（GitHub 或本地 Markdown）、分诊标签词汇（triage label vocabulary）、以及领域文档布局（domain doc layout）。在首次使用 `to-issues`、`to-prd`、`triage`、`diagnose`、`tdd`、`improve-codebase-architecture` 或 `zoom-out` 之前运行——或当这些技能缺少关于 issue tracker、triage labels 或 domain docs 的上下文时运行。
disable-model-invocation: true
---

# Setup Matt Pocock's Skills（初始化技能配置）

为每个仓库（per-repo）搭建工程技能默认假设的配置：

- **问题跟踪器（Issue tracker）** — issues 存放在哪里（默认 GitHub；也原生支持本地 Markdown）
- **分诊标签（Triage labels）** — 5 个规范分诊角色（canonical triage roles）在你的问题跟踪器里对应的 label 字符串
- **领域文档（Domain docs）** — `CONTEXT.md` 与 ADR（Architecture Decision Record）存放在哪里，以及技能读取它们的规则

这是一个“提示词驱动（prompt-driven）”的技能，而不是确定性脚本（deterministic script）。先探索、展示发现、与用户确认，然后再写入。

## 流程（Process）

### 1. 探索（Explore）

查看当前仓库以理解起始状态。读到什么算什么；不要假设任何东西存在：

- `git remote -v` 与 `.git/config` — 这是 GitHub（GitHub）仓库吗？是哪一个？
- 仓库根目录的 `AGENTS.md` 与 `CLAUDE.md` — 是否存在？其中是否已有 `## Agent skills` 区块？
- 根目录的 `CONTEXT.md` 与 `CONTEXT-MAP.md`
- `docs/adr/` 以及任何 `src/*/docs/adr/` 目录
- `docs/agents/` — 是否已经存在本技能之前输出的内容？
- `.scratch/` — 表示本地 Markdown issue tracker 约定已经在使用

### 2. 展示发现并逐项提问（Present findings and ask）

总结哪些已存在、哪些缺失。然后按三项决策 **一次一个** 带用户走：先展示一节，拿到用户答案，再进入下一节。不要一次性把三项全丢出来。

假设用户不知道这些术语是什么意思。每一节都先用短解释说明：它是什么、为什么这些技能需要它、选不同会带来什么差异。然后给出选项与默认值。

**A 节 — 问题跟踪器（Issue tracker）。**

> 解释：issue tracker 是本仓库的 issues 存放处。`to-issues`、`triage`、`to-prd`、`qa` 等技能会读写它——它们需要知道该调用 `gh issue create`、在 `.scratch/` 下写 Markdown 文件，还是遵循你描述的其他工作流。请选择你在这个仓库里真实用来跟踪工作的地方。

默认姿态：这些技能是围绕 GitHub 设计的。如果 `git remote` 指向 GitHub，优先提议 GitHub。如果 `git remote` 指向 GitLab（`gitlab.com` 或自托管），提议 GitLab。否则（或用户更偏好），提供：

- **GitHub** — issues 在仓库的 GitHub Issues（使用 `gh` CLI）
- **GitLab** — issues 在仓库的 GitLab Issues（使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI）
- **本地 Markdown（Local markdown）** — issues 以文件形式存放在本仓库 `.scratch/<feature>/` 下（适合个人项目或没有 remote 的仓库）
- **其他（Other）**（Jira、Linear 等）— 让用户用一段话描述工作流；技能会把它记录为自由文本（freeform prose）

**B 节 — 分诊标签词汇（Triage label vocabulary）。**

> 解释：`triage` 技能在处理新 issue 时，会通过状态机（state machine）推进：需要评估、等待报告者、已准备好交给 AFK（Away From Keyboard）智能体、需要人类实现、或不会修复。为此它需要应用 labels（或你问题跟踪器里的等价机制），而这些 labels 必须与你真实配置的字符串一致。如果仓库已用不同 label 名（例如用 `bug:triage` 代替 `needs-triage`），在这里映射，避免技能打出重复 labels。

五个规范角色（canonical roles）：

- `needs-triage` — 维护者需要评估
- `needs-info` — 等待报告者补充信息
- `ready-for-agent` — 规格完整，AFK-ready（智能体无需额外上下文即可接手）
- `ready-for-human` — 需要人类实现
- `wontfix` — 不会处理

默认值：每个角色的 label 字符串就等于角色名本身。询问用户是否要覆盖其中任何一个。如果问题跟踪器里还没有 labels，默认值通常就够用。

**C 节 — 领域文档（Domain docs）。**

> 解释：部分技能（`improve-codebase-architecture`、`diagnose`、`tdd`）会读取 `CONTEXT.md` 来学习项目领域语言（domain language），并读取 `docs/adr/` 来了解历史架构决策。它们需要知道仓库是单上下文（single context）还是多上下文（multi-context）（例如 monorepo 拆了前后端上下文），以便去正确位置查找。

确认布局：

- **单上下文（Single-context）** — 仓库根目录一个 `CONTEXT.md` + `docs/adr/`。多数仓库如此。
- **多上下文（Multi-context）** — 根目录 `CONTEXT-MAP.md` 指向每个上下文的 `CONTEXT.md`（通常是 monorepo）。

### 3. 确认并修改（Confirm and edit）

给用户展示草稿：

- 将要添加到 `CLAUDE.md` / `AGENTS.md`（取决于你编辑哪一个）的 `## Agent skills` 区块（文件选择规则见第 4 步）
- `docs/agents/issue-tracker.md`、`docs/agents/triage-labels.md`、`docs/agents/domain.md` 的内容

让用户在写入前先修改。

### 4. 写入（Write）

**选择要编辑的文件：**

- 如果存在 `CLAUDE.md`，编辑它。
- 否则如果存在 `AGENTS.md`，编辑它。
- 如果两者都不存在，询问用户要创建哪一个——不要替用户做决定。

当 `CLAUDE.md` 已存在时永远不要新建 `AGENTS.md`（反之亦然）——始终编辑已存在的那个文件。

如果选中的文件里已存在 `## Agent skills` 区块，就原位更新其内容，而不是追加重复区块。不要覆盖用户对周边章节的编辑。

区块内容：

```markdown
## Agent skills

### Issue tracker

[one-line summary of where issues are tracked]. See `docs/agents/issue-tracker.md`.

### Triage labels

[one-line summary of the label vocabulary]. See `docs/agents/triage-labels.md`.

### Domain docs

[one-line summary of layout — "single-context" or "multi-context"]. See `docs/agents/domain.md`.
```

然后以本技能文件夹里的种子模板（seed templates）为起点，写入三份 docs：

- [issue-tracker-github.md](./issue-tracker-github.md) — GitHub issue tracker
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md) — GitLab issue tracker
- [issue-tracker-local.md](./issue-tracker-local.md) — 本地 Markdown issue tracker
- [triage-labels.md](./triage-labels.md) — label 映射
- [domain.md](./domain.md) — domain docs 的读取规则 + 布局

对于“其他（other）”问题跟踪器，基于用户描述从零写 `docs/agents/issue-tracker.md`。

### 5. 完成（Done）

告知用户配置已完成，以及哪些工程技能会读取这些文件。并说明用户之后可以直接编辑 `docs/agents/*.md`——只有当他们要切换问题跟踪器或想从头重置时，才需要重新运行本技能。
