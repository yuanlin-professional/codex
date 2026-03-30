# `experimental_api.rs` — 测试模块

## 测试覆盖范围
测试实验性 API 能力（experimental_api capability）的门控机制，验证各种实验性方法在
未声明该能力时被正确拒绝，以及非实验性操作不受该能力影响。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `mock_experimental_method_requires_experimental_api_capability` | mock/experimentalMethod 需要 experimental_api 能力 |
| `realtime_conversation_start_requires_experimental_api_capability` | thread/realtime/start 需要 experimental_api 能力 |
| `thread_start_mock_field_requires_experimental_api_capability` | thread/start 的 mock 字段需要 experimental_api 能力 |
| `thread_start_without_dynamic_tools_allows_without_experimental_api_capability` | 不含动态工具的 thread/start 不需要该能力 |
| `thread_start_granular_approval_policy_requires_experimental_api_capability` | 细粒度审批策略需要 experimental_api 能力 |
