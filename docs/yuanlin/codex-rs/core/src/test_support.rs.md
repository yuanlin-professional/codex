# `test_support.rs` — 跨 crate 集成测试辅助工具

## 功能概述

此文件提供仅用于测试的辅助函数和工厂方法，供跨 crate 集成测试使用。包括创建测试用的 `ThreadManager`、`AuthManager`、`ModelsManager`，以及获取离线模型信息和模型预设列表等。生产代码不应依赖此模块。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TEST_MODEL_PRESETS` | 静态变量 | 从 `models.json` 加载的测试模型预设列表 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `set_thread_manager_test_mode` | `(enabled: bool)` | 启用/禁用线程管理器测试模式 |
| `set_deterministic_process_ids` | `(enabled: bool)` | 启用/禁用确定性进程 ID（用于可重复测试） |
| `auth_manager_from_auth` | `(auth) -> Arc<AuthManager>` | 从 CodexAuth 创建测试用 AuthManager |
| `thread_manager_with_models_provider` | `(auth, provider) -> ThreadManager` | 创建带指定提供商的测试 ThreadManager |
| `start_thread_with_user_shell_override` | `async (thread_manager, config, shell) -> Result<NewThread>` | 使用自定义 shell 启动测试线程 |
| `construct_model_info_offline` | `(model, config) -> ModelInfo` | 离线构建模型信息（无需网络） |
| `all_model_presets` | `() -> &'static Vec<ModelPreset>` | 获取所有测试模型预设 |

## 依赖关系

- **引用的 crate**: `codex_exec_server`、`codex_protocol`、`once_cell`、多个内部模块
- **被引用方**: `lib.rs` 公开导出此模块，供其他 crate 的集成测试使用

## 实现备注

- 使用 `Lazy` 延迟加载 `models.json` 并按优先级排序
- 明确声明生产代码不应依赖此模块，选择公开模块而非使用 crate feature 以避免多重编译组合
- 提供 `resume_thread_from_rollout_with_user_shell_override` 支持从回滚恢复的测试场景
