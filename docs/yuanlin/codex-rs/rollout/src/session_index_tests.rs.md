# `session_index_tests.rs` — 测试模块

## 测试覆盖范围
该文件测试会话索引文件的读写和查找逻辑。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `find_thread_id_by_name_prefers_latest_entry` | 同名多条目时优先返回最新条目 |
| `find_thread_name_by_id_prefers_latest_entry` | 同 ID 多条目时优先返回最新名称 |
