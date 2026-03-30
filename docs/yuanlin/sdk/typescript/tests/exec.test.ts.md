# `exec.test.ts` -- 测试模块

## 测试覆盖范围

测试 `CodexExec` 类的底层行为，包括子进程提前退出的错误处理、CLI 参数排列顺序以及环境变量覆盖功能。使用 Jest mock 完全替换 `child_process.spawn`，通过 `FakeChildProcess` 模拟子进程行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `rejects when exit happens before stdout closes` | 子进程以非零退出码提前退出时，异步迭代器应抛出包含 stderr 内容的错误 |
| `places resume args before image args` | 验证 `resume <thread-id>` 参数在 `--image` 参数之前，确保 CLI 参数顺序正确 |
| `allows overriding the env passed to the Codex CLI` | 提供自定义 env 时，spawn 应使用自定义环境变量且不泄漏 `process.env` 中的值；同时验证 `CODEX_API_KEY` 和 `--config openai_base_url` 的传递 |
