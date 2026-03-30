# `server_request_error.rs` — Turn 转换期间的服务端请求错误检测

本模块定义了 `TURN_TRANSITION_PENDING_REQUEST_ERROR_REASON` 常量（值为 `"turnTransition"`）和 `is_turn_transition_server_request_error` 函数。该函数检查 `JSONRPCErrorError` 的 `data` 字段中是否包含 `reason: "turnTransition"`，用于识别因 Turn 状态转换（如中断/回滚）而导致的待处理服务端请求被取消的情况。包含两个单元测试验证正向和反向匹配。
