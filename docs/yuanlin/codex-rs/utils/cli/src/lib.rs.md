# `lib.rs` — CLI 工具模块入口

本文件是 `cli` 子 crate 的入口模块，声明并重新导出以下子模块的公共类型：

- `ApprovalModeCliArg`：审批模式 CLI 参数枚举
- `CliConfigOverrides`：`-c key=value` 配置覆盖结构体
- `SandboxModeCliArg`：沙箱模式 CLI 参数枚举
- `format_env_display`：环境变量格式化模块
