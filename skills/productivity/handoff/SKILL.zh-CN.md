---
name: handoff
description: 把当前对话压缩成一份交接文档（handoff document），便于另一个智能体继续接手。
argument-hint: "下一次会话要用来做什么？"
---

写一份交接文档，总结当前对话内容，让一个全新的智能体可以继续推进工作。把它保存到由 `mktemp -t handoff-XXXXXX.md` 生成的路径（写入前先读一下该文件）。

如果下一次会话需要用到某些技能（skills），建议使用哪些技能。

不要重复已经被其他产物捕获的内容（PRD、计划（plans）、ADR、issues、commits、diffs）。改为用路径或 URL 引用它们。

如果用户传入了参数，把它们当作下一次会话要聚焦的方向描述，并据此调整交接文档。
