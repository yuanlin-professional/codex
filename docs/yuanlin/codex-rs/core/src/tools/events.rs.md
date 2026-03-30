# `events.rs` — 工具执行事件发射

## 功能概述
提供了工具执行过程中事件发射的统一机制。通过 `ToolEmitter` 枚举支持三种工具类型（Shell、ApplyPatch、UnifiedExec）的 Begin/Success/Failure 事件生命周期。负责将执行结果格式化为模型可消费的文本，并发射协议事件（ExecCommandBegin/End、PatchApplyBegin/End、TurnDiff）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ToolEventCtx<'a>` | struct | 事件上下文：session、turn、call_id、turn_diff_tracker |
| `ToolEmitter` | enum | 事件发射器：Shell / ApplyPatch / UnifiedExec |
| `ToolEventStage` | enum | 事件阶段：Begin / Success / Failure |
| `ToolEventFailure` | enum | 失败类型：Output / Message / Rejected |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ToolEmitter::emit` | `async fn emit(&self, ctx, stage)` | 根据工具类型和事件阶段发射对应的事件 |
| `ToolEmitter::finish` | `async fn finish(&self, ctx, result) -> Result<String, FunctionCallError>` | 完成执行：格式化输出、发射事件、处理成功/失败/拒绝 |
| `emit_exec_command_begin` | `async fn(ctx, command, cwd, ...)` | 发射 ExecCommandBegin 事件 |
| `emit_patch_end` | `async fn(ctx, changes, ...)` | 发射 PatchApplyEnd 事件并触发 TurnDiff |

## 依赖关系
- **引用的 crate**: `codex_protocol`
- **内部依赖**: `crate::protocol::EventMsg`, `crate::turn_diff_tracker`, `crate::tools::sandboxing`
- **被引用方**: 各工具处理器（shell、apply_patch、unified_exec handler）

## 实现备注
- 补丁完成后自动计算 unified diff 并发射 `TurnDiff` 事件
- Shell 的 freeform 模式使用 `format_exec_output_for_model_freeform`，其他使用 structured 格式
- 用户拒绝消息（"rejected by user"）会根据工具类型规范化为 "exec command rejected by user" 或 "patch rejected by user"
- `Declined` 状态用于被拒绝的补丁操作
