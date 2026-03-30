# string

## 功能概述

`codex-utils-string` 提供了一组字符串处理工具函数，涵盖文本截断、字节边界处理、UUID 查找、度量标签值清洗以及 Markdown 位置后缀规范化等功能。

该 crate 是 `codex-utils-output-truncation` 的底层依赖，提供了核心的截断算法实现。

## 目录结构

```
string/
├── Cargo.toml
└── src/
    ├── lib.rs          # 工具函数与模块导出
    └── truncate.rs     # 截断算法实现
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `regex-lite` | 轻量级正则表达式（UUID 查找） |

## 核心接口/API

### 截断函数

```rust
// 按字节预算截断字符串中部，保留首尾
pub fn truncate_middle_chars(s: &str, max_bytes: usize) -> String;

// 按 token 预算截断，返回截断结果和原始 token 数
pub fn truncate_middle_with_token_budget(s: &str, max_tokens: usize) -> (String, Option<u64>);

// Token 估算（约 4 字节/token）
pub fn approx_token_count(text: &str) -> usize;
pub fn approx_bytes_for_tokens(tokens: usize) -> usize;
pub fn approx_tokens_from_byte_count(bytes: usize) -> u64;
```

### 字节边界截取

```rust
// 截取不超过 maxb 字节的前缀（字符边界安全）
pub fn take_bytes_at_char_boundary(s: &str, maxb: usize) -> &str;

// 截取不超过 maxb 字节的后缀（字符边界安全）
pub fn take_last_bytes_at_char_boundary(s: &str, maxb: usize) -> &str;
```

### 度量标签清洗

```rust
// 清洗度量标签值：仅保留 ASCII 字母数字、'.'、'_'、'-'、'/'
pub fn sanitize_metric_tag_value(value: &str) -> String;
// 无效输入返回 "unspecified"，最大长度 256
```

### UUID 查找

```rust
pub fn find_uuids(s: &str) -> Vec<String>;
// 使用正则匹配标准 UUID 格式（8-4-4-4-12）
```

### Markdown 位置后缀规范化

```rust
pub fn normalize_markdown_hash_location_suffix(suffix: &str) -> Option<String>;
// "#L74C3" -> Some(":74:3")
// "#L74C3-L76C9" -> Some(":74:3-76:9")
```
