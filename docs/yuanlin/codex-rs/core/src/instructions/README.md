# instructions/ -- 指令系统

## 功能概述

`instructions/` 是 Codex 用户指令系统的核心适配层。该模块将外部 `codex-instructions` crate 的功能引入 core 内部使用，负责用户自定义指令的加载、解析和注入。用户指令（来自项目 `.codex/instructions.md` 或全局配置）会被拼接到发送给模型的 developer message 中，用于定制 AI 代理的行为。

## 目录结构

```
instructions/
└── mod.rs    # 模块入口，重新导出 codex-instructions crate 的核心类型
```

## 依赖关系

- **上游依赖**：`codex-instructions` crate（提供 `UserInstructions` 结构体和 `USER_INSTRUCTIONS_PREFIX` 常量）
- **内部依赖**：无直接内部依赖
- **被依赖方**：`config/`（配置层合并用户指令）、`codex/`（Session 构建 developer message 时注入用户指令）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `UserInstructions` | 用户指令结构体，封装从文件系统加载的自定义指令内容 |
| `USER_INSTRUCTIONS_PREFIX` | 用户指令在 developer message 中的前缀标记字符串 |
