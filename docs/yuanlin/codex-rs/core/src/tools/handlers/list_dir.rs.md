# `list_dir.rs` -- 目录列表工具处理器

## 功能概述
该文件实现了 `list_dir` 工具的处理器，提供分页式的目录树浏览功能。它支持可配置的递归深度、偏移量和数量限制，按字母排序输出目录内容，并通过缩进直观展示层级结构。文件类型标识包括目录（`/`）、符号链接（`@`）和其他类型（`?`）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ListDirHandler` | struct | 实现 `ToolHandler` trait，作为 `list_dir` 工具的处理器 |
| `ListDirArgs` | struct (Deserialize) | 工具参数：`dir_path`（绝对路径）、`offset`（1-indexed，默认 1）、`limit`（默认 25）、`depth`（递归深度，默认 2） |
| `DirEntry` | struct | 目录条目信息：排序键 `name`、显示名 `display_name`、层级深度 `depth` 和条目类型 `kind` |
| `DirEntryKind` | enum | 目录条目类型：`Directory`、`File`、`Symlink`、`Other` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ListDirHandler::handle` | `async fn handle(&self, invocation: ToolInvocation) -> Result<FunctionToolOutput, FunctionCallError>` | 验证参数合法性（offset >= 1、limit > 0、depth > 0、绝对路径），调用 `list_dir_slice` 获取分页结果 |
| `list_dir_slice` | `async fn list_dir_slice(path, offset, limit, depth) -> Result<Vec<String>, FunctionCallError>` | 收集所有条目、排序、按 offset/limit 分页截取、格式化输出，超出部分显示截断提示 |
| `collect_entries` | `async fn collect_entries(dir_path, relative_prefix, depth, entries) -> Result<(), FunctionCallError>` | 使用 BFS 队列递归收集目录条目，每层目录先按名称排序再入队 |
| `format_entry_line` | `fn format_entry_line(entry: &DirEntry) -> String` | 将条目格式化为带缩进和类型后缀的字符串 |
| `format_entry_name` | `fn format_entry_name(path: &Path) -> String` | 将路径标准化为正斜杠分隔并截断超长名称 |

## 依赖关系
- **引用的 crate**: `codex_utils_string`（`take_bytes_at_char_boundary` 安全截断）、`tokio::fs`（异步文件系统操作）、`serde`（参数反序列化）、`async_trait`
- **被引用方**: 由工具注册表注册为 `list_dir` 工具的处理器

## 实现备注
- 使用 BFS（广度优先搜索）而非 DFS 遍历目录树，通过 `VecDeque` 队列控制递归深度。
- offset 采用 1-indexed 而非 0-indexed，与用户直觉一致。
- 条目名称最大长度限制为 500 字符（`MAX_ENTRY_LENGTH`），超长时在字符边界安全截断，避免 UTF-8 截断问题。
- 缩进为每层 2 个空格（`INDENTATION_SPACES`），排序键使用标准化的正斜杠路径以确保跨平台一致性。
- 当分页后仍有更多条目时，输出末尾追加 "More than N entries found" 提示。
- 只接受绝对路径输入，拒绝相对路径以避免歧义。
