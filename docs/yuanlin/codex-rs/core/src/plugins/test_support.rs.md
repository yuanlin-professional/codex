# `test_support.rs` -- 测试辅助工具

## 功能概述
该文件提供了插件测试中共享的辅助函数和常量，用于快速搭建测试环境。包括写入测试文件、创建精选市场和插件目录结构、写入 SHA 文件和特性配置，以及异步加载测试配置等功能。仅在 `#[cfg(test)]` 下编译。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `TEST_CURATED_PLUGIN_SHA` | 常量 | 测试用的精选插件 SHA 值 `"0123456789abcdef0123456789abcdef01234567"` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `write_file` | `fn(path: &Path, contents: &str)` | 创建父目录并写入文件内容 |
| `write_curated_plugin` | `fn(root: &Path, plugin_name: &str)` | 创建完整的精选插件目录结构（清单、技能、MCP、应用配置） |
| `write_openai_curated_marketplace` | `fn(root: &Path, plugin_names: &[&str])` | 创建完整的 OpenAI 精选市场目录（marketplace.json + 所有插件） |
| `write_curated_plugin_sha` | `fn(codex_home: &Path)` | 写入默认测试 SHA 文件 |
| `write_curated_plugin_sha_with` | `fn(codex_home: &Path, sha: &str)` | 写入指定 SHA 文件 |
| `write_plugins_feature_config` | `fn(codex_home: &Path)` | 写入启用插件功能的配置文件 |
| `load_plugins_config` | `async fn(codex_home: &Path) -> Config` | 异步加载测试配置 |

## 依赖关系
- **引用的内部模块**: `crate::config::CONFIG_TOML_FILE`, `crate::config::ConfigBuilder`, `super::OPENAI_CURATED_MARKETPLACE_NAME`
- **被引用方**: `discoverable_tests.rs`, `manager_tests.rs`, `startup_sync_tests.rs` 等测试文件
