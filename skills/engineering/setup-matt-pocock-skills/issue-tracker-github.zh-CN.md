# 问题跟踪器（Issue tracker）：GitHub（GitHub）

本仓库的 issues 与 PRD（Product Requirements Document）以 GitHub（GitHub）issues 形式存放。所有操作使用 `gh` CLI。

## 约定（Conventions）

- **创建 issue**：`gh issue create --title "..." --body "..."`。多行正文使用 heredoc（heredoc）。
- **读取 issue**：`gh issue view <number> --comments`，用 `jq` 过滤评论，并同时获取 labels。
- **列出 issues**：`gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`，并按需加 `--label` 与 `--state` 过滤。
- **评论 issue**：`gh issue comment <number> --body "..."`
- **添加/移除 labels**：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **关闭**：`gh issue close <number> --comment "..."`

从 `git remote -v` 推断仓库 —— 在 clone 目录内运行时 `gh` 会自动处理。

## 当某个技能说“发布到 issue tracker”（publish to the issue tracker）

创建一个 GitHub issue。

## 当某个技能说“获取相关工单”（fetch the relevant ticket）

运行 `gh issue view <number> --comments`。
