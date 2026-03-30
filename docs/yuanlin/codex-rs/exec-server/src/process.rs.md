# `process.rs` — 进程执行抽象 trait

定义 `ExecBackend` 和 `ExecProcess` trait 以及 `StartedExecProcess` 结构体。`ExecBackend` 负责启动进程，`ExecProcess` 提供进程生命周期操作（read/write/terminate/subscribe_wake），被本地和远程实现共用。
