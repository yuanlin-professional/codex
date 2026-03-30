# tests/

## 功能概述

`tests/` 目录包含 `codex-tui` crate 的集成测试。测试通过单个二进制文件 `all.rs` 聚合所有测试模块，测试套件位于 `suite/` 子目录中。

部分测试依赖 `vt100-tests` feature flag，这些测试使用 VT100 终端模拟器来验证完整的终端渲染输出。

## 目录结构

```
tests/
├── all.rs                          # 单一集成测试入口，聚合所有测试模块
├── test_backend.rs                 # 测试用终端后端（VT100 模拟）
├── manager_dependency_regression.rs # 管理器依赖回归测试
├── suite/                          # 测试套件子目录
│   ├── mod.rs                      # 套件模块入口
│   ├── model_availability_nux.rs   # 模型可用性新用户体验测试
│   ├── no_panic_on_startup.rs      # 启动无 panic 测试
│   ├── status_indicator.rs         # 状态指示器测试
│   ├── vt100_history.rs            # VT100 历史记录渲染测试
│   └── vt100_live_commit.rs        # VT100 实时提交渲染测试
└── fixtures/                       # 测试固定数据
    └── oss-story.jsonl             # OSS 场景测试数据
```

## 核心接口/API

| 测试模块 | 说明 |
|----------|------|
| `no_panic_on_startup` | 验证 TUI 启动时不会 panic |
| `status_indicator` | 验证状态指示器的正确渲染 |
| `model_availability_nux` | 验证模型可用性新用户体验流程 |
| `vt100_history` | 使用 VT100 模拟器验证聊天历史渲染（需 `vt100-tests` feature） |
| `vt100_live_commit` | 使用 VT100 模拟器验证实时流式提交渲染（需 `vt100-tests` feature） |
