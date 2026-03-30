# `initialize.rs` — 测试模块

## 测试覆盖范围

验证 exec-server WebSocket 初始化握手流程。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `exec_server_accepts_initialize` | 发送 initialize 请求后收到正确的 InitializeResponse |
