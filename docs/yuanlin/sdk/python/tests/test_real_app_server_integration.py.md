# `test_real_app_server_integration.py` -- 测试模块

## 测试覆盖范围

端到端集成测试，需要设置 `RUN_REAL_CODEX_TESTS=1` 环境变量才会运行。测试通过实际启动 Codex app-server 进程来验证 SDK 在真实环境中的行为，覆盖初始化、线程创建、turn 执行、流式事件、中断操作、所有示例脚本运行以及 Jupyter notebook 单元格执行。使用 session 级别的 fixture 预装运行时依赖。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_real_initialize_and_model_list` | 验证真实 initialize 握手和模型列表查询 |
| `test_real_thread_and_turn_start_smoke` | 验证真实线程创建和 turn 执行的冒烟测试 |
| `test_real_thread_run_convenience_smoke` | 验证 Thread.run() 便捷方法的冒烟测试 |
| `test_real_async_thread_turn_usage_and_ids_smoke` | 验证异步 turn 执行和 token 使用量的冒烟测试 |
| `test_real_async_thread_run_convenience_smoke` | 验证 AsyncThread.run() 便捷方法的冒烟测试 |
| `test_notebook_bootstrap_resolves_sdk_and_runtime_from_unrelated_cwd` | 验证 notebook 引导在非项目目录下正常工作 |
| `test_notebook_sync_cell_smoke` | 验证 notebook 同步单元格执行 |
| `test_notebook_advanced_cell_smoke` | 验证 notebook 高级单元格（模型选择+参数组合）执行 |
| `test_real_streaming_smoke_turn_completed` | 验证真实流式事件中能收到 turn/completed |
| `test_real_turn_interrupt_smoke` | 验证真实 turn 中断操作 |
| `test_real_examples_run_and_assert`（参数化 x28） | 验证所有 14 个示例目录的 sync/async 脚本能成功运行并输出预期内容 |
