# `stopwatch.rs` — 可暂停计时器

## 功能概述
实现一个支持暂停/恢复的异步秒表组件。通过引用计数的暂停机制确保嵌套暂停正确处理。到达时限后触发 CancellationToken。支持无限制模式（永不超时）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Stopwatch` | 结构体 | 可暂停秒表：limit、累计时间、暂停计数 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(limit: Duration) -> Self` | 创建带时限的秒表 |
| `unlimited` | `fn() -> Self` | 创建无限制秒表 |
| `cancellation_token` | `fn(&self) -> CancellationToken` | 获取超时取消令牌 |
| `pause_for` | `async fn(&self, fut) -> T` | 在 future 执行期间暂停计时 |

## 依赖关系
- **引用的 crate**: `tokio`, `tokio_util`
- **被引用方**: 命令超时管理
