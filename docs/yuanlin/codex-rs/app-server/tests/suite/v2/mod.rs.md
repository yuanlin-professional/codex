# `mod.rs` — v2 测试套件模块聚合

## 功能概述
聚合 v2 协议版本下的所有测试模块（约 48 个），涵盖 account、thread、turn、plugin、
config、fs、command_exec、connection_handling 等功能域。部分模块（command_exec、
connection_handling_websocket_unix）仅在 Unix 平台编译。

## 模块列表
account, analytics, app_list, collaboration_mode_list, command_exec (unix), compaction,
config_rpc, connection_handling_websocket, connection_handling_websocket_unix (unix),
dynamic_tools, experimental_api, experimental_feature_list, fs, initialize,
mcp_server_elicitation, model_list, output_schema, plan_item, plugin_install, plugin_list,
plugin_read, plugin_uninstall, rate_limits, realtime_conversation, request_permissions,
request_user_input, review, safety_check_downgrade, skills_list, thread_archive, thread_fork,
thread_list, thread_loaded_list, thread_metadata_update, thread_name_websocket, thread_read,
thread_resume, thread_rollback, thread_shell_command, thread_start, thread_status,
thread_unarchive, thread_unsubscribe, turn_interrupt, turn_start, turn_start_zsh_fork,
turn_steer, windows_sandbox_setup
