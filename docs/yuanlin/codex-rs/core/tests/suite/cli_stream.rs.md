# `cli_stream.rs` — 测试模块

## 测试覆盖范围

测试 CLI 流式输出功能，包括通过模拟服务器的 Responses API 流式传输、环境变量回退(OPENAI_BASE_URL)、配置覆盖(openai_base_url)、自定义模型指令文件、profile 配置下的模型指令、SSE 固定文件流式传输、会话文件的创建/恢复/追加，以及 Git 信息的采集与序列化。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `responses_mode_stream_cli` | 通过模拟服务器流式传输 Responses API 响应，验证 CLI 输出和请求路径 |
| `responses_mode_stream_cli_supports_openai_base_url_env_fallback` | 验证 OPENAI_BASE_URL 环境变量作为已弃用回退仍可正常路由请求 |
| `responses_mode_stream_cli_supports_openai_base_url_config_override` | 验证 openai_base_url 配置覆盖可正确路由内置 OpenAI provider 的请求 |
| `exec_cli_applies_model_instructions_file` | 通过 `-c model_instructions_file=...` 传入自定义指令文件，验证请求中包含自定义标记 |
| `exec_cli_profile_applies_model_instructions_file` | 使用 `--profile` 参数时保留活跃 profile，验证 profile 中的 model_instructions_file 被正确应用 |
| `responses_api_stream_cli` | 使用本地 SSE 固定文件替代实时服务器进行流式测试 |
| `integration_creates_and_checks_session_file` | 创建会话并验证会话文件的目录结构(YYYY/MM/DD)、元数据和消息内容，然后恢复并验证追加 |
| `integration_git_info_unit_test` | 独立测试 Git 信息采集功能，验证 commit hash、分支名、远程 URL 的正确性及序列化 |
