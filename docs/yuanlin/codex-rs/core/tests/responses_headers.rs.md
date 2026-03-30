# `responses_headers.rs` — 测试模块

## 测试覆盖范围

验证 `ModelClient` 在向 OpenAI Responses API 发送请求时正确设置 HTTP 请求头，包括 subagent 标识头、沙箱标识头、turn 元数据头以及模型推理配置覆盖等。所有测试依赖 wiremock 模拟服务器来捕获和检查实际发出的 HTTP 请求。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `responses_stream_includes_subagent_header_on_review` | 当 `SessionSource` 为 `SubAgent(Review)` 时，验证请求头中包含 `x-openai-subagent: review`，且不包含 `x-codex-sandbox` 头。 |
| `responses_stream_includes_subagent_header_on_other` | 当 `SessionSource` 为 `SubAgent(Other("my-task"))` 时，验证请求头中包含 `x-openai-subagent: my-task`。 |
| `responses_respects_model_info_overrides_from_config` | 当配置中 `model_supports_reasoning_summaries = true` 且 `model_reasoning_summary = Detailed` 时，验证请求体中包含 `reasoning.summary = "detailed"` 字段，证明配置覆盖生效。 |
| `responses_stream_includes_turn_metadata_header_for_git_workspace_e2e` | 端到端验证 `x-codex-turn-metadata` 请求头：(1) 首次请求包含 turn_id 和 sandbox 字段；(2) 在 cwd 初始化 git 仓库后提交新 turn，验证两个请求共享相同 turn_id 但与首轮不同；(3) 验证 workspace 元数据包含正确的 `latest_git_commit_hash`、`associated_remote_urls` 和 `has_changes` 信息。 |
