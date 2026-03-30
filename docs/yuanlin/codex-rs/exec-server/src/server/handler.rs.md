# `handler.rs` — exec-server 统一请求处理器

## 功能概述

`ExecServerHandler` 是服务端的顶层请求处理器，组合 `ProcessHandler` 和 `FileSystemHandler`，统一暴露初始化、进程管理和文件系统操作的方法。所有文件系统方法在调用前会检查初始化状态。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecServerHandler` | struct | 组合进程和文件系统处理器的顶层处理器 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(notifications) -> Self` | 创建处理器，初始化子处理器 |
| `shutdown` | `async fn(&self)` | 终止所有进程 |
| `initialize` / `initialized` | `fn(&self) -> Result<...>` | 初始化握手 |
| `exec` / `exec_read` / `exec_write` / `terminate` | `async fn` | 进程操作代理 |
| `fs_*` | `async fn` | 文件系统操作代理（先检查初始化） |

## 依赖关系

- **被引用方**: `server/processor.rs`, `server/registry.rs`
