# `prompt_stdin.rs` — 测试模块

## 测试覆盖范围

验证 codex-exec 的 stdin prompt 处理行为：管道输入追加、空输入忽略、`-` 强制读取 stdin、无参数时从 stdin 读取。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `exec_appends_piped_stdin_to_prompt_argument` | 管道 stdin 内容以 `<stdin>` 块追加到 prompt 参数后 |
| `exec_ignores_empty_piped_stdin_when_prompt_argument_is_present` | 空 stdin 与 prompt 参数共存时忽略 stdin |
| `exec_dash_prompt_reads_stdin_as_the_prompt` | `-` 参数强制从 stdin 读取完整 prompt |
| `exec_without_prompt_argument_reads_piped_stdin_as_the_prompt` | 无 prompt 参数时从管道 stdin 读取 |
| `exec_without_prompt_argument_rejects_empty_piped_stdin` | 无 prompt 参数且 stdin 为空时以错误退出 |
| `exec_dash_prompt_rejects_empty_piped_stdin` | `-` 参数且 stdin 为空时以错误退出 |
