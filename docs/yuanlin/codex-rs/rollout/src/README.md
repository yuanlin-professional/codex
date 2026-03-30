# rollout/src — 源代码目录

## 功能概述
rollout crate 的源代码实现目录。管理功能灰度发布和渐进式上线。

## 目录结构
| 文件 | 说明 |
|------|------|
| `config.rs` | 灰度配置 |
| `lib.rs` | 库入口 |
| `list.rs` | 灰度列表 |
| `metadata.rs` | 灰度元数据 |
| `metadata_tests.rs` | 元数据测试 |
| `policy.rs` | 灰度策略 |
| `recorder.rs` | 记录器 |
| `recorder_tests.rs` | 记录器测试 |
| `session_index.rs` | 会话索引 |
| `session_index_tests.rs` | 会话索引测试 |
| `state_db.rs` | 状态数据库 |
| `state_db_tests.rs` | 状态数据库测试 |
| `tests.rs` | 通用测试 |
