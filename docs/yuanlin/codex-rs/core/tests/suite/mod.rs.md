# `mod.rs` — 测试模块入口

## 测试覆盖范围

作为测试套件(suite)的模块聚合入口文件，负责声明和组织所有独立集成测试模块。同时在测试二进制启动前通过 `#[ctor]` 初始化 Codex 辅助别名（apply_patch 和 codex-linux-sandbox 的 arg0 分发），确保测试环境与生产环境的 arg0 行为一致。

## 模块列表

该文件声明了以下测试子模块（按字母序，A-F 范围内的模块已在本次文档化中覆盖）：

- `abort_tasks` — 任务中断与中止
- `agent_jobs` — 代理批量任务
- `agent_websocket` — 代理 WebSocket 交互
- `apply_patch_cli` — apply_patch CLI 集成
- `approvals` — 命令执行审批机制
- `auth_refresh` — 认证令牌刷新
- `cli_stream` — CLI 流式输出
- `client` — 模型客户端请求构建
- `client_websockets` — 客户端 WebSocket 通信
- `code_mode` — 代码执行模式
- `codex_delegate` — Codex 委托机制
- `collaboration_instructions` — 协作指令
- `compact` — 上下文压缩
- `compact_remote` — 远程上下文压缩
- `compact_resume_fork` — 压缩/恢复/分叉组合
- `deprecation_notice` — 弃用通知
- `exec` — 命令执行(沙箱)
- `exec_policy` — 执行策略
- `fork_thread` — 线程分叉

以及更多未在本次文档化范围内的模块（如 hooks、items、memories、plugins 等）。
