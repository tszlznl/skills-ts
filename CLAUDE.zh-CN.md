# Matt Pocock Skills（Matt Pocock Skills）

一套由 Claude Code（Claude Code）加载的智能体技能（agent skills）集合（斜杠命令（slash commands）与行为（behaviors））。这些技能按“桶（bucket）”分类组织，并由 `/setup-matt-pocock-skills` 生成的“按仓库（repo）配置”来消费/使用。

## 语言

**问题跟踪器（Issue tracker）**：
承载某个仓库（repo）问题（issues）的工具——GitHub Issues（GitHub Issues）、Linear（Linear）、本地 `.scratch/` 的 Markdown 约定（markdown convention）等。`to-issues`、`to-prd`、`triage`、`qa` 等技能会读取并写入其中内容。
_避免使用_：backlog manager、backlog backend、issue host

**问题（Issue）**：
问题跟踪器（Issue tracker）里的一条工作单元（tracked unit of work）——bug、任务（task）、PRD 或由 `to-issues` 生成的切片（slice）。
_避免使用_：ticket（仅在引用外部系统原文确实称其为 ticket 时使用）

**分诊角色（Triage role）**：
在分诊（triage）过程中，应用到某个问题（Issue）上的规范化“状态机（state-machine）标签”（例如 `needs-triage`、`ready-for-afk`）。每个角色都会通过 `docs/agents/triage-labels.md` 映射到问题跟踪器（Issue tracker）里的真实标签（label）字符串。

## 关系

- 一个问题跟踪器（Issue tracker）包含多个问题（Issues）
- 一个问题（Issue）在任意时刻只携带一个分诊角色（Triage role）

## 已标记的歧义

- “backlog” 之前同时被用来表示“承载问题的工具”与“工具里的工作集合”——现已澄清：工具称为问题跟踪器（Issue tracker）；“backlog”不再作为领域术语（domain term）使用。
- “backlog backend” / “backlog manager”——现已合并归一为问题跟踪器（Issue tracker）。
