# 什么时候 Mock（When to Mock）

只在 **系统边界（system boundaries）** 上 mock：

- 外部 API（external APIs）（支付、邮件等）
- 数据库（databases）（有时——优先使用测试库（test DB））
- 时间/随机性（time/randomness）
- 文件系统（file system）（有时）

不要 mock：

- 你自己的类/模块（classes/modules）
- 内部协作者（internal collaborators）
- 任何你能控制的东西

## 为可 mock 性设计（Designing for Mockability）

在系统边界上，设计易于 mock 的接口（interfaces）：

**1. 使用依赖注入（dependency injection）**

把外部依赖作为参数传入，而不是在函数内部创建：

```typescript
// Easy to mock
function processPayment(order, paymentClient) {
  return paymentClient.charge(order.total);
}

// Hard to mock
function processPayment(order) {
  const client = new StripeClient(process.env.STRIPE_KEY);
  return client.charge(order.total);
}
```

**2. 相比通用 fetch，优先 SDK 风格接口（SDK-style interfaces）**

为每个外部操作创建独立函数，而不是用一个带条件分支的通用函数：

```typescript
// GOOD: Each function is independently mockable
const api = {
  getUser: (id) => fetch(`/users/${id}`),
  getOrders: (userId) => fetch(`/users/${userId}/orders`),
  createOrder: (data) => fetch('/orders', { method: 'POST', body: data }),
};

// BAD: Mocking requires conditional logic inside the mock
const api = {
  fetch: (endpoint, options) => fetch(endpoint, options),
};
```

SDK 风格的好处：

- 每个 mock 都只返回一种特定 shape（shape）
- 测试准备（test setup）里不需要条件逻辑
- 更容易看出一个测试触达了哪些 endpoints
- 每个 endpoint 都有类型安全（type safety）
