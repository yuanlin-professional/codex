# `ephemeral.rs` — 测试模块

## 测试覆盖范围

验证 --ephemeral 标志对会话持久化的影响。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `persists_rollout_file_by_default` | 默认模式下创建 rollout 文件 |
| `does_not_persist_rollout_file_in_ephemeral_mode` | --ephemeral 模式下不创建 rollout 文件 |
