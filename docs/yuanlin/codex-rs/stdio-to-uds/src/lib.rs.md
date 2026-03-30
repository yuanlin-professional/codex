# `lib.rs` — stdio 到 Unix Domain Socket 桥接

## 功能概述

连接到指定路径的 Unix Domain Socket，在标准输入/输出和 socket 之间双向转发数据。使用独立线程处理 socket→stdout 方向的数据拷贝，主线程处理 stdin→socket 方向。stdin EOF 后执行半关闭（shutdown write）并等待 stdout 线程完成。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run` | `(socket_path) -> Result<()>` | 执行 stdio↔UDS 桥接 |

## 依赖关系

- **引用的 crate**: `anyhow`
- **被引用方**: `stdio-to-uds/src/main.rs`
