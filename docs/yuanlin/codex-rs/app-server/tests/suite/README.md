# suite

## 功能概述

`tests/suite/` 是 `app-server` 集成测试的用例集合目录。它按功能模块组织测试，涵盖了认证、对话摘要、模糊文件搜索等功能的端到端测试，以及通过 `v2/` 子目录包含的大量基于 `McpProcess` 的协议级集成测试。

所有测试模块通过 `tests/all.rs` -> `tests/suite/mod.rs` 的模块链被编译到同一个测试二进制中。

## 目录结构

```
suite/
├── mod.rs                      # 模块入口，声明所有子模块
├── auth.rs                     # 认证相关测试
├── conversation_summary.rs     # 对话摘要功能测试
├── fuzzy_file_search.rs        # 模糊文件搜索测试
├── zsh                         # zsh 测试辅助脚本
└── v2/                         # V2 协议集成测试用例集
```

## 核心接口/API

### mod.rs - 模块声明

注册四个子模块：
- `auth` - 认证流程测试
- `conversation_summary` - 对话摘要生成测试
- `fuzzy_file_search` - 模糊文件搜索会话测试
- `v2` - V2 协议版本的综合集成测试

### auth.rs

测试 app-server 的认证状态查询和账户管理流程，包括 ChatGPT 认证模式下的认证状态获取、令牌刷新等场景。

### conversation_summary.rs

测试对话摘要功能，验证从线程历史中提取和生成对话摘要的正确性。

### fuzzy_file_search.rs

测试模糊文件搜索功能，包括搜索会话的启动、查询执行、结果返回和会话停止的完整生命周期。

### zsh

用于 zsh 相关 shell 命令测试的辅助脚本。
