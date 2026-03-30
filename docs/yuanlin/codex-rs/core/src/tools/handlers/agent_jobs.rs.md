# `agent_jobs.rs` -- 批量 Agent 作业调度与执行引擎

## 功能概述
该文件实现了批量 Agent 作业（Agent Jobs）的核心调度逻辑。它允许从 CSV 文件中读取多行数据，为每一行数据生成独立的子 Agent 任务，并以可控的并发度并行执行这些任务。执行结果会被收集并导出为输出 CSV 文件。此外，还提供了 `report_agent_job_result` 工具，供 worker 子 Agent 上报单个任务项的处理结果。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `BatchJobHandler` | struct | 实现 `ToolHandler` trait，作为 `spawn_agents_on_csv` 和 `report_agent_job_result` 两个工具的统一入口 |
| `SpawnAgentsOnCsvArgs` | struct | `spawn_agents_on_csv` 工具的参数定义，包含 CSV 路径、指令模板、ID 列、输出路径、输出 schema、并发数和运行时超时等 |
| `ReportAgentJobResultArgs` | struct | `report_agent_job_result` 工具的参数定义，包含 job_id、item_id、result 和可选的 stop 标志 |
| `SpawnAgentsOnCsvResult` | struct | 作业完成后返回给模型的结果摘要，包含状态、完成/失败计数和错误信息 |
| `AgentJobFailureSummary` | struct | 失败任务项的摘要信息，包含 item_id、source_id 和最后一次错误 |
| `AgentJobProgressUpdate` | struct | 作业进度更新事件的数据结构，包含各状态项计数和预估剩余时间 |
| `JobRunnerOptions` | struct | 作业运行器的配置选项，包含最大并发数和子 Agent 的配置 |
| `ActiveJobItem` | struct | 当前正在运行的任务项跟踪信息，包含 item_id、启动时间和状态订阅接收器 |
| `JobProgressEmitter` | struct | 进度发射器，控制进度事件的发送频率，支持 ETA 计算 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `BatchJobHandler::handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<FunctionToolOutput, FunctionCallError>` | 根据 tool_name 分发到 `spawn_agents_on_csv` 或 `report_agent_job_result` |
| `spawn_agents_on_csv::handle` | `async fn handle(session, turn, arguments) -> Result<FunctionToolOutput, FunctionCallError>` | 解析 CSV、创建作业、启动运行循环、等待完成并导出结果 |
| `report_agent_job_result::handle` | `async fn handle(session, arguments) -> Result<FunctionToolOutput, FunctionCallError>` | 接收 worker 上报的结果并写入数据库，支持 stop 标志取消作业 |
| `run_agent_job_loop` | `async fn run_agent_job_loop(session, turn, db, job_id, options) -> anyhow::Result<()>` | 主调度循环：分配待处理项给空闲槽位、回收超时项、检测完成项、发送进度更新 |
| `build_worker_prompt` | `fn build_worker_prompt(job, item) -> anyhow::Result<String>` | 为每个 worker 构建包含任务指令、输入行数据和输出 schema 的提示词 |
| `render_instruction_template` | `fn render_instruction_template(instruction, row_json) -> String` | 将指令模板中的 `{column}` 占位符替换为对应行的值，支持 `{{` 转义 |
| `parse_csv` | `fn parse_csv(content) -> Result<(Vec<String>, Vec<Vec<String>>), String>` | 解析 CSV 内容，支持引号、逗号和 BOM 头处理 |
| `render_job_csv` | `fn render_job_csv(headers, items) -> Result<String, FunctionCallError>` | 将作业结果渲染为输出 CSV，附加状态、错误等元数据列 |
| `normalize_concurrency` | `fn normalize_concurrency(requested, max_threads) -> usize` | 将请求的并发数限制在 1..64 范围内，并受 max_threads 约束 |
| `recover_running_items` | `async fn recover_running_items(session, db, job_id, active_items, runtime_timeout)` | 恢复之前处于 Running 状态的任务项，处理超时或已完成的项 |
| `export_job_csv_snapshot` | `async fn export_job_csv_snapshot(db, job) -> anyhow::Result<()>` | 将当前作业所有项导出为 CSV 快照文件 |

## 依赖关系
- **引用的 crate**: `codex_state`（SQLite 状态数据库）、`codex_protocol`（协议类型如 ThreadId、AgentStatus、SessionSource）、`codex_apply_patch`（间接通过 multi_agents）、`csv`（CSV 解析）、`uuid`（生成作业 ID）、`chrono`（时间计算）、`tokio`（异步运行时、超时、watch channel）、`futures`（FuturesUnordered 并发等待）、`serde`/`serde_json`（序列化）、`async_trait`
- **被引用方**: 由工具注册表（tool registry）注册并调度，作为 `spawn_agents_on_csv` 和 `report_agent_job_result` 工具的处理器

## 实现备注
- 并发调度采用经典的"槽位填充"模式：主循环不断检查空闲槽位，从数据库取出 Pending 项并分配给新的子 Agent，同时回收已完成或超时的项。
- 进度更新通过 `JobProgressEmitter` 节流，默认每秒最多发送一次，避免事件风暴。
- 支持 ETA 估算，基于已处理项的速率线性推算剩余时间。
- 使用 `watch::Receiver` 订阅子 Agent 状态变化，配合 `FuturesUnordered` 实现高效的状态变更等待，避免无谓轮询。
- 指令模板的占位符替换使用哨兵字符串（sentinel）来正确处理 `{{` 转义。
- CSV 输出的 `render_job_csv` 会在原始列基础上追加 job_id、item_id、status 等元数据列。
- 当 `AgentLimitReached` 错误发生时，会将任务项回退到 Pending 状态而非标记为失败。
- 默认单任务超时为 30 分钟（`DEFAULT_AGENT_JOB_ITEM_TIMEOUT`），最大并发数为 64。
