# plugins

## 功能概述

`codex-utils-plugins` 提供了插件路径解析和纯文本提及语法的共享常量与工具函数，供 Codex 各 crate 复用。

核心功能：
- 从技能文件路径向上查找最近的 `plugin.json` 清单文件，提取插件命名空间
- 定义工具和插件在纯文本中的提及符号（sigil）

## 目录结构

```
plugins/
├── Cargo.toml
└── src/
    ├── lib.rs               # 模块导出
    ├── mention_syntax.rs    # 提及语法常量
    └── plugin_namespace.rs  # 插件命名空间解析
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `serde` | 插件清单 JSON 反序列化 |
| `serde_json` | JSON 解析 |

## 核心接口/API

### 常量

```rust
pub const PLUGIN_MANIFEST_PATH: &str = ".codex-plugin/plugin.json";
pub const TOOL_MENTION_SIGIL: char = '$';          // 工具提及符号
pub const PLUGIN_TEXT_MENTION_SIGIL: char = '@';   // 插件文本提及符号
```

### `plugin_namespace_for_skill_path(path: &Path) -> Option<String>`

从给定的技能文件路径向上遍历祖先目录，查找包含有效 `plugin.json` 清单的目录，返回插件的 `name` 字段。

```rust
// 假设 /plugins/sample/.codex-plugin/plugin.json 包含 {"name": "sample"}
let ns = plugin_namespace_for_skill_path(Path::new("/plugins/sample/skills/search/SKILL.md"));
assert_eq!(ns, Some("sample".to_string()));
```

查找规则：
- 若清单中 `name` 字段为空或仅空白，使用插件根目录的目录名作为名称
- 否则使用清单中的 `name` 字段值
