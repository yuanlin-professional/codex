# `ipc_framed.rs` -- 父进程与命令运行器之间的帧式 IPC 协议

## 功能概述

该文件定义了 CLI 父进程与提升权限命令运行器之间的 IPC 通信协议。协议使用长度前缀的 JSON 编码帧，消息类型包括 SpawnRequest（父->子）、SpawnReady（子->父确认）、Output（输出数据）、Stdin（标准输入传递）、Exit（退出状态）、Error（错误信息）和 Terminate（终止信号）。该模块仅用于提升路径（elevated path），传统受限令牌路径不使用此协议。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `FramedMessage` | struct | 长度前缀的 JSON 帧，包含版本号和消息体 |
| `Message` | enum | IPC 消息变体：SpawnRequest, SpawnReady, Output, Stdin, Exit, Error, Terminate |
| `SpawnRequest` | struct | 从父进程发送的启动参数，包含命令、CWD、环境变量、策略、capability SID 等 |
| `SpawnReady` | struct | 运行器确认子进程已启动，包含 process_id |
| `OutputPayload` | struct | 输出数据载荷，包含 base64 编码数据和流标识 |
| `OutputStream` | enum | 输出流标识：Stdout 或 Stderr |
| `ExitPayload` | struct | 退出状态载荷，包含 exit_code 和 timed_out |
| `ErrorPayload` | struct | 错误载荷，包含 message 和 code |
| `StdinPayload` | struct | stdin 数据载荷（base64 编码） |
| `EmptyPayload` | struct | 空载荷，用于控制消息（如 Terminate） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `write_frame` | `fn<W: Write>(writer: W, msg: &FramedMessage) -> Result<()>` | 写入长度前缀的 JSON 帧 |
| `read_frame` | `fn<R: Read>(reader: R) -> Result<Option<FramedMessage>>` | 读取长度前缀帧，EOF 返回 None |
| `encode_bytes` | `fn(data: &[u8]) -> String` | 将字节数据 base64 编码 |
| `decode_bytes` | `fn(data: &str) -> Result<Vec<u8>>` | 将 base64 字符串解码为字节 |

## 依赖关系

- **引用的 crate**: `serde`, `serde_json`, `base64`, `anyhow`
- **被引用方**: `elevated_impl.rs`, `command_runner_win.rs`

## 实现备注

- 帧最大载荷限制为 8MB（`MAX_FRAME_LEN`），超出即报错
- 使用 `serde(tag = "type", rename_all = "snake_case")` 进行消息类型的 JSON 标签序列化
- 包含往返测试验证帧编解码的正确性
