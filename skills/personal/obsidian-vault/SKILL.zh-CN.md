---
name: obsidian-vault
description: 在 Obsidian（Obsidian）知识库（vault）中搜索、创建与管理笔记，使用 wikilinks（wikilinks）与索引笔记（index notes）。用户想在 Obsidian 里查找、创建或整理笔记时使用。
---

# Obsidian Vault（Obsidian 知识库）

## Vault 位置（Vault location）

`/mnt/d/Obsidian Vault/AI Research/`

根目录基本为扁平结构（mostly flat）。

## 命名约定（Naming conventions）

- **索引笔记（Index notes）**：聚合相关主题（例如 `Ralph Wiggum Index.md`、`Skills Index.md`、`RAG Index.md`）
- 所有笔记名使用 **标题式大小写（Title case）**
- 不用文件夹做组织——用链接与索引笔记代替

## 链接（Linking）

- 使用 Obsidian（Obsidian）的 `[[wikilinks]]` 语法：`[[Note Title]]`
- 在笔记底部链接依赖/相关笔记（dependencies/related notes）
- 索引笔记就是一串 `[[wikilinks]]` 列表

## 工作流（Workflows）

### 搜索笔记（Search for notes）

```bash
# 按文件名搜索
find "/mnt/d/Obsidian Vault/AI Research/" -name "*.md" | grep -i "keyword"

# 按内容搜索
grep -rl "keyword" "/mnt/d/Obsidian Vault/AI Research/" --include="*.md"
```

### 创建新笔记（Create a new note）

1. 文件名使用 **Title Case**
2. 内容以“一个学习单元（unit of learning）”方式书写（遵循 vault 规则）
3. 在底部添加指向相关笔记的 `[[wikilinks]]`
4. 如果属于编号序列，使用分层编号方案（hierarchical numbering scheme）

### 查找相关笔记（Find related notes）

在整个 vault 中搜索 `[[Note Title]]`，找到反向链接（backlinks）：

```bash
grep -rl "\\[\\[Note Title\\]\\]" "/mnt/d/Obsidian Vault/AI Research/"
```

### 查找索引笔记（Find index notes）

```bash
find "/mnt/d/Obsidian Vault/AI Research/" -name "*Index*"
```
