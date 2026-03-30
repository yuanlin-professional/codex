# `project_doc.rs` — 项目级文档发现与加载（AGENTS.md）

## 功能概述

此文件实现了项目级文档的发现与加载机制，主要用于自动查找和读取 `AGENTS.md` 文件。系统会从项目根目录沿路径向下搜索到当前工作目录，按顺序拼接所有找到的文档内容。项目根目录通过向上遍历查找标记文件（默认为 `.git`）来确定。文件还支持 `AGENTS.override.md` 覆盖文件和可配置的候选文件名列表。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `DEFAULT_PROJECT_DOC_FILENAME` | 常量 | 默认扫描的文件名 `"AGENTS.md"` |
| `LOCAL_PROJECT_DOC_FILENAME` | 常量 | 本地覆盖文件名 `"AGENTS.override.md"` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `get_user_instructions` | `async (config) -> Option<String>` | 合并用户指令、项目文档、JS REPL 指令和分层代理消息为完整的指令字符串 |
| `read_project_docs` | `async (config) -> io::Result<Option<String>>` | 读取并拼接所有发现的项目文档，支持字节上限截断 |
| `discover_project_doc_paths` | `(config) -> io::Result<Vec<PathBuf>>` | 发现从项目根到当前目录的所有文档文件路径 |
| `render_js_repl_instructions` | `(config) -> Option<String>` | 当启用 JS REPL 功能时，渲染 JavaScript REPL 使用说明 |
| `candidate_filenames` | `(config) -> Vec<&str>` | 构建候选文件名列表：覆盖文件 > 默认文件 > 回退文件名 |

## 依赖关系

- **引用的 crate**: `crate::config`、`crate::config_loader`、`codex_app_server_protocol`、`codex_features`、`dunce`、`tokio::io`
- **被引用方**: `lib.rs` 公开导出此模块，会话初始化时调用以构建系统指令

## 实现备注

- 搜索优先级为：`AGENTS.override.md` > `AGENTS.md` > 配置的回退文件名，每个目录只取第一个匹配
- 支持符号链接（symlink），但不会跨越项目根目录向上搜索
- `project_doc_max_bytes` 配置控制总读取上限，超过时会截断并发出警告
- 项目根标记列表可通过配置自定义，空列表会禁用父目录遍历
- JS REPL 指令包含大量关于 `js_repl` 工具的使用规范和限制说明
