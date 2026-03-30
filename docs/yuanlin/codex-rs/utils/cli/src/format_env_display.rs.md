# `format_env_display.rs` — 环境变量显示格式化

## 功能概述

提供 `format_env_display` 函数，将环境变量 map 和变量名列表格式化为掩码显示字符串（值以 `*****` 替代）。环境变量对按键排序，无内容时返回 `"-"`。主要用于 UI 中安全展示环境配置信息。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `format_env_display` | `fn(env: Option<&HashMap<String, String>>, env_vars: &[String]) -> String` | 格式化环境变量为掩码显示字符串 |

## 依赖关系

- **引用的 crate**: `std::collections::HashMap`
- **被引用方**: CLI/TUI 模块中显示环境变量配置的地方

## 实现备注

- 测试覆盖了空输入、排序对、单独变量名和混合场景。
