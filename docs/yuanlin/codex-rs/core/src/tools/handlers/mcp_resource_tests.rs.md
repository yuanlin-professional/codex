# `mcp_resource_tests.rs` — 测试模块

## 测试覆盖范围
针对 `mcp_resource.rs` 中的数据结构序列化逻辑和参数解析工具函数进行单元测试，覆盖 `ResourceWithServer`、`ListResourcesPayload`、`ResourceTemplateWithServer` 的序列化行为，以及 `call_tool_result_from_content` 和 `parse_arguments` 的边界情况。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `resource_with_server_serializes_server_field` | 验证 `ResourceWithServer` 序列化后包含 `server`、`uri`、`name` 字段 |
| `list_resources_payload_from_single_server_copies_next_cursor` | 验证单服务器模式下 `nextCursor` 和 `server` 字段正确传递 |
| `list_resources_payload_from_all_servers_is_sorted` | 验证全服务器聚合模式下资源按服务器名称字典序排列 |
| `call_tool_result_from_content_marks_success` | 验证 `call_tool_result_from_content` 在 success=true 时 `is_error` 为 `Some(false)` |
| `parse_arguments_handles_empty_and_json` | 验证空白字符串返回 None、`"null"` 返回 None、有效 JSON 正确解析 |
| `template_with_server_serializes_server_field` | 验证 `ResourceTemplateWithServer` 序列化后 `uriTemplate` 使用 camelCase |
