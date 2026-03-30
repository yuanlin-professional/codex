# `unstable_features_warning.rs` -- 测试模块

## 测试覆盖范围
验证当通过配置文件启用不稳定特性时，系统正确发出警告事件通知用户。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `emits_warning_when_unstable_features_enabled_via_config` | 启用不稳定特性（如 ChildAgentsMd）后初始化线程时收到 WarningEvent |
