---
name: write-a-skill
description: 创建具备正确结构、渐进式披露（progressive disclosure）与捆绑资源（bundled resources）的新智能体技能（agent skills）。当用户想创建/编写/构建一个新技能时使用。
---

# 编写技能（Writing Skills）

## 流程（Process）

1. **收集需求（Gather requirements）** - 询问用户：
   - 这个技能覆盖什么任务/领域（task/domain）？
   - 需要处理哪些具体用例（use cases）？
   - 需要可执行脚本（executable scripts）还是只要说明文档？
   - 是否有需要纳入的参考资料（reference materials）？

2. **起草技能（Draft the skill）** - 创建：
   - `SKILL.md`：简洁的主指令
   - 如果内容超过 500 行，则拆分额外参考文件
   - 如果需要可确定性操作（deterministic operations），则添加工具脚本（utility scripts）

3. **与用户复核（Review with user）** - 展示草稿并询问：
   - 是否覆盖了你的用例？
   - 有哪些缺失或不清晰？
   - 哪些章节需要更详细/更精简？

## 技能结构（Skill Structure）

```
skill-name/
├── SKILL.md           # 主指令（必须）
├── REFERENCE.md       # 详细文档（按需）
├── EXAMPLES.md        # 使用示例（按需）
└── scripts/           # 工具脚本（按需）
    └── helper.js
```

## SKILL.md 模板（Template）

```md
---
name: skill-name
description: Brief description of capability. Use when [specific triggers].
---

# Skill Name

## Quick start

[Minimal working example]

## Workflows

[Step-by-step processes with checklists for complex tasks]

## Advanced features

[Link to separate files: See [REFERENCE.md](REFERENCE.md)]
```

## description 字段要求（Description Requirements）

description 是 **智能体在决定加载哪个技能时唯一能看到的内容**。它会出现在系统提示词（system prompt）里，与其他已安装技能并列展示。智能体会读这些 description，并根据用户请求选择合适的技能。

**目标（Goal）**：给智能体刚好够用的信息，让它知道：

1. 这个技能提供什么能力（capability）
2. 何时/为什么触发（trigger）：具体关键词、上下文、文件类型等

**格式（Format）**：

- 最多 1024 字符
- 用第三人称（third person）书写
- 第一句：它做什么
- 第二句：`Use when [specific triggers]`

**好例子（Good example）**：

```
Extract text and tables from PDF files, fill forms, merge documents. Use when working with PDF files or when user mentions PDFs, forms, or document extraction.
```

**坏例子（Bad example）**：

```
Helps with documents.
```

坏例子没有给智能体任何区分信息，无法把它与其他“文档类技能”区别开。

## 什么时候要加脚本（When to Add Scripts）

以下情况添加工具脚本（utility scripts）：

- 操作是确定性的（deterministic），例如校验（validation）、格式化（formatting）
- 同一段代码会被反复生成
- 错误需要显式处理（explicit handling）

脚本可以节省 token，并且相对“让模型生成代码”更可靠。

## 什么时候要拆分文件（When to Split Files）

以下情况拆成独立文件：

- `SKILL.md` 超过 100 行
- 内容包含明显不同的领域（distinct domains），例如 finance vs sales schemas
- 高级功能（advanced features）很少用到

## 复核清单（Review Checklist）

草拟完成后检查：

- [ ] description 包含触发条件（“Use when...”）
- [ ] `SKILL.md` 少于 100 行
- [ ] 不包含时效性信息（time-sensitive info）
- [ ] 术语一致（consistent terminology）
- [ ] 包含具体示例（concrete examples）
- [ ] 引用深度不超过一层（one level deep）
