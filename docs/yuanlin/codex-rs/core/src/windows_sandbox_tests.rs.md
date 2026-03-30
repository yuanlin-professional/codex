# `windows_sandbox_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖 Windows 沙箱级别（WindowsSandboxLevel）的功能标志解析逻辑、遗留配置键的兼容处理、以及沙箱模式和私有桌面（private desktop）设置的解析优先级。验证 features 标志、TOML 配置层级（顶层 vs 配置文件）的优先级关系。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `elevated_flag_works_by_itself` | 验证仅启用 WindowsSandboxElevated 标志时返回 Elevated 级别 |
| `restricted_token_flag_works_by_itself` | 验证仅启用 WindowsSandbox 标志时返回 RestrictedToken 级别 |
| `no_flags_means_no_sandbox` | 验证无标志时返回 Disabled 级别 |
| `elevated_wins_when_both_flags_are_enabled` | 验证同时启用两个标志时 Elevated 优先 |
| `legacy_mode_prefers_elevated` | 验证遗留配置键同时存在时优先选择 Elevated 模式 |
| `legacy_mode_supports_alias_key` | 验证遗留配置支持别名键 enable_experimental_windows_sandbox |
| `resolve_windows_sandbox_mode_prefers_profile_windows` | 验证配置文件（profile）中的 windows.sandbox 优先于顶层配置 |
| `resolve_windows_sandbox_mode_falls_back_to_legacy_keys` | 验证无 windows 配置段时回退到遗留 features 键 |
| `resolve_windows_sandbox_mode_profile_legacy_false_blocks_top_level_legacy_true` | 验证 profile 中遗留键为 false 能覆盖顶层的 true 设置 |
| `resolve_windows_sandbox_private_desktop_prefers_profile_windows` | 验证 profile 中的 sandbox_private_desktop 优先于顶层配置 |
| `resolve_windows_sandbox_private_desktop_defaults_to_true` | 验证无配置时 sandbox_private_desktop 默认为 true |
| `resolve_windows_sandbox_private_desktop_respects_explicit_cfg_value` | 验证顶层显式设置 sandbox_private_desktop 为 false 时生效 |
