# `schema.rs` -- 配置 JSON Schema 生成

## 功能概述
为 `config.toml` 生成 JSON Schema（Draft 07），用于编辑器自动补全和配置校验。提供了自定义 schema 生成函数用于 `[features]` 和 `[mcp_servers]` 两个特殊表，以及 schema 的序列化、规范化和写入磁盘功能。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 无独立结构体 | - | 本文件主要提供函数 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `features_schema` | `fn(schema_gen) -> Schema` | 为 `[features]` 表生成 schema，包含所有已知 feature key 和遗留 key，禁止未知键 |
| `mcp_servers_schema` | `fn(schema_gen) -> Schema` | 为 `[mcp_servers]` 表生成 schema，值类型为 `RawMcpServerConfig` |
| `config_schema` | `fn() -> RootSchema` | 构建完整的 `config.toml` 根 schema |
| `config_schema_json` | `fn() -> Result<Vec<u8>>` | 将 schema 序列化为规范化的 pretty-print JSON |
| `write_config_schema` | `fn(out_path) -> Result<()>` | 将 schema JSON 写入指定路径 |
| `canonicalize` | `fn(value) -> Value` | 递归排序 JSON 对象的键以实现确定性输出 |

## 依赖关系
- **引用的 crate**: `schemars`, `serde_json`
- **引用的模块**: `crate::config::ConfigToml`, `crate::config::types::RawMcpServerConfig`, `codex_features`
- **被引用方**: `config/profile.rs`（`features` 字段的 schema 生成）、CI 管道中的 schema fixture 校验

## 实现备注
- `canonicalize` 函数通过递归排序确保 JSON Schema 输出在不同运行之间完全一致，便于 fixture 比较。
- `features_schema` 显式排除 `Feature::Artifact`，将其从用户可见的配置 schema 中移除。
- Schema 使用 `option_add_null_type = false` 设置，避免将 Option 类型映射为 null 联合类型。
