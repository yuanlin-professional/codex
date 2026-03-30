# `types_tests.rs` -- 测试模块

## 测试覆盖范围
覆盖 `types.rs` 中 MCP 服务器配置和 Skill 配置的反序列化逻辑，验证各种传输类型、可选字段和错误拒绝场景。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `deserialize_stdio_command_server_config` | 验证最简 stdio MCP 服务器反序列化 |
| `deserialize_stdio_command_server_config_with_args` | 验证带 args 的 stdio 配置 |
| `deserialize_stdio_command_server_config_with_arg_with_args_and_env` | 验证带 args 和 env 的 stdio 配置 |
| `deserialize_stdio_command_server_config_with_env_vars` | 验证带 env_vars 的 stdio 配置 |
| `deserialize_stdio_command_server_config_with_cwd` | 验证带 cwd 的 stdio 配置 |
| `deserialize_disabled_server_config` | 验证禁用的服务器配置 |
| `deserialize_required_server_config` | 验证 required 标志的服务器配置 |
| `deserialize_streamable_http_server_config` | 验证 HTTP 传输的 MCP 配置 |
| `deserialize_streamable_http_server_config_with_env_var` | 验证带 bearer_token_env_var 的 HTTP 配置 |
| `deserialize_streamable_http_server_config_with_headers` | 验证带 http_headers 和 env_http_headers 的 HTTP 配置 |
| `deserialize_streamable_http_server_config_with_oauth_resource` | 验证带 oauth_resource 的 HTTP 配置 |
| `deserialize_server_config_with_tool_filters` | 验证 enabled_tools/disabled_tools 工具过滤 |
| `deserialize_ignores_unknown_server_fields` | 验证未知字段被忽略 |
| `deserialize_skill_config_with_name_selector` | 验证按名称选择器的 skill 配置 |
| `deserialize_skill_config_with_path_selector` | 验证按路径选择器的 skill 配置 |
| `deserialize_rejects_command_and_url` | 验证同时设置 command 和 url 被拒绝 |
| `deserialize_rejects_env_for_http_transport` | 验证 HTTP 传输拒绝 env 字段 |
| `deserialize_rejects_headers_for_stdio` | 验证 stdio 传输拒绝 http_headers/env_http_headers/oauth_resource |
| `deserialize_rejects_inline_bearer_token_field` | 验证拒绝 inline bearer_token（须用 env_var） |
