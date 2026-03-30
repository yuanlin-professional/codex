# `config.rs` — 测试配置文件生成工具

## 功能概述
提供 `write_mock_responses_config_toml` 函数，用于在测试中生成 `config.toml` 配置文件。
该函数根据传入的参数（服务器 URI、功能标志、自动压缩限制、模型提供者 ID、压缩提示词等）
动态构建配置内容，并写入指定的 codex home 目录。支持自定义模型提供者和 OpenAI 认证需求配置。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `write_mock_responses_config_toml` | `pub fn write_mock_responses_config_toml(codex_home: &Path, server_uri: &str, feature_flags: &BTreeMap<Feature, bool>, ...) -> std::io::Result<()>` | 生成测试用 config.toml 文件，包含模型、审批策略、沙箱模式、特性标志和模型提供者配置 |

## 依赖关系
- **引用的 crate**: `codex_features`, `std`
- **被引用方**: `common/lib.rs` 导出，供 compaction、turn_start 等需要完整配置的测试使用
