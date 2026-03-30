# `policy_transforms.rs` — 沙箱策略转换与权限合并

## 功能概述

负责将基础沙箱策略与额外权限配置合并，生成有效的运行时策略。核心功能包括：(1) 有效沙箱策略计算（合并额外的读写根目录和网络权限）；(2) 额外权限的标准化、合并和交集操作；(3) 文件系统沙箱策略合并（向受限策略添加额外的读写条目）；(4) 判断是否需要启用平台沙箱。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `EffectiveSandboxPermissions` | 结构体 | 有效沙箱策略容器，包含合并后的 `sandbox_policy` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `EffectiveSandboxPermissions::new` | `(sandbox_policy, additional_permissions) -> Self` | 计算合并后的有效沙箱策略 |
| `normalize_additional_permissions` | `(permissions) -> Result<PermissionProfile>` | 标准化额外权限：规范化路径、去重、移除空节 |
| `merge_permission_profiles` | `(base, permissions) -> Option<PermissionProfile>` | 合并两个权限配置（并集） |
| `intersect_permission_profiles` | `(requested, granted) -> PermissionProfile` | 计算请求权限与已授予权限的交集 |
| `effective_file_system_sandbox_policy` | `(file_system_policy, additional_permissions) -> FileSystemSandboxPolicy` | 合并额外权限到文件系统沙箱策略 |
| `effective_network_sandbox_policy` | `(network_policy, additional_permissions) -> NetworkSandboxPolicy` | 合并额外权限到网络沙箱策略 |
| `should_require_platform_sandbox` | `(file_system_policy, network_policy, has_managed_network) -> bool` | 判断是否需要启用平台沙箱 |

## 依赖关系

- **引用的 crate**: `codex_protocol`、`codex_utils_absolute_path`、`dunce`
- **被引用方**: `manager.rs`

## 实现备注

- 路径标准化使用 `dunce::canonicalize` 解析符号链接并去除 Windows UNC 前缀。
- 权限合并遵循"最宽松"语义：任一配置允许网络则最终允许网络。
- 权限交集遵循"最严格"语义：仅在请求和授予双方都允许时才保留。
- `should_require_platform_sandbox` 在以下情况返回 true：有托管网络要求、网络受限且非外部沙箱、文件系统受限且无全盘写权限。
- ReadOnly 策略在有额外写入路径时会升级为 WorkspaceWrite 策略（标注为临时方案）。
