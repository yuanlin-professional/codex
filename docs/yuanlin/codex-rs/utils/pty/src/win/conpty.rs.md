# `conpty.rs` — Windows ConPTY 系统实现

## 功能概述

基于 WezTerm 开源代码（MIT 许可），实现 Windows ConPTY 伪终端系统。`ConPtySystem` 实现 `portable_pty::PtySystem` trait，提供 `openpty` 方法创建 ConPTY 对。`ConPtyMasterPty` 和 `ConPtySlavePty` 分别实现 `MasterPty` 和 `SlavePty` trait。`RawConPty` 提供对底层 ConPTY 句柄的原始访问。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ConPtySystem` | struct | ConPTY 系统，实现 `PtySystem` trait |
| `RawConPty` | struct | 原始 ConPTY 句柄封装 |
| `ConPtyMasterPty` | struct | ConPTY master 端 |
| `ConPtySlavePty` | struct | ConPTY slave 端 |
| `Inner` | struct(private) | 共享内部状态：con、readable、writable、size |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `openpty` | `fn(&self, size) -> Result<PtyPair>` | 创建 ConPTY 对 |
| `RawConPty::new` | `fn(cols, rows) -> Result<Self>` | 创建原始 ConPTY |
| `resize` | `fn(&self, size) -> Result<()>` | 调整 ConPTY 大小 |

## 依赖关系

- **引用的 crate**: `portable_pty`, `filedescriptor`, `winapi`
- **被引用方**: `pty/src/pty.rs`（Windows 平台）
