# 重构候选点（Refactor Candidates）

在一轮 TDD（Test-Driven Development）循环之后，寻找：

- **重复（Duplication）** → 抽取函数/类（function/class）
- **长方法（Long methods）** → 拆成私有辅助方法（private helpers）（测试仍只测公共接口）
- **浅模块（Shallow modules）** → 合并或加深（deepen）
- **特性嫉妒（Feature envy）** → 把逻辑移动到数据所在处
- **原始类型迷恋（Primitive obsession）** → 引入值对象（value objects）
- **现有代码问题（Existing code）**：新代码暴露出的问题点
