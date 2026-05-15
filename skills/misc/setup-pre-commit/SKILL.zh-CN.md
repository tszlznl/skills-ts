---
name: setup-pre-commit
description: 在当前仓库配置 Husky（Husky）pre-commit hooks，并集成 lint-staged（lint-staged）（Prettier）、类型检查（type checking）与测试（tests）。用户想添加 pre-commit hooks、设置 Husky、配置 lint-staged，或在提交时做格式化/类型检查/测试时使用。
---

# Setup Pre-Commit Hooks（设置提交前钩子）

## 本技能会配置什么（What This Sets Up）

- **Husky（Husky）** pre-commit hook
- **lint-staged（lint-staged）**：对所有 staged 文件运行 Prettier（Prettier）
- **Prettier（Prettier）** 配置（若缺失）
- 在 pre-commit hook 中运行 **typecheck** 与 **test** scripts

## 步骤（Steps）

### 1. 检测包管理器（Detect package manager）

检查 `package-lock.json`（npm）、`pnpm-lock.yaml`（pnpm）、`yarn.lock`（yarn）、`bun.lockb`（bun）。用已存在的那一个。不确定时默认 npm。

### 2. 安装依赖（Install dependencies）

以 devDependencies 安装：

```
husky lint-staged prettier
```

### 3. 初始化 Husky（Initialize Husky）

```bash
npx husky init
```

这会创建 `.husky/` 目录，并在 package.json 里添加 `prepare: "husky"`。

### 4. 创建 `.husky/pre-commit`

写入该文件（Husky v9+ 不需要 shebang）：

```
npx lint-staged
npm run typecheck
npm run test
```

**适配（Adapt）**：把 `npm` 替换为检测到的包管理器。如果仓库的 package.json 里没有 `typecheck` 或 `test` script，就省略对应行，并告知用户。

### 5. 创建 `.lintstagedrc`

```json
{
  "*": "prettier --ignore-unknown --write"
}
```

### 6. 创建 `.prettierrc`（若缺失）

只有当仓库里没有任何 Prettier 配置时才创建。默认值：

```json
{
  "useTabs": false,
  "tabWidth": 2,
  "printWidth": 80,
  "singleQuote": false,
  "trailingComma": "es5",
  "semi": true,
  "arrowParens": "always"
}
```

### 7. 验证（Verify）

- [ ] `.husky/pre-commit` 存在且可执行
- [ ] `.lintstagedrc` 存在
- [ ] package.json 中的 `prepare` script 为 `"husky"`
- [ ] 存在 `prettier` 配置
- [ ] 运行 `npx lint-staged` 验证可用

### 8. 提交（Commit）

把所有变更/新增文件加入暂存区并提交，提交信息：`Add pre-commit hooks (husky + lint-staged + prettier)`

这会触发新配置的 pre-commit hooks——是验证整体能跑通的良好冒烟测试（smoke test）。

## 备注（Notes）

- Husky v9+ hook 文件不需要 shebang
- `prettier --ignore-unknown` 会跳过 Prettier 无法解析的文件（图片等）
- pre-commit 先跑 lint-staged（快，只检查 staged），再跑完整类型检查与测试
