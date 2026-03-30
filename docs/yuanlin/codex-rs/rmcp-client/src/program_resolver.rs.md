# `program_resolver.rs` -- 跨平台程序路径解析

## 功能概述
为 MCP 服务器执行提供跨平台的可执行程序路径解析。Unix 上直接返回程序名（依赖 shebang 机制），Windows 上使用 `which` crate 解析完整路径（包括 `.cmd`、`.bat` 等脚本扩展名），使 npx/pnpm/yarn 等工具在 Windows 上无需手动指定扩展名即可正常工作。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `resolve` (Unix) | `fn(program, env) -> io::Result<OsString>` | Unix: 直接返回程序名 |
| `resolve` (Windows) | `fn(program, env) -> io::Result<OsString>` | Windows: 使用 which 解析完整可执行路径 |

## 依赖关系
- **引用的 crate**: `which`（仅 Windows）
- **被引用方**: `rmcp_client.rs` 在创建 stdio 传输时调用

## 实现备注
- Windows 解析失败时回退到原始程序名，让 `Command::new()` 处理错误
- 包含完整的跨平台测试套件
