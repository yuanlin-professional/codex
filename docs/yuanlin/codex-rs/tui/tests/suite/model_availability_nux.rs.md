# `model_availability_nux.rs` — 测试模块

## 测试覆盖范围

验证 TUI 的"模型可用性新用户体验 (NUX)"计数机制在 resume（恢复会话）启动流程中不会被重复消耗。该测试通过构造自定义模型目录和配置文件，执行 `codex exec` 创建初始会话，然后通过 PTY 执行 `codex resume --last` 来恢复会话，最后验证 `config.toml` 中的 NUX 显示计数未被递增。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `resume_startup_does_not_consume_model_availability_nux_count` | 构造带有 `availability_nux` 的自定义模型目录，先执行 `codex exec` 创建种子会话，再通过 PTY 执行 `codex resume --last` 恢复会话。通过模拟光标位置查询应答和发送 Ctrl+C 中断信号完成交互，最终断言 `config.toml` 中的 `tui.model_availability_nux` 计数仍为 1（未因 resume 而递增）。Windows 平台因 PTY 限制跳过此测试。 |
