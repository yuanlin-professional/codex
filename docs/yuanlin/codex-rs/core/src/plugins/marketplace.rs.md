# `marketplace.rs` -- 插件市场管理

## 功能概述
该文件实现了插件市场（marketplace）的发现、加载和解析功能。市场是一个 `marketplace.json` 文件，定义了可用插件列表及其来源、策略和元数据。该模块支持从多个来源（用户主目录、Git 仓库根目录、额外指定路径）发现市场文件，解析插件的本地路径，并提供安装时的插件解析（resolve）能力。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Marketplace` | struct | 已加载的市场，包含名称、路径、界面信息和插件列表 |
| `MarketplacePlugin` | struct | 市场中的插件条目，含名称、来源、策略和界面信息 |
| `MarketplacePluginSource` | enum | 插件来源（目前仅支持 `Local` 本地路径） |
| `MarketplacePluginPolicy` | struct | 插件策略，包含安装策略、认证策略和产品限制 |
| `MarketplacePluginInstallPolicy` | enum | 安装策略：NotAvailable / Available / InstalledByDefault |
| `MarketplacePluginAuthPolicy` | enum | 认证策略：OnInstall / OnUse |
| `MarketplaceInterface` | struct | 市场界面信息（显示名称） |
| `MarketplaceError` | enum | 市场操作错误类型，覆盖 IO、文件缺失、解析失败、插件未找到等情况 |
| `ResolvedMarketplacePlugin` | struct | 解析后的插件信息，含 ID、源路径和认证策略 |
| `MarketplaceListOutcome` | struct | 市场列表操作结果，包含成功的市场和加载错误 |
| `MarketplaceListError` | struct | 单个市场加载失败的错误记录 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `resolve_marketplace_plugin` | `fn(path, plugin_name, product) -> Result<ResolvedMarketplacePlugin>` | 从市场文件中解析指定插件，检查安装策略和产品限制 |
| `list_marketplaces` | `fn(additional_roots) -> Result<MarketplaceListOutcome>` | 发现并加载所有可用市场 |
| `load_marketplace` | `fn(path) -> Result<Marketplace>` | 加载单个市场文件 |
| `discover_marketplace_paths_from_roots` | `fn(additional_roots, home_dir) -> Vec<AbsolutePathBuf>` | 从指定根目录和主目录发现 marketplace.json 文件 |
| `resolve_plugin_source_path` | `fn(marketplace_path, source) -> Result<AbsolutePathBuf>` | 将插件的 `./` 相对路径解析为绝对路径 |
| `marketplace_root_dir` | `fn(marketplace_path) -> Result<AbsolutePathBuf>` | 从 marketplace.json 路径反推市场根目录 |

## 依赖关系
- **引用的 crate**: `codex_app_server_protocol`, `codex_git_utils`, `codex_plugin`, `codex_protocol`, `codex_utils_absolute_path`, `dirs`, `serde`, `tracing`
- **引用的内部模块**: `super::manifest::PluginManifestInterface`, `super::load_plugin_manifest`
- **被引用方**: `manager.rs` 大量使用此模块的类型和函数；`mod.rs` 导出多个公开类型

## 实现备注
- 市场文件的标准路径为 `<root>/.agents/plugins/marketplace.json`
- 插件本地路径相对于市场根目录（非 marketplace.json 所在目录），必须以 `./` 开头
- 路径安全性检查：拒绝 `..` 等路径穿越组件
- 支持从 Git 仓库根自动发现市场文件（通过 `get_git_repo_root`）
- 也支持非 Git 检出的目录（如 HTTP 下载的目录）
- 市场分类（category）可在市场和清单两处定义，市场级别的设置优先
- 加载错误会被记录但不阻止其他市场的加载
