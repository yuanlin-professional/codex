# `pending_thread_approvals.rs` -- 待处理线程审批提示

## 功能概述
该文件实现了待处理线程审批提示组件（PendingThreadApprovals），在编辑器上方显示有未处理审批请求的非活跃线程列表。最多显示 3 个线程名称，超出部分以省略号表示，底部提供 `/agent` 命令提示让用户切换线程。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `PendingThreadApprovals` | 结构体 | 持有需要审批的线程名称列表 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new() -> Self` | 创建空实例 |
| `set_threads` | `fn set_threads(&mut self, threads) -> bool` | 更新线程列表，返回是否有变化 |
| `is_empty` | `fn is_empty(&self) -> bool` | 检查是否为空 |

## 依赖关系
- **引用的 crate**: `ratatui`
- **被引用方**: `BottomPane` 持有 `PendingThreadApprovals` 实例

## 实现备注
- 每个线程名前显示红色感叹号 `!` 标识
- 超过 3 个线程时显示 `...` 省略
- 底部显示 `/agent` 提示切换线程
