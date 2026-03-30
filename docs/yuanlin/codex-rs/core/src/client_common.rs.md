# `client_common.rs` — 模型客户端公共类型与 Prompt 构建

## 功能概述

此文件定义了模型客户端的公共类型，核心是 `Prompt` 结构体（API 请求载荷）和 `ResponseStream`（响应事件流）。它还包含 shell 工具输出的重新序列化逻辑：当使用 Freeform 格式的 `apply_patch` 工具时，将 JSON 格式的 shell 输出转换为结构化文本格式（包含退出码、耗时和输出内容）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Prompt` | 结构体 | API 请求载荷，包含输入项、工具列表、基础指令、个性设定和输出模式 |
| `ResponseStream` | 结构体 | 基于 mpsc 通道的响应事件流，实现 `Stream` trait |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `Prompt::get_formatted_input` | `(&self) -> Vec<ResponseItem>` | 获取格式化后的输入，当存在 Freeform apply_patch 工具时重新序列化 shell 输出 |
| `reserialize_shell_outputs` | `(items: &mut [ResponseItem])` | 将 JSON 格式的 shell/apply_patch 工具输出转换为结构化文本 |
| `parse_structured_shell_output` | `(raw: &str) -> Option<String>` | 解析 JSON 格式的 shell 输出并构建结构化文本 |
| `strip_total_output_header` | `(output: &str) -> Option<(&str, u32)>` | 从输出中提取"Total output lines"头信息 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`ResponseItem`、`BaseInstructions`）、`codex_tools`（`ToolSpec`）、`serde`、`tokio::sync::mpsc`
- **被引用方**: `lib.rs` 公开导出 `Prompt`、`REVIEW_PROMPT`、`ResponseEvent`、`ResponseStream`

## 实现备注

- `REVIEW_PROMPT` 和审查退出模板从 markdown/XML 文件中 `include_str!` 编译时加载
- 重新序列化仅在 Freeform 格式的 `apply_patch` 工具存在时触发，Function 格式不受影响
- shell 工具名称匹配包括 `"shell"` 和 `"container.exec"`
- `ResponseStream` 是 `mpsc::Receiver` 的简单包装，实现标准 `Stream` trait
- 结构化输出格式包含：退出码、耗时（秒）、总输出行数（可选）和实际输出内容
