# `registry.rs` — 工具注册表与调度

## 功能概述
实现了工具注册表（`ToolRegistry`）和注册表构建器（`ToolRegistryBuilder`），管理工具处理器的注册与调度。`ToolHandler` trait 定义了工具处理器的标准接口，支持 Function 和 MCP 两种类型。调度流程包括 hook 集成（PreToolUse/PostToolUse/AfterToolUse）、遥测记录、工具门控（mutating 工具的串行化）和内存使用指标。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ToolKind` | enum | 工具类型：Function / Mcp |
| `ToolHandler` | trait | 工具处理器接口：kind、matches_kind、is_mutating、handle |
| `AnyToolResult` | struct | 类型擦除的工具结果：call_id + payload + Box<dyn ToolOutput> |
| `ToolRegistry` | struct | 工具注册表：name -> Arc<dyn AnyToolHandler> 映射 |
| `ToolRegistryBuilder` | struct | 注册表构建器：收集 handlers 和 specs |
| `PreToolUsePayload` / `PostToolUsePayload` | struct | Hook 载荷类型 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ToolRegistry::dispatch_any` | `async fn dispatch_any(&self, invocation) -> Result<AnyToolResult, FunctionCallError>` | 核心调度：查找处理器 -> PreToolUse hook -> 等待 tool gate -> 执行 -> PostToolUse hook -> AfterToolUse hook |
| `ToolRegistryBuilder::register_handler` | `fn register_handler<H>(&mut self, name, handler)` | 注册工具处理器 |
| `tool_handler_key` | `fn(tool_name, namespace) -> String` | 生成注册表查找键，格式为 `namespace:tool_name` |

## 依赖关系
- **引用的 crate**: `async_trait`, `codex_hooks`, `codex_tools`, `codex_utils_readiness`
- **内部依赖**: `crate::hook_runtime`, `crate::memories::usage`, `crate::sandbox_tags`
- **被引用方**: `router.rs`, `spec.rs`

## 实现备注
- `AnyToolHandler` trait 是 `ToolHandler` 的类型擦除版本，通过 blanket impl 自动实现
- mutating 工具在执行前等待 `tool_call_gate.wait_ready()`，确保与非 mutating 工具的有序执行
- Hook 集成支持三个时机：PreToolUse（可阻止）、PostToolUse（可替换输出）、AfterToolUse（可中止后续）
- `ToolPayload` 到 `HookToolInput` 的转换确保 hook JSON 格式与核心解耦
