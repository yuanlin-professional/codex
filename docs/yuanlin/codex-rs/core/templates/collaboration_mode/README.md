# templates/collaboration_mode/ -- 协作模式提示词

## 功能概述

`collaboration_mode/` 包含 Codex 各种协作模式的提示词模板。协作模式定义了 AI 代理在不同工作模式下的行为准则。模式切换通过 `<collaboration_mode>` 标签在 developer instructions 中控制，已知的模式名称包括 default、execute、pair_programming 和 plan。

## 目录结构

```
collaboration_mode/
├── default.md            # 默认模式：标准对话和任务执行
├── execute.md            # 执行模式：专注于代码编写和命令执行
├── pair_programming.md   # 结对编程模式：交互式协作开发
└── plan.md               # 计划模式：任务规划和方案设计
```

## 依赖关系

- **被引用方**：`src/models_manager/collaboration_mode_presets.rs`（协作模式预设加载模板）、`src/context_manager/updates.rs`（模式变更时注入模板）

## 核心接口/API

| 文件 | 说明 |
|---|---|
| `default.md` | 默认模式指令，支持占位符：`{{KNOWN_MODE_NAMES}}`、`{{REQUEST_USER_INPUT_AVAILABILITY}}`、`{{ASKING_QUESTIONS_GUIDANCE}}` |
| `execute.md` | 执行模式指令：专注于实际代码实现和命令执行 |
| `pair_programming.md` | 结对编程模式指令：增强交互性，主动提问和确认 |
| `plan.md` | 计划模式指令：先规划后执行，生成任务计划 |
