# `store.rs` -- 插件本地缓存存储

## 功能概述
该文件实现了 `PluginStore`，负责管理插件在本地磁盘上的缓存存储。提供插件的安装（复制到缓存）、卸载（删除缓存）、版本发现和路径解析功能。存储目录结构为 `plugins/cache/<marketplace>/<plugin>/<version>/`。安装和更新操作通过原子性的目录交换实现，确保不会出现半完成状态。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `PluginStore` | struct | 插件缓存存储管理器，持有缓存根目录的绝对路径 |
| `PluginInstallResult` | struct | 安装结果，包含 plugin_id、版本号和安装路径 |
| `PluginStoreError` | enum | 存储操作错误，包含 IO 错误和验证错误两种变体 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `PluginStore::new` | `fn(codex_home: PathBuf) -> Self` | 创建存储实例，缓存根为 `<codex_home>/plugins/cache` |
| `PluginStore::install` | `fn(&self, source_path, plugin_id) -> Result<PluginInstallResult>` | 安装插件（默认版本 "local"） |
| `PluginStore::install_with_version` | `fn(&self, source_path, plugin_id, version) -> Result<PluginInstallResult>` | 安装指定版本的插件 |
| `PluginStore::uninstall` | `fn(&self, plugin_id) -> Result<()>` | 卸载插件（删除整个插件目录） |
| `PluginStore::active_plugin_version` | `fn(&self, plugin_id) -> Option<String>` | 获取插件的活跃版本 |
| `PluginStore::active_plugin_root` | `fn(&self, plugin_id) -> Option<AbsolutePathBuf>` | 获取插件活跃版本的根目录 |
| `PluginStore::is_installed` | `fn(&self, plugin_id) -> bool` | 检查插件是否已安装 |
| `PluginStore::plugin_root` | `fn(&self, plugin_id, version) -> AbsolutePathBuf` | 获取指定版本的插件缓存路径 |
| `replace_plugin_root_atomically` | `fn(source, target_root, version) -> Result<()>` | 原子性地替换插件缓存目录 |
| `copy_dir_recursive` | `fn(source, target) -> Result<()>` | 递归复制目录 |

## 依赖关系
- **引用的 crate**: `codex_plugin`, `codex_utils_absolute_path`, `codex_utils_plugins`, `tempfile`
- **引用的内部模块**: `super::load_plugin_manifest`
- **被引用方**: `manager.rs` 中的 `PluginsManager` 持有 `PluginStore` 实例

## 实现备注
- 版本发现策略：优先返回 "local" 版本，否则返回字典序最大的版本
- 版本名称验证：仅允许 ASCII 字母、数字、`_` 和 `-`
- 安装前验证：检查源路径是否为目录、清单名称是否与插件 ID 匹配
- 原子性更新：使用临时目录 + rename 实现，失败时支持自动回滚到备份
- 卸载操作是幂等的：对不存在的路径不报错
