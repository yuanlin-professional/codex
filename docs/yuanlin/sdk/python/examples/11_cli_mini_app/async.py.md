# `async.py` -- 迷你 CLI 交互应用示例（异步版）

演示基于异步 SDK 构建的交互式命令行聊天应用。在循环中读取用户输入，通过 `turn.stream()` 异步迭代器实时打印助手回复的增量文本，同时捕获 token 使用量统计（`ThreadTokenUsageUpdatedNotification`）和 turn 完成状态（`TurnCompletedNotification`）。支持 `/exit` 和 `/quit` 退出命令。
