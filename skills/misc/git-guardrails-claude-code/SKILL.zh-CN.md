---
name: git-guardrails-claude-code
description: 为 Claude Code（Claude Code）配置 hooks（hooks），在执行危险的 git 命令（push、reset --hard、clean、branch -D 等）前拦截并阻止。用户想防止破坏性 git 操作、添加 git 安全 hooks，或在 Claude Code 中禁止 git push/reset 时使用。
---

# Setup Git Guardrails（设置 Git 护栏）

设置一个 PreToolUse hook，在 Claude 执行危险 git 命令之前进行拦截与阻止。

## 会被阻止的命令（What Gets Blocked）

- `git push`（包含 `--force` 在内的所有变体）
- `git reset --hard`
- `git clean -f` / `git clean -fd`
- `git branch -D`
- `git checkout .` / `git restore .`

被阻止时，Claude 会看到一条消息，提示它没有权限执行这些命令。

## 步骤（Steps）

### 1. 确认作用域（Ask scope）

询问用户：只安装在 **当前项目**（`.claude/settings.json`），还是安装到 **所有项目**（`~/.claude/settings.json`）？

### 2. 复制 hook 脚本（Copy the hook script）

随附脚本位于：[scripts/block-dangerous-git.sh](scripts/block-dangerous-git.sh)

按作用域复制到目标位置：

- **项目级（Project）**：`.claude/hooks/block-dangerous-git.sh`
- **全局（Global）**：`~/.claude/hooks/block-dangerous-git.sh`

用 `chmod +x` 赋予可执行权限。

### 3. 把 hook 加到 settings（Add hook to settings）

把内容加到对应 settings 文件：

**项目级（Project）**（`.claude/settings.json`）：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

**全局（Global）**（`~/.claude/settings.json`）：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

如果 settings 文件已存在，把 hook 合并进已有 `hooks.PreToolUse` 数组——不要覆盖其他 settings。

### 4. 询问是否要自定义（Ask about customization）

询问用户是否想在“阻止列表”里增删任何 pattern（patterns）。按需编辑已复制的脚本。

### 5. 验证（Verify）

做一个快速测试：

```bash
echo '{"tool_input":{"command":"git push origin main"}}' | <path-to-script>
```

应当以退出码 2 退出，并向 stderr 打印一条 BLOCKED 消息。
