# `mod_tests.rs` — 测试模块

## 测试覆盖范围

对统一执行模块（`unified_exec`）进行端到端集成测试，覆盖交互式 Shell 会话的创建与复用、跨请求状态持久化、输出超时控制、暂停阻塞、进程退出后重用检测、管道命令退出码保留、远程执行服务器集成等场景。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `push_chunk_preserves_prefix_and_suffix` | 验证 `HeadTailBuffer` 在写满后仍保留头部前缀和尾部后缀（同步测试） |
| `head_tail_buffer_default_preserves_prefix_and_suffix` | 验证默认容量的 `HeadTailBuffer` 保留首尾内容 |
| `unified_exec_persists_across_requests` | 验证交互式 Shell 会话的环境变量在多次 `write_stdin` 请求之间持久化 |
| `multi_unified_exec_sessions` | 验证多个独立 Shell 会话互不干扰，短命令在新 Shell 中执行，原会话状态不丢失 |
| `unified_exec_timeouts` | 验证 yield 超时机制：短超时无法获取完整输出，后续 poll 可以检索到剩余输出 |
| `unified_exec_pause_blocks_yield_timeout` | 验证暂停状态会阻塞 yield 超时计时，取消暂停后可正确获取输出 |
| `requests_with_large_timeout_are_capped` | 验证过大的 yield 超时值会被截断 |
| `completed_commands_do_not_persist_sessions` | 验证已完成的命令不会留下持久化的进程条目 |
| `reusing_completed_process_returns_unknown_process` | 验证尝试复用已退出的进程会返回 `UnknownProcessId` 错误 |
| `completed_pipe_commands_preserve_exit_code` | 验证管道模式（非 TTY）命令正确保留退出码 |
| `unified_exec_uses_remote_exec_server_when_configured` | 验证配置远程执行服务器时，统一执行能正确使用远程后端 |
| `remote_exec_server_rejects_inherited_fd_launches` | 验证远程执行服务器拒绝带继承文件描述符的启动请求 |
