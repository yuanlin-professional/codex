# `config_rpc.rs` — 测试模块

## 测试覆盖范围
测试 `config/read`、`config/value/write` 和 `config/batchWrite` JSON-RPC 方法，
涵盖配置读取（有效值和图层来源）、工具配置（web_search、view_image）、应用配置、
项目层级配置、系统管理层覆盖、单值写入、版本冲突检测和批量写入。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `config_read_returns_effective_and_layers` | 读取有效配置值及其来源图层 |
| `config_read_includes_tools` | 配置读取包含工具设置（web_search、view_image） |
| `config_read_includes_nested_web_search_tool_config` | 读取嵌套的 web_search 工具配置 |
| `config_read_ignores_bool_web_search_tool_config` | 布尔值形式的 web_search 配置被忽略 |
| `config_read_includes_apps` | 配置读取包含应用设置 |
| `config_read_includes_project_layers_for_cwd` | 按 cwd 读取项目级配置层 |
| `config_read_includes_system_layer_and_overrides` | 系统管理配置层覆盖用户配置 |
| `config_value_write_replaces_value` | 单值写入替换配置项 |
| `config_value_write_rejects_version_conflict` | 版本不匹配时拒绝写入 |
| `config_batch_write_applies_multiple_edits` | 批量写入多个配置项 |
