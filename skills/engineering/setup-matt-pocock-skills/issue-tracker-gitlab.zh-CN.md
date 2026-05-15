# 问题跟踪器（Issue tracker）：GitLab（GitLab）

本仓库的 issues 与 PRD（Product Requirements Document）以 GitLab（GitLab）issues 形式存放。所有操作使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI。

## 约定（Conventions）

- **创建 issue**：`glab issue create --title "..." --description "..."`。多行描述使用 heredoc（heredoc）。传 `--description -` 会打开编辑器（editor）。
- **读取 issue**：`glab issue view <number> --comments`。使用 `-F json` 获取机器可读输出。
- **列出 issues**：`glab issue list -F json`，并按需加 `--label` 过滤。
- **评论 issue**：`glab issue note <number> --message "..."`。GitLab 把评论称为 “notes”。
- **添加/移除 labels**：`glab issue update <number> --label "..."` / `--unlabel "..."`。多个 labels 可以用逗号分隔，或重复该 flag。
- **关闭**：`glab issue close <number>`。`glab issue close` 不支持关闭评论，因此先用 `glab issue note <number> --message "..."` 发布解释，再关闭。
- **合并请求（Merge requests）**：GitLab 把 PR（Pull Request）称为 “merge requests”。使用 `glab mr create`、`glab mr view`、`glab mr note` 等——形态与 `gh pr ...` 类似，只是 `pr` 变 `mr`，`comment`/`--body` 变 `note`/`--message`。

从 `git remote -v` 推断仓库 —— 在 clone 目录内运行时 `glab` 会自动处理。

## 当某个技能说“发布到 issue tracker”（publish to the issue tracker）

创建一个 GitLab issue。

## 当某个技能说“获取相关工单”（fetch the relevant ticket）

运行 `glab issue view <number> --comments`。
