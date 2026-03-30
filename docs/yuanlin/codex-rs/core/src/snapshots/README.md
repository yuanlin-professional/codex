# snapshots/ -- 测试快照

## 功能概述

`snapshots/` 存放 `codex/` 模块的 insta 测试快照文件。这些快照用于回归测试，确保会话初始化和上下文构建的输出在代码变更后保持一致。

## 目录结构

```
snapshots/
└── codex_core__codex_tests__fork_startup_context_then_first_turn_diff.snap    # 分叉启动上下文和首轮差异快照
```

## 依赖关系

- **上游依赖**：`insta`（快照测试框架）
- **内部依赖**：由 `codex/` 模块的测试生成和验证
- **被依赖方**：无（仅测试使用）

## 核心接口/API

无公开接口。此目录仅存储测试快照文件。
