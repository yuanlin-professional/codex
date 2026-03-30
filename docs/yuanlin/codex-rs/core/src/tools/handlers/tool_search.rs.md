# `tool_search.rs` — 工具搜索处理器

## 功能概述
该文件实现了 `tool_search` 工具，允许模型通过关键词在已注册的 MCP 工具列表中进行全文搜索。使用 BM25 算法对工具名称、描述、连接器信息和参数属性名进行相关性排序，返回按命名空间分组的匹配结果。支持结果数量限制（默认 8 条）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ToolSearchHandler` | struct | 包含 `HashMap<String, ToolInfo>` 工具注册表的搜索处理器 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `pub fn new(tools: HashMap<String, ToolInfo>) -> Self` | 使用工具映射表创建处理器 |
| `handle` | `async fn handle(&self, invocation) -> Result<ToolSearchOutput, FunctionCallError>` | 验证查询/限制参数，构建 BM25 搜索引擎，执行搜索并序列化结果 |
| `serialize_tool_search_output_tools` | `fn serialize_tool_search_output_tools(matched_entries) -> Result<Vec<ToolSearchOutputTool>, ...>` | 将匹配结果按命名空间分组，生成 `ResponsesApiNamespace` 结构 |
| `build_search_text` | `fn build_search_text(name, info) -> String` | 拼接工具名、服务器名、标题、描述、连接器信息和参数属性名为可搜索文本 |

## 依赖关系
- **引用的 crate**: `async_trait`, `bm25`（全文搜索引擎）, `codex_tools`, `rmcp`, `serde_json`
- **内部依赖**: `mcp_connection_manager::ToolInfo`, `tools::context`, `tools::registry`
- **被引用方**: 工具注册表中注册为 `tool_search` 工具

## 实现备注
- 使用 BM25（英语语言模型）进行文本相关性排序，适合短文本匹配
- 搜索文本包含：工具全名、工具短名、服务器名、标题、描述、连接器名称/描述、参数属性名
- 结果按命名空间分组，每个命名空间使用 `connector_description` 或基于 `connector_name` 生成的描述
- 空查询和零限制参数会返回错误
- `DEFAULT_LIMIT` 常量设为 8
