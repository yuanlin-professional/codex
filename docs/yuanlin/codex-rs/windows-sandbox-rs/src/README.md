# windows-sandbox-rs/src — 源代码目录

## 功能概述
windows-sandbox-rs crate 的源代码实现目录。Windows 平台沙箱实现。

## 目录结构
| 文件 | 说明 |
|------|------|
| `acl.rs` | 访问控制列表 |
| `allow.rs` | 允许规则 |
| `audit.rs` | 审计功能 |
| `cap.rs` | 能力管理 |
| `desktop.rs` | 桌面隔离 |
| `dpapi.rs` | 数据保护 API |
| `elevated_impl.rs` | 提权实现 |
| `env.rs` | 环境变量 |
| `firewall.rs` | 防火墙规则 |
| `helper_materialization.rs` | 辅助程序实例化 |
| `hide_users.rs` | 用户隐藏 |
| `identity.rs` | 身份管理 |
| `lib.rs` | 库入口 |
| `logging.rs` | 日志 |
| `path_normalization.rs` | 路径规范化 |
| `policy.rs` | 沙箱策略 |
| `process.rs` | 进程管理 |
| `read_acl_mutex.rs` | ACL 读取互斥 |
| `sandbox_users.rs` | 沙箱用户 |
| `sandbox_utils.rs` | 沙箱工具 |
| `setup_error.rs` | 配置错误 |
| `setup_main_win.rs` | Windows 主配置 |
| `setup_orchestrator.rs` | 配置编排器 |
| `token.rs` | 令牌管理 |
| `winutil.rs` | Windows 工具 |
| `workspace_acl.rs` | 工作区 ACL |
