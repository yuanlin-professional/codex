# `setupCodexHome.ts` -- 测试环境 CODEX_HOME 管理

`setupCodexHome.ts` 是一个 Jest 设置文件，在每个测试用例前创建临时的 `CODEX_HOME` 目录并设置环境变量，在测试结束后恢复原始值并删除临时目录。这确保了测试之间的隔离性，避免测试污染用户的真实 Codex 配置目录。
