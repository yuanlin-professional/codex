# `thread_fork.rs` — 测试模块

## 测试覆盖范围
测试 `thread/fork` JSON-RPC 方法，验证 fork 创建新线程并发送 thread/started 通知、
拒绝未物化线程、云需求加载错误的表面化，以及临时线程 fork 后保持无路径。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_fork_creates_new_thread_and_emits_started` | fork 创建新线程并发送启动通知 |
| `thread_fork_rejects_unmaterialized_thread` | 拒绝未物化线程的 fork |
| `thread_fork_surfaces_cloud_requirements_load_errors` | 云需求加载错误被正确表面化 |
| `thread_fork_ephemeral_remains_pathless_and_omits_listing` | 临时线程 fork 后保持无路径且不在列表中 |
