# output-truncation

## 功能概述

`codex-utils-output-truncation` 提供了工具输出和执行结果的智能截断功能。支持两种截断策略：按字节数截断和按 token 数截断。截断时保留输出的前半部分和后半部分，中间插入截断标记，确保 AI 模型能看到输出的开头和结尾。

主要用于限制发送给 AI 模型的输出内容大小，避免超出上下文窗口限制。

## 目录结构

```
output-truncation/
├── Cargo.toml
└── src/
    ├── lib.rs              # 截断函数实现
    └── truncate_tests.rs   # 测试
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-protocol` | `TruncationPolicy` 和 `FunctionCallOutputContentItem` 类型 |
| `codex-utils-string` | 底层字符串截断实现 |

## 核心接口/API

### `TruncationPolicy`（重导出）

截断策略枚举：
- `Bytes(usize)` -- 按字节数限制
- `Tokens(usize)` -- 按 token 数限制

### `formatted_truncate_text(content: &str, policy: TruncationPolicy) -> String`

截断文本内容，超出预算时在结果前添加 `Total output lines: N` 前缀。

### `truncate_text(content: &str, policy: TruncationPolicy) -> String`

根据策略截断文本内容，保留前后部分。

### `formatted_truncate_text_content_items_with_policy(...)`

截断函数调用输出内容项列表中的文本段。将所有文本段合并后统一截断，保留图像项不变。返回截断后的内容项列表和原始 token 数。

### `truncate_function_output_items_with_policy(...)`

逐项截断函数调用输出内容项，按预算依次分配。超出预算的文本项被省略，末尾添加 `[omitted N text items ...]` 标记。

### Token 估算函数（重导出）

```rust
pub fn approx_token_count(text: &str) -> usize;          // 估算 token 数
pub fn approx_bytes_for_tokens(tokens: usize) -> usize;  // token 数转字节数
pub fn approx_tokens_from_byte_count(bytes: usize) -> u64; // 字节数转 token 数
```
