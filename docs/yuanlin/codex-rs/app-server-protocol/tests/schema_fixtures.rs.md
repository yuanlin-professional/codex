# `schema_fixtures.rs` — 测试模块

## 测试覆盖范围

验证 app-server 协议的 Schema fixture 文件（vendored 快照）与当前代码生成的输出一致。确保任何协议变更都会被 CI 检测到，开发者需运行 `just write-app-server-schema` 更新 fixture。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `typescript_schema_fixtures_match_generated` | 验证 vendored TypeScript fixture 文件集合与内存中新生成的 TypeScript 类型定义完全一致（文件列表和内容逐文件比对） |
| `json_schema_fixtures_match_generated` | 验证 vendored JSON Schema fixture 文件与临时目录中新生成的 JSON Schema 完全一致（经过 JSON 规范化后比对） |
