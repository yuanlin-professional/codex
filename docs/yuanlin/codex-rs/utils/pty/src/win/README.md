# pty/src/win — Windows PTY 实现

## 功能概述
Windows 平台的伪终端实现，使用 ConPTY API。

## 目录结构
| 文件 | 说明 |
|------|------|
| `conpty.rs` | ConPTY 封装 |
| `mod.rs` | 模块入口 |
| `procthreadattr.rs` | 进程线程属性 |
| `psuedocon.rs` | 伪控制台 |
