# `compact_resume_fork.rs` — 测试模块

## 测试覆盖范围

测试上下文压缩(Compact)、恢复(Resume)和分叉(Fork)的组合流程，验证模型可见的对话历史在各操作步骤中的正确性，包括压缩后恢复和分叉保持历史前缀一致、二次压缩后恢复的历史保持、以及回滚(Rollback)穿越压缩点后的历史重放和上下文更新裁剪。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `compact_resume_and_fork_preserve_model_history_view` | 压缩初始对话后恢复、再分叉一轮，验证各请求中模型可见历史的一致性和正确前缀 |
| `compact_resume_after_second_compaction_preserves_history` | 二次压缩后恢复，验证压缩历史被正确保留且恢复后的输入为压缩输入的超集 |
| `snapshot_rollback_past_compaction_replays_append_only_history` | 回滚穿越压缩点后，验证仅追加历史的正确重放，保留压缩摘要和原始轮次 |
| `snapshot_rollback_followup_turn_trims_context_updates` | 回滚一轮后验证预轮上下文 diff（如 cwd 变更和协作指令）被正确裁剪，不重复出现 |
