# stdio-to-uds

## 功能概述

`codex-stdio-to-uds` 是 Codex 项目的标准 I/O 到 Unix Domain Socket (UDS) 桥接模块。它将进程的标准输入/输出（stdin/stdout）与指定的 Unix Domain Socket 进行双向数据转发，充当一个透明的 I/O 中继器。这在 Codex 架构中用于将基于 stdio 的协议（如 JSON-RPC）转换为基于 UDS 的通信。

## 架构说明

该模块同时提供库（lib.rs）和独立可执行文件（main.rs）两种形式。

### 数据流模型

```
stdin  ──io::copy──>  UnixStream (写入端)
                            |
stdout <──io::copy──  UnixStream (读取端)
```

### 实现细节

1. 连接到指定路径的 Unix Domain Socket
2. 克隆 Socket 获得独立的读写句柄
3. 启动独立线程将 Socket 数据复制到 stdout
4. 主线程将 stdin 数据复制到 Socket
5. stdin EOF 后，半关闭 Socket 的写入端（`Shutdown::Write`）
6. 等待 stdout 线程完成（Socket 读取端 EOF）

### 跨平台支持

- **Unix** - 使用标准库 `std::os::unix::net::UnixStream`
- **Windows** - 使用 `uds_windows` crate 提供的 `UnixStream` 兼容实现

### 优雅关闭

当对端在发送响应后立即关闭连接时，半关闭写入端可能报告 `NotConnected` 错误，模块对此进行了特殊处理以避免误报。

## 目录结构

```
stdio-to-uds/
  Cargo.toml
  src/
    lib.rs    # 核心实现：run() 函数
    main.rs   # 独立可执行文件入口
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `anyhow` | 错误处理 |
| `uds_windows`（仅 Windows） | Windows 平台 Unix Domain Socket 支持 |

该模块依赖极为精简，仅需要 `anyhow` 和平台条件依赖。

## 核心接口/API

### 函数

```rust
/// 连接到 Unix Domain Socket 并在 stdin/stdout 与 Socket 之间双向转发数据
/// 阻塞运行直到 stdin EOF 且 Socket 读取完成
pub fn run(socket_path: &Path) -> anyhow::Result<()>;
```

### 可执行文件

```bash
codex-stdio-to-uds <socket-path>
```

接受一个参数：Unix Domain Socket 的路径，启动后在 stdio 和该 Socket 之间进行双向转发，直到任一端关闭。
