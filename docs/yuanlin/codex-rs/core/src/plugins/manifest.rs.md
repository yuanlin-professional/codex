# `manifest.rs` -- 插件清单解析

## 功能概述
该文件负责解析插件的 `plugin.json` 清单文件（位于 `.codex-plugin/plugin.json`）。它将原始 JSON 清单转换为类型安全的 `PluginManifest` 结构体，处理路径解析、界面资产路径验证、默认提示词解析等逻辑。所有相对路径都要求以 `./` 开头，并解析为绝对路径。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `PluginManifest` | struct | 解析后的插件清单，包含名称、描述、路径配置和界面信息 |
| `PluginManifestPaths` | struct | 插件组件路径配置（skills、mcp_servers、apps） |
| `PluginManifestInterface` | struct | 插件界面元数据，包含显示名、描述、开发者、分类、能力列表、URL、默认提示词、品牌颜色、图标/截图路径等 |
| `RawPluginManifest` | struct (private) | 原始 JSON 反序列化用的中间结构 |
| `RawPluginManifestInterface` | struct (private) | 原始界面数据反序列化结构 |
| `RawPluginManifestDefaultPrompt` | enum (private) | 默认提示词的多态解析：字符串、字符串数组、或无效值 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load_plugin_manifest` | `fn(plugin_root: &Path) -> Option<PluginManifest>` | 加载并解析插件清单文件，路径不存在或解析失败返回 None |
| `resolve_manifest_path` | `fn(plugin_root, field, path) -> Option<AbsolutePathBuf>` | 将清单中的相对路径解析为绝对路径，验证 `./` 前缀和路径安全性 |
| `resolve_default_prompts` | `fn(plugin_root, value) -> Option<Vec<String>>` | 解析并验证默认提示词（支持单字符串和数组格式） |
| `resolve_default_prompt_str` | `fn(plugin_root, field, prompt) -> Option<String>` | 规范化单个提示词字符串，检查长度限制 |

## 依赖关系
- **引用的 crate**: `codex_utils_absolute_path`, `codex_utils_plugins`, `serde`, `serde_json`, `tracing`
- **被引用方**: `manager.rs` 和 `marketplace.rs` 调用 `load_plugin_manifest`；`mod.rs` 导出 `PluginManifestInterface` 和 `PluginManifestPaths`

## 实现备注
- 路径安全性检查：拒绝包含 `..` 的路径，拒绝非 `./` 开头的路径
- 默认提示词限制：最多 3 条，每条最多 128 字符
- 空白字符规范化：提示词中的多余空白合并为单个空格
- 如果清单中的 `name` 为空，会降级使用插件根目录名
- 界面信息如果所有字段都为空，则整体返回 None
- 内嵌了清单解析相关的单元测试（在文件末尾的 `tests` 模块中）
