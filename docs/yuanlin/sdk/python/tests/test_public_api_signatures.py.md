# `test_public_api_signatures.py` -- 测试模块

## 测试覆盖范围

验证公共 API 的方法签名契约，确保生成的方法参数名称使用 snake_case、参数列表完整正确、不包含 `Any` 类型注解，以及生命周期方法归属到正确的类上。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_root_exports_app_server_config` | 验证顶层包导出 AppServerConfig |
| `test_root_exports_run_result` | 验证顶层包导出 RunResult |
| `test_package_includes_py_typed_marker` | 验证包包含 py.typed 标记文件（PEP 561 类型支持） |
| `test_generated_public_signatures_are_snake_case_and_typed` | 验证 Codex/AsyncCodex/Thread/AsyncThread 12 个方法的关键字参数列表精确匹配 |
| `test_lifecycle_methods_are_codex_scoped` | 验证 resume/fork/archive/unarchive 方法在 Codex 上而非 Thread 上 |
| `test_initialize_metadata_parses_user_agent_shape` | 验证 user-agent 字符串正确解析为 serverInfo |
| `test_initialize_metadata_requires_non_empty_information` | 验证空 initialize 响应抛出正确错误 |
