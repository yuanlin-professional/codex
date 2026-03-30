# `experimental_feature_list.rs` — 测试模块

## 测试覆盖范围
测试 `experimentalFeature/list` 和 `experimentalFeature/enablement/set` JSON-RPC 方法，
验证特性元数据返回、特性启用/禁用对全局和线程配置的影响、用户配置不被覆盖、
部分更新行为，以及非白名单特性的拒绝。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `experimental_feature_list_returns_feature_metadata_with_stage` | 返回带有阶段信息的特性元数据 |
| `experimental_feature_enablement_set_applies_to_global_and_thread_config_reads` | 启用设置同时影响全局和线程级配置 |
| `experimental_feature_enablement_set_does_not_override_user_config` | 用户配置中的值不被实验性设置覆盖 |
| `experimental_feature_enablement_set_only_updates_named_features` | 只更新指定的特性，其他保持不变 |
| `experimental_feature_enablement_set_empty_map_is_no_op` | 空映射为无操作 |
| `experimental_feature_enablement_set_rejects_non_allowlisted_feature` | 非白名单特性被拒绝 |
