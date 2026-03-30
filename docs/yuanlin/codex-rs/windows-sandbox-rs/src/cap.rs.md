# `cap.rs` — 能力 SID 管理与持久化

## 功能概述
该文件管理 Windows 沙盒使用的能力 SID（Capability SID），支持全局和按工作区分配的 SID。SID 以 JSON 格式持久化到 `cap_sid` 文件中，首次使用时自动生成随机 SID。按工作区的 SID 用于隔离不同工作空间的沙盒写入权限。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `CapSids` | struct | 存储全局和按工作区的能力 SID |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load_or_create_cap_sids` | `(codex_home) -> Result<CapSids>` | 加载或创建能力 SID |
| `workspace_cap_sid_for_cwd` | `(codex_home, cwd) -> Result<String>` | 获取工作区特定的能力 SID |

## 依赖关系
- **引用的 crate**: `rand`, `serde`, `serde_json`
- **被引用方**: `setup_orchestrator`, `lib.rs`, `audit.rs`
