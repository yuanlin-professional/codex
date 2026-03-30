# `mod.rs` — auth 模块入口
聚合认证子模块：`default_client`（HTTP 客户端）、`error`（错误类型）、`storage`（存储后端）、`util`（工具函数）和 `manager`（核心认证管理器）。从 `manager` 模块 re-export 所有公共类型。
