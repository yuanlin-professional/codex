# `abort.test.ts` -- 测试模块

## 测试覆盖范围

测试 `AbortSignal` 对 `run()` 和 `runStreamed()` 方法的取消支持。覆盖信号预先中止、执行期间中止、迭代期间中止以及未中止正常完成等场景。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `aborts run() when signal is aborted` | 信号已预先中止时调用 `run()` 应抛出异常 |
| `aborts runStreamed() when signal is aborted` | 信号已预先中止时调用 `runStreamed()` 后迭代应失败 |
| `aborts run() when signal is aborted during execution` | 在 `run()` 执行过程中中止信号，操作应抛出异常 |
| `aborts runStreamed() when signal is aborted during iteration` | 在流式迭代过程中中止信号（第 5 个事件后），迭代应终止并抛出异常 |
| `completes normally when signal is not aborted` | 提供 AbortController 但不触发中止，操作应正常完成并返回预期结果 |
