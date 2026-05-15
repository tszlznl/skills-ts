---
name: review
description: 围绕一个固定基点（commit、branch、tag 或 merge-base）以来的变更做双轴评审：Standards（代码是否符合本仓库记录的编码规范？）与 Spec（代码是否符合来源 issue/PRD 要求？）。两个评审轴用并行子智能体（parallel sub-agents）分别执行，最后并排汇总。用户想评审某个分支、PR、进行中的变更，或说“review since X”时使用。
---

# Review（评审）

对 `HEAD` 与用户提供的固定基点之间的 diff 做双轴评审：

- **Standards** — 代码是否符合本仓库记录的编码规范（coding standards）？
- **Spec** — 代码是否忠实实现了来源 issue / PRD / spec？

两个轴都会作为 **并行子智能体（parallel sub-agents）** 运行，避免彼此污染上下文，然后由本技能汇总结果。

问题跟踪器（issue tracker）应当已经提供给你——如果缺少 `docs/agents/issue-tracker.md`，运行 `/setup-matt-pocock-skills`。

## 流程（Process）

### 1. 固定基点（Pin the fixed point）

用户说的是什么就是什么：commit SHA、branch 名、tag、`main`、`HEAD~5` 等。不要替用户做判断；直接透传。如果用户没指定，询问：“要对比什么——一个 branch、一个 commit，还是 `main`？”在拿到基点前不要继续。

只记录一次 diff 命令：`git diff <fixed-point>...HEAD`（三点 diff，因此是以 merge-base 为比较基准）。同时记录提交列表：`git log <fixed-point>..HEAD --oneline`。

### 2. 识别规格来源（Identify the spec source）

按顺序寻找来源 spec：

1. commit message 里的 issue 引用（`#123`、`Closes #45`、GitLab `!67` 等）——按 `docs/agents/issue-tracker.md` 的工作流抓取。
2. 用户作为参数传入的路径。
3. `docs/`、`specs/` 或 `.scratch/` 下与 branch 名或 feature 匹配的 PRD/spec 文件。
4. 若仍未找到，询问用户 spec 在哪里。如果用户说没有，**Spec** 子智能体将跳过并报告 “no spec available”。

### 3. 识别规范来源（Identify the standards sources）

仓库里所有记录“代码应该怎么写”的东西。常见位置：

- `CLAUDE.md`、`AGENTS.md`
- `CONTRIBUTING.md`
- `CONTEXT.md`、`CONTEXT-MAP.md`、各上下文 `CONTEXT.md`
- `docs/adr/`（架构决策也是规范的一部分）
- `.editorconfig`、`eslint.config.*`、`biome.json`、`prettier.config.*`、`tsconfig.json`（工具强制的规范——记录它们，但不要重复检查工具已检查的内容）
- 仓库根目录或 `docs/` 下的 `STYLE.md`、`STANDARDS.md`、`STYLEGUIDE.md` 等

收集这些文件列表。**Standards** 子智能体将读取它们。

### 4. 并行启动两个子智能体（Spawn both sub-agents in parallel）

发送一条消息，包含两个 `Agent` 工具调用。两个都用 `general-purpose` 子智能体。

**Standards 子智能体提示词（prompt）** 包含：

- 完整 diff 命令与 commit 列表
- 你在第 3 步找到的 standards-source 文件列表
- brief：“读规范文档，然后读 diff。逐文件/逐 hunk（如有必要）报告 diff 中每一处违反已记录规范的点。引用规范来源（文件 + 规则）。区分硬性违规与判断题。跳过工具已强制的内容。400 字以内。”

**Spec 子智能体提示词（prompt）** 包含：

- diff 命令与 commit 列表
- spec 的路径或抓取内容
- brief：“读 spec，然后读 diff。报告：（a）spec 要求但缺失或不完整的内容；（b）diff 里出现但 spec 没要求的行为（scope creep）；（c）看起来实现了但实现可能错误的需求。每条发现都引用 spec 的原文行。400 字以内。”

如果找不到 spec，则跳过 Spec 子智能体，并在最终报告中说明。

### 5. 汇总（Aggregate）

把两份报告分别放在 `## Standards` 与 `## Spec` 标题下，原样或轻微清理后输出。**不要** 合并或重排发现——这两个轴刻意分离，让用户独立看清楚。

最后用一句话总结：每个轴的发现数量，以及（如有）最严重的单个问题。

## 为什么要双轴（Why two axes）

同一个变更可能一个轴通过、另一个轴失败：

- 代码符合所有规范，但实现了错误的东西 → **Standards 通过，Spec 失败。**
- 代码完全符合 issue 要求，但违反项目约定 → **Spec 通过，Standards 失败。**

分开报告能避免一个轴掩盖另一个轴。
