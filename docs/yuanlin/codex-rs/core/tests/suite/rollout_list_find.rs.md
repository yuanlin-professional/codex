# `rollout_list_find.rs` -- 测试模块

## 测试覆盖范围
测试 rollout 文件的列表和查找功能，包括按 ID 查找线程路径、按名称查找以及归档线程的查找。验证 RolloutRecorder 的文件写入和元数据记录。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `find_thread_path_by_id_str_locates_rollout` | 按 UUID 查找 rollout 文件路径 |
| `find_thread_path_by_name_str_locates_rollout` | 按线程名称查找 rollout 文件路径 |
| `find_archived_thread_path_by_id_str_locates_rollout` | 在归档目录中按 ID 查找 |
| `rollout_recorder_writes_session_meta` | RolloutRecorder 正确写入会话元数据 |
| `rollout_recorder_persistence_modes` | 不同持久化模式下的行为验证 |
