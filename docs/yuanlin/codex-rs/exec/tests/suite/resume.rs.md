# `resume.rs` — 测试模块

## 测试覆盖范围

全面测试 codex-exec 的 resume 子命令，覆盖按 ID 恢复、按 --last 恢复、cwd 过滤、--all 标志、全局标志传递、配置覆盖保留、图片附件等场景。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `exec_resume_last_appends_to_existing_file` | resume --last 追加到已有会话文件 |
| `exec_resume_last_accepts_prompt_after_flag_in_json_mode` | resume --last --json 后跟 prompt 正确解析 |
| `exec_resume_last_respects_cwd_filter_and_all_flag` | resume --last 尊重 cwd 过滤；--all 忽略 cwd 选择最新会话 |
| `exec_resume_accepts_global_flags_after_subcommand` | resume 子命令后接受全局标志（--json、--model 等） |
| `exec_resume_by_id_appends_to_existing_file` | 按 session ID 恢复并追加到同一文件 |
| `exec_resume_preserves_cli_configuration_overrides` | resume 保留 --sandbox、--model 等 CLI 配置覆盖 |
| `exec_resume_accepts_images_after_subcommand` | resume 子命令支持 --image 附件 |
