---
name: migrate-to-shoehorn
description: 将测试文件中的 `as` 类型断言迁移为 @total-typescript/shoehorn（@total-typescript/shoehorn）。用户提到 shoehorn、想在测试里替换 `as`，或需要在测试里传入不完整数据（partial test data）时使用。
---

# Migrate to Shoehorn（迁移到 Shoehorn）

## 为什么用 shoehorn？（Why shoehorn?）

`shoehorn` 允许你在测试中传入不完整数据（partial data），同时保持 TypeScript（TypeScript）类型检查满意。它用类型安全（type-safe）的替代方案取代 `as` 断言（assertions）。

**仅限测试代码（Test code only）。** 永远不要在生产代码（production code）里使用 shoehorn。

测试里用 `as` 的问题：

- 训练自己“不用它”很难
- 必须手动指定目标类型（target type）
- 为了故意造错数据，要写双重断言（double-as）：`as unknown as Type`

## 安装（Install）

```bash
npm i @total-typescript/shoehorn
```

## 迁移模式（Migration patterns）

### 大对象但只需要少量字段（Large objects with few needed properties）

Before:

```ts
type Request = {
  body: { id: string };
  headers: Record<string, string>;
  cookies: Record<string, string>;
  // ...20 more properties
};

it("gets user by id", () => {
  // Only care about body.id but must fake entire Request
  getUser({
    body: { id: "123" },
    headers: {},
    cookies: {},
    // ...fake all 20 properties
  });
});
```

After:

```ts
import { fromPartial } from "@total-typescript/shoehorn";

it("gets user by id", () => {
  getUser(
    fromPartial({
      body: { id: "123" },
    }),
  );
});
```

### `as Type` → `fromPartial()`

Before:

```ts
getUser({ body: { id: "123" } } as Request);
```

After:

```ts
import { fromPartial } from "@total-typescript/shoehorn";

getUser(fromPartial({ body: { id: "123" } }));
```

### `as unknown as Type` → `fromAny()`

Before:

```ts
getUser({ body: { id: 123 } } as unknown as Request); // wrong type on purpose
```

After:

```ts
import { fromAny } from "@total-typescript/shoehorn";

getUser(fromAny({ body: { id: 123 } }));
```

## 什么时候用哪个（When to use each）

| Function        | Use case                                           |
| --------------- | -------------------------------------------------- |
| `fromPartial()` | Pass partial data that still type-checks           |
| `fromAny()`     | Pass intentionally wrong data (keeps autocomplete) |
| `fromExact()`   | Force full object (swap with fromPartial later)    |

## 工作流（Workflow）

1. **收集需求（Gather requirements）** - 询问用户：
   - 哪些测试文件里的 `as` 断言导致问题？
   - 是否在处理大对象，而实际只关心少量字段？
   - 是否需要为了错误测试（error testing）传入“故意错误”的数据？

2. **安装并迁移（Install and migrate）**：
   - [ ] 安装：`npm i @total-typescript/shoehorn`
   - [ ] 查找包含 `as` 断言的测试文件：`grep -r " as [A-Z]" --include="*.test.ts" --include="*.spec.ts"`
   - [ ] 用 `fromPartial()` 替换 `as Type`
   - [ ] 用 `fromAny()` 替换 `as unknown as Type`
   - [ ] 添加来自 `@total-typescript/shoehorn` 的 imports
   - [ ] 运行类型检查（type check）验证
