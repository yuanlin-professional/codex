# `setup_main_win.rs` — Windows 沙盒提权安装主程序

## 功能概述
该文件是 Windows 沙盒安装辅助程序的核心实现。它以提权（elevated）方式运行，负责创建和管理沙盒用户账户、配置文件系统 ACL 权限、设置防火墙规则、锁定沙盒目录以及保护工作区敏感目录。支持完整安装和仅读取 ACL 刷新两种模式。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Payload` | struct | 安装请求载荷（用户名、路径、代理端口等） |
| `SetupMode` | enum | 安装模式：Full（完整）或 ReadAclsOnly（仅读ACL） |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `main` | `() -> Result<()>` | 程序入口，解码 base64 载荷并执行安装 |
| `run_setup_full` | `(payload, log, sbx_dir) -> Result<()>` | 完整安装流程 |
| `run_read_acl_only` | `(payload, log) -> Result<()>` | 仅刷新读取 ACL |
| `lock_sandbox_dir` | `(dir, real_user, ...) -> Result<()>` | 锁定沙盒目录权限 |
| `apply_read_acls` | `(read_roots, subjects, ...) -> Result<()>` | 应用读取 ACL |

## 依赖关系
- **引用的 crate**: `windows_sys`, `codex_windows_sandbox`, `chrono`, `base64`
- **被引用方**: 作为 `codex-windows-sandbox-setup.exe` 的入口

## 实现备注
- 通过 base64 编码的 JSON 载荷接收安装参数
- 写入 ACL 操作使用多线程并行执行
- 安装失败时写入 `setup_error.json` 报告
