# `async.py` -- Turn/Run 分离示例（异步版）

演示将 `turn()` 和 `run()` 分开调用的低级用法。创建 turn 后通过 `turn.run()` 执行，然后使用 `thread.read(include_turns=True)` 读取持久化的线程数据，并从中提取 turn 的状态、ID 和助手文本。
