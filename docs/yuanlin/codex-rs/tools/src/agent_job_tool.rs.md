# `agent_job_tool.rs` -- Agent Job 批处理工具定义

## 功能概述
定义两个用于 CSV 批量处理的工具：`spawn_agents_on_csv` 每行生成一个 worker 子 agent，支持 instruction 模板变量替换和并发控制；`report_agent_job_result` 供 worker 报告处理结果。支持可选的 output_schema 约束结果格式、max_concurrency/max_workers 并发控制和 max_runtime_seconds 超时。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_spawn_agents_on_csv_tool` | `fn() -> ToolSpec` | 创建 CSV 批处理工具定义 |
| `create_report_agent_job_result_tool` | `fn() -> ToolSpec` | 创建 worker 结果报告工具定义 |

## 依赖关系
- **被引用方**: `lib.rs` 公开导出

## 实现备注
- `report_agent_job_result` 的 `stop` 参数可提前取消剩余任务项
