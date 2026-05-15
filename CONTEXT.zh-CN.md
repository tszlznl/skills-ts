技能（skills）按“桶（bucket）”文件夹组织在 `skills/` 下：

- `engineering/` — 日常代码工作
- `productivity/` — 日常非代码工作流工具
- `misc/` — 保留但很少用到
- `personal/` — 与我个人环境绑定，不对外推广
- `in-progress/` — 尚未准备好发布的草稿
- `deprecated/` — 已不再使用

`engineering/`、`productivity/` 或 `misc/` 里的每个技能，都必须在顶层 `README.md` 中有引用，并且在 `.claude-plugin/plugin.json` 里有一条条目。`personal/`、`in-progress/` 与 `deprecated/` 下的技能则必须不出现在这两处。

顶层 `README.md` 里的每条技能条目，都必须把技能名链接到该技能的 `SKILL.md`。

每个桶（bucket）文件夹都有一个 `README.md`，列出桶里所有技能并提供一句话描述，其中技能名需链接到对应的 `SKILL.md`。
