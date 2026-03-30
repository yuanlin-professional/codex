# `phase1.rs` -- 记忆 Phase 1 启动提取管线

## 功能概述

实现记忆系统第一阶段（Phase 1）的完整工作流：声明待处理的 rollout 作业、构建请求上下文、并行执行 stage-1 提取任务、收集统计数据并上报指标。每个作业从 rollout 文件加载会话内容，发送到 LLM 进行提取，得到原始记忆（raw_memory）和摘要（rollout_summary），然后将结果持久化到状态数据库。还包含过期记忆的修剪逻辑和对敏感信息的脱敏处理。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RequestContext` | 结构体 | 封装单次 Phase 1 请求所需的模型信息、遥测、推理参数等上下文 |
| `StageOneOutput` | 结构体 | Phase 1 模型输出的反序列化载体，包含 raw_memory、rollout_summary、rollout_slug |
| `JobResult` | 结构体 | 单个作业的执行结果，包含结果状态和 token 用量 |
| `JobOutcome` | 枚举 | 作业结果状态：成功有输出、成功无输出、失败 |
| `Stats` | 结构体 | 所有作业的聚合统计信息 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run` | `async fn(&Arc<Session>, &Config)` | Phase 1 主入口：声明作业 -> 构建上下文 -> 并行采样 -> 上报指标 |
| `prune` | `async fn(&Arc<Session>, &Config)` | 清理超过保留期限的旧 stage-1 输出 |
| `output_schema` | `fn() -> Value` | 返回约束 Phase 1 模型输出格式的 JSON Schema |
| `claim_startup_jobs` | `async fn` | 从状态数据库声明待处理的 rollout 作业 |
| `build_request_context` | `async fn` | 构建包含模型信息和遥测数据的请求上下文 |
| `run_jobs` | `async fn` | 使用 `buffer_unordered` 并行执行提取作业 |
| `job::run` | `async fn` | 单个作业的执行逻辑：加载 rollout -> 采样 -> 持久化结果 |
| `job::sample` | `async fn` | 核心采样逻辑：构建 prompt、调用模型流式 API、解析输出并脱敏 |
| `serialize_filtered_rollout_response_items` | `fn` | 过滤并序列化 rollout 中适合记忆提取的响应条目 |
| `aggregate_stats` | `fn(Vec<JobResult>) -> Stats` | 聚合所有作业的结果统计和 token 用量 |
| `emit_metrics` | `fn` | 上报 Phase 1 各项 OpenTelemetry 指标 |

## 依赖关系

- **引用的 crate**: `codex_api`、`codex_otel`、`codex_protocol`、`codex_secrets`、`codex_state`、`futures`、`serde`、`serde_json`
- **被引用方**: `memories/start.rs` 调用 `run` 和 `prune`；`memories/tests.rs` 引用 `output_schema`

## 实现备注

- 使用 `futures::stream::buffer_unordered` 实现并行提取，并发上限由 `phase_one::CONCURRENCY_LIMIT`（8）控制。
- 模型输出经过 `codex_secrets::redact_secrets` 脱敏，防止敏感信息泄露到记忆存储。
- rollout 内容序列化时会过滤掉 developer 角色消息和记忆排除的上下文片段（如 AGENTS.md 指令、skill 消息等）。
- 作业失败时设置重试延迟（默认 3600 秒），通过数据库状态管理实现分布式作业调度。
