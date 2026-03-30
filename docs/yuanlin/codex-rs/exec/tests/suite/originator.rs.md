# `originator.rs` — 测试模块

## 测试覆盖范围

验证 codex-exec 在请求头中发送正确的 Originator 标识。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `send_codex_exec_originator` | 默认发送 Originator: codex_exec 头 |
| `supports_originator_override` | 通过 CODEX_INTERNAL_ORIGINATOR_OVERRIDE 环境变量覆盖 originator |
