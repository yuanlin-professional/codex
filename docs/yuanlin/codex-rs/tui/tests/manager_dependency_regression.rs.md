# `manager_dependency_regression.rs` — 测试模块

## 测试覆盖范围

验证 TUI 运行时源码不依赖于 Manager 层的"逃逸舱口"（escape hatches），防止 TUI 源码中出现对 `AuthManager`、`ThreadManager` 等直接引用的回归问题。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `tui_runtime_source_does_not_depend_on_manager_escape_hatches` | 递归扫描 `src/` 下所有 `.rs` 文件，检查是否包含 `AuthManager`、`ThreadManager`、`auth_manager(`、`thread_manager(` 等禁止的字符串引用。若发现任何违规则测试失败，确保 TUI 层与 Manager 层保持解耦。 |
