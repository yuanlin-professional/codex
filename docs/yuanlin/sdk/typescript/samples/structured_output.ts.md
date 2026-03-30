# `structured_output.ts` -- 结构化输出示例（原生 JSON Schema）

`structured_output.ts` 演示了如何使用 Codex SDK 的结构化输出功能。通过在 `thread.run()` 的 `turnOptions` 中传入 `outputSchema`（原生 JSON Schema 对象），要求代理以指定格式返回结果。示例定义了一个包含 `summary`（字符串）和 `status`（枚举 ok/action_required）的 schema，让代理总结仓库状态并以 JSON 格式输出。
