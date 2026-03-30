# `thread_name_websocket.rs` — 测试模块

## 测试覆盖范围
测试通过 WebSocket 传输的线程名称更新广播功能，验证已加载和未加载线程的名称更新通知。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_name_updated_broadcasts_for_loaded_threads` | 已加载线程的名称更新广播到其他连接 |
| `thread_name_updated_broadcasts_for_not_loaded_threads` | 未加载线程的名称更新也广播到其他连接 |
