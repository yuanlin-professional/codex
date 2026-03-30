# `registry_tests.rs` — 测试模块

## 测试覆盖范围
验证工具注册表的命名空间查找逻辑。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `handler_looks_up_namespaced_aliases_explicitly` | 验证带命名空间的处理器查找：无命名空间查找返回 plain handler，带命名空间查找返回 namespaced handler，不存在的命名空间返回 None |
