# `utility_tool.rs` -- 实用工具定义

## 功能概述
定义两个实用工具：`list_dir`（列出目录内容，支持分页 offset/limit、递归深度 depth 和文件类型过滤 file_type）和 `test_sync`（用于集成测试同步的内部工具，接受 test_id 参数）。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_list_dir_tool` | `fn() -> ToolSpec` | 创建目录列表工具 |
| `create_test_sync_tool` | `fn() -> ToolSpec` | 创建测试同步工具（内部使用） |

## 依赖关系
- **被引用方**: `lib.rs` 公开导出
