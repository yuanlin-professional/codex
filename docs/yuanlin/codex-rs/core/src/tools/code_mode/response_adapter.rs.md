# `response_adapter.rs` — Code Mode 响应类型适配器

## 功能概述
将 `codex_code_mode` crate 定义的内容项类型转换为 `codex_protocol` 的标准 `FunctionCallOutputContentItem` 类型。通过 `IntoProtocol` trait 实现两套类型系统之间的映射，包括文本和图像内容项的转换。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `IntoProtocol<T>` | trait | 类型转换 trait，将 Code Mode 类型映射为协议类型 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `into_function_call_output_content_items` | `fn(items: Vec<codex_code_mode::FunctionCallOutputContentItem>) -> Vec<FunctionCallOutputContentItem>` | 批量转换内容项列表 |

## 依赖关系
- **引用的 crate**: `codex_code_mode`, `codex_protocol`
- **被引用方**: `code_mode/mod.rs` 中的 `handle_runtime_response` 函数

## 实现备注
- 支持 `InputText` 和 `InputImage` 两种内容项的转换
- `ImageDetail` 枚举映射包含 Auto/Low/High/Original 四个变体
