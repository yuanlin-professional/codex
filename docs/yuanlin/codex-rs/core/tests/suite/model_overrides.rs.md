# `model_overrides.rs` — 测试模块

## 测试覆盖范围
模型覆盖（Override）配置的持久化行为，验证 OverrideTurnContext 操作不会将临时覆盖写入 config.toml 文件。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `override_turn_context_does_not_persist_when_config_exists` | 已有 config.toml 时，OverrideTurnContext 不修改文件内容 |
| `override_turn_context_does_not_create_config_file` | 无 config.toml 时，OverrideTurnContext 不创建新的配置文件 |
