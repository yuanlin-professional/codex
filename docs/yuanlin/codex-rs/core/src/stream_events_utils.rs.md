# `stream_events_utils.rs` — 模型响应流事件处理工具集

## 功能概述

该文件提供了处理模型（LLM）响应流输出事件的核心工具函数。主要职责包括：将完成的响应项记录到会话历史中、将模型输出解析为 TurnItem 并通知 UI、处理工具调用（shell 执行、函数调用等）的调度、剥离助手消息中的隐藏标记（引用标注、计划模式块）、保存生成的图像到磁盘、记录内存引用使用情况。它是 Session 主循环中处理 `OutputItemDone` 事件的核心逻辑所在。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `HandleOutputCtx` | struct | 输出处理上下文：会话、回合上下文、工具运行时、取消令牌 |
| `OutputItemResult` | struct | 输出处理结果：最后的助手消息、是否需要后续回合、待执行的工具 future |
| `InFlightFuture` | type alias | `Pin<Box<dyn Future<Output = Result<ResponseInputItem>>>>` 异步工具执行 future |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle_output_item_done` | `async fn(ctx, item, previously_active_item) -> Result<OutputItemResult>` | 核心处理函数：分发工具调用或处理消息/推理输出项 |
| `handle_non_tool_response_item` | `async fn(sess, turn_context, item, plan_mode) -> Option<TurnItem>` | 处理非工具类响应项（消息、推理、网页搜索、图像生成） |
| `record_completed_response_item` | `async fn(sess, turn_context, item)` | 记录完成的响应项到历史并追踪内存引用 |
| `save_image_generation_result` | `async fn(codex_home, session_id, call_id, result) -> Result<PathBuf>` | 将 Base64 编码的生成图像保存为 PNG 文件 |
| `strip_hidden_assistant_markup` | `fn(text, plan_mode) -> String` | 剥离助手消息中的引用标注和计划模式块 |
| `raw_assistant_output_text_from_item` | `fn(item) -> Option<String>` | 从响应项中提取原始助手文本 |
| `last_assistant_message_from_item` | `fn(item, plan_mode) -> Option<String>` | 提取剥离标记后的助手消息文本 |
| `response_input_to_response_item` | `fn(input) -> Option<ResponseItem>` | 将输入项转换为响应项（用于记录工具输出） |
| `image_generation_artifact_path` | `fn(codex_home, session_id, call_id) -> PathBuf` | 计算生成图像的存储路径 |

## 依赖关系

- **引用的 crate**: `base64`（图像解码）、`codex_protocol`（模型类型和协议）、`codex_utils_stream_parser`（引用和计划块解析）、`tokio_util`（CancellationToken）、`futures`
- **被引用方**: Session 主循环的流事件处理逻辑直接调用这些函数

## 实现备注

- `handle_output_item_done` 通过 `ToolRouter::build_tool_call` 判断响应项是否为工具调用，如果是则立即记录到历史并返回工具执行 future；否则解析为 UI 展示项。
- 工具调用有三种错误处理路径：`MissingLocalShellCallId`（记录错误并需要后续回合）、`RespondToModel`（直接回复模型）、`Fatal`（致命错误终止）。
- 图像生成结果的 call_id 和 session_id 会经过字符清洗（只保留字母数字和 `-_`），防止路径注入。
- `maybe_mark_thread_memory_mode_polluted_from_web_search` 在检测到 web 搜索调用时标记线程内存模式为"受污染"状态。
- `record_stage1_output_usage_for_completed_item` 追踪引用了哪些线程的内存引用，用于使用统计。
