# `thread_unarchive.rs` — 测试模块

## 测试覆盖范围
测试 `thread/unarchive` JSON-RPC 方法，验证将归档的 rollout 文件移回 sessions 目录。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_unarchive_moves_rollout_back_into_sessions_directory` | 取消归档将 rollout 移回 sessions 目录 |
