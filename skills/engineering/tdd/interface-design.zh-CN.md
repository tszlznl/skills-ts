# 为可测试性设计接口（Interface Design for Testability）

好的接口（interfaces）会让测试变得自然：

1. **接收依赖，不要在内部创建依赖（Accept dependencies, don't create them）**

   ```typescript
   // Testable
   function processOrder(order, paymentGateway) {}

   // Hard to test
   function processOrder(order) {
     const gateway = new StripeGateway();
   }
   ```

2. **返回结果，不要靠副作用（Return results, don't produce side effects）**

   ```typescript
   // Testable
   function calculateDiscount(cart): Discount {}

   // Hard to test
   function applyDiscount(cart): void {
     cart.total -= discount;
   }
   ```

3. **表面积小（Small surface area）**
   - 方法更少（fewer methods）= 需要的测试更少
   - 参数更少（fewer params）= 测试准备更简单
