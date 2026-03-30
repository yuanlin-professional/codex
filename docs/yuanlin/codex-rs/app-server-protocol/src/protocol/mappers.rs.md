# `mappers.rs` — 协议版本间类型映射

`mappers.rs` 实现了 v1 遗留类型到 v2 新类型之间的转换。目前仅包含 `From<v1::ExecOneOffCommandParams> for v2::CommandExecParams` 的实现，将旧版一次性命令执行参数映射到新版命令执行参数，设置适当的默认值（如 `tty: false`、`stream_stdin: false`）并转换超时和沙箱策略字段。
