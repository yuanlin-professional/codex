# `seatbelt_tests.rs` — 测试模块

## 测试覆盖范围

macOS Seatbelt 沙箱策略生成的综合测试，覆盖网络策略、文件读写策略、受保护路径排除、Unix 域套接字以及端到端的 `sandbox-exec` 执行验证。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `base_policy_allows_node_cpu_sysctls` | 基础策略包含 CPU 品牌和硬件模型 sysctl 允许规则 |
| `create_seatbelt_args_routes_network_through_proxy_ports` | 网络策略正确路由到指定代理端口，无全局出站允许 |
| `explicit_unreadable_paths_are_excluded_from_full_disk_read_and_write_access` | 不可读路径从全盘读写访问中正确排除 |
| `explicit_unreadable_paths_are_excluded_from_readable_roots` | 不可读路径从可读根目录中正确排除 |
| `seatbelt_args_without_extension_profile_keep_legacy_preferences_read_access` | 旧版策略保留用户偏好读取权限 |
| `seatbelt_legacy_workspace_write_nested_readable_root_stays_writable` | 旧版工作区写入策略中 cwd 下的可读根不会成为排除项 |
| `create_seatbelt_args_allows_local_binding_when_explicitly_enabled` | 显式启用时允许本地回环绑定和入站流量 |
| `dynamic_network_policy_preserves_restricted_policy_when_proxy_config_without_ports` | 有代理配置但无端口时保留受限网络策略 |
| `dynamic_network_policy_preserves_restricted_policy_for_managed_network_without_proxy_config` | 托管网络无代理端点时保留受限网络策略 |
| `create_seatbelt_args_allowlists_unix_socket_paths` | Unix 域套接字路径白名单正确生成绑定和出站规则 |
| `unix_socket_policy_non_empty_output_is_newline_terminated` | Unix 套接字策略输出以换行符结尾 |
| `unix_socket_dir_params_use_stable_param_names` | Unix 套接字参数名称稳定排序且去重 |
| `normalize_path_for_sandbox_rejects_relative_paths` | 相对路径被拒绝 |
| `create_seatbelt_args_allows_all_unix_sockets_when_enabled` | AllowAll 模式允许所有 Unix 域套接字 |
| `create_seatbelt_args_full_network_with_proxy_is_still_proxy_only` | 完全网络模式有代理时仍限制为代理端口 |
| `create_seatbelt_args_with_read_only_git_and_codex_subpaths` | 端到端验证：.git 和 .codex 目录写入被 Seatbelt 阻止，其他路径允许写入 |
| `create_seatbelt_args_block_first_time_dot_codex_creation_with_exact_and_descendant_carveouts` | .codex 目录首次创建也被阻止（literal + subpath 双重排除） |
| `create_seatbelt_args_with_read_only_git_pointer_file` | .git 指针文件和实际 gitdir 的写入都被阻止 |
| `create_seatbelt_args_for_cwd_as_git_repo` | cwd 为 git 仓库时正确生成策略和参数 |
