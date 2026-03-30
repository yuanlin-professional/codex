# `scenarios.rs` -- 测试模块

## 测试覆盖范围

基于 fixture 目录的场景化集成测试。遍历 `tests/fixtures/scenarios/` 下的每个场景目录，将 input 文件复制到临时目录，执行 apply-patch 命令，然后对比最终文件系统状态与 expected 目录的快照是否完全一致。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_apply_patch_scenarios` | 遍历所有场景目录，对每个场景执行 input -> apply patch -> compare with expected 的完整流程 |
| `run_apply_patch_scenario` | 辅助函数：复制 input、读取 patch.txt、运行 apply_patch 二进制、对比文件系统快照 |
| `snapshot_dir` | 辅助函数：递归构建目录的 BTreeMap 快照（文件内容或目录标记） |
| `copy_dir_recursive` | 辅助函数：递归复制目录，兼容 Buck2 下的 symlink 文件 |
