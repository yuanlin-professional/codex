# `test_contract_generation.py` -- 测试模块

## 测试覆盖范围

验证自动生成的代码文件是否与当前 schema 保持同步。通过在测试中重新运行 `generate-types` 命令，对比前后文件内容是否一致。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_generated_files_are_up_to_date` | 重新运行代码生成后验证 notification_registry.py、v2_all.py 和 api.py 未发生变化 |
