# `windows_sandbox_setup.rs` — 测试模块

## 测试覆盖范围
测试 `windowsSandbox/setupStart` JSON-RPC 方法，验证发送完成通知以及拒绝相对 cwd 路径。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `windows_sandbox_setup_start_emits_completion_notification` | 沙箱设置完成后发送通知 |
| `windows_sandbox_setup_start_rejects_relative_cwd` | 拒绝相对路径的 cwd |
