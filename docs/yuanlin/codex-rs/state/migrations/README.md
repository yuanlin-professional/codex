# state/migrations - 数据库迁移脚本

## 功能概述

此目录包含 Codex 状态数据库（SQLite）的 SQL 迁移脚本，按编号顺序执行以维护数据库 schema 的版本演进。

## 文件列表

| 文件 | 说明 |
|------|------|
| `0001_threads.sql` | 创建线程表 |
| `0002_logs.sql` | 创建日志表 |
| `0003_logs_thread_id.sql` | 日志表添加 thread_id 字段 |
| `0004_thread_dynamic_tools.sql` | 添加动态工具支持 |
| `0005_threads_cli_version.sql` | 记录 CLI 版本 |
| `0006_memories.sql` | 创建记忆表 |
| `0007_threads_first_user_message.sql` | 记录首条用户消息 |
| `0008_backfill_state.sql` | 状态回填迁移 |
| `0009_stage1_outputs_rollout_slug.sql` | 阶段一输出灰度标识 |
| `0010_logs_process_id.sql` | 日志添加进程 ID |
| `0011_logs_partition_prune_indexes.sql` | 日志分区裁剪索引 |
| `0012_logs_estimated_bytes.sql` | 日志预估字节数 |
| `0013_threads_agent_nickname.sql` | 代理昵称字段 |
| `0014_agent_jobs.sql` | 代理任务表 |
| `0015_agent_jobs_max_runtime_seconds.sql` | 代理任务最大运行时间 |
| `0016_memory_usage.sql` | 内存使用记录 |
| `0017_phase2_selection_flag.sql` | 第二阶段选择标志 |
| `0018_phase2_selection_snapshot.sql` | 第二阶段选择快照 |
| `0019_thread_dynamic_tools_defer_loading.sql` | 延迟加载动态工具 |
| `0020_threads_model_reasoning_effort.sql` | 模型推理努力程度 |
| `0021_thread_spawn_edges.sql` | 线程生成边关系 |
| `0022_threads_agent_path.sql` | 代理路径字段 |
