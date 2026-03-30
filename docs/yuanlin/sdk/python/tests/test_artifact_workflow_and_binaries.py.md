# `test_artifact_workflow_and_binaries.py` -- 测试模块

## 测试覆盖范围

验证 SDK 构建产物工作流的正确性，包括代码生成脚本结构、schema 规范化、运行时包构建与暂存、SDK 包构建与暂存、codex 二进制解析逻辑等。测试通过动态加载 `update_sdk_artifacts.py` 和 `_runtime_setup.py` 模块来验证其行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_generation_has_single_maintenance_entrypoint_script` | 验证 scripts 目录只有唯一入口脚本 |
| `test_generate_types_wires_all_generation_steps` | 验证 generate_types() 按正确顺序调用三个生成步骤 |
| `test_schema_normalization_only_flattens_string_literal_oneofs` | 验证 schema 展平只作用于纯字符串字面量的 oneOf |
| `test_python_codegen_schema_annotation_adds_stable_variant_titles` | 验证 schema 标注为联合分支添加了稳定的标题名 |
| `test_generate_v2_all_uses_titles_for_generated_names` | 验证代码生成命令包含正确的标志参数 |
| `test_runtime_package_template_has_no_checked_in_binaries` | 验证运行时包模板不含预提交的二进制文件 |
| `test_examples_readme_matches_pinned_runtime_version` | 验证示例 README 中的版本号与锁定版本一致 |
| `test_release_metadata_retries_without_invalid_auth` | 验证 GitHub API 401 时自动去除认证重试 |
| `test_runtime_package_is_wheel_only_and_builds_platform_specific_wheels` | 验证运行时包配置为仅 wheel 且平台特定 |
| `test_stage_runtime_release_copies_binary_and_sets_version` | 验证运行时暂存正确复制二进制并设置版本 |
| `test_stage_runtime_release_replaces_existing_staging_dir` | 验证运行时暂存会替换已有暂存目录 |
| `test_stage_sdk_release_injects_exact_runtime_pin` | 验证 SDK 暂存注入精确的运行时版本锁定 |
| `test_stage_sdk_release_replaces_existing_staging_dir` | 验证 SDK 暂存会替换已有暂存目录 |
| `test_stage_sdk_runs_type_generation_before_staging` | 验证 stage-sdk 命令先运行类型生成再暂存 |
| `test_stage_runtime_stages_binary_without_type_generation` | 验证 stage-runtime 命令不运行类型生成 |
| `test_default_runtime_is_resolved_from_installed_runtime_package` | 验证默认 codex 路径从已安装运行时包解析 |
| `test_explicit_codex_bin_override_takes_priority` | 验证显式 codex_bin 配置优先于运行时包 |
| `test_missing_runtime_package_requires_explicit_codex_bin` | 验证运行时包缺失时抛出正确错误 |
| `test_broken_runtime_package_does_not_fall_back` | 验证运行时包异常时不会静默回退 |
