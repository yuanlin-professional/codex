# `resources.rs` -- 测试模块

## 测试覆盖范围
验证 `RmcpClient` 通过 stdio 传输与 MCP 服务器交互时，资源（Resources）和资源模板（Resource Templates）的列表和读取功能是否正常工作。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `rmcp_client_can_list_and_read_resources` | 启动 stdio 测试服务器，初始化客户端后依次测试 list_resources、list_resource_templates 和 read_resource 操作，验证返回的资源 URI、名称、描述、MIME 类型和文本内容均与预期一致 |
