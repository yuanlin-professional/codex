# `validation.rs` — 指标名称和标签校验

## 功能概述
该文件提供指标名称和标签键/值的校验函数。指标名称仅允许字母数字、点、下划线和连字符；标签键/值额外允许斜杠。所有校验函数在输入为空或包含非法字符时返回对应的 `MetricsError`。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| 无独立结构体 | — | 纯校验函数 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `validate_tags` | `(tags: &BTreeMap) -> Result<()>` | 校验所有标签键和值 |
| `validate_metric_name` | `(name: &str) -> Result<()>` | 校验指标名称 |
| `validate_tag_key` | `(key: &str) -> Result<()>` | 校验标签键 |
| `validate_tag_value` | `(value: &str) -> Result<()>` | 校验标签值 |

## 依赖关系
- **引用的 crate**: 无
- **被引用方**: `MetricsClient`, `MetricsConfig`

## 实现备注
- 指标字符允许集：`[a-zA-Z0-9._-]`
- 标签字符允许集：`[a-zA-Z0-9._-/]`
