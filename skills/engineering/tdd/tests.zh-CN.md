# 好测试与坏测试（Good and Bad Tests）

## 好测试（Good Tests）

**集成风格（integration-style）**：通过真实接口（real interfaces）测试，而不是 mock 内部部件（internal parts）。

```typescript
// GOOD: Tests observable behavior
test("user can checkout with valid cart", async () => {
  const cart = createCart();
  cart.add(product);
  const result = await checkout(cart, paymentMethod);
  expect(result.status).toBe("confirmed");
});
```

特征（Characteristics）：

- 测用户/调用方真正关心的行为
- 只使用公共 API（public API）
- 能经受内部重构（internal refactors）
- 描述 WHAT（做什么），不描述 HOW（怎么做）
- 每个测试只表达一个逻辑断言（one logical assertion）

## 坏测试（Bad Tests）

**实现细节测试（implementation-detail tests）**：与内部结构耦合。

```typescript
// BAD: Tests implementation details
test("checkout calls paymentService.process", async () => {
  const mockPayment = jest.mock(paymentService);
  await checkout(cart, payment);
  expect(mockPayment.process).toHaveBeenCalledWith(cart.total);
});
```

危险信号（Red flags）：

- mock 内部协作者（internal collaborators）
- 测试私有方法（private methods）
- 断言调用次数/顺序（call counts/order）
- 只是重构且行为不变，测试却失败
- 测试名描述 HOW 而不是 WHAT
- 绕过接口用外部手段验证

```typescript
// BAD: Bypasses interface to verify
test("createUser saves to database", async () => {
  await createUser({ name: "Alice" });
  const row = await db.query("SELECT * FROM users WHERE name = ?", ["Alice"]);
  expect(row).toBeDefined();
});

// GOOD: Verifies through interface
test("createUser makes user retrievable", async () => {
  const user = await createUser({ name: "Alice" });
  const retrieved = await getUser(user.id);
  expect(retrieved.name).toBe("Alice");
});
```
