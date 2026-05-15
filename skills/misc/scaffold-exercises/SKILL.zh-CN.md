---
name: scaffold-exercises
description: 创建包含 sections、problems、solutions、explainers 的练习目录结构，并通过 lint（linting）。当用户想搭建练习（scaffold exercises）、创建练习骨架（exercise stubs）或建立新的课程章节（course section）时使用。
---

# Scaffold Exercises（搭建练习目录）

创建能通过 `pnpm ai-hero-cli internal lint` 的练习目录结构，然后用 `git commit` 提交。

## 目录命名（Directory naming）

- **章节（Sections）**：放在 `exercises/` 下的 `XX-section-name/`（例如 `01-retrieval-skill-building`）
- **练习（Exercises）**：放在章节目录下的 `XX.YY-exercise-name/`（例如 `01.03-retrieval-with-bm25`）
- 章节编号 = `XX`，练习编号 = `XX.YY`
- 名称用 dash-case（小写 + 连字符）

## 练习变体（Exercise variants）

每个练习至少需要以下子目录之一：

- `problem/` - 学员工作区，包含 TODOs
- `solution/` - 参考实现（reference implementation）
- `explainer/` - 概念材料，不包含 TODOs

做骨架（stubbing）时，除非计划明确说明，否则默认建 `explainer/`。

## 必需文件（Required files）

每个子目录（`problem/`、`solution/`、`explainer/`）都需要一个 `readme.md`，并且：

- **不能为空**（即便只有一个标题行也可以）
- 不包含断链（broken links）

做骨架时，创建一个最小 readme：标题 + 描述：

```md
# Exercise Title

Description here
```

如果子目录里有代码，还需要一个 `main.ts`（>1 行）。但对骨架练习来说，只写 readme 也可以。

## 工作流（Workflow）

1. **解析计划（Parse the plan）** - 提取章节名、练习名与变体类型
2. **创建目录（Create directories）** - 对每个路径执行 `mkdir -p`
3. **创建 readme 骨架（Create stub readmes）** - 每个变体目录一个 `readme.md`，至少包含标题
4. **运行 lint（Run lint）** - 执行 `pnpm ai-hero-cli internal lint` 验证
5. **修复错误（Fix any errors）** - 迭代直到 lint 通过

## Lint 规则摘要（Lint rules summary）

linter（`pnpm ai-hero-cli internal lint`）会检查：

- 每个练习包含子目录（`problem/`、`solution/`、`explainer/`）
- `problem/`、`explainer/` 或 `explainer.1/` 至少存在一个
- 主子目录里存在 `readme.md` 且非空
- 不允许 `.gitkeep` 文件
- 不允许 `speaker-notes.md` 文件
- readmes 中不允许断链
- readmes 中不允许 `pnpm run exercise` 命令
- 每个子目录要求 `main.ts`，除非该子目录是“仅 readme”

## 移动/重命名练习（Moving/renaming exercises）

当重编号或移动练习时：

1. 使用 `git mv`（不要用 `mv`）重命名目录——保留 git 历史
2. 更新数字前缀保持顺序
3. 移动后重新跑 lint

示例：

```bash
git mv exercises/01-retrieval/01.03-embeddings exercises/01-retrieval/01.04-embeddings
```

## 示例：从计划做骨架（Example: stubbing from a plan）

给定一个计划：

```
Section 05: Memory Skill Building
- 05.01 Introduction to Memory
- 05.02 Short-term Memory (explainer + problem + solution)
- 05.03 Long-term Memory
```

创建：

```bash
mkdir -p exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer
mkdir -p exercises/05-memory-skill-building/05.02-short-term-memory/{explainer,problem,solution}
mkdir -p exercises/05-memory-skill-building/05.03-long-term-memory/explainer
```

然后创建 readme 骨架：

```
exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer/readme.md -> "# Introduction to Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/explainer/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/problem/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/solution/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.03-long-term-memory/explainer/readme.md -> "# Long-term Memory"
```
