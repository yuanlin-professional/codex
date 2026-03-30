# `registry.rs` — JSON-RPC 方法路由注册

## 功能概述

`build_router` 函数构建 `RpcRouter<ExecServerHandler>`，注册所有 exec-server 协议方法（initialize、initialized、process/start、process/read、process/write、process/terminate、fs/readFile、fs/writeFile、fs/createDirectory、fs/getMetadata、fs/readDirectory、fs/remove、fs/copy）到对应的处理器方法。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_router` | `fn() -> RpcRouter<ExecServerHandler>` | 注册全部 RPC 方法路由 |

## 依赖关系

- **被引用方**: `server/processor.rs`
