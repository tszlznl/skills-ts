# 深模块（Deep Modules）

出自《软件设计哲学（A Philosophy of Software Design）》：

**深模块（deep module）** = 小接口（small interface） + 大量实现（lots of implementation）

```
┌─────────────────────┐
│   Small Interface   │  ← 方法少、参数简单
├─────────────────────┤
│                     │
│                     │
│  Deep Implementation│  ← 复杂逻辑被隐藏
│                     │
│                     │
└─────────────────────┘
```

**浅模块（shallow module）** = 大接口（large interface） + 少量实现（little implementation）（避免）

```
┌─────────────────────────────────┐
│       Large Interface           │  ← 方法多、参数复杂
├─────────────────────────────────┤
│  Thin Implementation            │  ← 只是透传
└─────────────────────────────────┘
```

设计接口（interfaces）时自问：

- 我能减少方法数量吗？
- 我能简化参数吗？
- 我能把更多复杂度隐藏在内部吗？
