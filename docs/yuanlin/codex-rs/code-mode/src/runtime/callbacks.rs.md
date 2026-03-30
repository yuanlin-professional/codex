# `callbacks.rs` — V8 运行时回调函数集合

## 功能概述
该文件定义了 code-mode V8 运行时中所有可用的 JavaScript 全局回调函数。这些回调实现了 JS 代码与 Rust 运行时之间的桥接，包括工具调用、文本/图片输出、键值存储、通知发送、控制权让出和退出等功能。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `tool_callback` | `(scope, args, retval)` | 发起工具调用并返回 Promise |
| `text_callback` | `(scope, args, retval)` | 输出文本内容项 |
| `image_callback` | `(scope, args, retval)` | 输出图片内容项 |
| `store_callback` | `(scope, args, retval)` | 持久化存储键值对 |
| `load_callback` | `(scope, args, retval)` | 加载已存储的值 |
| `notify_callback` | `(scope, args, retval)` | 发送通知消息 |
| `yield_control_callback` | `(scope, args, retval)` | 请求让出执行控制权 |
| `exit_callback` | `(scope, args, retval)` | 请求退出运行时 |

## 依赖关系
- **引用的 crate**: `v8`, `serde_json`
- **被引用方**: V8 运行时初始化时注册到全局对象

## 实现备注
- `tool_callback` 为每次工具调用生成递增的唯一 ID
- `exit_callback` 通过设置 `exit_requested` 标志并抛出哨兵异常来实现退出
- 所有输出回调（text/image/notify）返回 `undefined`
