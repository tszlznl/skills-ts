# 问题跟踪器（Issue tracker）：本地 Markdown（Local Markdown）

本仓库的 issues 与 PRD（Product Requirements Document）以 Markdown 文件形式存放在 `.scratch/` 下。

## 约定（Conventions）

- 每个 feature 一个目录：`.scratch/<feature-slug>/`
- PRD 存放在 `.scratch/<feature-slug>/PRD.md`
- 实现 issues 存放在 `.scratch/<feature-slug>/issues/<NN>-<slug>.md`，从 `01` 开始编号
- 分诊状态（triage state）记录在每个 issue 文件顶部附近的一行 `Status:`（角色字符串见 `triage-labels.md`）
- 评论与对话历史追加到文件末尾的 `## Comments` 标题下

## 当某个技能说“发布到 issue tracker”（publish to the issue tracker）

在 `.scratch/<feature-slug>/` 下创建新文件（必要时创建目录）。

## 当某个技能说“获取相关工单”（fetch the relevant ticket）

读取引用路径对应的文件。用户通常会直接传入路径或 issue 编号。
