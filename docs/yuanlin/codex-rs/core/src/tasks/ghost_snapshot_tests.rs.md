# `ghost_snapshot_tests.rs` — 测试模块

## 测试覆盖范围

测试 `ghost_snapshot.rs` 中大型未跟踪目录警告消息的格式化逻辑。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `large_untracked_warning_includes_threshold` | 验证当设置了阈值时，警告消息包含 ">= N files" 的阈值描述 |
| `large_untracked_warning_disabled_when_threshold_disabled` | 验证当阈值为 `None` 时，不生成警告消息 |
