# `flags.rs` — 一句话概述

通过 `env_flags!` 宏定义环境变量标志 `CODEX_RS_SSE_FIXTURE`，用于在离线测试中指定 SSE fixture 文件路径。当该环境变量被设置时，客户端将使用本地 fixture 文件替代真实的 SSE 网络请求。
