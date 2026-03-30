# `runStreamed.test.ts` -- 测试模块

## 测试覆盖范围

测试 `Codex` SDK 的 `runStreamed()` 方法的流式事件输出。覆盖基本事件序列验证、多轮流式对话、线程恢复以及流式模式下的结构化输出 schema 传递等场景。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `returns thread events` | 验证流式输出的完整事件序列：thread.started、turn.started、item.completed、turn.completed，以及各事件的数据内容 |
| `sends previous items when runStreamed is called twice` | 多轮流式对话：第二次请求应包含第一轮的助手响应上下文 |
| `resumes thread by id when streaming` | 通过 `resumeThread(id)` 恢复流式对话，验证 thread ID 一致性和上下文传递 |
| `applies output schema turn options when streaming` | 验证流式模式下 outputSchema 正确转换为 `text.format` JSON Schema 传递给 API |
