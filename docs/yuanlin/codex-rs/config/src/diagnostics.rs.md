# `diagnostics.rs` — 配置解析错误诊断与定位

## 功能概述

提供将 TOML 配置文件的解析和验证错误映射到精确文件位置的能力，并生成用户友好的错误诊断信息（包含文件名、行号、列号、错误消息以及带有 caret 指示器的源码上下文）。支持从多层配置栈中定位第一个出错的具体文件，以及通过 `serde_path_to_error` 追踪嵌套字段路径到 TOML 文档中的精确字节偏移。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TextPosition` | 结构体 | 1-based 行号/列号文本位置 |
| `TextRange` | 结构体 | 起止文本位置范围 |
| `ConfigError` | 结构体 | 配置错误信息，包含文件路径、位置范围和错误消息 |
| `ConfigLoadError` | 结构体 | 可打印的配置加载错误，实现了 `Display` 和 `Error` trait |
| `TomlNode` | 枚举（内部） | TOML 文档树节点类型（Item/Table/Value） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `config_error_from_toml` | `(path, contents, err) -> ConfigError` | 从 `toml::de::Error` 创建带位置信息的配置错误 |
| `config_error_from_typed_toml` | `<T>(path, contents) -> Option<ConfigError>` | 尝试反序列化 TOML 到类型 T，失败时返回带路径的错误 |
| `first_layer_config_error` | `async <T>(layers, file) -> Option<ConfigError>` | 遍历配置层栈，返回第一个能定位到具体文件的类型错误 |
| `format_config_error` | `(error, contents) -> String` | 格式化错误为带源码上下文和 caret 指示器的可读字符串 |
| `format_config_error_with_source` | `(error) -> String` | 自动读取文件内容后格式化错误 |
| `position_for_offset` | `(contents, index) -> TextPosition` | 将字节偏移转换为行号/列号 |
| `span_for_config_path` | `(contents, path) -> Option<Range>` | 通过 serde 路径在 TOML 文档中查找对应的字节范围 |

## 依赖关系

- **引用的 crate**: `toml`、`toml_edit`、`serde_path_to_error`、`codex_app_server_protocol`、`codex_utils_absolute_path`
- **被引用方**: `codex-config` 的配置加载和错误报告流程

## 实现备注

- 特别处理了 `features` 表的路径定位：当 features 表下的非布尔值导致反序列化失败时，会尝试直接定位到该非布尔值的位置。
- `format_config_error` 生成类似编译器诊断的输出格式，包含行号边沟、源代码行和 `^` caret 高亮。
