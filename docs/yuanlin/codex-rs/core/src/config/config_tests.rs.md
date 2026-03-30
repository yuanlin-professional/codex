# `config_tests.rs` -- 测试模块

## 测试覆盖范围
覆盖 `config/mod.rs` 中 `Config` 结构体的加载、合并与校验逻辑，包括配置文件解析、MCP 服务器配置、沙箱策略、权限配置、功能特性（features）、配置编辑持久化、Agent 角色加载、模型提供者、以及各种配置覆盖场景。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `load_config_normalizes_relative_cwd_override` | 验证相对路径 cwd 被正确规范化为绝对路径 |
| `stdio_mcp` / `http_mcp` | 辅助函数，构造 stdio 和 HTTP MCP 服务器配置 |
| 其他测试（6000+ 行） | 广泛覆盖模型设置、sandbox 策略、MCP 服务器合并、profile 选择、功能特性约束、Agent 角色加载、权限编译、通知配置、历史记录配置、OTEL 配置、memories 配置、shell 环境策略、项目信任级别、配置编辑持久化等场景 |
