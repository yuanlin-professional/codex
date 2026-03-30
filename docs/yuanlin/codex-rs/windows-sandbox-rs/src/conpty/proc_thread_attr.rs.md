# `proc_thread_attr.rs` — Windows 进程线程属性列表 RAII 封装

## 功能概述
该文件封装了 Win32 `PROC_THREAD_ATTRIBUTE_LIST` API，提供 RAII 风格的属性列表管理。主要用于将 ConPTY 句柄附加到子进程，使沙盒进程能够使用伪终端（PTY）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ProcThreadAttributeList` | struct | PROC_THREAD_ATTRIBUTE_LIST 的 RAII 封装 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `(attr_count: u32) -> io::Result<Self>` | 创建并初始化属性列表 |
| `set_pseudoconsole` | `(&mut self, hpc: isize) -> io::Result<()>` | 附加 ConPTY 句柄 |

## 依赖关系
- **引用的 crate**: `windows_sys`
- **被引用方**: `conpty/mod.rs`
