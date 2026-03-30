# `sanitizer.rs` — 密钥内容清洗

## 功能概述

使用正则表达式尽力清除字符串中的敏感信息。匹配 OpenAI API key（`sk-`前缀）、AWS Access Key ID（`AKIA`前缀）、Bearer token 和通用密钥赋值模式，将匹配内容替换为 `[REDACTED_SECRET]`。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `redact_secrets` | `(input: String) -> String` | 清除字符串中的已知密钥模式 |

## 依赖关系

- **引用的 crate**: `regex`
- **被引用方**: 日志和反馈输出的脱敏处理
