# `async.py` -- 流式事件监听示例（异步版）

演示通过 `turn.stream()` 异步迭代器逐个接收服务端事件。监听 `turn/started`、`item/agentMessage/delta`（增量文本）和 `turn/completed` 事件，实时打印助手回复的增量文本，并输出事件统计信息。
