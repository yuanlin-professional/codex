# `thread_loaded_list.rs` — 测试模块

## 测试覆盖范围
测试 `thread/loaded/list` JSON-RPC 方法，验证已加载线程 ID 的返回和分页行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_loaded_list_returns_loaded_thread_ids` | 返回当前已加载的线程 ID 列表 |
| `thread_loaded_list_paginates` | 分页查询已加载线程正常工作 |
