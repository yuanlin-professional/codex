# `store_tests.rs` -- 测试模块

## 测试覆盖范围
测试 `store.rs` 中 `PluginStore` 的安装、卸载、版本管理和安全性验证逻辑。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `install_copies_plugin_into_default_marketplace` | 验证安装将插件复制到正确的缓存路径 |
| `install_uses_manifest_name_for_destination_and_key` | 验证使用清单名称（而非源目录名）作为缓存路径 |
| `plugin_root_derives_path_from_key_and_version` | 验证路径格式为 `plugins/cache/<marketplace>/<plugin>/<version>` |
| `install_with_version_uses_requested_cache_version` | 验证使用指定版本号作为缓存子目录名 |
| `active_plugin_version_reads_version_directory_name` | 验证正确读取版本目录名 |
| `active_plugin_version_prefers_default_local_version_when_multiple_versions_exist` | 验证多版本共存时优先返回 "local" 版本 |
| `active_plugin_version_returns_last_sorted_version_when_default_is_missing` | 验证无 "local" 版本时返回字典序最大的版本 |
| `plugin_root_rejects_path_separators_in_key_segments` | 验证拒绝包含路径分隔符的插件 ID（防止路径穿越） |
| `install_rejects_manifest_names_with_path_separators` | 验证拒绝包含路径分隔符的清单名称 |
| `install_rejects_marketplace_names_with_path_separators` | 验证拒绝包含路径分隔符的市场名称 |
| `install_rejects_manifest_names_that_do_not_match_marketplace_plugin_name` | 验证清单名称必须与市场插件名称匹配 |
