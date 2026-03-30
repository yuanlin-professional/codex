# `rmcp_client.rs` -- 测试模块

## 测试覆盖范围
测试 MCP（Model Context Protocol）客户端的端到端集成，包括 stdio 传输的工具调用往返、图片响应处理、纯文本模型的图片净化、环境变量白名单传播、Streamable HTTP 传输的工具调用，以及 OAuth 认证的 Streamable HTTP 传输。测试使用 wiremock MockServer 和自定义测试 MCP 服务器二进制。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `stdio_server_round_trip` | stdio MCP 服务器 echo 工具调用往返，验证 McpToolCallBegin/End 事件、structured_content 和环境变量传播 |
| `stdio_image_responses_round_trip` | stdio MCP 服务器 image 工具返回 PNG 图片，验证 content 中包含 base64 图片数据，function_call_output 中为 input_image 格式 |
| `stdio_image_responses_are_sanitized_for_text_only_model` | 对仅支持文本输入的模型，MCP 图片响应被替换为文本占位符 |
| `stdio_server_propagates_whitelisted_env_vars` | 通过 env_vars 白名单配置传播宿主环境变量到 MCP 服务器 |
| `streamable_http_tool_call_round_trip` | Streamable HTTP 传输的 MCP echo 工具调用往返 |
| `streamable_http_with_oauth_round_trip` | 携带 OAuth 凭据的 Streamable HTTP MCP 工具调用，使用 fallback 凭据文件 |
