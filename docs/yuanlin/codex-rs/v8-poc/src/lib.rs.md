# `lib.rs` — V8 概念验证 crate

## 功能概述

Bazel 连接的概念验证 crate，用于未来的 V8 JavaScript 引擎实验。提供获取 Bazel 目标标签和嵌入式 V8 版本号的函数。测试中演示了基本的 V8 表达式求值（整数加法和字符串拼接）。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `bazel_target` | `() -> &'static str` | 返回 Bazel 目标标签 |
| `embedded_v8_version` | `() -> &'static str` | 返回嵌入的 V8 版本号 |

## 依赖关系

- **引用的 crate**: `v8`
- **被引用方**: 无（实验性 crate）
