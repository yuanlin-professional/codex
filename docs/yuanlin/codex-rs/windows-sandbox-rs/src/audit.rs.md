# `audit.rs` -- Windows 沙箱世界可写目录审计

## 功能概述

本模块实现了对文件系统中"Everyone 可写"目录的安全审计。它扫描工作目录、临时目录、用户配置目录和 PATH 条目等关键位置，检测 ACL 中是否存在允许 Everyone 用户写入的权限条目。检测到此类目录后，可自动应用 Capability SID 拒绝 ACE 来阻止沙箱进程的写入访问。扫描受时间限制（2秒）和条目数量限制（50000）控制。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `audit_everyone_writable` | `pub fn audit_everyone_writable(cwd, env, logs_base_dir) -> Result<Vec<PathBuf>>` | 扫描并返回所有 Everyone 可写的目录列表 |
| `apply_world_writable_scan_and_denies` | `pub fn apply_world_writable_scan_and_denies(codex_home, cwd, env_map, policy, logs_base_dir) -> Result<()>` | 扫描并自动应用拒绝 ACE |
| `apply_capability_denies_for_world_writable` | `pub fn apply_capability_denies_for_world_writable(codex_home, flagged, policy, cwd, logs_base_dir) -> Result<()>` | 对标记的目录应用 Capability SID 拒绝写入 ACE |
| `gather_candidates` | `fn gather_candidates(cwd, env) -> Vec<PathBuf>` | 收集审计候选目录（CWD、TEMP、用户目录、PATH 条目、系统根目录） |

## 依赖关系

- **引用的 crate**: `windows_sys`, `anyhow`, 内部 `acl`/`cap`/`token` 模块
- **被引用方**: `lib.rs`（导出为 `apply_world_writable_scan_and_denies`）

## 实现备注

- 扫描策略：先快速扫描 CWD 直接子目录，再广域扫描候选列表及其一级子目录
- 跳过 `Windows/Installer`、`Windows/Registration`、`ProgramData` 等已知噪声目录
- 跳过符号链接以避免审计链接 ACL 而非目标 ACL
- workspace-write 策略的可写根目录被排除在拒绝 ACE 之外
