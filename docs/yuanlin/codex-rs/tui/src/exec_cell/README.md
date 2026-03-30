# src/exec_cell/

## 功能概述

`exec_cell/` 模块负责命令执行单元格的数据模型和渲染。在 TUI 聊天转录中，当 AI 助手执行 shell 命令时，每个命令（或一组相关的探索性命令）会显示为一个 `ExecCell`。

`ExecCell` 可以表示单个命令或一组相关的读取/列表/搜索命令（"exploring" 分组）。聊天组件通过稳定的 `call_id` 将进度和结束事件路由到正确的单元格。

## 目录结构

```
exec_cell/
├── mod.rs      # 模块入口，重导出核心类型
├── model.rs    # 数据模型（ExecCell、ExecCall、CommandOutput）
└── render.rs   # 渲染逻辑（命令高亮、输出截断、旋转动画）
```

## 核心接口/API

| 类型/函数 | 说明 |
|-----------|------|
| `ExecCell` | 命令执行单元格，包含一个或多个 `ExecCall` |
| `ExecCall` | 单次命令调用，包含命令文本、解析结果、输出、时长等 |
| `CommandOutput` | 命令输出数据（退出码、聚合输出、格式化输出） |
| `new_active_exec_command()` | 创建一个新的活动执行命令单元格 |
| `output_lines()` | 将命令输出格式化为显示行 |
| `spinner()` | 渲染加载旋转动画 |
| `TOOL_CALL_MAX_LINES` | 工具调用输出最大显示行数（5 行） |
| `OutputLinesParams` | 输出行渲染参数（行数限制、是否仅显示错误等） |
