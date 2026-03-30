# `turn_metadata_tests.rs` — 测试模块

## 测试覆盖范围

测试对话轮次元数据（turn metadata）的构建和序列化逻辑，包括 git 仓库变更检测的 `has_changes` 字段，以及 `TurnMetadataState` 生成的 JSON 头中沙箱标签和会话 ID 的正确性。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `build_turn_metadata_header_includes_has_changes_for_clean_repo` | 在已提交所有变更的干净 git 仓库中，构建的元数据头中 `has_changes` 应为 `false` |
| `turn_metadata_state_uses_platform_sandbox_tag` | `TurnMetadataState` 生成的 JSON 头中 `sandbox` 字段应使用平台沙箱标签，`session_id` 应与初始化时传入的值一致 |
