---
name: prototype
description: 在正式投入之前，用一次性原型（throwaway prototype）把设计“跑通”，先回答关键问题。分两条分支：用于状态/业务逻辑问题的可运行终端应用（terminal app）；或在同一路由（route）上生成多个差异巨大的 UI（User Interface）变体，通过 URL 查询参数切换。用户想做原型、校验数据模型/状态机（state machine）、做 UI 草图、探索设计选项，或说“prototype this”“let me play with it”“try a few designs”时使用。
---

# Prototype（原型）

原型（prototype）是 **用一次性代码回答一个问题**。问题决定原型的形态。

## 选择分支（Pick a branch）

识别你要回答的是什么问题——来自用户提示、周边代码，或（如果用户在线）直接询问：

- **“这段逻辑 / 这个状态模型靠谱吗？”** → [LOGIC.zh-CN.md](LOGIC.zh-CN.md)。构建一个小型交互式终端应用（terminal app），把状态机（state machine）推过那些纸面上难以推理的案例。
- **“它应该长什么样？”** → [UI.zh-CN.md](UI.zh-CN.md)。在同一个路由上生成多个差异巨大的 UI 变体，通过 URL 查询参数（search param）和一个悬浮底部栏进行切换。

两条分支会产出完全不同的产物——选错分支会让整个原型白做。如果问题确实模糊且联系不到用户，就默认选择更贴近周边代码的分支（后端模块 → logic；页面或组件 → UI），并在原型开头明确写出该假设。

## 两条分支都适用的规则（Rules that apply to both）

1. **从第一天起就明确它是一次性的（throwaway），并清晰标记。** 把原型代码放在它未来真实使用位置附近（紧挨着被原型验证的模块或页面），上下文更清楚——但命名要让随便一扫的人就知道它是原型，不是生产代码。对于一次性的 UI 路由（routes），遵循项目已有路由约定；不要发明新的顶层结构。
2. **一个命令可运行（One command to run）。** 用项目已有任务运行器支持的方式：`pnpm <name>`、`python <path>`、`bun <path>` 等。用户必须不经思考就能启动。
3. **默认不做持久化（No persistence by default）。** 状态放内存里。持久化往往是原型要 _验证_ 的东西，而不应该成为原型的依赖。如果问题明确涉及数据库，就用 scratch DB 或本地文件，并用清晰的 “PROTOTYPE — wipe me” 名称标记。
4. **跳过打磨（Skip the polish）。** 不写测试、不做超出“可运行（runnable）”所需的错误处理、不做抽象。目标是快速学到东西，然后删掉。
5. **显式展示状态（Surface the state）。** 每次动作之后（logic）或每次切换变体时（UI），都打印/渲染完整相关状态，让用户看清楚变化。
6. **完成后删除或吸收（Delete or absorb when done）。** 原型回答完问题后，要么删掉，要么把已验证的决策折叠进真实代码——不要让它在仓库里腐烂。

## 完成时（When done）

原型里唯一值得保留的是 _答案（answer）_。把它记录到某个耐久载体里（commit message、ADR（Architecture Decision Record）、issue，或紧挨着原型的 `NOTES.md`），并写清楚它在回答什么问题。如果用户在线，这通常是一段很快的对话；如果不在线，就留下占位内容，让他们（或你下一次）在删原型前补上结论。
