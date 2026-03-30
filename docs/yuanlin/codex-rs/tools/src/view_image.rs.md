# `view_image.rs` -- 图像查看工具定义

## 功能概述
定义 `view_image` 工具的 ToolSpec，允许 LLM 从本地文件系统加载图像。支持可选的 `detail` 参数控制图像分辨率（"original" 保留原始分辨率）。输出 schema 包含 `image_url`（data URL）和 `detail`（分辨率提示）字段。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ViewImageToolOptions` | struct | 工具选项，控制是否允许请求 original 分辨率 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_view_image_tool` | `fn(options) -> ToolSpec` | 创建 view_image 工具定义 |

## 依赖关系
- **引用的 crate**: `codex_protocol`
- **被引用方**: `lib.rs` 公开导出
