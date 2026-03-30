# `test_stdio_server.rs` -- Stdio 传输测试 MCP 服务器

## 功能概述
基于 rmcp SDK 实现的 stdio 传输 MCP 测试服务器，提供 echo、echo-tool（包含非法 JS 标识符的工具名）、image 和 image_scenario 四个工具，以及资源列表/读取和资源模板功能。主要用于集成测试，支持多种图像渲染场景的手动测试。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `TestToolServer` | struct | 包含工具、资源和资源模板的 MCP 服务器处理器 |
| `ImageScenario` | enum | 图像测试场景枚举：纯图像、文本+图像、无效 base64+图像等 7 种 |
| `ImageScenarioArgs` | struct | image_scenario 工具的参数结构体 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `main` | `async fn() -> Result<()>` | 以 stdio 传输模式启动 MCP 服务器 |
| `parse_data_url` | `fn(url) -> Option<(String, String)>` | 解析 data URL 提取 MIME 类型和 base64 数据 |
| `image_scenario_result` | `fn(args) -> Result<CallToolResult>` | 根据场景参数构建不同的图像内容块组合 |

## 依赖关系
- **引用的 crate**: `rmcp`, `serde_json`, `tokio`
- **被引用方**: `resources.rs` 和其他集成测试

## 实现备注
- 内置一个 1x1 像素的 PNG base64 常量用于默认图像测试
- image 工具从 `MCP_TEST_IMAGE_DATA_URL` 环境变量读取数据 URL
