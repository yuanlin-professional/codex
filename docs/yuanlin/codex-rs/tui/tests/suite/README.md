# tests/suite/

## 功能概述

`suite/` 目录包含 `codex-tui` 的集成测试套件模块。所有测试通过 `mod.rs` 聚合，由顶层 `all.rs` 统一编译为单个测试二进制。

测试覆盖以下方面：
- **启动安全性**：确保 TUI 启动时不会 panic
- **状态指示器**：验证状态指示器组件的正确渲染行为
- **模型可用性新用户体验**：验证首次使用时模型不可用的 UI 提示
- **VT100 终端渲染**：使用 VT100 模拟器验证聊天历史和实时流式提交的完整终端输出

## 目录结构

```
suite/
├── mod.rs                       # 模块入口，聚合所有测试子模块
├── model_availability_nux.rs    # 模型可用性新用户体验测试
├── no_panic_on_startup.rs       # 启动无 panic 测试
├── status_indicator.rs          # 状态指示器测试
├── vt100_history.rs             # VT100 历史记录渲染测试
└── vt100_live_commit.rs         # VT100 实时提交渲染测试
```

## 核心接口/API

| 测试模块 | 说明 |
|----------|------|
| `no_panic_on_startup` | 启动 TUI 应用并立即退出，验证无 panic |
| `status_indicator` | 测试状态指示器组件在不同状态下的渲染输出 |
| `model_availability_nux` | 测试当配置的模型不可用时的新用户体验流程 |
| `vt100_history` | 通过 VT100 模拟器验证聊天历史消息的完整渲染（需 `vt100-tests` feature） |
| `vt100_live_commit` | 通过 VT100 模拟器验证流式响应的实时提交动画渲染（需 `vt100-tests` feature） |
