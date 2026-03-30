# `view_image.rs` — 图片查看工具处理器

## 功能概述
该文件实现了 `view_image` 工具，允许模型加载并查看指定路径的图片文件。支持两种图片详细度模式：默认的缩放适配模式和 `original` 原始模式（需要模型和 feature flag 支持）。处理流程包括：验证模型支持图片输入、解析路径、读取文件、处理图片为 data URL 格式，并发送查看事件。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ViewImageHandler` | struct | 图片查看工具处理器 |
| `ViewImageArgs` | struct | 工具参数：`path`（图片路径）, `detail`（可选详细度） |
| `ViewImageDetail` | enum (私有) | 详细度枚举，当前仅有 `Original` |
| `ViewImageOutput` | struct | 输出：`image_url`（data URL）和可选的 `image_detail` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation) -> Result<ViewImageOutput, FunctionCallError>` | 验证图片输入能力 -> 解析参数 -> 解析路径 -> 读取文件 -> 处理图片 -> 发送事件 -> 返回 data URL |
| `log_preview` | `fn log_preview(&self) -> String` | 返回 image_url 作为日志预览 |
| `to_response_item` | `fn to_response_item(&self, call_id, payload) -> ResponseInputItem` | 将输出转为 `InputImage` 类型的响应项 |
| `code_mode_result` | `fn code_mode_result(&self, payload) -> serde_json::Value` | 返回包含 image_url 和 detail 的 JSON 对象 |

## 依赖关系
- **引用的 crate**: `async_trait`, `codex_protocol`（`FunctionCallOutputBody`, `ImageDetail`, `InputModality`）, `codex_utils_absolute_path`, `codex_utils_image`（`PromptImageMode`, `load_for_prompt_bytes`）, `serde`
- **内部依赖**: `original_image_detail`, `protocol::EventMsg/ViewImageToolCallEvent`, `tools::context`, `tools::handlers::parse_arguments`, `tools::registry`
- **被引用方**: 工具注册表中注册为 `view_image` 工具

## 实现备注
- 首先检查模型是否支持图片输入（`InputModality::Image`），不支持则直接返回错误
- `detail` 参数仅接受 `"original"` 或省略，其他值均报错
- 原始详细度模式需要同时满足 feature flag 和模型支持两个条件
- 图片通过 `load_for_prompt_bytes` 处理后转为 data URL，支持 `ResizeToFit` 和 `Original` 两种处理模式
- 文件内含内联测试模块，验证 `code_mode_result` 的 JSON 输出格式
