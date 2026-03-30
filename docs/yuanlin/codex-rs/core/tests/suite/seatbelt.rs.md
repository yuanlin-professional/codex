# `seatbelt.rs` -- 测试模块

## 测试覆盖范围
测试 macOS Seatbelt 沙箱机制，验证在不同 `SandboxPolicy` 配置下文件系统写入权限的正确性。包括仓库外文件、仓库根目录文件和 `.git` 目录文件在各策略下的读写权限验证。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `locked_down_seatbelt` | SandboxPolicy::LockedDown 下验证仓库外、仓库内和 .git 目录的写权限控制 |
| `read_only_seatbelt` | SandboxPolicy::ReadOnly 下验证所有位置均不可写 |
| `write_cwd_seatbelt` | SandboxPolicy::WriteCwd 下验证仓库内可写、仓库外不可写 |
| `additional_writable_roots` | 验证额外可写根目录配置的正确性 |
| `multiple_scenarios` | 验证多种场景组合下的沙箱文件系统权限 |
