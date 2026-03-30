# `psuedocon.rs` — Windows PseudoConsole 封装

## 功能概述

基于 WezTerm 源码（MIT 许可），封装 Windows PseudoConsole API（`CreatePseudoConsole`、`ResizePseudoConsole`、`ClosePseudoConsole`）。`PsuedoCon` 管理控制台句柄的生命周期，提供 `new`、`resize` 和 `spawn_command` 方法。通过 `shared_library!` 宏动态加载 `kernel32.dll`（或 `conpty.dll`），支持 Windows 10 1809（build 17763）及以上版本。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PsuedoCon` | struct | PseudoConsole 句柄封装 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `PsuedoCon::new` | `fn(size, input, output) -> Result<Self>` | 创建 PseudoConsole |
| `PsuedoCon::resize` | `fn(&self, size) -> Result<()>` | 调整大小 |
| `PsuedoCon::spawn_command` | `fn(&self, cmd) -> Result<WinChild>` | 在控制台中生成命令 |
| `conpty_supported` | `fn() -> bool` | 检查 Windows 版本是否支持 ConPTY |

## 依赖关系

- **引用的 crate**: `winapi`, `filedescriptor`, `portable_pty`, `shared_library`, `lazy_static`
- **被引用方**: `conpty.rs`

## 实现备注

- `spawn_command` 使用 `CreateProcessW` 配合 `EXTENDED_STARTUPINFO_PRESENT` 和 `CREATE_UNICODE_ENVIRONMENT`。
- 命令行参数按 Windows 规范进行引号转义。
- `search_path` 查找 `PATH` 和 `PATHEXT` 中的可执行文件。
