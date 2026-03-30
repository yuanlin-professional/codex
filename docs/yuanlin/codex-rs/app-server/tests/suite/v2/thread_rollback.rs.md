# `thread_rollback.rs` — 测试模块

## 测试覆盖范围
测试 `thread/rollback` JSON-RPC 方法，验证回滚删除最后几轮并持久化到 rollout 文件。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_rollback_drops_last_turns_and_persists_to_rollout` | 回滚丢弃最后几轮并持久化到 rollout |
