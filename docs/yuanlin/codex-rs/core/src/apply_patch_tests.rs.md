# `apply_patch_tests.rs` — apply_patch 协议转换测试

测试 `convert_apply_patch_to_protocol` 函数将 `ApplyPatchAction` 的 `Add` 变体正确转换为协议层的 `FileChange::Add` 结构，确保文件路径和内容映射无误。
