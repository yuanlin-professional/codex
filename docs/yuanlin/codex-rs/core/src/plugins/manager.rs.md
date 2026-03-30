# `manager.rs` -- 插件管理器核心实现

## 功能概述
这是插件系统的核心管理器文件，实现了 `PluginsManager` 结构体，提供插件的安装、卸载、远程同步、市场列表、插件读取、缓存管理等完整的插件生命周期管理功能。它协调本地插件存储（PluginStore）、市场（marketplace）、远程同步、配置服务等子系统，是插件功能的中枢协调者。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `PluginsManager` | struct | 插件管理器主结构体，持有 codex_home 路径、PluginStore、缓存、远程同步锁等 |
| `PluginInstallRequest` | struct | 插件安装请求，包含插件名和市场路径 |
| `PluginReadRequest` | struct | 插件读取请求，包含插件名和市场路径 |
| `PluginInstallOutcome` | struct | 安装结果，包含 plugin_id、版本、安装路径和认证策略 |
| `PluginReadOutcome` | struct | 读取结果，包含市场名、路径和插件详情 |
| `PluginDetail` | struct | 插件详细信息，包括 ID、名称、描述、来源、策略、技能、MCP 服务器等 |
| `ConfiguredMarketplace` | struct | 已配置的市场信息，包含名称、路径、界面信息和插件列表 |
| `ConfiguredMarketplacePlugin` | struct | 市场中的单个插件条目，含安装和启用状态 |
| `RemotePluginSyncResult` | struct | 远程同步结果，记录安装/启用/禁用/卸载的插件 ID 列表 |
| `PluginRemoteSyncError` | enum | 远程同步错误类型，覆盖认证、请求、解析、本地市场等各类错误 |
| `PluginInstallError` | enum | 安装错误类型 |
| `PluginUninstallError` | enum | 卸载错误类型 |
| `FeaturedPluginIdsCacheKey` | struct (private) | 推荐插件 ID 缓存的键，基于 URL 和用户信息 |
| `PluginMcpFile` | struct (private) | 插件 .mcp.json 文件的反序列化结构 |
| `PluginAppFile` | struct (private) | 插件 .app.json 文件的反序列化结构 |
| `PluginMcpDiscovery` | struct (private) | MCP 服务器发现结果 |
| `ResolvedPluginSkills` | struct (private) | 解析后的插件技能结果，含技能列表、禁用路径和错误标记 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `PluginsManager::new` | `fn(codex_home: PathBuf) -> Self` | 创建管理器实例（默认产品为 Codex） |
| `PluginsManager::plugins_for_config` | `fn(&self, config: &Config) -> PluginLoadOutcome` | 加载配置中的所有插件，支持缓存 |
| `PluginsManager::install_plugin` | `async fn(&self, request) -> Result<PluginInstallOutcome>` | 安装插件到本地缓存并更新配置 |
| `PluginsManager::install_plugin_with_remote_sync` | `async fn(&self, config, auth, request) -> Result<PluginInstallOutcome>` | 安装插件并同步远程状态 |
| `PluginsManager::uninstall_plugin` | `async fn(&self, plugin_id: String) -> Result<()>` | 卸载插件，移除缓存和配置 |
| `PluginsManager::sync_plugins_from_remote` | `async fn(&self, config, auth, additive_only) -> Result<RemotePluginSyncResult>` | 从远程同步插件状态（安装/卸载/启用） |
| `PluginsManager::list_marketplaces_for_config` | `fn(&self, config, roots) -> Result<ConfiguredMarketplaceListOutcome>` | 列出所有可用市场及其插件 |
| `PluginsManager::read_plugin_for_config` | `fn(&self, config, request) -> Result<PluginReadOutcome>` | 读取单个插件的详细信息 |
| `PluginsManager::maybe_start_plugin_startup_tasks_for_config` | `fn(self: &Arc<Self>, config, auth_manager)` | 启动插件系统的后台任务（精选仓库同步、远程同步、推荐缓存预热） |
| `PluginsManager::featured_plugin_ids_for_config` | `async fn(&self, config, auth) -> Result<Vec<String>>` | 获取推荐插件 ID 列表（带 TTL 缓存） |
| `load_plugins_from_layer_stack` | `fn(stack, store, product) -> PluginLoadOutcome` | 从配置层栈加载所有插件 |
| `load_plugin` | `fn(config_name, plugin, store, product, rules) -> LoadedPlugin` | 加载单个插件的完整信息 |
| `load_plugin_mcp_servers` | `fn(plugin_root: &Path) -> HashMap<String, McpServerConfig>` | 加载插件的 MCP 服务器配置 |
| `load_plugin_apps` | `fn(plugin_root: &Path) -> Vec<AppConnectorId>` | 加载插件的应用连接器列表 |
| `refresh_curated_plugin_cache` | `fn(codex_home, version, ids) -> Result<bool>` | 刷新精选插件缓存 |

## 依赖关系
- **引用的 crate**: `codex_analytics`, `codex_app_server_protocol`, `codex_features`, `codex_plugin`, `codex_protocol`, `codex_utils_absolute_path`, `serde`, `serde_json`, `tokio`, `toml_edit`, `tracing`
- **引用的内部模块**: `super::marketplace`, `super::manifest`, `super::remote`, `super::startup_sync`, `super::store`, `crate::config`, `crate::config_loader`, `crate::config_rules`, `crate::loader`, `crate::auth`
- **被引用方**: `mod.rs` 导出为模块的核心公开 API

## 实现备注
- 使用 `RwLock` 缓存已加载的插件状态和推荐插件 ID，避免重复加载
- 推荐插件 ID 缓存的 TTL 为 3 小时（`FEATURED_PLUGIN_IDS_CACHE_TTL`）
- 远程同步使用 `Mutex` 保证串行执行
- 精选仓库同步通过 `AtomicBool` 确保全局只启动一次
- 产品限制（product restriction）在市场准入时执行，运行时不再重新过滤
- 插件安装通过 `spawn_blocking` 在阻塞线程中执行文件操作
- MCP 服务器配置支持相对路径的 `cwd` 自动解析为绝对路径
- 去除了 OAuth `callbackPort` 配置（由 Codex 全局管理）
