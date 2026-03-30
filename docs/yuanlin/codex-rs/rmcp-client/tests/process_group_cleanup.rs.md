# `process_group_cleanup.rs` -- 测试模块

## 测试覆盖范围
验证 `RmcpClient` 在 Unix 平台上通过 stdio 传输创建的子进程组在客户端被 drop 后能被正确清理（终止），防止孤儿进程泄漏。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `drop_kills_wrapper_process_group` | 创建 stdio MCP 客户端并启动一个后台 sleep 子进程，验证 drop 客户端后孙进程也被终止 |
