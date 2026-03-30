# `allow.rs` — 沙盒允许/拒绝路径计算

## 功能概述
该文件根据沙盒策略计算文件系统的允许写入和拒绝写入路径集合。对于 `WorkspaceWrite` 策略，会将工作目录和额外可写根目录加入允许列表，同时将 `.git`、`.codex`、`.agents` 等受保护子目录加入拒绝列表。还支持通过环境变量 TEMP/TMP 添加临时目录。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `AllowDenyPaths` | struct | 包含 allow 和 deny 两个 PathBuf 集合 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `compute_allow_paths` | `(policy, policy_cwd, command_cwd, env_map) -> AllowDenyPaths` | 计算允许/拒绝路径集合 |

## 依赖关系
- **引用的 crate**: `dunce`, `codex_protocol`
- **被引用方**: `setup_orchestrator`, `lib.rs`, `elevated_impl`

## 实现备注
- 使用 `dunce::canonicalize` 规范化路径
- 受保护子目录仅在实际存在时才加入拒绝列表
