# `environment.rs` — 执行环境管理器

## 功能概述

提供 `Environment` 和 `EnvironmentManager`，根据是否配置了 `CODEX_EXEC_SERVER_URL` 环境变量，自动选择本地进程执行（`LocalProcess`）或远程 exec-server 执行（`RemoteProcess`）。`EnvironmentManager` 使用 `OnceCell` 实现懒初始化和缓存。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Environment` | struct | 包含 exec 后端和文件系统抽象的运行环境 |
| `EnvironmentManager` | struct | 缓存式环境管理器，延迟创建 Environment |
| `ExecutorEnvironment` | trait | 获取 exec 后端的 trait 接口 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `Environment::create` | `async fn(Option<String>) -> Result<Self>` | 按 URL 创建本地或远程环境 |
| `get_exec_backend` | `fn(&self) -> Arc<dyn ExecBackend>` | 获取进程执行后端 |
| `get_filesystem` | `fn(&self) -> Arc<dyn ExecutorFileSystem>` | 获取文件系统抽象 |
| `EnvironmentManager::current` | `async fn(&self) -> Result<Arc<Environment>>` | 获取或创建缓存环境 |

## 依赖关系

- **引用的 crate**: `tokio::sync::OnceCell`
- **被引用方**: `lib.rs`（公开导出）

## 实现备注

- 空字符串或纯空白的 URL 会被规范化为 `None`，回退到本地执行。
- 包含单元测试验证缓存行为和默认环境的就绪状态。
