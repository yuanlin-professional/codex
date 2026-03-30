# `plugin_read.rs` — 测试模块

## 测试覆盖范围
测试 `plugin/read` JSON-RPC 方法，验证插件详情读取（含 bundle 内容）、需要认证的应用返回、
旧版字符串提示格式支持、缺失插件和缺失 manifest 文件的错误处理。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `plugin_read_returns_plugin_details_with_bundle_contents` | 返回插件详情和 bundle 内容 |
| `plugin_read_returns_app_needs_auth` | 返回需要认证的应用信息 |
| `plugin_read_accepts_legacy_string_default_prompt` | 接受旧版字符串格式的默认提示 |
| `plugin_read_returns_invalid_request_when_plugin_is_missing` | 缺失插件返回错误 |
| `plugin_read_returns_invalid_request_when_plugin_manifest_is_missing` | 缺失 manifest 返回错误 |
