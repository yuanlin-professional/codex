# `phase2.rs` -- 记忆 Phase 2 合并管线

## 功能概述

实现记忆系统第二阶段（Phase 2，即 Consolidation 合并阶段）的完整工作流。该阶段获取全局合并锁、从数据库查询 Phase 1 产出的原始记忆、同步文件系统产物（rollout_summaries 和 raw_memories.md），然后启动一个受限的子代理（subagent）来执行实际的记忆合并操作。子代理运行期间通过心跳机制维持作业所有权，完成后更新数据库状态。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Claim` | 结构体 | Phase 2 作业声明信息，包含所有权 token 和输入水位标记 |
| `Counters` | 结构体 | 输入记忆数量等统计计数器 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run` | `async fn(&Arc<Session>, Arc<Config>)` | Phase 2 主入口：声明作业 -> 查询记忆 -> 同步文件系统 -> 启动子代理 -> 处理结果 |
| `artifact_memories_for_phase2` | `fn(&Phase2InputSelection) -> Vec<Stage1Output>` | 合并当前选中和上次保留的记忆，去重后供文件系统同步使用 |
| `get_watermark` | `fn(i64, &[Stage1Output]) -> i64` | 计算完成水位标记，取声明水位和最新记忆时间戳的较大值 |
| `job::claim` | `async fn` | 尝试声明全局 Phase 2 作业锁 |
| `job::failed` | `async fn` | 标记作业失败并设置重试延迟 |
| `job::succeed` | `async fn` | 标记作业成功并更新完成水位 |
| `agent::get_config` | `fn(Arc<Config>) -> Option<Config>` | 为合并子代理构建受限配置（禁用记忆生成、子代理派生、设置沙箱等） |
| `agent::get_prompt` | `fn(Arc<Config>, &Phase2InputSelection) -> Vec<UserInput>` | 构建合并子代理的提示词 |
| `agent::handle` | `fn` | 启动异步任务监控子代理运行状态，定期发送心跳 |
| `agent::loop_agent` | `async fn` | 循环等待子代理完成，同时维持心跳保持作业所有权 |

## 依赖关系

- **引用的 crate**: `codex_config`、`codex_features`、`codex_protocol`、`codex_state`、`codex_utils_absolute_path`、`tokio`
- **被引用方**: `memories/start.rs` 调用 `run`

## 实现备注

- 合并子代理被严格限制：禁用记忆生成（防止递归）、禁用子代理派生和协作模式、设置 `AskForApproval::Never`。
- 沙箱策略仅允许写入 `codex_home` 目录，禁止网络访问。
- 心跳间隔为 90 秒，通过数据库的 `heartbeat_global_phase2_job` 续租作业锁。
- 当心跳续租失败时，代理会被标记为错误状态以避免孤立运行。
- 作业失败时采用双重回退策略：先尝试标记为失败，若失败则尝试 `failed_if_unowned`。
