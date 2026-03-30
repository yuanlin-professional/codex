# `model_list.rs` — 测试模块

## 测试覆盖范围
测试 `model/list` JSON-RPC 方法，验证模型列表查询、分页、隐藏模型包含以及无效游标拒绝。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `list_models_returns_all_models_with_large_limit` | 大 limit 返回所有模型 |
| `list_models_includes_hidden_models` | 包含隐藏模型 |
| `list_models_pagination_works` | 分页查询正常工作 |
| `list_models_rejects_invalid_cursor` | 无效游标被拒绝 |
