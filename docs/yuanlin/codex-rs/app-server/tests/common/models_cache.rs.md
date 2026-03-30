# `models_cache.rs` — 模型缓存写入工具

## 功能概述
提供用于在测试中创建 `models_cache.json` 文件的函数。`write_models_cache` 从内置模型预设列表中
筛选可见模型，转换为 `ModelInfo` 格式并写入缓存文件，防止 `ModelsManager` 发起网络请求。
`write_models_cache_with_models` 允许写入自定义模型列表。缓存文件包含获取时间戳、etag、客户端版本和模型数据。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `write_models_cache` | `pub fn write_models_cache(codex_home: &Path) -> std::io::Result<()>` | 使用内置预设写入模型缓存文件 |
| `write_models_cache_with_models` | `pub fn write_models_cache_with_models(codex_home: &Path, models: Vec<ModelInfo>) -> std::io::Result<()>` | 使用自定义模型列表写入缓存文件 |

## 依赖关系
- **引用的 crate**: `chrono`, `codex_core`, `codex_protocol`, `serde_json`
- **被引用方**: `common/lib.rs` 导出，供 account、model_list 等测试使用
