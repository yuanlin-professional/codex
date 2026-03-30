# `resume_warning.rs` -- 测试模块

## 测试覆盖范围
测试会话恢复时的警告机制，验证当恢复的会话使用了不同模型或配置时，系统正确发出警告事件。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `resume_with_different_model_emits_warning` | 使用不同模型恢复会话时发出模型变更警告 |
| `resume_with_same_model_no_warning` | 使用相同模型恢复时不发出警告 |
