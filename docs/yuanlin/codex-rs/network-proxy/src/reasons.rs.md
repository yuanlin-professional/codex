# `reasons.rs` -- 网络代理阻断原因常量

网络代理各模块共用的阻断原因字符串常量，包括 `denied`（拒绝列表命中）、`not_allowed`（不在允许列表）、`not_allowed_local`（本地/私有地址不允许）、`method_not_allowed`（方法不允许）、`mitm_required`（Limited 模式需要 MITM）、`policy_denied`（策略拒绝）、`proxy_disabled`（代理未启用）和 `unix_socket_unsupported`（Unix 套接字不支持）。
