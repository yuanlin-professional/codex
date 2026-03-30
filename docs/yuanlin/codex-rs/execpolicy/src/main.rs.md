# `main.rs` — execpolicy CLI 入口

`main.rs` 是 `codex-execpolicy` 命令行工具的入口。使用 clap 定义了 `Cli` 枚举，目前包含唯一子命令 `Check`（委托给 `ExecPolicyCheckCommand::run`）。该工具用于从命令行评估命令是否符合给定的执行策略文件。
